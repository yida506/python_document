## feapder document 
feapder源码解读

### Spider
分布式爬虫

Spider 继承 BaseParser, Scheduler

#### 运行流程
1. 初始化Spider(传入rediskey),此时会初始化Spider中的参数,以及Scheduler中的参数,调用多线程中的start方法
2. 在start方法中,将当前Spider对象存入self._parsers中,然后调用Scheduler中的_start方法
3. 在_start方法中,调用初始化后的request_buffer,item_buffer,collector的start方法
4. 接着根据线程数及进程数,开启多个parser_control(主要是处理collector)



#### Spider内置方法


###### 方法
- start_monitor_task 开始监控 常驻：主要涉及redis数量任务监控以及下发任务
- distribute_task 分发任务,由于继承了Scheduler,所以会初始化parser列表
- run 运行线程 然后调用Scheduler中的_start()方法 启动request_buffer,item_buffer,collector(均继承多线程,用start()常驻)
- parser_control 


#### 组件介绍

##### Scheduler

###### init
- self._request_buffer 根据redis_key初始化的request_buffer
- self._item_buffer 根据redis_key初始化的item_buffer
- self._collector 根据redis_key初始化的collector
- self._parsers 数组,用于存放Spider对象本身
- self._parser_controls 数组
- self._parser_control_obj  PaserControl组件



##### request_buffer

- init 初始化requests队列,del_requests队列
- run: 只要未结束,就不断的往redis中添加任务
- __add_request_to_db: 将内存队列的中的任务添加至redis中

##### item_buffer

##### collector

##### Request
封装了request
缓存配置(用于debug爬虫)

##### PaserControl

###### run 
从内存队列中取request,对其数量进行判断,然后处理中间件,获取返回后的结果



