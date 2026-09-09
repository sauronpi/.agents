# C 命名

## 优先级

依次服从用户当前要求、项目规则和工具配置、邻近 C 代码风格、本规范默认值。标准接口、
第三方 API、平台 ABI、回调签名以及框架规定的名称 MUST 按其契约保留。除非任务明确要求
全局迁移，否则不得为了统一风格修改无关名称。

## 通用原则

- 名称 MUST 清晰、无歧义，并在同一概念和操作之间保持一致。
- 只使用目标领域普遍理解的缩写；缩写在 snake_case 中视为普通单词，例如
  `uart_rx_count`，不得写成 `u_a_r_t_rx_count`。
- 不使用匈牙利命名，不把基础类型、指针层级或存储类别编码进名称。
- 文件名 SHOULD 使用 `snake_case.c` 或 `snake_case.h`。

## 标识符形式

| 对象                     | 形式                       | 示例                       |
| ------------------------ | -------------------------- | -------------------------- |
| 函数                     | `snake_case`               | `acme_device_create`       |
| 变量、参数和字段         | `snake_case`               | `sample_count`             |
| struct、union、enum 标签 | `snake_case`               | `struct acme_device`       |
| 枚举值                   | `UPPER_SNAKE_CASE`         | `ACME_DEVICE_STATE_READY`  |
| 常量和对象式宏           | `UPPER_SNAKE_CASE`         | `ACME_DEVICE_LIMIT`        |
| 函数式宏                 | `UPPER_SNAKE_CASE`         | `ACME_ARRAY_COUNT(value)`  |

### typedef 后缀

本节适用于所有自有 typedef 名，包括 struct、union、enum、标量和函数指针的 typedef
名；不适用于 struct、union、enum 标签，标签继续使用不带 `_t` 或 `_type` 的 snake_case。

- 项目存在 typedef 命名约定时 MUST 遵循该约定。
- 没有项目约定且项目不涉及 POSIX 时，自有 typedef SHOULD 使用 `_t`，回调 typedef
  SHOULD 使用 `_callback_t`。
- 仅当项目规则、目标平台、构建配置或所用接口表明项目涉及 POSIX 时，自有 typedef
  SHOULD 改用 `_type`，回调 typedef SHOULD 改用 `_callback_type`。POSIX 允许实现在其
  任意标准头文件中增加以 `_t` 结尾的类型名。
- 不得仅因代码使用 ISO C 标准库就推断项目涉及 POSIX。

公开 struct、union、enum 标签和 typedef SHOULD 使用项目或模块前缀。不要仅为统一后缀
而给可直接使用标签的既有项目批量新增 typedef；Linux 内核等限制 typedef 的项目服从其
原生规范。标准、平台、SDK、第三方 API 或 ABI 规定的 `size_t`、`uint32_t`、`pthread_t`、
`vendor_handle_t` 等名称 MUST 保持不变。

公共函数、变量、类型、枚举值和宏 MUST 使用项目声明的前缀。以下示例采用项目不涉及
POSIX 时的 `_t` 默认：

```c
typedef struct acme_device acme_device_t;

typedef enum acme_device_state
{
    ACME_DEVICE_STATE_IDLE,
    ACME_DEVICE_STATE_READY
} acme_device_state_t;

acme_device_t *acme_device_create(void);
```

文件内 `static` 函数和变量 MAY 省略公共前缀，但 MUST 保持 snake_case。全局变量 SHOULD
避免；确有需要时 MUST 使用公共前缀并明确其所有权和并发约束。

## 语义命名

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

## 设计依据

- POSIX 为实现保留其标准头文件中的 `_t` 类型后缀：
  [POSIX.1-2024 The Name Space](https://pubs.opengroup.org/onlinepubs/9799919799/functions/V2_chap02.html)。
- Linux 内核代码以其项目规范为准，尤其遵循其 typedef 和命名要求：
  [Linux kernel coding style](https://docs.kernel.org/process/coding-style.html)。
