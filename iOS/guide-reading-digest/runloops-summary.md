# Run Loops 总览

- 线程的工作，需要有一系列的基础设施进行支撑，而 Run Loops 是这些基础设施的一部分
- 一个 Run Loop 的目的是：当有任务时，保证线程运作；当没有任务时，让线程休眠
- Cocoa 框架和 Core Foundation 都提供了 Run Loop 对象来配置和管理线程的 Run Loop, 开发者无需显式地创建 Run Loop 对象
- 每个线程，包括应用的主线程，都有一个与之相连的 Run Loop 对象，只有次要的(secondary)线程需要显式地开启 Run Loop

