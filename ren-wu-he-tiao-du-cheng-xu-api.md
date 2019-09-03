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

