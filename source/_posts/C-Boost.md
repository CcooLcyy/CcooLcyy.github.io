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

## 基础教程

### 同步使用定时器

对于所有使用asio的程序我们都至少需要提供一个IO执行上下文，可以是`io_context`或者是`thread_pool`对象，一个IO执行上下文提供了IO访问能力，通过以下方式声明一个IO执行上下文。

```c++
boost::asio::io_context io;
```

接下来我们需要声明一个定时器类型的对象。提供IO功能核心asio类（在这个例子中是一个定时器功能）总是需要获取一个执行器，或者执行上下文的引用（比如io_context）用来作为第一个构造函数的参数，第二个构造函数的参数可以设置为一个从现在开始5秒到期的时间。

```c++
boost::asio::steady_timer t(io, std::chrono::seconds(5));
```

在这个例子中我们使用`wait`在计时器中阻塞。也就是说对`steady_timer::wait()`的调用不会返回，直到定时器过期，也就是定时器创建之后的5秒，而不是从等待开始时5秒。

定时器只有两种状态，到期（expired）未到期（not expired），如果`steady_timer::wait()`功能通过一个到期的定时器调用，他会立即返回。

### 异步使用定时器

使用asio的异步功能需要提供一个完成标志，这个标志决定了结果当异步操作完成后如何传递给完成事件处理函数。在这个例子中我们提供一个`print`函数当我们的异步等待结束之后这个函数会被调用。

```c++
void print(const boost::system::error_code&) {
    //
}
```

接下来我们调用`steady_timer::async_wait()`函数展示异步等待，当我们调用这个函数时，我们传递`print`函数的指针到这个函数中。

```c++
t.async_wait(&print);
```

最后我们在`io_context`对象上调用`boost::asio::io_context::run()`成员函数。asio保证事件处理函数只会被正在调用`boost::asio::io_context::run()`的线程调用。因此除非`boost::asio::io_context::run()`函数被调用，否则永远不会调用异步等待完成的事件处理函数。

`boost::asio::io_context::run()`函数当有"work"要做的时候将会继续运行，在这个例子中，这个work是定时器的异步等待，所以这个调用不会被返回，直到定时器超时并且完成事件处理函数之前。

所以最重要的是记得给`io_context`一些工作在调用`boost::asio::io_context::run()`之前。例如，如果我们忽略了之前的定时器异步等待操作而直接`run`IO执行上下文的话，IO执行上下文会没事可干并且直接返回。

### 为异步等待绑定参数

在异步等待的时候函数`async_wait()`传入一个函数指针，如果需要传入一个参数，需要使用`std::bind`主要是`std::bind`函数的使用。

### 将成员函数作为事件处理函数

与前面的教程很相似，没有什么新的概念。由于任何非静态成员变量都会隐含一个`this`指针，因此当我们需要传入类的非静态成员变量的时候需要使用`std::bind`将`this`指针绑定。其他与之前的没有区别。

### 并发异步处理

```c++
#include <boost/asio.hpp>
#include <functional>
#include <iostream>
#include <thread>

class printer {
public:
    printer(boost::asio::io_context& io) :
            strand_(boost::asio::make_strand(io)),
            timer1_(io, boost::asio::chrono::seconds(1)),
            timer2_(io, boost::asio::chrono::seconds(1)),
            count_(0) {
        timer1_.async_wait(boost::asio::bind_executor(strand_, std::bind(&printer::print1, this)));
        timer2_.async_wait(boost::asio::bind_executor(strand_, std::bind(&printer::print2, this)));
    }
    ~printer() {
        std::cout << "Final count is " << count_ << std::endl;
    }
    void print1() {
        if (count_ < 10) {
            std::cout << "Timer 1: " << count_ << std::endl;
            ++count_;
            timer1_.expires_at(timer1_.expiry() + boost::asio::chrono::seconds(1));
            timer1_.async_wait(boost::asio::bind_executor(strand_, std::bind(&printer::print1, this)));
        }
    }
    void print2() {
        if (count_ < 10) {
            std::cout << "Timer 2: " << count_ << std::endl;
            ++count_;
            timer2_.expires_at(timer2_.expiry() + boost::asio::chrono::seconds(1));
            timer2_.async_wait(boost::asio::bind_executor(strand_, std::bind(&printer::print2, this)));
        }
    }
private:
    boost::asio::strand<boost::asio::io_context::executor_type> strand_;
    boost::asio::steady_timer timer1_;
    boost::asio::steady_timer timer2_;
    int count_;
};
int main() {
    boost::asio::io_context io;
    printer p(io);
    std::thread t([&]() { io.run(); });
    io.run();
    t.join();
}
```

首先`pointer`类构造函数接收一个`io_context`并且在内部使用这个IO执行上下文创建两个计时器，在这个类中还需要使用`io_context`构建一个`strand`接下来的内容和以成员函数作为功能处理函数相同，接下来主要介绍`strand`。

`strand`是一个串行执行器，在`boost.asio`中是一个非常重要的概念，用来保证多线程环境中的线程安全，如果有多个线程同时从`io_context`中取任务进行操作，尤其是在操作临界资源的时候会引发线程安全，串行执行器能够保证绑定到通过一个串行执行器上的任务在多线程操作时线程的安全。

**strand**用法：

1. 首先通过`boost::make_strand(io_context)`来创建一个`strand`。
2. 通过`boost::asio::bind_executor(strand, handler)`来绑定执行器和处理函数。
3. 使用`run`函数执行IO执行上下文中的任务时会保证同一个`strand`中的任务不会触发。

总结来看，使用`strand`需要使用`boost::asio::strand<boost::asio::io_context::executor_type>`创建，这个变量就可以使用`boost::asio::make_strand`函数将`io_context`传入到`strand`中，这样`strand`就和`io_context`进行了绑定。接着需要将添加到IO执行上下文中的任务使用`boost::asio::bind_executor()`绑定到`strand`上，并且需要将特定的`strand`和待执行的（回调函数）传入（如果需要`std::bind`的话要加上）。

## 异步编程

给出最简单的异步server实现方式。

<!-- ```c++
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
``` -->

### 异步server

```c++
#include <spdlog/spdlog.h>
#include <boost/asio.hpp>
#include <ctime>
#include <functional>
#include <iostream>
#include <memory>
#include <string>

using boost::asio::ip::tcp;

std::string make_daytime_string() {
    using namespace std;  // For time_t, time and ctime;
    time_t now = time(0);
    return ctime(&now);
}

class tcp_connection : public std::enable_shared_from_this<tcp_connection> {
public:
    typedef std::shared_ptr<tcp_connection> pointer;
    static pointer create(boost::asio::io_context& io_context) {
        return pointer(new tcp_connection(io_context));
    }

    tcp::socket& socket() {
        return socket_;
    }

    void start() {
        message_ = make_daytime_string();

        boost::asio::async_write(
            socket_,
            boost::asio::buffer(message_),
            std::bind(
                &tcp_connection::handle_write,
                shared_from_this(),
                boost::asio::placeholders::error,
                boost::asio::placeholders::bytes_transferred));
    }

private:
    tcp_connection(boost::asio::io_context& io_context) : socket_(io_context) {}

    void handle_write(const boost::system::error_code& /*error*/, size_t /*bytes_transferred*/) {}

    tcp::socket socket_;
    std::string message_;
};

class tcp_server {
public:
    tcp_server(boost::asio::io_context& io_context) :
            io_context_(io_context), acceptor_(io_context, tcp::endpoint(tcp::v4(), 13)) {
        start_accept();
    }

private:
    void start_accept() {
        tcp_connection::pointer new_connection = tcp_connection::create(io_context_);

        acceptor_.async_accept(
            new_connection->socket(),
            std::bind(&tcp_server::handle_accept, this, new_connection, boost::asio::placeholders::error));
    }

    void handle_accept(tcp_connection::pointer new_connection, const boost::system::error_code& error) {
        if (!error) {
            spdlog::info("client connected");
            new_connection->start();
        }

        start_accept();
    }

    boost::asio::io_context& io_context_;
    tcp::acceptor acceptor_;
};

int main() {
    try {
        boost::asio::io_context io_context;
        tcp_server server(io_context);
        io_context.run();
    } catch (std::exception& e) {
        std::cerr << e.what() << std::endl;
    }

    return 0;
}
```

### 异步操作的关键：io_context对象

在异步编程的时候我们调用一个异步函数时，这个函数会直接返回，但是我们会在调用这个函数的时候传入一个`handler`这也是一个函数（可调用对象），这个函数代表着虽然异步函数直接返回了，但是如果当有事件到达的时候所进行的处理。也就是使用回调函数的方式来进行操作。

对于`asio`来说，实现异步的方式是使用`io_context`对象，这个对象是asio中最关键的部分，它提供了一种在多线程环境中安全的调度和执行任务的方式，当我们调用一个异步函数的时候，这个函数不管io操作是否完成都会直接返回，当函数返回之后这个函数的内存空间也就释放了，但是我们传入了一个可调用对象时，如果传入的函数返回了，那么传入的可调用对象也就消失了，但是`io_context`会将可调用对象添加到`io_context`的内部队列中，当异步操作完成时会从队列中取出对应的可调用对象并且执行。因此我们可以将`io_context`看作是一个管理和调度回调函数的工具。

### 防止提前析构connection

异步编程相比于同步编程区别在于，在同步的时候如果开启了一个work，则开启work的对象会被阻塞，直到work完成，但是异步的时候work开启之后会立刻返回，开启work的对象也就不会被阻塞。假设我们开启一个服务器进行同步等待连接，在客户端连接到服务端之前服务端都会进行阻塞不会做其他事，如果使用异步编程的话，熟悉异步编程的都知道，需要提供一个回调函数，用来在任务可以执行的时候给出具体的动作。这里代表着如果启动服务端异步等待，在连接到来之前服务端如果有其他任务可以继续做，并不会进行阻塞会继续做其他的事情，当连接到来的时候会调用回调函数处理连接。

异步操作不是多线程操作，本质还是单线程的，并不涉及线程安全的问题，所以即使进行了异步操作，但是当某个任务操作时间很长时，有新的任务到来依然会进行阻塞，主要原因就是在于其本质还是单线程的。异步操作的有点就是在像服务器等待连接这种不占用CPU运行时只进行IO阻塞的情况下能够让出CPU运行时给需要的任务。想要实际解决计算密集型任务的单线程阻塞问题还是得需要多线程工作。

但是由于`io_context`的特性，在没有任务的时候会自动结束，所以我们需要一种方式让`io_contex`知道现在有任务正在进行，实现方式就是使用`std::enable_shared_from_this<class>`来进行，在使用`std::bind`进行绑定的时候需要使用`shared_from_this()`将共享指针传入到回调函数中，由于引用计数器不归零指向连接的只能指针就不会自动析构，也就意味着仍然有任务进行，`io_contex.run()`就不会被返回。

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

**需要注意，可以直接使用`std::bind`将函数`shared_from_this()`绑定到回调函数中，即使这个回调函数没有相应的形参。**

### 能够异步的对象

`io_context`是能够异步操作的关键，只有我们在创建时将`io_context`对象传入的对象才能够进行异步操作。也就是，虽然我们的`io_context`对象不直接管理我们的`Session`对象，但是由于我们使用能够被`io_context`对象管理的`socket`对象创建了这个对象，并且在进行异步的时候使用lambda表达式捕获了`Session`对象的共享指针，因此可以使用`io_context`对`Session`进行管理。

### server和client对于网络操作的不同

对于server来说，我们创建`acceptor`的时候能够监听某个ip协议的某个端口，当client通过socket对server发起连接的时候acceptor能够建立一个socket我们的server可以从这个socket读取或者写入数据，具体体现在使用`accpetor_`对象的`async_accept`方法的时候其中的回调函数有一个参数是`socket`，这个`scoket`就是我们`acceptor`创建的一个`socket`，之后我们就可以对这个具体的`socket`进行读写数据的操作。

而对于client来说，我们需要显式的创建一个`socket`用来主动对server进行连接。

最后由于`TCP`连接是全双工的，就算没有公网的client连接到了有公网的server上，server也可以对没有公网的client传输数据。

### 管理io_context

当我们运行`io_context::run()`的时候会循环处理`io_context`对象的所有时间处理循环，并且处理所有挂起的操作，当没有要进行的操作时`io_context::run()`函数会返回。也就是说如果在某个时刻`io_context`中没有挂起的循环，函数会返回当再有新的操作到来的时候`run()`函数没有运行也就不会处理挂起的操作了。

我们可使用`boost::asio::io_context::work`对象让其保持一直运行，就算没有挂起操作`run()`函数也不会返回。