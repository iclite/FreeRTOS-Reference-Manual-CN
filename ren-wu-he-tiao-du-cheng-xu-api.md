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

