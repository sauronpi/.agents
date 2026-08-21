# 通用 C 代码风格参考

## 适用边界与优先级

本规范适用于人工维护的 `.c`、`.h`，包括普通 C、嵌入式 C 和同一受控构建环境中的
C/C++ 共享头文件。第三方代码、生成代码、独立 C++ 或其他语言文件不适用。

发生冲突时，依次服从用户当前要求、项目规则和工具配置、邻近 C 代码风格、本规范默认
值。除非任务明确要求全局迁移，否则不得为了统一风格修改无关代码。

## 命名

### 通用原则

- 名称 MUST 清晰、无歧义，并在同一概念和操作之间保持一致。
- 只使用目标领域普遍理解的缩写；缩写在 snake_case 中视为普通单词，例如
  `uart_rx_count`，不得写成 `u_a_r_t_rx_count`。
- 不使用匈牙利命名，不把基础类型、指针层级或存储类别编码进名称。
- 文件名 SHOULD 使用 `snake_case.c` 或 `snake_case.h`。

### 标识符形式

| 对象                     | 形式                       | 示例                       |
| ------------------------ | -------------------------- | -------------------------- |
| 函数                     | `snake_case`               | `acme_device_create`       |
| 变量、参数和字段         | `snake_case`               | `sample_count`             |
| struct、union、enum 标签 | `snake_case`               | `struct acme_device`       |
| typedef 类型             | `snake_case_type`          | `acme_device_type`         |
| 回调 typedef             | `snake_case_callback_type` | `acme_event_callback_type` |
| 枚举值                   | `UPPER_SNAKE_CASE`         | `ACME_DEVICE_STATE_READY`  |
| 常量和对象式宏           | `UPPER_SNAKE_CASE`         | `ACME_DEVICE_LIMIT`        |
| 函数式宏                 | `UPPER_SNAKE_CASE`         | `ACME_ARRAY_COUNT(value)`  |

typedef 名 MUST 使用 `_type` 后缀，不使用 `_t`。公开 struct、union、enum 标签和 typedef
SHOULD 使用项目或模块前缀。不要仅为添加 `_type` 而给可直接使用标签的既有项目批量新增
typedef；Linux 内核等限制 typedef 的项目服从其原生规范。

公共函数、变量、类型、枚举值和宏 MUST 使用项目声明的前缀：

```c
typedef struct acme_device acme_device_type;

typedef enum acme_device_state
{
    ACME_DEVICE_STATE_IDLE,
    ACME_DEVICE_STATE_READY
} acme_device_state_type;

acme_device_type *acme_device_create(void);
```

文件内 `static` 函数和变量 MAY 省略公共前缀，但 MUST 保持 snake_case。全局变量 SHOULD
避免；确有需要时 MUST 使用公共前缀并明确其所有权和并发约束。

### 语义命名

- 函数 MUST 使用语义化名称。名称应使用领域或模块中的准确词汇表达职责，使读者仅结合
  函数声明即可理解其主要目的。
- 执行动作的函数 MUST 使用能描述可观察行为的动词，不得以内部算法、数据结构或实现步骤
  代替接口语义。避免 `process`、`handle`、`do`、`manage` 等不能独立说明职责的宽泛词；
  领域术语或既有惯用名称除外。
- 名称 MUST 包含消除歧义所需的信息，并 SHOULD 省略不能增加调用处语义的冗余词。不得
  强制套用固定的单词组合或命名模板。
- `create` 表示建立新对象或资源，`destroy` 表示终止其生命周期；没有对应生命周期语义
  时不得使用这些词。
- `get` 和 `set` 只用于读取和设置同一概念；有副作用或昂贵计算时名称 SHOULD 明确表达。
- 布尔变量和查询函数 SHOULD 使用 `is_`、`has_`、`can_`、`should_` 等前缀，使真假含义
  无需依赖注释。
- 同一类集合操作 MUST 对 `add`、`remove`、`replace`、`find` 等动词保持一致语义。
- `from`、`to`、`in`、`at`、`by`、`with` 等关系词 SHOULD 在能够明确来源、目标、位置、
  依据或附加条件时使用；不得仅为保持形式一致而添加。关系词常见用法示例：
  - `from` 表示数据或资源来源：`acme_decode_from_stream`、`acme_copy_from_buffer`；
  - `to` 表示转换或移动的目标：`acme_convert_to_utf8`、`acme_write_to_file`；
  - `in` 表示值或操作的容器或域：`acme_find_in_table`、`acme_search_in_tree`；
  - `at` 表示位置或索引：`acme_remove_at_index`、`acme_insert_at_position`；
  - `by` 表示操作方式或倍数：`acme_scale_by_factor`、`acme_sort_by_name`；
  - `with` 表示配套条件或参数：`acme_create_with_allocator`、`acme_init_with_config`。
- 具有外部链接的项目函数 MUST 使用项目约定的命名空间前缀。仅供当前翻译单元使用的
  函数 MUST 声明为 `static`。
- 标准接口、第三方 API、平台 ABI、回调签名以及框架规定的函数名称 MUST 按其契约保留。

## 源文件与头文件

- 头文件 MUST 自包含，并使用带项目或模块前缀的 include guard。
- 函数原型 MUST 包含参数名；无参数函数 MUST 显式写 `(void)`。
- 一个声明只声明一个对象，避免在同一行混合不同指针层级。
- `.c` 文件 SHOULD 首先包含对应头文件，再包含标准库和其他项目头文件；项目已有顺序
  优先。
- 公共接口 SHOULD 说明输入、输出、错误、单位、生命周期和并发要求中实际适用的部分，
  不为不存在的约束增加模板化注释。
- C 专属实现细节 SHOULD 留在 `.c` 文件；仅供当前翻译单元使用的函数和变量 MUST 声明
  为 `static`。

## C/C++ 共享头文件

项目声明为公共或 C/C++ 共享的头文件 MUST 可被 C 和 C++ 编译器分别独立包含：

```c
#ifndef ACME_DEVICE_H
#define ACME_DEVICE_H

#ifdef __cplusplus
extern "C"
{
#endif

typedef struct acme_device acme_device_type;

acme_device_type *acme_device_create(void);
void acme_device_destroy(acme_device_type *device);

#ifdef __cplusplus
}
#endif

#endif
```

- `extern "C"` MUST 仅出现在 `#ifdef __cplusplus` 条件内，不得写入 `.c` 实现。
- 不使用 C++ 关键字作为函数、参数、字段、标签、typedef 或宏名称。
- 共享声明 MUST 属于项目选择的 C 和 C++ 标准都接受的语法子集。
- C++ 文件保持其自身命名和排版规范；本规范只约束 C 文件及共享头文件中的 C 接口。
- 兼容范围只包括同一项目声明的编译器、平台和构建配置。本规范不规定数据布局、调用
  约定、跨编译器兼容、动态库版本兼容、其他语言 FFI 或绑定生成。

## 嵌入式与可移植性

- 不默认假设操作系统、线程、文件系统、动态内存或标准输出可用；项目明确提供时方可
  依赖。
- 不默认使用编译器扩展、内联汇编或厂商属性；确有硬件或工具链需要时，按项目约定隔离
  并说明适用范围。
- 根据语义选择整数类型。协议字段、寄存器和存储格式确实要求宽度时使用可用的固定宽度
  类型；普通计数、大小和索引优先使用项目和接口所需的标准类型。
- `volatile` 仅用于硬件寄存器、信号处理或项目已确认的语义，不得替代原子操作、锁或
  其他并发同步。
- 硬件寄存器、位域、对齐、字节序和中断约束 MUST 服从芯片、编译器和项目文档，不从
  通用代码风格推断。

## 排版默认

存在 `.clang-format` 或项目 formatter 时 MUST 使用其配置。否则保持邻近且仍有效的 C
风格。只有两者均不存在时才使用以下默认：

- 缩进 4 个空格，禁止使用 Tab 缩进；
- 函数、控制语句、类型定义和初始化器的左大括号另起一行；
- 指针星号靠近声明符，例如 `char *name`；
- 100 列为软限制；只有换行明显降低可读性时才超过；
- 关键字与左括号之间保留空格，函数名与左括号之间不留空格；
- 二元和三元运算符两侧留空格，一元运算符与操作数之间不留空格；
- 控制语句即使只有一条语句也使用大括号；
- 一行只放一条语句或声明，不保留行尾空白。

不得用这些默认覆盖 Linux 内核、既有项目规范或 formatter 输出。

## 设计依据

- `_type` 用于避开 POSIX 为实现保留的 `_t` 类型后缀：
  [POSIX Defined Types Rationale](https://pubs.opengroup.org/onlinepubs/9799919799/xrat/V4_xsh_chap01.html)。
- Linux 内核代码以其项目规范为准，尤其遵循其 typedef 和排版要求：
  [Linux kernel coding style](https://docs.kernel.org/process/coding-style.html)。
