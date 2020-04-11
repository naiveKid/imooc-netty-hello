######  源码来源于慕课网: nett构建微信聊天服务器



简单的使用了netty作为服务器，学习简单的使用。

1. HelloServer 在main方法中：

```java
         // 定义一对线程组
		// 主线程组, 用于接受客户端的连接，但是不做任何处理，跟老板一样，不做事
		EventLoopGroup bossGroup = new NioEventLoopGroup();
		// 从线程组, 老板线程组会把任务丢给他，让手下线程组去做任务
		EventLoopGroup workerGroup = new NioEventLoopGroup();
		
		try {
			// netty服务器的创建, ServerBootstrap 是一个启动类
			ServerBootstrap serverBootstrap = new ServerBootstrap();
			serverBootstrap.group(bossGroup, workerGroup)			// 设置主从线程组
						.channel(NioServerSocketChannel.class)	// 设置nio的双向通道
						.childHandler(new HelloServerInitializer()); // 子处理器，用于处理workerGroup
			
			// 启动server，并且设置8088为启动的端口号，同时启动方式为同步
			ChannelFuture channelFuture = serverBootstrap.bind(8088).sync();
			
			// 监听关闭的channel，设置位同步方式
			channelFuture.channel().closeFuture().sync();
		} finally {
             //优雅的关闭
			bossGroup.shutdownGracefully();
			workerGroup.shutdownGracefully();
		}
```

​	通过浏览器访问： localhost:8080  或  通过： curl localhost:8080 

​	均能看到响应结果：*Hello netty~*

2. WSServer 在main方法中：

```java
		EventLoopGroup mainGroup = new NioEventLoopGroup();
		EventLoopGroup subGroup = new NioEventLoopGroup();
		
		try {
			ServerBootstrap server = new ServerBootstrap();
			server.group(mainGroup, subGroup)
				.channel(NioServerSocketChannel.class)
				.childHandler(new WSServerInitialzer());
			
			ChannelFuture future = server.bind(8088).sync();
			
			future.channel().closeFuture().sync();
		} finally {
			mainGroup.shutdownGracefully();
			subGroup.shutdownGracefully();
		}
```

​	通过html目录的 testWebsocket.html 可以进行测试。通过发生一段文本内容，可收到对应的服务器响应消息