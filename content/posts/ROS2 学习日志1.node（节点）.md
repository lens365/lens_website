+++
title = "ROS2 学习日志1.node（节点）"
description = "ROS2 学习日志1.node（节点） - Lens Note"
date = "2025-10-11"
draft = false
tags = ["note"]
categories = ["笔记"]
+++

具体内容可以去看ROS2机器人开发：从入门到实践 (桑欣) (Z-Library)
`class PublisherNode(Node):`
    `def __init__(self, name):` 
        `super().__init__(name) # ROS2节点父类初始化` 
        `self.pub = self.create_publisher(String, "chatter", 10) # 创建发布者对象（消息类型、话题名、队列长度）`
        `self.timer = self.create_timer(0.5, self.timer_callback) # 创建一个定时器（单位为秒的周期，定时执行的回调函数）` 
    `def timer_callback(self): # 创建定时器周期执行的回调函数` 
        `msg = String() # 创建一个String类型的消息对象` 
        `msg.data = 'Hello World' # 填充消息对象中的消息数据` 
        `self.pub.publish(msg) # 发布话题消息` 
        `self.get_logger().info('Publishing: "%s"' % msg.data)`
我们首先解读node节点的基本使用
在开头，就要继承Node的属性等等内容，这些都是在ros2的系统里的打包好的文件，我们需要继承使用
然后，进行类的基本使用，包括定义__int__函数等等，包括在其内部进行初始化，进行诸如`self.pub = self.create_publisher(String, "chatter", 10)`   
创建发布者对象，在其中需要数据类型、话题名、队列长度诸如此类的东西，然后由于节点的信息操作等等需要以一定频率进行操作，故有了
`self.timer = self.create_timer(0.5, self.timer_callback) # 创建一个定时器（单位为秒的周期，定时执行的回调函数）` 
调用定时器，create_timer(周期（秒)，`self.timer_callback`)
然后再设计实际执行的函数（周期性执行），在消息内容上可以进行操作，一般使用msg等等
`import rclpy`
`from rclpy.node import Node`
`def node():`
     `rclpy.init()`
     `node=Node("first_node")`
     `rclpy.spin(node)`
     `rclpy.shutdown()`
`if __name__=" __main__ ":`
     `main()`


与此同时，你进行保存了，使用`python3 文件名.py` 即可进行运行
 那么如何查询节点呢：`ros2 node list`
 那么，除此之外，我们还可以做什么呢，我们可以在建立节点后发布information
`node.get_logger().info("hello world!")`
 
