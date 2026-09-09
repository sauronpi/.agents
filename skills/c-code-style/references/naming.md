# C 命名

标准接口、第三方 API、平台 ABI、回调签名以及框架规定的名称 MUST 按其契约保留。

## 通用原则

- 名称 MUST 清晰、无歧义，并在同一概念和操作之间保持一致。
- 只使用目标领域普遍理解的缩写；缩写在 snake_case 中视为普通单词，例如
  `uart_rx_count`，不得写成 `u_a_r_t_rx_count`。
- 不使用匈牙利命名，不把基础类型、指针层级或存储类别编码进名称。
- 文件名 SHOULD 使用 `snake_case.c` 或 `snake_case.h`。

## 标识符形式

| 对象                     | 形式               | 示例                      |
| ------------------------ | ------------------ | ------------------------- |
| 函数                     | `snake_case`       | `acme_device_create`      |
| 变量、参数和字段         | `snake_case`       | `sample_count`            |
| struct、union、enum 标签 | `snake_case`       | `struct acme_device`      |
| 枚举值                   | `UPPER_SNAKE_CASE` | `ACME_DEVICE_STATE_READY` |
| 常量和对象式宏           | `UPPER_SNAKE_CASE` | `ACME_DEVICE_LIMIT`       |
| 函数式宏                 | `UPPER_SNAKE_CASE` | `ACME_ARRAY_COUNT(value)` |

### typedef 后缀

本节适用于所有自有 typedef 名；struct、union、enum 标签仍使用不带类型后缀的 snake_case。

没有项目约定时，自有 typedef SHOULD 使用 `_t`，回调 typedef SHOULD 使用 `_callback_t`。

不要仅为统一后缀而给可直接使用标签的既有项目批量新增 typedef；Linux 内核等限制
typedef 的项目服从其[原生规范](https://docs.kernel.org/process/coding-style.html)。

### 公共前缀

公共符号（函数、变量、类型标签、typedef、枚举值和宏）及其他具有外部链接的项目函数
MUST 使用一致的项目或模块前缀，已有约定优先。以下示例采用 `_t` 默认：

```c
typedef struct acme_device acme_device_t;

typedef enum acme_device_state
{
    ACME_DEVICE_STATE_IDLE,
    ACME_DEVICE_STATE_READY
} acme_device_state_t;

acme_device_t *acme_device_create(void);
```

文件内 `static` 函数和变量 MAY 省略公共前缀。全局变量 SHOULD 避免；确有需要时 MUST
明确其所有权和并发约束。

## 语义命名

- 函数名称 MUST 使用准确的领域词汇表达职责；动作函数用动词描述可观察行为，不以内部
  实现步骤代替接口语义。避免 `process`、`handle`、`do`、`manage` 等无信息的宽泛词；
  领域术语或既有惯用名称除外。
- 保留消除歧义所需的信息，省略冗余词，不强制套用固定命名模板。
- `create` 表示建立新对象或资源，`destroy` 表示终止其生命周期；没有对应生命周期语义
  时不得使用这些词。
- `get` 和 `set` 只用于读取和设置同一概念；有副作用或昂贵计算时名称 SHOULD 明确表达。
- 布尔变量和查询函数 SHOULD 使用 `is_`、`has_`、`can_`、`should_` 等前缀，使真假含义
  无需依赖注释。
- 同一类集合操作 MUST 对 `add`、`remove`、`replace`、`find` 等动词保持一致语义。

## 设计依据

- POSIX 为实现保留其标准头文件中的 `_t` 类型后缀：
  [POSIX.1-2024 The Name Space](https://pubs.opengroup.org/onlinepubs/9799919799/functions/V2_chap02.html)。
  `_t` 是本 Skill 的风格默认，不构成 POSIX 命名空间合规保证；明确要求该合规性时，
  使用项目允许的非保留名称。
