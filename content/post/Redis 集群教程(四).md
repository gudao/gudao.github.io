---
title: "Redis 集群教程(四)"
date: 2014-02-08
draft: false
tags: [Redis cluster tutorial]
categories: [Redis]
---

![](/Content/upload/Img20131226/s_02wcido5_0xx.png) 


## 创建集群


现在我们已经有了六个正在运行中的 Redis 实例，接下来我们需要使用这些实例来创建集群，并为每个节点编写配置文件。

通过使用 Redis 集群命令行工具 <tt class="docutils literal">redis-trib</tt> ，编写节点配置文件的工作可以非常容易地完成： <tt class="docutils literal">redis-trib</tt> 位于 Redis 源码的 <tt class="docutils literal">src</tt> 文件夹中，它是一个 Ruby 程序，这个程序通过向实例发送特殊命令来完成创建新集群，检查集群，或者对集群进行重新分片（reshared）等工作。

我们需要执行以下命令来创建集群：



<pre>./redis-trib.rb create --replicas 1 127.0.0.1:7000 127.0.0.1:7001 \
127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005</pre>


命令的意义如下：


- 给定 redis-trib.rb 程序的命令是 create ，这表示我们希望创建一个新的集群。
- 选项 --replicas 1 表示我们希望为集群中的每个主节点创建一个从节点。
- 之后跟着的其他参数则是实例的地址列表，我们希望程序使用这些地址所指示的实例来创建新集群。

简单来说，以上命令的意思就是让 <tt class="docutils literal">redis-trib</tt> 程序创建一个包含三个主节点和三个从节点的集群。

接着， <tt class="docutils literal">redis-trib</tt> 会打印出一份预想中的配置给你看，如果你觉得没问题的话，就可以输入 <tt class="docutils literal">yes</tt> ， <tt class="docutils literal">redis-trib</tt> 就会将这份配置应用到集群当中：




<pre>&gt;&gt;&gt; Creating cluster Connecting to node 127.0.0.1:7000: OK Connecting to node 127.0.0.1:7001: OK Connecting to node 127.0.0.1:7002: OK Connecting to node 127.0.0.1:7003: OK Connecting to node 127.0.0.1:7004: OK Connecting to node 127.0.0.1:7005: OK &gt;&gt;&gt; Performing hash slots allocation on 6 nodes... Using 3 masters: 127.0.0.1:7000 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7000 replica #1 is 127.0.0.1:7003 127.0.0.1:7001 replica #1 is 127.0.0.1:7004 127.0.0.1:7002 replica #1 is 127.0.0.1:7005 M: 9991306f0e50640a5684f1958fd754b38fa034c9 127.0.0.1:7000 slots:0-5460 (5461 slots) master M: e68e52cee0550f558b03b342f2f0354d2b8a083b 127.0.0.1:7001 slots:5461-10921 (5461 slots) master M: 393c6df5eb4b4cec323f0e4ca961c8b256e3460a 127.0.0.1:7002 slots:10922-16383 (5462 slots) master S: 48b728dbcedff6bf056231eb44990b7d1c35c3e0 127.0.0.1:7003 S: 345ede084ac784a5c030a0387f8aaa9edfc59af3 127.0.0.1:7004 S: 3375be2ccc321932e8853234ffa87ee9fde973ff 127.0.0.1:7005 Can I set the above configuration? (type &#39;yes&#39; to accept): yes </pre>



输入 <tt class="docutils literal">yes</tt> 并按下回车确认之后，集群就会将配置应用到各个节点，并连接起（join）各个节点 ——也即是，让各个节点开始互相通讯：




<pre>&gt;&gt;&gt; Nodes configuration updated &gt;&gt;&gt; Sending CLUSTER MEET messages to join the cluster Waiting for the cluster to join... &gt;&gt;&gt; Performing Cluster Check (using node 127.0.0.1:7000) M: 9991306f0e50640a5684f1958fd754b38fa034c9 127.0.0.1:7000 slots:0-5460 (5461 slots) master M: e68e52cee0550f558b03b342f2f0354d2b8a083b 127.0.0.1:7001 slots:5461-10921 (5461 slots) master M: 393c6df5eb4b4cec323f0e4ca961c8b256e3460a 127.0.0.1:7002 slots:10922-16383 (5462 slots) master M: 48b728dbcedff6bf056231eb44990b7d1c35c3e0 127.0.0.1:7003 slots: (0 slots) master M: 345ede084ac784a5c030a0387f8aaa9edfc59af3 127.0.0.1:7004 slots: (0 slots) master M: 3375be2ccc321932e8853234ffa87ee9fde973ff 127.0.0.1:7005 slots: (0 slots) master [OK] All nodes agree about slots configuration. </pre>



如果一切正常的话， <tt class="docutils literal">redis-trib</tt> 将输出以下信息：




<pre>&gt;&gt;&gt; Check for open slots... &gt;&gt;&gt; Check slots coverage... [OK] All 16384 slots covered. </pre>



这表示集群中的 <tt class="docutils literal">16384</tt> 个槽都有至少一个主节点在处理，集群运作正常。




## 集群的客户端


Redis 集群现阶段的一个问题是客户端实现很少。以下是一些我知道的实现：


- redis-rb-cluster 是我（@antirez）编写的 Ruby 实现，用于作为其他实现的参考。该实现是对 redis-rb 的一个简单包装，高效地实现了与集群进行通讯所需的最少语义（semantic）。
- redis-py-cluster 看上去是 redis-rb-cluster 的一个 Python 版本，这个项目有一段时间没有更新了（最后一次提交是在六个月之前），不过可以将这个项目用作学习集群的起点。
- 流行的 Predis 曾经对早期的 Redis 集群有过一定的支持，但我不确定它对集群的支持是否完整，也不清楚它是否和最新版本的 Redis 集群兼容（因为新版的 Redis 集群将槽的数量从 4k 改为 16k 了）。
- Redis unstable 分支中的 redis-cli 程序实现了非常基本的集群支持，可以使用命令 redis-cli -c 来启动。

测试 Redis 集群比较简单的办法就是使用 <tt class="docutils literal">redis-rb-cluster</tt> 或者 <tt class="docutils literal">redis-cli</tt> ，接下来我们将使用 <tt class="docutils literal">redis-cli</tt> 为例来进行演示：



<pre>$ redis-cli -c -p 7000
redis 127.0.0.1:7000&gt; set foo bar
-&gt; Redirected to slot [12182] located at 127.0.0.1:7002
OK

redis 127.0.0.1:7002&gt; set hello world
-&gt; Redirected to slot [866] located at 127.0.0.1:7000
OK

redis 127.0.0.1:7000&gt; get foo
-&gt; Redirected to slot [12182] located at 127.0.0.1:7002
&#34;bar&#34;

redis 127.0.0.1:7000&gt; get hello
-&gt; Redirected to slot [866] located at 127.0.0.1:7000
&#34;world&#34;</pre>


<tt class="docutils literal">redis-cli</tt> 对集群的支持是非常基本的，所以它总是依靠 Redis 集群节点来将它转向（redirect）至正确的节点。

一个真正的（serious）集群客户端应该做得比这更好：它应该用缓存记录起哈希槽与节点地址之间的映射（map），从而直接将命令发送到正确的节点上面。

这种映射只会在集群的配置出现某些修改时变化，比如说，在一次故障转移（failover）之后，或者系统管理员通过添加节点或移除节点来修改了集群的布局（layout）之后，诸如此类。




## 使用 <tt class="docutils literal">redis-rb-cluster</tt> 编写一个示例应用


在展示如何使用集群进行故障转移、重新分片等操作之前，我们需要创建一个示例应用，了解一些与 Redis 集群客户端进行交互的基本方法。

在运行示例应用的过程中，我们会尝试让节点进入失效状态，又或者开始一次重新分片，以此来观察 Redis 集群在真实世界运行时的表现，并且为了让这个示例尽可能地有用，我们会让这个应用向集群进行写操作。

本节将通过两个示例应用来展示 <tt class="docutils literal">redis-rb-cluster</tt> 的基本用法，以下是本节的第一个示例应用，它是一个名为 [example.rb](https://github.com/antirez/redis-rb-cluster/blob/master/cluster.rb) 的文件，包含在[redis-rb-cluster 项目里面](https://github.com/antirez/redis-rb-cluster)：



<table><tbody><tr><td>


<pre> 1
 2
 3
 4
 5
 6
 7
 8
 9
10
11
12
13
14
15
16
17
18
19
20
21
22
23
24
25
26
27
28
29
30</pre>

</td><td>


<pre>require &#39;./cluster&#39; startup_nodes = [ {:host =&gt; &#34;127.0.0.1&#34;, :port =&gt; 7000}, {:host =&gt; &#34;127.0.0.1&#34;, :port =&gt; 7001} ] rc = RedisCluster.new(startup_nodes,32,:timeout =&gt; 0.1) last = false while not last begin last = rc.get(&#34;__last__&#34;) last = 0 if !last rescue =&gt; e puts &#34;error #{e.to_s}&#34; sleep 1 end end ((last.to_i+1)..1000000000).each{|x| begin rc.set(&#34;foo#{x}&#34;,x) puts rc.get(&#34;foo#{x}&#34;) rc.set(&#34;__last__&#34;,x) rescue =&gt; e puts &#34;error #{e.to_s}&#34; end sleep 0.1 } </pre>

</td></tr></tbody></table>



这个应用所做的工作非常简单：它不断地以 <tt class="docutils literal">foo&lt;number&gt;</tt> 为键， <tt class="docutils literal">number</tt> 为值，使用 [_SET_](http://www.redisdoc.com/en/latest/string/set.html#set) 命令向数据库设置键值对。

如果我们执行这个应用的话，应用将按顺序执行以下命令：


- SET foo0 0 
- SET foo1 1 
- SET foo2 2 
- 诸如此类。。。

代码中的每个集群操作都使用一个 <tt class="docutils literal">begin</tt> 和 <tt class="docutils literal">rescue</tt> 代码块（block）包裹着，因为我们希望在代码出错时，将错误打印到终端上面，而不希望应用因为异常（exception）而退出。

代码的**第七行**是代码中第一个有趣的地方，它创建了一个 Redis 集群对象，其中创建对象所使用的参数及其意义如下：


- 第一个参数是记录了启动节点的 startup_nodes 列表，列表中包含了两个集群节点的地址。
- 第二个参数指定了对于集群中的各个不同的节点，Redis 集群对象可以获得（take）的最大连接数（maximum number of connections this object is allowed to take）。
- 第三个参数 timeout 指定了一个命令在执行多久之后，才会被看作是执行失败。

记住，启动列表中并不需要包含所有集群节点的地址，但这些地址中至少要有一个是有效的（reachable）：一旦 <tt class="docutils literal">redis-rb-cluster</tt> 成功连接上集群中的某个节点时，集群节点列表就会被自动更新，任何真正的（serious）的集群客户端都应该这样做。

现在，程序创建的 Redis 集群对象实例被保存到 <tt class="docutils literal">rc</tt> 变量里面，我们可以将这个对象当作普通 Redis 对象实例来使用。

在**十一至十九行**，我们先尝试阅读计数器中的值，如果计数器不存在的话，我们才将计数器初始化为 <tt class="docutils literal">0</tt> ：通过将计数值保存到 Redis 的计数器里面，我们可以在示例重启之后，仍然继续之前的执行过程，而不必每次重启之后都从 <tt class="docutils literal">foo0</tt> 开始重新设置键值对。

为了让程序在集群下线的情况下，仍然不断地尝试读取计数器的值，我们将读取操作包含在了一个 <tt class="docutils literal">while</tt> 循环里面，一般的应用程序并不需要如此小心。

**二十一至三十行**是程序的主循环，这个循环负责设置键值对，并在设置出错时打印错误信息。

程序在主循环的末尾添加了一个 <tt class="docutils literal">sleep</tt> 调用，让写操作的执行速度变慢，帮助执行示例的人更容易看清程序的输出。

执行 <tt class="docutils literal">example.rb</tt> 程序将产生以下输出：



<pre>ruby ./example.rb
1
2
3
4
5
6
7
8
9
...</pre>


这个程序并不是十分有趣，稍后我们就会看到一个更有趣的集群应用示例，不过在此之前，让我们先使用这个示例来演示集群的重新分片操作。




## 对集群进行重新分片


现在，让我们来试试对集群进行重新分片操作。

在执行重新分片的过程中，请让你的 <tt class="docutils literal">example.rb</tt> 程序处于运行状态，这样你就会看到，重新分片并不会对正在运行的集群程序产生任何影响，你也可以考虑将 <tt class="docutils literal">example.rb</tt> 中的 <tt class="docutils literal">sleep</tt> 调用删掉，从而让重新分片操作在近乎真实的写负载下执行。

重新分片操作基本上就是将某些节点上的哈希槽移动到另外一些节点上面，和创建集群一样，重新分片也可以使用 <tt class="docutils literal">redis-trib</tt> 程序来执行。

执行以下命令可以开始一次重新分片操作：



<pre>$ ./redis-trib.rb reshard 127.0.0.1:7000</pre>


你只需要指定集群中其中一个节点的地址， <tt class="docutils literal">redis-trib</tt> 就会自动找到集群中的其他节点。

目前 <tt class="docutils literal">redis-trib</tt> 只能在管理员的协助下完成重新分片的工作，要让 <tt class="docutils literal">redis-trib</tt> 自动将哈希槽从一个节点移动到另一个节点，目前来说还做不到（不过实现这个功能并不难）。

执行 <tt class="docutils literal">redis-trib</tt> 的第一步就是设定你打算移动的哈希槽的数量：



<pre>$ ./redis-trib.rb reshard 127.0.0.1:7000
Connecting to node 127.0.0.1:7000: OK
Connecting to node 127.0.0.1:7002: OK
Connecting to node 127.0.0.1:7005: OK
Connecting to node 127.0.0.1:7001: OK
Connecting to node 127.0.0.1:7003: OK
Connecting to node 127.0.0.1:7004: OK
&gt;&gt;&gt; Performing Cluster Check (using node 127.0.0.1:7000)
M: 9991306f0e50640a5684f1958fd754b38fa034c9 127.0.0.1:7000
slots:0-5460 (5461 slots) master
M: 393c6df5eb4b4cec323f0e4ca961c8b256e3460a 127.0.0.1:7002
slots:10922-16383 (5462 slots) master
S: 3375be2ccc321932e8853234ffa87ee9fde973ff 127.0.0.1:7005
slots: (0 slots) slave
M: e68e52cee0550f558b03b342f2f0354d2b8a083b 127.0.0.1:7001
slots:5461-10921 (5461 slots) master
S: 48b728dbcedff6bf056231eb44990b7d1c35c3e0 127.0.0.1:7003
slots: (0 slots) slave
S: 345ede084ac784a5c030a0387f8aaa9edfc59af3 127.0.0.1:7004
slots: (0 slots) slave
[OK] All nodes agree about slots configuration.
&gt;&gt;&gt; Check for open slots...
&gt;&gt;&gt; Check slots coverage...
[OK] All 16384 slots covered.
How many slots do you want to move (from 1 to 16384)? 1000</pre>


我们将打算移动的槽数量设置为 <tt class="docutils literal">1000</tt> 个，如果 <tt class="docutils literal">example.rb</tt> 程序一直运行着的话，现在 <tt class="docutils literal">1000</tt> 个槽里面应该有不少键了。

除了移动的哈希槽数量之外， <tt class="docutils literal">redis-trib</tt> 还需要知道重新分片的目标（target node），也即是，负责接收这 <tt class="docutils literal">1000</tt> 个哈希槽的节点。

指定目标需要使用节点的 ID ，而不是 IP 地址和端口。比如说，我们打算使用集群的第一个主节点来作为目标，它的 IP 地址和端口是 <tt class="docutils literal">127.0.0.1:7000</tt> ，而节点 ID 则是 <tt class="docutils literal">9991306f0e50640a5684f1958fd754b38fa034c9</tt> ，那么我们应该向 <tt class="docutils literal">redis-trib</tt> 提供节点的 ID ：



<pre>$ ./redis-trib.rb reshard 127.0.0.1:7000
...
What is the receiving node ID? 9991306f0e50640a5684f1958fd754b38fa034c9</pre>



<tt class="docutils literal">redis-trib</tt> 会打印出集群中所有节点的 ID ，并且我们也可以通过执行以下命令来获得节点的运行 ID ：



<pre>$ ./redis-cli -p 7000 cluster nodes | grep myself
9991306f0e50640a5684f1958fd754b38fa034c9 :0 myself,master - 0 0 0 connected 0-5460</pre>



接着， <tt class="docutils literal">redis-trib</tt> 会向你询问重新分片的源节点（source node），也即是，要从哪个节点中取出 <tt class="docutils literal">1000</tt> 个哈希槽，并将这些槽移动到目标节点上面。

如果我们不打算从特定的节点上取出指定数量的哈希槽，那么可以向 <tt class="docutils literal">redis-trib</tt> 输入 <tt class="docutils literal">all</tt> ，这样的话，集群中的所有主节点都会成为源节点， <tt class="docutils literal">redis-trib</tt> 将从各个源节点中各取出一部分哈希槽，凑够 <tt class="docutils literal">1000</tt> 个，然后移动到目标节点上面：



<pre>$ ./redis-trib.rb reshard 127.0.0.1:7000
...
Please enter all the source node IDs.
Type &#39;all&#39; to use all the nodes as source nodes for the hash slots.
Type &#39;done&#39; once you entered all the source nodes IDs.
Source node #1:all</pre>


输入 <tt class="docutils literal">all</tt> 并按下回车之后， <tt class="docutils literal">redis-trib</tt> 将打印出哈希槽的移动计划，如果你觉得没问题的话，就可以输入 <tt class="docutils literal">yes</tt> 并再次按下回车：



<pre>$ ./redis-trib.rb reshard 127.0.0.1:7000
...
Moving slot 11421 from 393c6df5eb4b4cec323f0e4ca961c8b256e3460a
Moving slot 11422 from 393c6df5eb4b4cec323f0e4ca961c8b256e3460a
Moving slot 5461 from e68e52cee0550f558b03b342f2f0354d2b8a083b
Moving slot 5469 from e68e52cee0550f558b03b342f2f0354d2b8a083b
...
Moving slot 5959 from e68e52cee0550f558b03b342f2f0354d2b8a083b
Do you want to proceed with the proposed reshard plan (yes/no)? yes</pre>


输入 <tt class="docutils literal">yes</tt> 并使用按下回车之后， <tt class="docutils literal">redis-trib</tt> 就会正式开始执行重新分片操作，将指定的哈希槽从源节点一个个地移动到目标节点上面：



<pre>$ ./redis-trib.rb reshard 127.0.0.1:7000
...
Moving slot 5934 from 127.0.0.1:7001 to 127.0.0.1:7000:
Moving slot 5935 from 127.0.0.1:7001 to 127.0.0.1:7000:
Moving slot 5936 from 127.0.0.1:7001 to 127.0.0.1:7000:
Moving slot 5937 from 127.0.0.1:7001 to 127.0.0.1:7000:
...
Moving slot 5959 from 127.0.0.1:7001 to 127.0.0.1:7000:</pre>


在重新分片的过程中， <tt class="docutils literal">example.rb</tt> 应该可以继续正常运行，不会出现任何问题。

在重新分片操作执行完毕之后，可以使用以下命令来检查集群是否正常：



<pre>$ ./redis-trib.rb check 127.0.0.1:7000
Connecting to node 127.0.0.1:7000: OK
Connecting to node 127.0.0.1:7002: OK
Connecting to node 127.0.0.1:7005: OK
Connecting to node 127.0.0.1:7001: OK
Connecting to node 127.0.0.1:7003: OK
Connecting to node 127.0.0.1:7004: OK
&gt;&gt;&gt; Performing Cluster Check (using node 127.0.0.1:7000)
M: 9991306f0e50640a5684f1958fd754b38fa034c9 127.0.0.1:7000
slots:0-5959,10922-11422 (6461 slots) master
M: 393c6df5eb4b4cec323f0e4ca961c8b256e3460a 127.0.0.1:7002
slots:11423-16383 (4961 slots) master
S: 3375be2ccc321932e8853234ffa87ee9fde973ff 127.0.0.1:7005
slots: (0 slots) slave
M: e68e52cee0550f558b03b342f2f0354d2b8a083b 127.0.0.1:7001
slots:5960-10921 (4962 slots) master
S: 48b728dbcedff6bf056231eb44990b7d1c35c3e0 127.0.0.1:7003
slots: (0 slots) slave
S: 345ede084ac784a5c030a0387f8aaa9edfc59af3 127.0.0.1:7004
slots: (0 slots) slave
[OK] All nodes agree about slots configuration.
&gt;&gt;&gt; Check for open slots...
&gt;&gt;&gt; Check slots coverage...
[OK] All 16384 slots covered.</pre>


根据检查结果显示，集群运作正常。

需要注意的就是，在三个主节点中，节点 <tt class="docutils literal">127.0.0.1:7000</tt> 包含了 <tt class="docutils literal">6461</tt> 个哈希槽，而节点 <tt class="docutils literal">127.0.0.1:7001</tt> 和节点 <tt class="docutils literal">127.0.0.1:7002</tt> 都只包含了 <tt class="docutils literal">4961</tt> 个哈希槽，因为后两者都将自己的 <tt class="docutils literal">500</tt> 个哈希槽移动到了节点 <tt class="docutils literal">127.0.0.1:7000</tt> 。




## 一个更有趣的示例应用


我们在前面使用的示例程序 <tt class="docutils literal">example.rb</tt> 并不是十分有趣，因为它只是不断地对集群进行写入，但并不检查写入结果是否正确。比如说，集群可能会错误地将 <tt class="docutils literal">example.rb</tt> 发送的所有 [_SET_](http://www.redisdoc.com/en/latest/string/set.html#set) 命令都改成了 <tt class="docutils literal">SET foo 42</tt> ，但因为 <tt class="docutils literal">example.rb</tt> 并不检查写入后的值，所以它不会意识到集群实际上写入的值是错误的。

因为这个原因， [redis-rb-cluster 项目](https://github.com/antirez/redis-rb-cluster)包含了一个名为 [consistency-test.rb](https://github.com/antirez/redis-rb-cluster/blob/master/consistency-test.rb) 的示例应用，这个应用比起 <tt class="docutils literal">example.rb</tt> 有趣得多：它创建了多个计数器（默认为 <tt class="docutils literal">1000</tt> 个），并通过发送 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令来增加这些计数器的值。

在增加计数器值的同时， <tt class="docutils literal">consistency-test.rb</tt> 还执行以下操作：


- 每次使用 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令更新一个计数器时，应用会记录下计数器执行 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令之后应该有的值。举个例子，如果计数器的起始值为 0 ，而这次是程序第 50 次向它发送 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令，那么计数器的值应该是 50 。
- 在每次发送 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令之前，程序会随机从集群中读取一个计数器的值，并将它与自己记录的值进行对比，看两个值是否相同。

换句话说，这个程序是一个一致性检查器（consistency checker）：如果集群在执行 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令的过程中，丢失了某条 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令，又或者多执行了某条客户端没有确认到的 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令，那么检查器将察觉到这一点 ——在前一种情况中， <tt class="docutils literal">consistency-test.rb</tt> 记录的计数器值将比集群记录的计数器值要大；而在后一种情况中， <tt class="docutils literal">consistency-test.rb</tt> 记录的计数器值将比集群记录的计数器值要小。

运行 <tt class="docutils literal">consistency-test</tt> 程序将产生类似以下的输出：



<pre>$ ruby consistency-test.rb
925 R (0 err) | 925 W (0 err) |
5030 R (0 err) | 5030 W (0 err) |
9261 R (0 err) | 9261 W (0 err) |
13517 R (0 err) | 13517 W (0 err) |
17780 R (0 err) | 17780 W (0 err) |
22025 R (0 err) | 22025 W (0 err) |
25818 R (0 err) | 25818 W (0 err) |</pre>


每行输出都打印了程序执行的读取次数和写入次数，以及执行操作的过程中因为集群不可用而产生的错误数。

如果程序察觉了不一致的情况出现，它将在输出行的末尾显式不一致的详细情况。

比如说，如果我们在 <tt class="docutils literal">consistency-test.rb</tt> 运行的过程中，手动修改某个计数器的值：



<pre>$ redis 127.0.0.1:7000&gt; set key_217 0
OK</pre>


那么 <tt class="docutils literal">consistency-test.rb</tt> 将向我们报告不一致情况：



<pre>(in the other tab I see...)

94774 R (0 err) | 94774 W (0 err) |
98821 R (0 err) | 98821 W (0 err) |
102886 R (0 err) | 102886 W (0 err) | 114 lost |
107046 R (0 err) | 107046 W (0 err) | 114 lost |</pre>


在我们修改计数器值的时候，计数器的正确值是 <tt class="docutils literal">114</tt> （执行了 <tt class="docutils literal">114</tt> 次 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令），因为我们将计数器的值设成了 <tt class="docutils literal">0</tt> ，所以 <tt class="docutils literal">consistency-test.rb</tt> 会向我们报告说丢失了 <tt class="docutils literal">114</tt> 个 [_INCR_](http://www.redisdoc.com/en/latest/string/incr.html#incr) 命令。

因为这个示例程序具有一致性检查功能，所以我们用它来测试 Redis 集群的故障转移操作。




## 故障转移测试



在执行本节操作的过程中，请一直运行 <tt class="docutils literal">consistency-test</tt> 程序。


要触发一次故障转移，最简单的办法就是令集群中的某个主节点进入下线状态。

首先用以下命令列出集群中的所有主节点：



<pre>$ redis-cli -p 7000 cluster nodes | grep master
3e3a6cb0d9a9a87168e266b0a0b24026c0aae3f0 127.0.0.1:7001 master - 0 1385482984082 0 connected 5960-10921
2938205e12de373867bf38f1ca29d31d0ddb3e46 127.0.0.1:7002 master - 0 1385482983582 0 connected 11423-16383
97a3a64667477371c4479320d683e4c8db5858b1 :0 myself,master - 0 0 0 connected 0-5959 10922-11422</pre>


通过命令输出，我们知道端口号为 <tt class="docutils literal">7000</tt> 、 <tt class="docutils literal">7001</tt> 和 <tt class="docutils literal">7002</tt> 的节点都是主节点，然后我们可以通过向端口号为 <tt class="docutils literal">7002</tt> 的主节点发送 [_DEBUG SEGFAULT_](http://www.redisdoc.com/en/latest/server/debug_segfault.html#debug-segfault) 命令，让这个主节点崩溃：



<pre>$ redis-cli -p 7002 debug segfault
Error: Server closed the connection</pre>


现在，切换到运行着 <tt class="docutils literal">consistency-test</tt> 的标签页，可以看到， <tt class="docutils literal">consistency-test</tt> 在 <tt class="docutils literal">7002</tt> 下线之后的一段时间里将产生大量的错误警告信息：



<pre>18849 R (0 err) | 18849 W (0 err) |
23151 R (0 err) | 23151 W (0 err) |
27302 R (0 err) | 27302 W (0 err) |

... many error warnings here ...

29659 R (578 err) | 29660 W (577 err) |
33749 R (578 err) | 33750 W (577 err) |
37918 R (578 err) | 37919 W (577 err) |
42077 R (578 err) | 42078 W (577 err) |</pre>


从 <tt class="docutils literal">consistency-test</tt> 的这段输出可以看到，集群在执行故障转移期间，总共丢失了 <tt class="docutils literal">578</tt> 个读命令和 <tt class="docutils literal">577</tt> 个写命令，但是并没有产生任何数据不一致。

这听上去可能有点奇怪，因为在教程的开头我们提到过，Redis 使用的是异步复制，在执行故障转移期间，集群可能会丢失写命令。

但是在实际上，丢失命令的情况并不常见，因为 Redis 几乎是同时执行将命令回复发送给客户端，以及将命令复制给从节点这两个操作，所以实际上造成命令丢失的时间窗口是非常小的。

不过，尽管出现的几率不高，但丢失命令的情况还是有可能会出现的，所以我们对 Redis 集群不能提供强一致性的这一描述仍然是正确的。

现在，让我们使用 <tt class="docutils literal">cluster nodes</tt> 命令，查看集群在执行故障转移操作之后，主从节点的布局情况：



<pre>$ redis-cli -p 7000 cluster nodes
3fc783611028b1707fd65345e763befb36454d73 127.0.0.1:7004 slave 3e3a6cb0d9a9a87168e266b0a0b24026c0aae3f0 0 1385503418521 0 connected
a211e242fc6b22a9427fed61285e85892fa04e08 127.0.0.1:7003 slave 97a3a64667477371c4479320d683e4c8db5858b1 0 1385503419023 0 connected
97a3a64667477371c4479320d683e4c8db5858b1 :0 myself,master - 0 0 0 connected 0-5959 10922-11422
3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e 127.0.0.1:7005 master - 0 1385503419023 3 connected 11423-16383
3e3a6cb0d9a9a87168e266b0a0b24026c0aae3f0 127.0.0.1:7001 master - 0 1385503417005 0 connected 5960-10921
2938205e12de373867bf38f1ca29d31d0ddb3e46 127.0.0.1:7002 slave 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e 0 1385503418016 3 connected</pre>


我重启了之前下线的 <tt class="docutils literal">127.0.0.1:7002</tt> 节点，该节点已经从原来的主节点变成了从节点，而现在集群中的三个主节点分别是 <tt class="docutils literal">127.0.0.1:7000</tt> 、 <tt class="docutils literal">127.0.0.1:7001</tt> 和 <tt class="docutils literal">127.0.0.1:7005</tt> ，其中 <tt class="docutils literal">127.0.0.1:7005</tt> 就是因为 <tt class="docutils literal">127.0.0.1:7002</tt> 下线而变成主节点的。

<tt class="docutils literal">cluster nodes</tt> 命令的输出有点儿复杂，它的每一行都是由以下信息组成的：


- 节点 ID ：例如 3fc783611028b1707fd65345e763befb36454d73 。
- ip:port ：节点的 IP 地址和端口号，例如 127.0.0.1:7000 ，其中 :0 表示的是客户端当前连接的 IP 地址和端口号。
- flags ：节点的角色（例如 master 、 slave 、 myself ）以及状态（例如 fail ，等等）。
- 如果节点是一个从节点的话，那么跟在 flags 之后的将是主节点的节点 ID ：例如 127.0.0.1:7002 的主节点的节点 ID 就是 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e 。
- 集群最近一次向节点发送 [_PING_](http://www.redisdoc.com/en/latest/connection/ping.html#ping) 命令之后，过去了多长时间还没接到回复。
- 节点最近一次返回 PONG 回复的时间。
- 节点的配置纪元（configuration epoch）：详细信息请参考 [_Redis 集群规范_](http://www.redisdoc.com/en/latest/topic/cluster-spec.html#cluster-spec) 。
- 本节点的网络连接情况：例如 connected 。
- 节点目前包含的槽：例如 127.0.0.1:7001 目前包含号码为 5960 至 10921 的哈希槽。




## 添加新节点到集群


根据新添加节点的种类，我们需要用两种方法来将新节点添加到集群里面：


- 如果要添加的新节点是一个主节点，那么我们需要创建一个空节点（empty node），然后将某些哈希桶移动到这个空节点里面。
- 另一方面，如果要添加的新节点是一个从节点，那么我们需要将这个新节点设置为集群中某个节点的复制品（replica）。

本节将对以上两种情况进行介绍，首先介绍主节点的添加方法，然后再介绍从节点的添加方法。

无论添加的是那种节点，第一步要做的总是添加一个空节点。

我们可以继续使用之前启动 <tt class="docutils literal">127.0.0.1:7000</tt> 、 <tt class="docutils literal">127.0.0.1:7001</tt> 等节点的方法，创建一个端口号为 <tt class="docutils literal">7006</tt> 的新节点，使用的配置文件也和之前一样，只是记得要将配置中的端口号改为 <tt class="docutils literal">7000</tt> 。

以下是启动端口号为 <tt class="docutils literal">7006</tt> 的新节点的详细步骤：

- 在终端里创建一个新的标签页。
- 进入 cluster-test 文件夹。
- 创建并进入 7006 文件夹。
- 将 redis.conf 文件复制到 7006 文件夹里面，然后将配置中的端口号选项改为 7006 。
- 使用命令 ../../redis-server redis.conf 启动节点。
如果一切正常，那么节点应该会正确地启动。

接下来，执行以下命令，将这个新节点添加到集群里面：



<pre>./redis-trib.rb addnode 127.0.0.1:7006 127.0.0.1:7000</pre>


命令中的 <tt class="docutils literal">addnode</tt> 表示我们要让 <tt class="docutils literal">redis-trib</tt> 将一个节点添加到集群里面， <tt class="docutils literal">addnode</tt> 之后跟着的是新节点的 IP 地址和端口号，再之后跟着的是集群中任意一个已存在节点的 IP 地址和端口号，这里我们使用的是 <tt class="docutils literal">127.0.0.1:7000</tt> 。

通过 <tt class="docutils literal">cluster nodes</tt> 命令，我们可以确认新节点 <tt class="docutils literal">127.0.0.1:7006</tt> 已经被添加到集群里面了：



<pre>redis 127.0.0.1:7006&gt; cluster nodes
3e3a6cb0d9a9a87168e266b0a0b24026c0aae3f0 127.0.0.1:7001 master - 0 1385543178575 0 connected 5960-10921
3fc783611028b1707fd65345e763befb36454d73 127.0.0.1:7004 slave 3e3a6cb0d9a9a87168e266b0a0b24026c0aae3f0 0 1385543179583 0 connected
f093c80dde814da99c5cf72a7dd01590792b783b :0 myself,master - 0 0 0 connected
2938205e12de373867bf38f1ca29d31d0ddb3e46 127.0.0.1:7002 slave 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e 0 1385543178072 3 connected
a211e242fc6b22a9427fed61285e85892fa04e08 127.0.0.1:7003 slave 97a3a64667477371c4479320d683e4c8db5858b1 0 1385543178575 0 connected
97a3a64667477371c4479320d683e4c8db5858b1 127.0.0.1:7000 master - 0 1385543179080 0 connected 0-5959 10922-11422
3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e 127.0.0.1:7005 master - 0 1385543177568 3 connected 11423-16383</pre>


新节点现在已经连接上了集群，成为集群的一份子，并且可以对客户端的命令请求进行转向了，但是和其他主节点相比，新节点还有两点区别：


- 新节点没有包含任何数据，因为它没有包含任何哈希桶。
- 尽管新节点没有包含任何哈希桶，但它仍然是一个主节点，所以在集群需要将某个从节点升级为新的主节点时，这个新节点不会被选中。

接下来，只要使用 <tt class="docutils literal">redis-trib</tt> 程序，将集群中的某些哈希桶移动到新节点里面，新节点就会成为真正的主节点了。

因为使用 <tt class="docutils literal">redis-trib</tt> 移动哈希桶的方法在前面已经介绍过，所以这里就不再重复介绍了。

现在，让我们来看看，将一个新节点转变为某个主节点的复制品（也即是从节点）的方法。

举个例子，如果我们打算让新节点成为 <tt class="docutils literal">127.0.0.1:7005</tt> 的从节点，那么我们只要用客户端连接上新节点，然后执行以下命令就可以了：



<pre>redis 127.0.0.1:7006&gt; cluster replicate 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e</pre>


其中命令提供的 <tt class="docutils literal">3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e</tt> 就是主节点 <tt class="docutils literal">127.0.0.1:7005</tt> 的节点 ID 。

执行 <tt class="docutils literal">cluster replicate</tt> 命令之后，我们可以使用以下命令来确认 <tt class="docutils literal">127.0.0.1:7006</tt> 已经成为了 ID 为 <tt class="docutils literal">3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e</tt> 的节点的从节点：



<pre>$ redis-cli -p 7000 cluster nodes | grep slave | grep 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e
f093c80dde814da99c5cf72a7dd01590792b783b 127.0.0.1:7006 slave 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e 0 1385543617702 3 connected
2938205e12de373867bf38f1ca29d31d0ddb3e46 127.0.0.1:7002 slave 3c3a0c74aae0b56170ccb03a76b60cfe7dc1912e 0 1385543617198 3 connected</pre>


<tt class="docutils literal">3c3a0c...</tt> 现在有两个从节点，一个从节点的端口号为 <tt class="docutils literal">7002</tt> ，而另一个从节点的端口号为 <tt class="docutils literal">7006</tt> 。



## 移除一个节点


官方没有给出例子。



此系列到此全部结束，如有问题请指正。文章翻译了多大部分后发现在[这里](http://www.redisdoc.com/en/latest/topic/cluster-tutorial.html)有全部的已翻译内容，还好没有彻底做无用功，至少深入的理解了其中大部分的意思。
 
- - -
 