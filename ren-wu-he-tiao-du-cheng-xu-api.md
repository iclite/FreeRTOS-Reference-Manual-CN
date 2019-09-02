# 任务和调度程序 API

## portSWITCH\_TO\_USER\_MODE\(\)

```c
#include "FreeRTOS.h"
#include "task.h"

void portSWITCH_TO_USER_MODE(void);
```

清单1. `portSWITCH TO USER MODE()` 宏原型

### 总结

此函数仅供高级用户使用，仅与 FreeRTOS MPU 端口（使用存储器保护单元的 FreeRTOS 端口）相关。

使用 `xTaskCreateRestricted()` 创建 MPU 受限任务。 提供给 `xTaskCreateRestricted()` 的参数指定正在创建的任务是应该是用户（非特权）模式任务还是超级用户（特权）模式任务。 超级用户模式任务可以调用 `portSWITCH_TO_USER_MODE()` 将自身从超级用户模式任务转换为用户模式任务。

### 参数

无

### 返回值

无

### 注意

`portSWITCH_TO_USER_MODE()` 没有允许任务将自己从用户模式转换为超级用户模式任务的对等函数。



