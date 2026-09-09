# C/C++ 共享头文件

以下规则只约束 C 文件及共享头文件中的 C 接口，不把 C 命名或排版规范扩展到独立 C++
文件。

项目明确要求由 C 和 C++ 共同使用的头文件 MUST 可被两种语言的编译器分别独立包含。
公共头文件身份本身不构成 C++ 兼容要求。示例：

```c
#ifndef ACME_DEVICE_H
#define ACME_DEVICE_H

#ifdef __cplusplus
extern "C"
{
#endif

struct acme_device;

struct acme_device *acme_device_create(void);
void acme_device_destroy(struct acme_device *device);

#ifdef __cplusplus
}
#endif

#endif
```

- 语言链接声明 MUST 确保 `extern "C"` 在 C 编译时不可见；优先沿用项目已有的等价条件
  编译或封装宏，不得写入 `.c` 实现。
- 不使用 C++ 关键字作为函数、参数、字段、标签、typedef 或宏名称。
- 共享声明 MUST 属于项目选择的 C 和 C++ 标准都接受的语法子集。
- 兼容范围只包括同一项目声明的编译器、平台和构建配置。本规范不规定数据布局、调用
  约定、跨编译器兼容、动态库版本兼容、其他语言 FFI 或绑定生成。

## 验证

- 变更可能影响共享头文件的自包含性或 C/C++ 语法兼容性时，分别使用项目指定的两种
  语言标准执行独立包含的语法编译；缺少等价检查时创建临时 `.c` 和 `.cpp` 包含单元。
- 修改 `extern "C"`、新增或修改跨语言调用接口时，运行项目已有混合链接测试；缺少
  等价覆盖时构造最小的 C 实现与 C++ 调用方链接检查。
