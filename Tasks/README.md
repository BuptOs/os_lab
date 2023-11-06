# RROS 任务列表

RROS目前大概任务分类和举例如下，如果大家对改进内核和Lab有想法，也欢迎大家向我们自定义新的任务：

- RROS kernel相关：
  1. 实时调度算法：已有调度算法的调优，移植新的调度算法，调度观测机制的建立；
  2. 实时内存分配算法调优：已有分配算法调优；
  3. 实时内核功能补全：实时内核类futex机制，用户态观测/控制实时内核的机制；
  4. 在Rust-for-Linux（RFL）框架下进行实时驱动开发：gpio，monitor驱动开发；
  5. 移植内核到新的架构或者开发板下：内核移植，补丁回合，Bsp板级包构建，文件系统打包；
  6. RFL测量（学术向）：测量RFL编写driver的性能/存储开销，为提升RROS做准备；

- Lab相关：
  1. 新lab的设计：设计内核态/用户态Lab，设计Rust-for-Linux包裹Linux接口的lab，编写驱动的Lab，网络模块的Lab；
  2. 已有lab的改进：改进Lab文档/代码注释，Lab测试内容的改进；

## RROS侧

1. 任务1：补全实时内核中时钟子系统的部分Syscall
   - 时间：3周
   - 描述：目前实时内核的时钟子系统还没有给用户态用的syscall接口，只能在内核态调用实时接口，而我们编写实时程序一般需要在用户态，所以现在需要在用户态使用系统调用导出实时接口
   - 要求：
     - 仿照evl的kernel/evl/clock.c代码，在rros的时钟子系统中添加EVL_CLKIOC_GET_RES，EVL_CLKIOC_GET_TIME，EVL_CLKIOC_SET_TIME，EVL_CLKIOC_SLEEP四个系统调用；
     - 添加的系统调用可以通过libevl中的lib/clock.c相关的测试任务；
   - 提示
     - 运行qemu+raspi树莓派文件系统+libevl的文档
     - 可以参考rros中kernel/rros/proxy.rs或者kernel/rros/thread.rs的系统调用是如何实现的
     - [evl的项目链接](https://github.com/rust-real-time-os/xenomai_sourcecode)
     - [libevl的项目链接](https://github.com/rust-real-time-os/libevl/tree/r27_net)
   - 导师：李弘宇 微信：13935084378
   - 技术栈：
     - rust
     - C
     - linux
     - rust-for-linux

2. 任务2：测量RFL社区driver的性能（学术向）[已被选，2023/7/22]
   - 时间：2周
   - 描述：目前RFL社区用Rust重写了Linux社区的很多driver，如果能够系统地测量这些driver的性能，比如说网络设备的latency，磁盘设备的读写性能，对我们进一步用rust开发driver有很大的意义。
   - 要求：
     - 测量以下driver的性能
       - https://lore.kernel.org/rust-for-linux/87mt2fae4i.fsf@metaspace.dk/T/#t
       - https://lore.kernel.org/rust-for-linux/87mt29e9vb.fsf@metaspace.dk/T/#t
       - https://lore.kernel.org/rust-for-linux/20230609063118.24852-1-amiculas@cisco.com/T/#mebb060381c0071b913228a9892d464a7dfc27a29
     - 为保证测量结果的准确性，需要在物理pc上测试
     - 具体测量指标需要和导师联系；
   - 提示
     - 需要在RFL的最新代码上回合以上driver补丁，然后编译RFL内核，替换物理pc内核；
   - 导师：李弘宇 微信：13935084378
   - 技术栈：
     - rust
     - C
     - linux
     - rust-for-linux

3. 任务3：在RFL下重写gpio driver[已被选，2023/7/23]
   - 时间：4周
   - 描述：目前RFL社区已经提供了gpio的基本wrappers，如果我们可以进一步改写gpio相关的drivers，对于RFL能力的测试评估和我们内核将其进一步改造为实时gpio driver有很大的意义；
   - 要求：
     - 将drivers/gpio/gpiolib.c改写为rust版本
     - 需要在一个树莓派上成功运行这个驱动
     - 如果遇到没有被包裹的kernel API，需要完成对这个API的包裹
     - 评估这个driver在rust和C下的性能差异
     - 将这个driver提交到RFL内核社区
   - 提示：
     - 可以首先运行C版本的gpiolib.c，然后输出数据结构关系和函数调用关系文档，具体格式参考https://www.cnblogs.com/LoyenWang/p/11397084.html
   - 导师：李弘宇 微信：13935084378
   - 技术栈
     - rust
     - C
     - linux
     - rust-for-linux

4. 任务4：在树莓派上debug rros
   - 时间：2周
   - 描述：在qemu里面运行rros时，我们可以用gdb进行调试，在树莓派上运行rros时，我们也可以利用一个上位机进行调试，相比于使用qemu+gdb，我们可以获得更多的信息；
   - 要求：
     - 根据文档，在树莓派上成功运行rros
     - 在自己的主机上配置gdb，然后使用uart2usb模块成功连接电脑，观察内核的线程信息
     - 成功利用上位机debug
     - 探索qemu和上位机debug信息不同的原因
   - 提示：
     - 选择这个任务的同学首先找导师要交叉编译Linux内核然后移植到树莓派上的文档
     - 可以首先在树莓派上运行正常的Linux内核跑通整个流程
     - 校内的同学，如果缺少树莓派/uart2usb模块，可以找导师咨询
   - 导师：李弘宇 微信：13935084378
   - 技术栈
     - rust
     - C
     - linux
     - rust-for-linux

5. 任务5：采用shim层适配freertos接口和libevl的接口
   - 时间：6周
   - 描述：在xenomai 3.x时，xenomai通过shim层实现了对freertos系统库接口的兼容，进而支持运行freertos的实时应用程序，而到evl（xenomai4）后，社区目前还没有类似的机制兼容过去的程序，经过前期沟通，社区maintainer有增加shim层的意愿，本任务就是要继续对接上游社区，增加这些功能；
   - 要求：
     - 和上游社区沟通，明确libevl和freertos对接的需求；
     - 适配freertos和libevl的接口：
       - 技术方案1：将xenomai 3.x的shim代码程序迁移到libevl中
       - 技术方案2：重写shim层兼容freertos的接口
     - 将shim推进libevl主线
     - bonus：目前libevl有rust的wrapping层，可以在wrapping层中加入对shim层的wrapping
   - 导师：李弘宇 微信：13935084378
   - 技术栈
     - rust
     - C
     - linux
     - freertos

6. 任务6：补全rros在reboot阶段的逻辑
    - 时间：2周
    - 描述：目前rros如果采用reboot的命令重启，会陷入卡死状态，需要补全rros在reboot阶段的逻辑
    - 要求：
      - 首先明确Linux在reboot阶段经历了哪些函数调用，然后考察在rros中是否缺乏他们，如果缺乏，补全这些函数调用
      - tips:
        - 可以利用qeme/上位机进行debug，观察rros在reboot阶段被卡死的原因
        - 可以参考evl和reboot相关的代码，对比实现rros
    - 导师：李弘宇 微信：13935084378
    - 技术栈
      - rust
      - C
      - linux

## lab侧

1. 任务1：让网站支持通过HTTPS传输
   - 时间： 1周
   - 描述：目前网站仅支持HTTP协议，需要进行一定的配置以支持HTTPS协议。
   - 要求：
     - 提供能够运行的脚本和相应的说明（比如修改Dockerfile和一些script）
     - 在服务器上部署
   - 提示：
     - 网站使用uvicorn作为ASGI，也可以添加一层nginx，然后参考网上教程
   - 导师：邱奇琛 微信：ruiqurm
   - 技术栈：
     - python
     - https
     - docker

2. 任务2：使用异步方式查询数据库、启动容器
   - 时间： 2周
   - 描述：目前项目使用sqlalchemy查询和修改postgres数据库，使用docker库启动容器。使用同步的方式会导致服务器有时无法即使响应新的请求。我们希望将旧的接口替换为异步的接口。
   - 要求：
     - 修改相关代码，并在本地运行
     - 在服务器上成功部署
   - 提示：
     - sqlalchemy支持启动一个async session，然后进行异步操作。
     - aiodocker是一个异步操作docker接口的库。可以将目前的接口替换为aiodocker的接口。
   - 导师：邱奇琛 微信：ruiqurm
   - 技术栈：
     - python
     - python asyncio