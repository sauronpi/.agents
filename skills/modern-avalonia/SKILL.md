---
name: modern-avalonia
description: '实现或审查 Avalonia UI 的数据绑定、样式或控件行为时，选择适用于项目版本和视图设置的框架机制。适用于 XAML 和代码中的 UI 实现，不用于纯 C# 语法或通用 .NET API 选择。'
disable-model-invocation: false
---

# 选择 Avalonia UI 实现

- 先确认要实现的 UI 行为和实际解析的 Avalonia 包版本。以当前官方稳定版的推荐做法为起点，核对项目版本是否提供对应 API；不要把预览文档或其他 XAML 框架的同名机制当作支持依据。
- 判断绑定时，先看单处是否显式使用 `CompiledBinding` 或 `ReflectionBinding`。普通 `{Binding ...}` 看所在作用域生效的 `x:CompileBindings`，再看项目（含导入属性）的 `AvaloniaUseCompiledBindingsByDefault`。均未覆盖时，按[官方编译绑定说明](https://docs.avaloniaui.net/docs/data-binding/compiled-bindings)，11.x 默认反射绑定，12.x 默认编译绑定。
- 绑定源类型已知时优先编译绑定：让编译器知道实际源类型，例如在视图声明 `x:DataType`，进入数据模板则核对模板项的类型。类型声明不设置运行时 `DataContext`，也不负责开启编译绑定。默认已开启时保留普通绑定；默认关闭而只需检查一处路径时，在该处使用 `CompiledBinding` 并提供类型，不改项目默认值。仅在源必须动态解析，或编译绑定无法保持所需行为时，局部使用 `ReflectionBinding`，并说明原因。
- 选择样式或控件 API 时，先看该版本的控件是否已有满足行为的属性或命令；外观随状态变化时优先使用 Avalonia 样式选择器及控件提供的伪类，不直接移植其他框架的触发器。采用旧机制时说明缺少哪个 API 或哪种行为不等价，而非沿用习惯或旧示例；不为局部问题迁移项目、升级依赖或改写无关视图。
- 按项目原有方式构建受影响的视图以检查 XAML 和编译绑定路径；反射绑定要在实际数据上下文中检查显示结果及绑定日志，样式或控件变更则核对触发行为前后的 UI 状态。
