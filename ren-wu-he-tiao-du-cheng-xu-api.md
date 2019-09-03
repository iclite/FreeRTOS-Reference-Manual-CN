# 任务和调度程序 API

## portSWITCH\_TO\_USER\_MODE\(\)

```c
#include "FreeRTOS.h"
#include "task.h"

void portSWITCH_TO_USER_MODE( void );
```

清单1. `portSWITCH_TO_USER_MODE()` 声明原型

### 概述

此函数仅供高级用户使用，仅与 FreeRTOS MPU 移植（使用存储器保护单元的 FreeRTOS 移植）相关。

使用 `xTaskCreateRestricted()` 创建 MPU 受限任务。 提供给 `xTaskCreateRestricted()` 的参数指定正在创建的任务是应该是用户（非特权）模式任务还是超级用户（特权）模式任务。 超级用户模式任务可以调用 `portSWITCH_TO_USER_MODE()` 将自身从超级用户模式任务转换为用户模式任务。

### 参数

无

### 返回值

无

### 注意

`portSWITCH_TO_USER_MODE()` 没有允许任务将自己从用户模式转换为超级用户模式任务的对等函数。

## vTaskAllocateMPURegions\(\)

```c
#include "FreeRTOS.h"
#include "task.h"
void vTaskAllocateMPURegions( TaskHandle_t xTaskToModify, 
                             const MemoryRegion_t* const xRegions );
```

清单2. `vTaskAllocateMPURegions()` 函数原型

### 概述

定义一组内存保护单元（MPU）区域供 MPU 限制任务使用。此功能仅供高级用户使用，仅与 FreeRTOS MPU 移植（使用内存保护单元的 FreeRTOS 移植）相关。 使用 `xTaskCreateRestricted()` 函数创建任务时，可以将内存区域分配给 MPU 受限任务。 然后可以使用 `vTaskAllocateMPURegions()` 函数在运行时重新定义（或重新分配）它们。

### 参数

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;</th>
      <th style="text-align:left">&#x63CF;&#x8FF0;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">xTaskToModify</td>
      <td style="text-align:left">
        <p>&#x6B63;&#x5728;&#x4FEE;&#x6539;&#x53D7;&#x9650;&#x4EFB;&#x52A1;&#x7684;&#x53E5;&#x67C4;&#xFF08;&#x53EF;&#x4EE5;&#x8BBF;&#x95EE;&#x7531; <code>xRegions</code> &#x53C2;&#x6570;&#x5B9A;&#x4E49;&#x7684;&#x5185;&#x5B58;&#x533A;&#x57DF;&#x7684;&#x4EFB;&#x52A1;&#xFF09;&#x3002;</p>
        <p>&#x4F7F;&#x7528; <code>xTaskCreateRestricted()</code> API &#x51FD;&#x6570;&#x7684; <code>pxCreatedTask</code> &#x53C2;&#x6570;&#x83B7;&#x53D6;&#x4EFB;&#x52A1;&#x53E5;&#x67C4;&#x3002;</p>
        <p>&#x4EFB;&#x52A1;&#x53EF;&#x4EE5;&#x901A;&#x8FC7;&#x4F20;&#x9012; <code>NULL</code> &#x4EE3;&#x66FF;&#x6709;&#x6548;&#x7684;&#x4EFB;&#x52A1;&#x53E5;&#x67C4;&#x6765;&#x4FEE;&#x6539;&#x81EA;&#x5DF1;&#x7684;&#x5185;&#x5B58;&#x533A;&#x57DF;&#x8BBF;&#x95EE;&#x5B9A;&#x4E49;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">xRegions</td>
      <td style="text-align:left">
        <p><code>MemoryRegion_t</code> &#x7ED3;&#x6784;&#x7684;&#x6570;&#x7EC4;&#x3002;
          &#x6570;&#x7EC4;&#x4E2D;&#x7684;&#x4F4D;&#x7F6E;&#x6570;&#x7531;&#x79FB;&#x690D;&#x7279;&#x5B9A;&#x7684; <code>portNUM_CONFIGURABLE_REGIONS</code> &#x5E38;&#x91CF;&#x5B9A;&#x4E49;&#x3002;
          &#x5728; Cortex-M3 &#x4E0A;&#xFF0C;<code>NUM_CONFIGURABLE_REGIONS</code> &#x5B9A;&#x4E49;&#x4E3A;
          3&#x3002;</p>
        <p>&#x6570;&#x7EC4;&#x4E2D;&#x7684;&#x6BCF;&#x4E2A; <code>MemoryRegion_t</code> &#x7ED3;&#x6784;&#x5B9A;&#x4E49;&#x5355;&#x4E2A;
          MPU &#x5185;&#x5B58;&#x533A;&#x57DF;&#xFF0C;&#x4F9B; <code>xTaskToModify</code> &#x53C2;&#x6570;&#x5F15;&#x7528;&#x7684;&#x4EFB;&#x52A1;&#x4F7F;&#x7528;&#x3002;</p>
      </td>
    </tr>
  </tbody>
</table>### 注意

MPU 内存区域使用清单 3 中所示的 `MemoryRegion_t` 结构定义。

```c
typedef struct xMEMORY_REGION{
    void *pvBaseAddress;
    unsigned long ulLengthInBytes;
    unsigned long ulParameters;
} MemoryRegion_t;
```

清单3. `xTaskCreateRestricted()` 使用的数据结构

`pvBaseAddress` 和 `ulLengthInBytes` 成员分别作为内存区域的开始和内存区域的长度自解释。 这些必须符合 MPU 规定的尺寸和对齐限制。 特别地，每个区域的大小和对齐必须都等于两个值的相同幂次。 

`ulParameters` 定义如何允许任务访问正在定义的内存区域，并且可以采用以下值的按位或：

* `portMPU_REGION_READ_WRITE`
* `portMPU_REGION_PRIVILEGED_READ_ONLY`
* `portMPU_REGION_READ_ONLY`
* `portMPU_REGION_PRIVILEGED_READ_WRITE`
* `portMPU_REGION_CACHEABLE_BUFFERABLE`
* `portMPU_REGION_EXECUTE_NEVER`

### 例子

```c
/* Define an array that the task will both read from and write to.  Make sure the 
size and alignment are appropriate for an MPU region (note this uses GCCsyntax). */
static unsigned char ucOneKByte[ 1024 ] __attribute__((align( 1024 )));

/* Define an array of MemoryRegion_t structures that configures an MPU region
allowing 
read/write access for 1024 bytes starting at the beginning of the ucOneKByte array.  
The other two of the maximum three definable regions are unused,so set to zero. */
static const MemoryRegion_t xAltRegions[ portNUM_CONFIGURABLE_REGIONS ] =
{
    /* Base address    Length    Parameters */
    { ucOneKByte,      1024,     portMPU_REGION_READ_WRITE },
    { 0,               0,        0 },
    { 0,               0,        0 }
};

void vATask( void *pvParameters )
{
    /* This task was created using xTaskCreateRestricted() to have access to a 
    maximum of three MPU controlled memory regions. At some point it is required
    that these MPU regions are replaced with those defined in the xAltRegions const 
    structure defined above.  Use a call to vTaskAllocateMPURegions() for this 
    purpose.  NULL is used as the task handle to indicate that the change should be
    applied to the calling task.*/
    vTaskAllocateMPURegions( NULL, xAltRegions );
    
    /* Now the task can continue its function, but from this point on can only access
    its stack and the ucOneKByte array (unless any other statically defined or shared
    regions have been declared elsewhere). */
}
```

清单4. `vTaskAllocateMPURegions()` 的使用示例

## xTaskAbortDelay\(\)

```c
#include "FreeRTOS.h"
#include "task.h"

BaseType_t xTaskAbortDelay( TaskHandle_t xTask );
```

清单5. `xTaskAbortDelay()` 函数原型

### 概述

调用包含超时参数的 API 函数可能导致调用任务进入阻塞状态。 处于阻塞状态的任务要么等待超时时间过去，要么等待事件发生超时，之后任务将自动离开阻塞状态并进入就绪状态。 这种行为有很多例子，其中两个是：

* 如果任务调用 `vTaskDelay()`，它将进入阻塞状态，直到函数参数指定的超时时间结束，此时任务将自动离开阻塞状态并进入就绪状态。
* 如果任务在其通知值为零时调用 `ulTask​​NotifyTake()`，它将进入阻塞状态，直到它收到通知或其中一个函数参数指定的超时已经过去，此时任务将自动离开阻塞状态并进入就绪状态。

即使任务正在等待的事件尚未发生，`xTaskAbortDelay()` 也会将任务从 “阻止” 状态移动到 “就绪” 状态，并且当任务进入 “阻止” 状态时指定的超时尚未过去。

当任务处于阻塞状态时，它不可用于调度程序，并且不会消耗任何处理时间。

### 参数

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;</th>
      <th style="text-align:left">&#x63CF;&#x8FF0;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">xTask</td>
      <td style="text-align:left">
        <p>&#x5C06;&#x88AB;&#x79FB;&#x51FA;&#x963B;&#x585E;&#x72B6;&#x6001;&#x7684;&#x4EFB;&#x52A1;&#x53E5;&#x67C4;&#x3002;</p>
        <p>&#x8981;&#x83B7;&#x53D6;&#x4EFB;&#x52A1;&#x7684;&#x53E5;&#x67C4;&#xFF0C;&#x8BF7;&#x4F7F;&#x7528; <code>xTaskCreate()</code> &#x521B;&#x5EFA;&#x4EFB;&#x52A1;&#x5E76;&#x4F7F;&#x7528; <code>pxCreatedTask</code> &#x53C2;&#x6570;&#xFF0C;&#x6216;&#x4F7F;&#x7528; <code>xTaskCreateStatic()</code> &#x521B;&#x5EFA;&#x4EFB;&#x52A1;&#x5E76;&#x5B58;&#x50A8;&#x8FD4;&#x56DE;&#x7684;&#x503C;&#xFF0C;&#x6216;&#x8005;&#x5728;&#x8C03;&#x7528; <code>xTaskGetHandle()</code> &#x65F6;&#x4F7F;&#x7528;&#x4EFB;&#x52A1;&#x7684;&#x540D;&#x79F0;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">&#x8FD4;&#x56DE;&#x503C;</td>
      <td style="text-align:left">&#x5982;&#x679C; <code>xTask</code> &#x5F15;&#x7528;&#x7684;&#x4EFB;&#x52A1;&#x5DF2;&#x4ECE;&#x963B;&#x585E;&#x72B6;&#x6001;&#x4E2D;&#x5220;&#x9664;&#xFF0C;&#x5219;&#x8FD4;&#x56DE; <code>pdPASS</code>&#x3002;
        &#x5982;&#x679C; <code>xTask</code> &#x5F15;&#x7528;&#x7684;&#x4EFB;&#x52A1;&#x672A;&#x4ECE;&#x963B;&#x585E;&#x72B6;&#x6001;&#x4E2D;&#x5220;&#x9664;&#xFF0C;&#x56E0;&#x4E3A;&#x5B83;&#x672A;&#x5904;&#x4E8E;&#x963B;&#x585E;&#x72B6;&#x6001;&#xFF0C;&#x5219;&#x8FD4;&#x56DE; <code>pdFAIL</code>&#x3002;</td>
    </tr>
  </tbody>
</table>### 注意

在 `FreeRTOSConfig.h` 中，`INCLUDE_xTaskAbortDelay` 必须设置为 1 才能使 `xTaskAbortDelay()` 可用。

### 例子

```c
void vAFunction( TaskHandle_t xTask )
{
    /* The task referenced by xTask is blocked to wait for something that the task calling
    this function has determined will never happen.  Force the task referenced by xTask
    out of the Blocked state. */
    if( xTaskAbortDelay( xTask ) == pdFAIL)
    {
        /* The task referenced by xTask was not in the Blocked state anyway. */
    }
    else
    {
        /* The task referenced by xTask was in the Blocked state, but is not now. */
    }
}
```

清单6. `xTaskAbortDelay()` 的使用示例

## xTaskCallApplicationTaskHook\(\)

```c
#include "FreeRTOS.h"
#include "task.h"

BaseType_t xTaskCallApplicationTaskHook( TaskHandle_t xTask, void *pvParameters );
```

清单7. `xTaskCallApplicationTaskHook()` 函数原型

### 概述

此函数仅供高级用户使用。

`vTaskSetApplicationTaskTag()` 函数可用于为任务分配 “标记” 值。 标记值的含义和用法由应用程序编写者定义。 内核本身通常不会访问标记值。 

作为特殊情况，标记值可用于将 “任务挂钩”（或回调）函数与任务相关联。 完成后，使用 `xTaskCallApplicationTaskHook()` 调用钩子函数。 

任务钩子函数可用于任何目的。 本节中显示的示例演示了用于输出调试跟踪信息的任务挂钩。

任务挂钩函数必须具有清单 8 所示的原型。

```c
BaseType_t xAnExampleTaskHookFunction( void *pvParameters );
```

清单 8. 所有任务钩子函数必须符合的原型

`xTaskCallApplicationTaskHook()` 仅在 `FreeRTOSConfig.h` 中 `configUSE_APPLICATION_TASK_TAG` 设置为 1 时可用。

### 参数

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;</th>
      <th style="text-align:left">&#x63CF;&#x8FF0;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">xTask</td>
      <td style="text-align:left">
        <p>&#x6B63;&#x5728;&#x8C03;&#x7528;&#x5176;&#x5173;&#x8054;&#x7684;&#x94A9;&#x5B50;&#x51FD;&#x6570;&#x7684;&#x4EFB;&#x52A1;&#x7684;&#x53E5;&#x67C4;&#x3002;</p>
        <p>&#x8981;&#x83B7;&#x53D6;&#x4EFB;&#x52A1;&#x7684;&#x53E5;&#x67C4;&#xFF0C;&#x8BF7;&#x4F7F;&#x7528; <code>xTaskCreate()</code> &#x521B;&#x5EFA;&#x4EFB;&#x52A1;&#x5E76;&#x4F7F;&#x7528; <code>pxCreatedTask</code> &#x53C2;&#x6570;&#xFF0C;&#x6216;&#x4F7F;&#x7528; <code>xTaskCreateStatic()</code> &#x521B;&#x5EFA;&#x4EFB;&#x52A1;&#x5E76;&#x5B58;&#x50A8;&#x8FD4;&#x56DE;&#x7684;&#x503C;&#xFF0C;&#x6216;&#x8005;&#x5728;&#x8C03;&#x7528;
          xTaskGetHandle&#xFF08;&#xFF09;&#x65F6;&#x4F7F;&#x7528;&#x4EFB;&#x52A1;&#x7684;&#x540D;&#x79F0;&#x3002;</p>
        <p>&#x4EFB;&#x52A1;&#x53EF;&#x4EE5;&#x901A;&#x8FC7;&#x4F20;&#x9012;NULL&#x4EE3;&#x66FF;&#x6709;&#x6548;&#x7684;&#x4EFB;&#x52A1;&#x53E5;&#x67C4;&#x6765;&#x8C03;&#x7528;&#x81EA;&#x5DF1;&#x7684;&#x94A9;&#x5B50;&#x51FD;&#x6570;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">pvParameters</td>
      <td style="text-align:left">&#x8BE5;&#x503C;&#x7528;&#x4F5C;&#x4EFB;&#x52A1;&#x94A9;&#x5B50;&#x51FD;&#x6570;&#x672C;&#x8EAB;&#x7684;&#x53C2;&#x6570;&#x3002;&#x6B64;&#x53C2;&#x6570;&#x5177;&#x6709;&#x5141;&#x8BB8;&#x4EFB;&#x52A1;&#x94A9;&#x5B50;&#x51FD;&#x6570;
        &#x201C;&#x6307;&#x5411; void &#x7684;&#x6307;&#x9488;&#x201D; &#x7C7B;&#x578B;&#xFF0C;&#x5E76;&#x901A;&#x8FC7;&#x8F6C;&#x6362;&#x95F4;&#x63A5;&#x5730;&#x63A5;&#x6536;&#x4EFB;&#x4F55;&#x7C7B;&#x578B;&#x7684;&#x53C2;&#x6570;&#x3002;
        &#x4F8B;&#x5982;&#xFF0C;&#x6574;&#x6570;&#x7C7B;&#x578B;&#x53EF;&#x4EE5;&#x901A;&#x8FC7;&#x5C06;&#x6574;&#x6570;&#x8F6C;&#x6362;&#x4E3A;&#x8C03;&#x7528;&#x94A9;&#x5B50;&#x51FD;&#x6570;&#x7684;&#x6307;&#x5411;&#x7684;
        void &#x6307;&#x9488;&#xFF0C;&#x7136;&#x540E;&#x901A;&#x8FC7;&#x5C06; void
        &#x6307;&#x9488;&#x53C2;&#x6570;&#x8F6C;&#x6362;&#x56DE;&#x94A9;&#x5B50;&#x51FD;&#x6570;&#x672C;&#x8EAB;&#x5185;&#x7684;&#x6574;&#x6570;&#x6765;&#x4F20;&#x9012;&#x7ED9;&#x94A9;&#x5B50;&#x51FD;&#x6570;&#x3002;</td>
    </tr>
  </tbody>
</table>### 例子

```c
/* Define a hook (callback) function – using the required prototype as 
demonstrated by Listing 8*/
static BaseType_t prvExampleTaskHook( void * pvParameter )
{
    /* Perform an action - this could be anything.  In this example the hook
    is used to output debug trace information.  pxCurrentTCB is the handle 
    of the currently executing task.  (vWriteTrace() is not an API function,
    its just used as an example.)*/
    vWriteTrace( pxCurrentTCB );
    
    /* This example does not make use of the hook return value so just returns
    0 in every case. */
    return 0;
}

/* Define an example task that makes use of its tag value. */
void vAnotherTask( void *pvParameters )
{
    /* vTaskSetApplicationTaskTag() sets the ‘tag’ value associated with a task.
    NULL is used in place of a valid task handle to indicate that it should be
    the tag value of the calling task that gets set.  In this example the ‘value’ 
    being set is the hook function. */
    vTaskSetApplicationTaskTag( NULL, prvExampleTaskHook );
    
    for( ;; )
    {
        /* The rest of the task code goes here. */
    }
}

/* Define the traceTASK_SWITCHED_OUT() macro to call the hook function of each
task that is switched out.  pxCurrentTCB points to the handle of the currently
running task. */
#define traceTASK_SWITCHED_OUT() xTaskCallApplicationTaskHook( pxCurrentTCB, 0 )
```

清单9. `xTaskCallApplicationTaskHook()` 使用示例

## xTaskCheckForTimeOut\(\)

```c
#include "FreeRTOS.h"
#include "task.h"
BaseType_t xTaskCheckForTimeOut( TimeOut_t * const pxTimeOut, 
                                 TickType_t * const pxTicksToWait );
```

清单10. `xTaskCheckForTimeOut()` 函数原型

### 概述

本函数仅供高级用户使用。

任务可以进入阻塞状态来等待一个事件。通常， 任务不会由于无限期的等待而处于阻塞状态，会指定超时期限。如果超时期限在任务等待事件发生之前到期，则任务将会从阻塞状态中移除。

如果任务在等待事件发生时不止一次的进入和退出阻塞状态，则每次进入阻塞状态时使用的超时必须进行调整，以确保在阻塞状态下花费的总时间不超过最初指定的超时时间。一般使用 `xTaskCheckForTimeOut()` 执行调整，考虑到偶然发生的事件，例如 Tick 计数器溢出，否则手动调整很容易出错。

### 参数

| 参数 | 描述 |
| :--- | :--- |
| pxTimeOut | 一个指向结构体的指针，该结构体包含确定是否发生超时所必须的信息。使用 `vTaskSetTimeOutState()` 初始化 `pxTimeOut` 。 |
| pxTicksToWait | 用于传递调整过后的阻塞时间，这是在考虑已经在阻塞状态下花费的时间之后剩余的阻塞时间。 |
| 返回值 | 若返回 `pdTRUE` ，则无剩余阻塞时间，并且发生超时。若返回 `pdFALSE` ，则还有剩余的阻塞时间，所以不发生超时。  |

### 例子

```c
/* Driver library function used to receive uxWantedBytes from an Rx buffer that is filled 
by a UART interrupt.  If there are not enough bytes in the Rx buffer then the task enters 
the Blocked state until it is notified that more data has been placed into the buffer.  If 
there is still not enough data then the task re-enters the Blocked state, and 
xTaskCheckForTimeOut() is used to re-calculate the Block time to ensure the total amount 
of time spent in the Blocked state does not exceed MAX_TIME_TO_WAIT. This continues until 
either the buffer contains at least uxWantedBytes bytes, or the total amount of time spent 
in the Blocked state reaches MAX_TIME_TO_WAIT – at which point the task reads however many 
bytes are available up to a maximum of uxWantedBytes.*/
size_t xUART_Receive(uint8_t *pucBuffer, size_t uxWantedBytes )
{
    size_t uxReceived = 0;
    TickType_t xTicksToWait = MAX_TIME_TO_WAIT;
    TimeOut_t xTimeOut;
    
    /* Initialize xTimeOut.  This records the time at which this function was entered. */
    vTaskSetTimeOutState( &xTimeOut );
    
    /* Loop until the buffer contains the wanted number of bytes, or a timeout occurs. */
    while( UART_bytes_in_rx_buffer( pxUARTInstance ) < uxWantedBytes )
    {
        /* The buffer didn’t contain enough data so this task is going to enter the Blocked 
        state.  Adjusting xTicksToWait to account for any time that has been spent in the 
        Blocked state within this function so far to ensure the total amount of time spent 
        in the Blocked state does not exceed MAX_TIME_TO_WAIT. */
        if( xTaskCheckForTimeOut( &xTimeOut, &xTicksToWait ) != pdFALSE )
        {
            /* Timed out before the wanted number of bytes were available, exit the loop. */
            break;
        }
        
        /* Wait for a maximum of xTicksToWait ticks to be notified that the receive 
        interrupt has placed more data intothe buffer. */
        ulTaskNotifyTake( pdTRUE, xTicksToWait );
    }
    
    /* Attempt to read uxWantedBytes from the receive buffer into pucBuffer.  The actual 
    number of bytes read (which might be less than uxWantedBytes) is returned. */
    uxReceived = UART_read_from_receive_buffer( pxUARTInstance, pucBuffer, uxWantedBytes );
    
    return uxReceived;
}
```

清单11. `vTaskSetTimeOutState()` 与 `xTaskCheckForTimeOut()` 的使用示例

## xTaskCreate\(\)

```c
#include "FreeRTOS.h"
#include "task.h"
BaseType_t xTaskCreate( TaskFunction_tpvTaskCode, 
                        const char* const pcName, 
                        unsigned shortusStackDepth, 
                        void *pvParameters, 
                        UBaseType_t uxPriority, 
                        TaskHandle_t *pxCreatedTask);
```

清单 12. `xTaskCreate()` 函数原型

### 概述

创建一个新的任务实例。

每个任务都与要用于保存任务状态的 RAM（任务控制块或 TCB），并被任务用作于堆。如果任务由 `xTaskCreate()` 创建，它所需的 RAM 从 FreeRTOS 栈中自动分配。如果任务由 `xTaskCreateStatic()` 创建，那么它所需的 RAM 应由应用编写者提供，这会产生两个额外的函数参数，但允许在编译时静态分配 RAM。

新创建的任务最初处于 “就绪” 状态，但如果没有能够运行的优先级较高的任务，则会立即成为 “运行状态” 任务。

可以在启动调度程序之前和之后创建任务。

### 参数

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x53C2;&#x6570;</th>
      <th style="text-align:left">&#x63CF;&#x8FF0;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">pvTaskCode</td>
      <td style="text-align:left">&#x4EFB;&#x52A1;&#x53EA;&#x662F;&#x4ECE;&#x4E0D;&#x9000;&#x51FA;&#x7684;
        C &#x51FD;&#x6570;&#xFF0C;&#x6240;&#x4EE5;&#x901A;&#x5E38;&#x5B9E;&#x73B0;&#x4E3A;&#x4E00;&#x4E2A;&#x65E0;&#x9650;&#x7684;&#x5FAA;&#x73AF;&#x3002;<code>pvTaskCode</code> &#x53C2;&#x6570;&#x53EA;&#x662F;&#x4E00;&#x4E2A;&#x51FD;&#x6570;&#x6307;&#x9488;&#xFF0C;&#x6307;&#x5411;&#x5B9E;&#x73B0;&#x4EFB;&#x52A1;&#x7684;&#x51FD;&#x6570;&#xFF08;&#x5B9E;&#x9645;&#x4E0A;&#x662F;&#x4E2A;&#x51FD;&#x6570;&#x540D;&#xFF09;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">pcName</td>
      <td style="text-align:left">
        <p>&#x4EFB;&#x52A1;&#x7684;&#x63CF;&#x8FF0;&#x6027;&#x540D;&#x79F0;&#x3002;&#x8FD9;&#x4E3B;&#x8981;&#x7528;&#x4E8E;&#x8C03;&#x8BD5;&#x65B9;&#x4FBF;&#xFF0C;&#x4F46;&#x4E5F;&#x53EF;&#x7528;&#x6765;&#x8C03;&#x7528; <code>xTaskGetHandle()</code> &#x4EE5;&#x83B7;&#x53D6;&#x4EFB;&#x52A1;&#x7684;&#x53E5;&#x67C4;&#x3002;</p>
        <p>&#x5E94;&#x7528;&#x7A0B;&#x5E8F;&#x5B9A;&#x4E49;&#x7684;&#x5E38;&#x91CF; <code>configMAX_TASK_NAME_LEN</code> &#x4EE5;&#x5B57;&#x7B26;&#x5B9A;&#x4E49;&#x540D;&#x79F0;&#x7684;&#x6700;&#x5927;&#x957F;&#x5EA6;&#xFF0C;&#x5305;&#x62EC; <code>NULL</code> &#x7EC8;&#x6B62;&#x7B26;&#x3002;
          &#x63D0;&#x4F9B;&#x957F;&#x4E8E;&#x6B64;&#x6700;&#x5927;&#x503C;&#x7684;&#x5B57;&#x7B26;&#x4E32;&#x5C06;&#x5BFC;&#x81F4;&#x5B57;&#x7B26;&#x4E32;&#x88AB;&#x9759;&#x9ED8;&#x622A;&#x65AD;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">usStackDepth</td>
      <td style="text-align:left">
        <p>&#x6BCF;&#x4E2A;&#x4EFB;&#x52A1;&#x90FD;&#x6709;&#x81EA;&#x5DF1;&#x552F;&#x4E00;&#x7684;&#x5806;&#xFF0C;&#x5728;&#x521B;&#x5EFA;&#x4EFB;&#x52A1;&#x65F6;&#x7531;&#x5185;&#x6838;&#x5206;&#x914D;&#x7ED9;&#x4EFB;&#x52A1;&#x3002; <code>usStackDepth</code> &#x503C;&#x544A;&#x8BC9;&#x5185;&#x6838;&#x5806;&#x6808;&#x7684;&#x5927;&#x5C0F;&#x3002;</p>
        <p>&#x8BE5;&#x503C;&#x6307;&#x5B9A;&#x5806;&#x6808;&#x53EF;&#x4EE5;&#x5BB9;&#x7EB3;&#x7684;&#x5B57;&#x6570;&#xFF0C;&#x800C;&#x4E0D;&#x662F;&#x5B57;&#x8282;&#x6570;&#x3002;
          &#x4F8B;&#x5982;&#xFF0C;&#x5728;&#x5177;&#x6709; 4 &#x5B57;&#x8282;&#x5806;&#x6808;&#x5BBD;&#x5EA6;&#x7684;&#x4F53;&#x7CFB;&#x7ED3;&#x6784;&#x4E0A;&#xFF0C;&#x5982;&#x679C;&#x5C06; <code>usStackDepth</code> &#x4F5C;&#x4E3A;
          100 &#x4F20;&#x5165;&#xFF0C;&#x5219;&#x5C06;&#x5206;&#x914D; 400 &#x5B57;&#x8282;&#x7684;&#x5806;&#x6808;&#x7A7A;&#x95F4;&#xFF08;100
          * 4 &#x5B57;&#x8282;&#xFF09;&#x3002; &#x5806;&#x6808;&#x6DF1;&#x5EA6;&#x4E58;&#x4EE5;&#x5806;&#x6808;&#x5BBD;&#x5EA6;&#x4E0D;&#x5F97;&#x8D85;&#x8FC7; <code>size_t</code> &#x7C7B;&#x578B;&#x7684;&#x53D8;&#x91CF;&#x4E2D;&#x53EF;&#x5305;&#x542B;&#x7684;&#x6700;&#x5927;&#x503C;&#x3002;</p>
        <p>&#x7A7A;&#x95F2;&#x4EFB;&#x52A1;&#x4F7F;&#x7528;&#x7684;&#x5806;&#x6808;&#x5927;&#x5C0F;&#x7531;&#x5E94;&#x7528;&#x7A0B;&#x5E8F;&#x5B9A;&#x4E49;&#x7684;&#x5E38;&#x91CF; <code>configMINIMAL_STACK_SIZE</code> &#x5B9A;&#x4E49;&#x3002;
          &#x4E3A;&#x6240;&#x9009;&#x62E9;&#x7684;&#x5FAE;&#x63A7;&#x5236;&#x5668;&#x67B6;&#x6784;&#x63D0;&#x4F9B;&#x7684;&#x6F14;&#x793A;&#x5E94;&#x7528;&#x7A0B;&#x5E8F;&#x4E2D;&#x4E3A;&#x6B64;&#x5E38;&#x91CF;&#x5206;&#x914D;&#x7684;&#x503C;&#x662F;&#x8BE5;&#x67B6;&#x6784;&#x4E2D;&#x4EFB;&#x4F55;&#x4EFB;&#x52A1;&#x7684;&#x6700;&#x4F4E;&#x5EFA;&#x8BAE;&#x503C;&#x3002;
          &#x5982;&#x679C;&#x60A8;&#x7684;&#x4EFB;&#x52A1;&#x4F7F;&#x7528;&#x5927;&#x91CF;&#x5806;&#x6808;&#x7A7A;&#x95F4;&#xFF0C;&#x5219;&#x5FC5;&#x987B;&#x5206;&#x914D;&#x66F4;&#x5927;&#x7684;&#x503C;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">pvParameters</td>
      <td style="text-align:left">
        <p>&#x4EFB;&#x52A1;&#x51FD;&#x6570;&#x63A5;&#x53D7; &#x201C;&#x6307;&#x5411;void&#x7684;&#x6307;&#x9488;&#x201D;&#xFF08;void
          *&#xFF09;&#x7C7B;&#x578B;&#x7684;&#x53C2;&#x6570;&#x3002; &#x5206;&#x914D;&#x7ED9; <code>pvParameters</code> &#x7684;&#x503C;&#x5C06;&#x662F;&#x4F20;&#x9012;&#x7ED9;&#x4EFB;&#x52A1;&#x7684;&#x503C;&#x3002;</p>
        <p>&#x6B64;&#x53C2;&#x6570;&#x5177;&#x6709; &#x201C;&#x6307;&#x5411;void&#x7684;&#x6307;&#x9488;&#x201D;
          &#x7C7B;&#x578B;&#xFF0C;&#x4EE5;&#x5141;&#x8BB8;&#x4EFB;&#x52A1;&#x53C2;&#x6570;&#x6709;&#x6548;&#x5730;&#xFF0C;&#x5E76;&#x901A;&#x8FC7;&#x5F3A;&#x5236;&#x8F6C;&#x6362;&#xFF0C;&#x95F4;&#x63A5;&#x63A5;&#x6536;&#x4EFB;&#x4F55;&#x7C7B;&#x578B;&#x7684;&#x53C2;&#x6570;&#x3002;
          &#x4F8B;&#x5982;&#xFF0C;&#x901A;&#x8FC7;&#x5728;&#x521B;&#x5EFA;&#x4EFB;&#x52A1;&#x7684;&#x70B9;&#x5904;&#x5C06;&#x6574;&#x6570;&#x8F6C;&#x6362;&#x4E3A;
          void &#x6307;&#x9488;&#xFF0C;&#x7136;&#x540E;&#x901A;&#x8FC7;&#x5C06; void
          &#x6307;&#x9488;&#x53C2;&#x6570;&#x8F6C;&#x6362;&#x56DE;&#x4EFB;&#x52A1;&#x51FD;&#x6570;&#x5B9A;&#x4E49;&#x672C;&#x8EAB;&#x4E2D;&#x7684;&#x6574;&#x6570;&#xFF0C;&#x53EF;&#x4EE5;&#x5C06;&#x6574;&#x6570;&#x7C7B;&#x578B;&#x4F20;&#x9012;&#x5230;&#x4EFB;&#x52A1;&#x51FD;&#x6570;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">uxPriority</td>
      <td style="text-align:left">
        <p>&#x5B9A;&#x4E49;&#x4EFB;&#x52A1;&#x5C06;&#x6267;&#x884C;&#x7684;&#x4F18;&#x5148;&#x7EA7;&#x3002;
          &#x53EF;&#x4EE5;&#x5C06;&#x4F18;&#x5148;&#x7EA7;&#x4ECE; 0&#xFF08;&#x6700;&#x4F4E;&#x4F18;&#x5148;&#x7EA7;&#xFF09;&#x5206;&#x914D;&#x7ED9;&#xFF08;<code>configMAX_PRIORITIES - 1</code>&#xFF09;&#xFF0C;&#x8FD9;&#x662F;&#x6700;&#x9AD8;&#x4F18;&#x5148;&#x7EA7;&#x3002;</p>
        <p><code>configMAX_PRIORITIES</code> &#x662F;&#x7528;&#x6237;&#x5B9A;&#x4E49;&#x7684;&#x5E38;&#x91CF;&#x3002;
          &#x5982;&#x679C;<code>configUSE_PORT_OPTIMISED_TASK_SELECTION</code> &#x8BBE;&#x7F6E;&#x4E3A;
          0&#xFF0C;&#x90A3;&#x4E48;&#x53EF;&#x7528;&#x7684;&#x4F18;&#x5148;&#x7EA7;&#x6570;&#x6CA1;&#x6709;&#x4E0A;&#x9650;&#xFF08;&#x9664;&#x4E86;&#x4F7F;&#x7528;&#x7684;&#x6570;&#x636E;&#x7C7B;&#x578B;&#x7684;&#x9650;&#x5236;&#x548C;&#x5FAE;&#x63A7;&#x5236;&#x5668;&#x4E2D;&#x53EF;&#x7528;&#x7684;
          RAM&#xFF09;&#xFF0C;&#x4F46;&#x5EFA;&#x8BAE;&#x4F7F;&#x7528;&#x6240;&#x9700;&#x7684;&#x6700;&#x4F4E;&#x4F18;&#x5148;&#x7EA7;&#x6570;
          &#xFF0C;&#x4EE5;&#x907F;&#x514D;&#x6D6A;&#x8D39; RAM&#x3002;</p>
        <p>&#x4F20;&#x9012;&#x4E0A;&#x9762;&#x7684; <code>uxPriority</code> &#x503C;&#xFF08;<code>configMAX_PRIORITIES - 1</code>&#xFF09;&#x5C06;&#x5BFC;&#x81F4;&#x5206;&#x914D;&#x7ED9;&#x4EFB;&#x52A1;&#x7684;&#x4F18;&#x5148;&#x7EA7;&#x88AB;&#x9650;&#x5236;&#x4E3A;&#x6700;&#x5927;&#x5408;&#x6CD5;&#x503C;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">pxCreatedTask</td>
      <td style="text-align:left"><code>pxCreatedTask</code> &#x53EF;&#x7528;&#x4E8E;&#x4F20;&#x9012;&#x6B63;&#x5728;&#x521B;&#x5EFA;&#x7684;&#x4EFB;&#x52A1;&#x7684;&#x53E5;&#x67C4;&#x3002;
        &#x7136;&#x540E;&#xFF0C;&#x6B64;&#x53E5;&#x67C4;&#x53EF;&#x7528;&#x4E8E;&#x5F15;&#x7528;
        API &#x8C03;&#x7528;&#x4E2D;&#x7684;&#x4EFB;&#x52A1;&#xFF0C;&#x4F8B;&#x5982;&#xFF0C;&#x66F4;&#x6539;&#x4EFB;&#x52A1;&#x4F18;&#x5148;&#x7EA7;&#x6216;&#x5220;&#x9664;&#x4EFB;&#x52A1;&#x3002;&#x5982;&#x679C;&#x60A8;&#x7684;&#x5E94;&#x7528;&#x7A0B;&#x5E8F;&#x6CA1;&#x6709;&#x4F7F;&#x7528;&#x4EFB;&#x52A1;&#x53E5;&#x67C4;&#xFF0C;&#x90A3;&#x4E48; <code>pxCreatedTask</code> &#x53EF;&#x4EE5;&#x8BBE;&#x7F6E;&#x4E3A; <code>NULL</code>&#x3002;</td>
    </tr>
  </tbody>
</table>### 返回值

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x8FD4;&#x56DE;&#x503C;</th>
      <th style="text-align:left">&#x63CF;&#x8FF0;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">pdPASS</td>
      <td style="text-align:left">&#x8868;&#x793A;&#x5DF2;&#x6210;&#x529F;&#x521B;&#x5EFA;&#x4EFB;&#x52A1;&#x3002;</td>
    </tr>
    <tr>
      <td style="text-align:left">errCOULD_NOT_ALLOCATE_REQUIRED_MEMORY</td>
      <td style="text-align:left">
        <p>&#x8868;&#x793A;&#x65E0;&#x6CD5;&#x521B;&#x5EFA;&#x4EFB;&#x52A1;&#xFF0C;&#x56E0;&#x4E3A;
          FreeRTOS &#x6CA1;&#x6709;&#x8DB3;&#x591F;&#x7684;&#x5806;&#x5185;&#x5B58;&#x6765;&#x5206;&#x914D;&#x4EFB;&#x52A1;&#x6570;&#x636E;&#x7ED3;&#x6784;&#x548C;&#x5806;&#x6808;&#x3002;</p>
        <p>&#x5982;&#x679C;&#x9879;&#x76EE;&#x4E2D;&#x5305;&#x542B; <code>heap_1.c</code>&#xFF0C;<code>heap_2.c</code> &#x6216; <code>heap_4.c</code> &#xFF0C;&#x90A3;&#x4E48;&#x53EF;&#x7528;&#x5806;&#x7684;&#x603B;&#x91CF;&#x7531; <code>FreeRTOSConfig.h</code> &#x4E2D;&#x7684; <code>configTOTAL_HEAP_SIZE</code> &#x5B9A;&#x4E49;&#xFF0C;&#x5E76;&#x4E14;&#x4F7F;&#x7528;&#x53EF;&#x6355;&#x83B7;&#x5185;&#x5B58;&#x5206;&#x914D;&#x5931;&#x8D25;&#x7684; <code>vApplicationMallocFailedHook()</code> &#x56DE;&#x8C03;&#xFF08;&#x6216; <code>hook</code>&#xFF09;&#x51FD;&#x6570;&#xFF0C;&#x53EF;&#x4EE5;&#x4F7F;&#x7528; <code>xPortGetFreeHeapSize()</code> API
          &#x51FD;&#x6570;&#x67E5;&#x8BE2;&#x5269;&#x4F59;&#x7684;&#x53EF;&#x7528;&#x5806;&#x5185;&#x5B58;&#x91CF;&#x3002;</p>
        <p>&#x5982;&#x679C; <code>heap_3.c</code> &#x5305;&#x542B;&#x5728;&#x9879;&#x76EE;&#x4E2D;&#xFF0C;&#x5219;&#x603B;&#x5806;&#x5927;&#x5C0F;&#x7531;&#x94FE;&#x63A5;&#x5668;&#x914D;&#x7F6E;&#x5B9A;&#x4E49;&#x3002;</p>
      </td>
    </tr>
  </tbody>
</table>### 注意

在 `FreeRTOSConfig.h` 中，`configSUPPORT_DYNAMIC_ALLOCATION` 必须设置为 1，或者就设置成简单的未定义，以使此函数可用。

### 例子

```c
/* Define a structure called xStruct and a variable of type xStruct.  These are just used to 
demonstrate a parameter being passed into a task function. */
typedef struct A_STRUCT
{
    char cStructMember1;
    char cStructMember2;
} xStruct;    

/* Define a variable of the type xStruct to pass as the task parameter. */
xStruct xParameter = { 1, 2 };

/* Define the task that will be created.  Note the name of the function that implements the task 
is used as the first parameter in the call to xTaskCreate() below. */
void vTaskCode( void * pvParameters )
{
    xStruct *pxParameters;
    
    /* Cast the void * parameter back to the required type. */
    pxParameters = ( xStruct * ) pvParameters;
    
    /* The parameter can now be accessed as expected. */
    if( pxParameters->cStructMember1 != 1 )
    {
        /* Etc. */
    }        
    
    /* Enter an infinite loop to perform the task processing. */
    for( ;; )
    {
        /* Task code goes here. */
    }
}    

/* Definea function that creates a task.  This could be called either before or after the 
scheduler has been started. */
void vAnotherFunction( void )
{
    TaskHandle_txHandle;
    
    /* Create the task. */
    if( xTaskCreate( 
        vTaskCode,            /* Pointer to the function that implements the task. */
        "Demo task",          /* Text name given to the task. */
        STACK_SIZE,           /* The size of the stack that should be created for the task.
                                 This is defined in words, not bytes. */
        (void*) &xParameter,  /* A reference to xParameters is used as the task parameter.
                                 This is cast to a void * to prevent compiler warnings. */
        TASK_PRIORITY,        /* The priority to assign to the newly created task.*/
        &xHandle              /* The handle to the task being created will be placed in
                                 xHandle. */
        ) != pdPASS )
    {
        /* The task could not be created as there was insufficient heap memory remaining. 
        If heap_1.c, heap_2.c or heap_4.c are included in the project then this situation can be 
        trapped using the vApplicationMallocFailedHook() callback (or ‘hook’) function,and the 
        amount of FreeRTOS heap memory that remains unallocated can be queried using the 
        xPortGetFreeHeapSize() API function.*/
    }
    else
    {
        /* The task was created successfully. The handle can now be used in other API functions,
        for example to change the priority of the task.*/
        vTaskPrioritySet( xHandle, 2 );
    }    
}
```

清单 13. `xTaskCreate()` 使用示例

