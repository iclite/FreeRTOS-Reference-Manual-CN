# 关于本手册

## 范围

本文档提供了主要 FreeRTOS API1 和 FreeRTOS 内核配置选项的技术参考。 假设读者已经熟悉编写多任务应用程序的概念，以及实时内核提供的原语。 建议不熟悉这些基本概念的读者阅读 《掌握 FreeRTOS 实时内核 - 实用指南》（Masteringthe FreeRTOS Real Time Kernel –A Practical Guide）一书，以获得更具描述性，实用性和教程的样式文本。本书可以从以下地址获得：[https://www.freertos.org/Documentation/](https://www.freertos.org/Documentation/RTOS_book.html)

### 本手册函数出现的顺序

在本文档中，API 函数已分为五组：任务和调度程序相关函数，队列相关函数，信号量相关函数，软件计时器相关函数和事件组相关函数。 每个组都记录在自己的章节中，在每章中，API 函数按字母顺序列出。 但请注意，每个 API 函数的名称都以一个或多个指定函数返回类型的字母为前缀，每个章节中 API 函数的字母顺序忽略函数返回类型前缀。 附录1：更详细地描述前缀。

例如，考虑用于创建 FreeRTOS 任务的 API 函数。 它的名字是 `xTaskCreate()`。 `x` 前缀指定`xTaskCreate()` 返回非标准类型。 第二个 `Task` 前缀指定该函数是与任务相关的函数，因此，将在包含任务和调度程序相关函数的章节中进行说明。 `x` 不按字母顺序排列，因此 `xTaskCreate()` 将出现在任务和调度程序章节中，就好像它的名称只是 `TaskCreate()` 一样。

### API 的使用限制

使用 FreeRTOS API 时，以下规则适用：

1. 不以 `FromISR` 结尾的 API 函数不得用于中断服务程序（ISR）。 一些 FreeRTOS 移植会进一步限制，即使以 `FromISR` 结尾的 API 函数也不能用于中断服务例程，该例程的（硬件）优先级高于 `configMAX_SYSCALL_INTERRUPT_PRIORITY` （或 `configMAX_API_CALL_INTERRUPT_PRIORITY` ，具体取决于端口）内核设置的优先级配置常量，在本文档的第 7.1 节中描述。 第二个限制是确保优先级高于 `configMAX_SYSCALL_INTERRUPT_PRIORITY` 设置的中断的时序，确定性和延迟不受 FreeRTOS 的影响。
2. 在调度程序挂起时，不得调用可能导致上下文切换的 API 函数。
3. 不得在临界区域内调用可能导致上下文切换的 API 函数。

