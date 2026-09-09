# C/C++ 共享头文件

依次服从用户当前要求、项目规则和工具配置、邻近代码惯例、本规范默认值。以下规则只约束
C 文件及共享头文件中的 C 接口，不把 C 命名或排版规范扩展到独立 C++ 文件。

项目声明为公共或 C/C++ 共享的头文件 MUST 可被 C 和 C++ 编译器分别独立包含。以下示例
假定项目不涉及 POSIX，因此自有 typedef 使用 `_t`；涉及 POSIX 时按命名规范改用 `_type`：

```c
#ifndef ACME_DEVICE_H
#define ACME_DEVICE_H

#ifdef __cplusplus
extern "C"
{
#endif

typedef struct acme_device acme_device_t;

acme_device_t *acme_device_create(void);
void acme_device_destroy(acme_device_t *device);

#ifdef __cplusplus
}
#endif

#endif
```

- `extern "C"` MUST 仅出现在 `#ifdef __cplusplus` 条件内，不得写入 `.c` 实现。
- 不使用 C++ 关键字作为函数、参数、字段、标签、typedef 或宏名称。
- 共享声明 MUST 属于项目选择的 C 和 C++ 标准都接受的语法子集。
- 兼容范围只包括同一项目声明的编译器、平台和构建配置。本规范不规定数据布局、调用
  约定、跨编译器兼容、动态库版本兼容、其他语言 FFI 或绑定生成。
