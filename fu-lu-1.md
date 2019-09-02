# 附录 1: 数据类型和编程风格指南

## 数据类型

每个移植 FreeRTOS 的实例都有一个唯一的 `portmacro.h` 头文件，其中包含两种特殊数据类型\(`TickType_t` 和 `BaseType_t`\)的定义。表 3 中描述了这些数据类型。

表3. FreeRTOS 使用的特殊数据类型

<table>
  <thead>
    <tr>
      <th style="text-align:left">&#x4F7F;&#x7528;&#x5B8F;&#x6216;&#x7C7B;&#x578B;&#x5B9A;&#x4E49;</th>
      <th
      style="text-align:left">&#x5B9E;&#x9645;&#x7C7B;&#x578B;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">TickType_t</td>
      <td style="text-align:left">
        <p>&#x8FD9;&#x7528;&#x4E8E;&#x5B58;&#x50A8; tick &#x8BA1;&#x6570;&#x503C;&#xFF0C;&#x4EE5;&#x53CA;&#x7528;&#x4E8E;&#x6307;&#x5B9A;&#x5757;&#x65F6;&#x95F4;&#x7684;&#x53D8;&#x91CF;&#x3002;</p>
        <p><code>TickType_t</code>&#x53EF;&#x4EE5;&#x662F;&#x65E0;&#x7B26;&#x53F7;
          16 &#x4F4D;&#x7C7B;&#x578B;&#x6216;&#x65E0;&#x7B26;&#x53F7; 32 &#x4F4D;&#x7C7B;&#x578B;&#xFF0C;&#x5177;&#x4F53;&#x53D6;&#x51B3;&#x4E8E;<code>FreeRTOSConfig.h</code> &#x4E2D; <code>configUSE_16_BIT_TICKS</code> &#x7684;&#x8BBE;&#x7F6E;&#x3002;</p>
        <p>&#x4F7F;&#x7528; 16 &#x4F4D;&#x7C7B;&#x578B;&#x53EF;&#x4EE5;&#x6781;&#x5927;&#x5730;&#x63D0;&#x9AD8;
          8 &#x4F4D;&#x548C; 16 &#x4F4D;&#x67B6;&#x6784;&#x7684;&#x6548;&#x7387;&#xFF0C;&#x4F46;&#x4E25;&#x91CD;&#x9650;&#x5236;&#x4E86;&#x53EF;&#x4EE5;&#x6307;&#x5B9A;&#x7684;&#x6700;&#x5927;&#x5757;&#x5468;&#x671F;&#x3002;
          &#x6CA1;&#x6709;&#x7406;&#x7531;&#x5728; 32 &#x4F4D;&#x67B6;&#x6784;&#x4E0A;&#x4F7F;&#x7528;
          16 &#x4F4D;&#x7C7B;&#x578B;&#x3002;</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">BaseType_t</td>
      <td style="text-align:left">
        <p>&#x8FD9;&#x59CB;&#x7EC8;&#x88AB;&#x5B9A;&#x4E49;&#x4E3A;&#x67B6;&#x6784;&#x7684;&#x6700;&#x6709;&#x6548;&#x6570;&#x636E;&#x7C7B;&#x578B;&#x3002;
          &#x901A;&#x5E38;&#xFF0C;&#x8FD9;&#x662F; 32 &#x4F4D;&#x67B6;&#x6784;&#x4E0A;&#x7684;
          32 &#x4F4D;&#x7C7B;&#x578B;&#xFF0C;16 &#x4F4D;&#x67B6;&#x6784;&#x4E0A;&#x7684;
          16 &#x4F4D;&#x7C7B;&#x578B;&#x548C; 8 &#x4F4D;&#x67B6;&#x6784;&#x4E0A;&#x7684;
          8 &#x4F4D;&#x7C7B;&#x578B;&#x3002;</p>
        <p><code>BaseType_t</code> &#x901A;&#x5E38;&#x7528;&#x4E8E;&#x53EA;&#x80FD;&#x91C7;&#x7528;&#x975E;&#x5E38;&#x6709;&#x9650;&#x7684;&#x503C;&#x8303;&#x56F4;&#x7684;&#x53D8;&#x91CF;&#xFF0C;&#x4EE5;&#x53CA;&#x5E03;&#x5C14;&#x503C;&#x3002;</p>
      </td>
    </tr>
  </tbody>
</table>不使用 `char` 以外的标准数据类型（见下文），而是使用编译器的 `stdint.h` 头文件中定义的类型名称。 `char` 类型仅允许指向 ASCII 字符串或引用单个 ASCII 字符。

## 变量名

变量的前缀是它们的类型：`c` 表示 `char`，`s` 表示 `short`，`l` 表示 `long`，`x` 表示 `BaseType_t` 和任何其他类型（结构，任务句柄，队列句柄等）。如果变量是无符号的，它也带有 `u` 前缀。 如果变量是指针，则它也带有 `p` 前缀。 因此，`unsigned char` 类型的变量将以 `uc` 为前缀，而指向 `char` 的类型指针的变量将以 `pc` 为前缀。

