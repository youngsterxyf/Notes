## 杂项

- Node.js

Node.js是服务器端的JavaScript运行环境，它具有无阻塞(non-blocking)和事件驱动(event-driven)等特色，Node.js采用V8引擎。

> 我想不仅仅是Node.js，当我们要引入任何一种新技术前都必须要搞清楚几个问题：

> 1.我们遇到了什么问题？

> 2.这项新技术解决什么问题，是否契合我们遇到的问题？

> 3.我们遇到问题的多种解决方案中，当前这项新技术的优势体现在哪儿？

> 4.使用新技术，带来哪些新问题，严重么？我们能否解决掉？

----

Node.js被设计用来解决服务器端阻塞问题。下面通过一段简单的代码解释何为阻塞：

js代码：

	//根据ID，在数据库的Persons表中查出Name
	var name = db.query("select name from persons where id=1")
	//进程等待数据查询完毕，然后使用查询结果。
	output(name)

这段代码的问题是在上面两个语句之间，在整个数据查询的过程中，当前程序进程往往只是在等待结果的返回。这就造成了进程的阻塞。对于高并发，I/O密集型的网络应用中，一方面进程很长时间处于等待状态，另一方面为了应付新的请求不断地增加新的进程。这样的浪费会导致系统支持QPS远远小于后端数据服务能够支撑的QPS，成为了系统的瓶颈。而且这样的系统也特别容易被慢链接攻击(客户端故意不接收或减缓接收数据，加长进程等待时间)。

如何解决阻塞问题？可以引入事件处理机制解决。在查询请求发起之前注册数据加载事件的响应函数，请求发出后立即将进程交出，而当数据返回后再触发这个事件并在预定好的事件响应函数中继续处理数据:

js代码:

	//定义后续如何处理数据的函数
	function onDataLoad(name){
		output(name);	
	}
	//发起数据请求，同时指定数据返回后的回调函数
	db.query("select name from persons where id=1", onDataLoad);

按照这个思路解决阻塞问题，首先我们要提供一套高效的异步事件调度机制。而主要用于处理浏览器端的各种交互事件的JavaScript，相比其他语言，至少有两个关键点特别适合完成这个任务。

首先JavaScript是一种函数式编程语言，函数编程语言最重要的数学基础是lambda演算-即函数对象可以作为其他函数对象的输入(参数)和输出(返回值)。这个特性使得为事件指定回调函数变得很容易。特别是JavaScript还支持匿名函数。通过匿名函数的辅助，之前的代码可以进行简写如下:

js代码:

	db.query("select name from persons where id=1", function(name){output(name);});

另外一个关键问题是，异步回调的运行上下文保持(暂且称其为"状态保持")。先来看一段代码来说明何为状态保持:

js代码：
	
	//传统同步写法:将查询和结果打印抽象为一个方法
	function main(){
		var id = "1";
		var name = db.query("select name from persons where id=" + id);
		output("person id:"+id+", name:"+name);
	}
	main();

上述写法在传统的阻塞式编程中非常常见，但接下来进行异步改写时会遇到一些困扰：

js代码:

	//异步写法
	function main(){
		var id = "1";
		db.query("select name from persons where id="+id, function(name){
			output("person id:"+ id + ", name:"+name); // n秒后数据返回执行回调。		
		});
	}
	main();

上述代码中，当等待了n秒数据查询结果返回后执行回调时。回调函数中却仍然使用了main函数的局部变量"id"，而"id"似乎应该在n秒前走出了其作用域。为什么此时"id"仍然可以访问呢，这是因为JavaScript的另外一个重要语言特性：闭包(closure)。

*(上述内容摘自:<http://www.infoq.com/cn/articles/why-recommend-nodejs>)*

----

Node公开宣称的目标是“旨在提供一种简单的构建可伸缩网络程序的方法”。当前服务器程序有什么问题？我们来做个数学题：在java和PHP这类语言中，每个连接都会生成一个新线程，每个线程可能需要2MB的配套程序。在一个拥有8GB RAM的系统上，理论上最大的并发连接数量是4000个用户。随着您的客户群的增长，如果希望您的Web应用程序支持更多用户，那么，您必须添加更多的服务器。当然，这会增加服务器成本，流量成本和人工成本等。除了这些成本上升之外，还有一个潜在技术问题，即用户可能针对每个请求使用不同的服务器，因此，任何共享资源都必须在所有服务器之间共享。鉴于上述所有原因，整个Web应用程序架构(包括流量，处理器速度和内存速度)中的瓶颈是：服务器能够处理的并发连接的最大数量。

----

Node解决这个问题的方法是：更改连接到服务器的方式。每个连接触发一个在Node引擎的进程中运行的事件，而不是为每个连接生成一个新的OS线程(并为其分配一些配套内存)。

Node本身运行V8 JavaScript，什么是V8？V8 JavaScript引擎是Google用于其Chrome浏览器的底层JavaScript引擎。V8使用C++语言实现，效率超快。更重要的是：V8 can run standalone, or can be embedded into any C++ application。而Node就是将V8 JavaScript重建为可在服务器上使用。

-----

Node 表现出众的典型示例包括：

> 1. RESTful API: 提供 RESTful API 的 Web 服务，接收几个参数，解析它们，组合一个响应，并返回一个响应（通常是较少的文本）给用户。这是适合 Node 的理想情况，因为您可以构建它来处理数万条连接。它仍然不需要大量逻辑；它本质上只是从某个数据库中查找一些值并将它们组成一个响应。由于响应是少量文本，入站请求也是少量的文本，因此流量不高，一台机器甚至也可以处理最繁忙的公司的 API 需求。

> 2. Twitter 队列: 想像一下像 Twitter 这样的公司，它必须接收 tweets 并将其写入数据库。实际上，每秒几乎有数千条 tweet 达到，数据库不可能及时处理高峰时段所需的写入数量。Node 成为这个问题的解决方案的重要一环。如您所见，Node 能处理数万条入站 tweet。它能快速而又轻松地将它们写入一个内存排队机制（例如 memcached），另一个单独进程可以从那里将它们写入数据库。Node 在这里的角色是迅速收集 tweet，并将这个信息传递给另一个负责写入的进程。想象一下另一种设计（常规 PHP 服务器会自己尝试处理对数据库本身的写入）：每个 tweet 都会在写入数据库时导致一个短暂的延迟，因为数据库调用正在阻塞通道。由于数据库延迟，一台这样设计的机器每秒可能只能处理 2000 条入站 tweet。每秒处理 100 万条 tweet 则需要 500 个服务器。相反，Node 能处理每个连接而不会阻塞通道，从而能够捕获尽可能多的 tweets。一个能处理 50,000 条 tweet 的 Node 机器仅需 20 台服务器即可。

(上述内容摘自<http://www.ibm.com/developerworks/cn/opensource/os-nodejs/index.html?ca=drs>)

- Apache Thrift

The Apache Thrift software framework, for scalable cross-language services development, combines a software stack with a code generation engine to build services that work efficiently and seamlessly between C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, OCaml and Delphi and other languages.

Apache Thrift allows you to define data types and service interfaces in a simple definition file. Taking that file as input, the compiler generates code to be used to easily build RPC clients and servers that communicate seamlessly across programming languages. Instead of writing a load of boilerplate code to serialize and transport your objects and invoke remote methods, you can get right down to business.
