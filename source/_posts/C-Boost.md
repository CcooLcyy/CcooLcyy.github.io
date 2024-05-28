---
title: C++ Boost
date: 2024-05-27 14:44:32
tags:
---

# 编译

首先运行`./bootstrap.sh --with-toolset=gcc`，运行结束之后会产生一个`project-config.jam`文件，在这个文件中修改12行，添加以下内容

`using gcc : arm : /usr/bin/aarch64-linux-gnu-g++-8 ;`

**要注意，在上面分号前一定要有空格，不然无法识别！！！**
**要注意，在上面分号前一定要有空格，不然无法识别！！！**
**要注意，在上面分号前一定要有空格，不然无法识别！！！**

![](../image/C++Boost/project-config.png)

`./b2 toolset=gcc target-os=linux architecture=arm address-model=64`

使用`-a`来重新进行编译。

在编译完成之后使用`./b2 install`将其安装到默认的路径。

# asio

在使用boost库的asio时要依赖pthread，要按照以下方式编写cmake文件

```cmake
find_package(Boost REQUIRED COMPONENTS system)
find_package(Threads REQUIRED)

target_include_directories(${PROJECT_NAME} PRIVATE ${Boost_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} PRIVATE Boost::json Boost::system Threads::Threads)
```

# asio

## 异步编程

给出最简单的异步server实现方式。

```c++
#include <boost/asio/io_context.hpp>
#include <boost/asio/ip/address.hpp>
#include <boost/asio/ip/address_v4.hpp>
#include <boost/asio/registered_buffer.hpp>
#include <boost/asio/steady_timer.hpp>
#include <boost/asio/write.hpp>
#include <boost/system/detail/error_code.hpp>
#include <cstddef>
#include <cstdint>
#include <cstdlib>
#include <iostream>
#include <memory>

#include "boost/asio.hpp"

using namespace boost::asio;
using namespace boost::asio::ip;

class Session : public std::enable_shared_from_this<Session> {
public:
    Session(tcp::socket socket) : socket_(std::move(socket)) {}
    void start() {
        do_read();
    }

private:
    void do_read() {
        auto self(shared_from_this());
        socket_.async_read_some(
            boost::asio::buffer(buffer_), [this, self](boost::system::error_code err, std::size_t length) {
                if (!err) {
                    do_write(length);
                }
            });
    }
    void do_write(std::size_t length) {
        auto self(shared_from_this());
        boost::asio::async_write(
            socket_,
            boost::asio::buffer(buffer_, length),
            [this, self](boost::system::error_code err, std::size_t length) {
                if (!err) {
                    do_read();
                }
            });
    }

private:
    tcp::socket socket_;
    std::array<char, 1024> buffer_;
};

class Server {
public:
    Server(io_context &io, std::uint16_t port) : acceptor_(io, tcp::endpoint(tcp::v4(), port)) {
        do_accept();
    }

private:
    void do_accept() {
        acceptor_.async_accept([this](boost::system::error_code err, tcp::socket socket) {
            if (!err) {
                std::make_shared<Session>(std::move(socket))->start();
            }
            do_accept();
        });
    }

private:
    tcp::acceptor acceptor_;
};

int main(int argc, char *argv[]) {
    if (argc != 2) {
        std::cerr << "Usage: " << argv[0] << " <port>" << std::endl;
        return 1;
    }

    std::uint16_t port = std::atoi(argv[1]);
    boost::asio::io_context io;
    Server server(io, port);
    io.run();
}
```

### 异步操作的关键：io_context对象

在异步编程的时候我们调用一个异步函数时，这个函数会直接返回，但是我们会在调用这个函数的时候传入一个`handler`这也是一个函数（可调用对象），这个函数代表着虽然异步函数直接返回了，但是如果当有事件到达的时候所进行的处理。也就是使用回调函数的方式来进行操作。

对于`asio`来说，实现异步的方式是使用`io_context`对象，这个对象是asio中最关键的部分，它提供了一种在多线程环境中安全的调度和执行任务的方式，当我们调用一个异步函数的时候，这个函数不管io操作是否完成都会直接返回，当函数返回之后这个函数的内存空间也就释放了，但是我们传入了一个可调用对象时，如果传入的函数返回了，那么传入的可调用对象也就消失了，但是`io_context`会将可调用对象添加到`io_context`的内部队列中，当异步操作完成时会从队列中取出对应的可调用对象并且执行。因此我们可以将`io_context`看作是一个管理和调度回调函数的工具。

### 防止提前析构session

在进行异步编程的时候因为异步函数直接返回了，而回调操作是由`io_context`进行管理的，当有消息到达的时候仔细观察上面的session类可以发现，在调用回调函数的时候需要保证session对象一直是存在的，如何做到这一点？

```c++
class Session : public std::enable_shared_from_this<Session> {
public:
    Session(tcp::socket socket) : socket_(std::move(socket)) {}
    void start() {
        do_read();
    }

private:
    void do_read() {
        auto self(shared_from_this());
        socket_.async_read_some(
            boost::asio::buffer(buffer_), [this, self](boost::system::error_code err, std::size_t length) {
                if (!err) {
                    do_write(length);
                }
            });
    }
    void do_write(std::size_t length) {
        auto self(shared_from_this());
        boost::asio::async_write(
            socket_,
            boost::asio::buffer(buffer_, length),
            [this, self](boost::system::error_code err, std::size_t length) {
                if (!err) {
                    do_read();
                }
            });
    }

private:
    tcp::socket socket_;
    std::array<char, 1024> buffer_;
};
```

我们通过使`Session`继承`std::enable_shared_from_this<Session>`来实现，其中`std::enable_shared_from_this<Session>`的作用是允许对象通过`shared_from_this`成员函数来安全的创建一个`std::shared_ptr`，如果一个类`T`这里是`Session`，继承了`std::enable_shared_from_this<T>`那么它就可以使用`shared_from_this`函数来获取一个指向自身的`std::shared_ptr`。

我们都知道`shared_ptr`是共享指针，其中维护了一个引用计数，只要这个计数不为0的话，就不会对指针指向的对象进行析构，我们可以通过这个特性来保证在调用回调函数的时候`Session`对象仍然是存在的。

我们可以发现在每个具体的do函数中第一行都有一个`auto self(shared_from_this())`，这行代码可以通过刚才提到的函数来创建一个指向自身对象的共享指针，并且将这个指针交给`self`变量。因为在外面也就是`Server`中我们是使用`std::make_shared<Session>()`创建了对象，此时共享指针的引用计数为1，当我们执行`Session`中第一个do方法`do_read`的时候又会创建一个共享指针，此时指针的引用计数变成了2，接着`self`变量被lambda表达式捕获，由于lambda表达式普通捕获是复制操作，因此这里的引用计数就变成了3，接着异步函数会直接返回，而我们执行的`do_read`函数也有由于执行完成而返回，`auto self(shared_from_this())`创建的`shared_ptr`就会被销毁，此时引用计数变为2。lambda表达式会交给`io_context`对象管理，而lambda中捕获了创建出来的共享指针，因此只要`io_context`对象不销毁对`Session`的引用计数就至少为1，由此就实现了保证`Session`对象在使用的时候一定会存在。

需要注意，lambda捕获的`this`和`self`作用是不同的，`self`作用是用来延长`Session`对象的声明周期，而`this`的作用是让lambda中能够使用该`Session`的成员函数。

### 能够异步的对象

`io_context`是能够异步操作的关键，只有我们在创建时将`io_context`对象传入的对象才能够进行异步操作。也就是，虽然我们的`io_context`对象不直接管理我们的`Session`对象，但是由于我们使用能够被`io_context`对象管理的`socket`对象创建了这个对象，并且在进行异步的时候使用lambda表达式捕获了`Session`对象的共享指针，因此可以使用`io_context`对`Session`进行管理。

### server和client对于网络操作的不同

对于server来说，我们创建`acceptor`的时候能够监听某个ip协议的某个端口，当client通过socket对server发起连接的时候acceptor能够建立一个socket我们的server可以从这个socket读取或者写入数据，具体体现在使用`accpetor_`对象的`async_accept`方法的时候其中的回调函数有一个参数是`socket`，这个`scoket`就是我们`acceptor`创建的一个`socket`，之后我们就可以对这个具体的`socket`进行读写数据的操作。

而对于client来说，我们需要显式的创建一个`socket`用来主动对server进行连接。

最后由于`TCP`连接是全双工的，就算没有公网的client连接到了有公网的server上，server也可以对没有公网的client传输数据。

### 管理io_context

当我们运行`io_context::run()`的时候会循环处理`io_context`对象的所有时间处理循环，并且处理所有挂起的操作，当没有要进行的操作时`io_context::run()`函数会返回。也就是说如果在某个时刻`io_context`中没有挂起的循环，函数会返回当再有新的操作到来的时候`run()`函数没有运行也就不会处理挂起的操作了。

我们可使用`boost::asio::io_context::work`对象让其保持一直运行，就算没有挂起操作`run()`函数也不会返回。