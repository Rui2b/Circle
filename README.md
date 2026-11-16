# Circle

Circle 是一个面向系统编程的语言原型。编译器和安装器的新版实现直接维护 x86-64 十六进制机器码，当前支持 Windows x64。

## 安装与运行

双击 [CircleSetup.exe](CircleSetup.exe)，选择中文或 English，然后确认安装。安装器将内置的 `circle.exe` 安装到当前用户的 `%LOCALAPPDATA%\Circle`，无需管理员权限。

当前安装器尚未自动配置 PATH。可以直接运行已安装的命令：

```powershell
& "$env:LOCALAPPDATA\Circle\circle.exe" --help
& "$env:LOCALAPPDATA\Circle\circle.exe" run examples\assignment-input.cir
& "$env:LOCALAPPDATA\Circle\circle.exe" build examples\repeat.cir -o repeat.exe
.\repeat.exe
```

如需在新终端中直接使用 `circle <命令>`，在 Windows 的用户环境变量 `Path` 中加入 `%LOCALAPPDATA%\Circle`。编译产物在启动时检查安装目录，找不到 `circle.exe` 会返回 126。

## 当前能力

支持 `.cir` 源码、`u8`、`u16`、`u32`、局部变量和赋值、整数加法、单数字输入、标准输出、条件表达式及固定步长的计数循环。当前语法和限制见 [机器码说明](machine/README.md)，后续工作见 [开发任务清单](ROADMAP.md)。

`cc.exe`、`cc.hex` 和栈语言示例来自早期原型。新版入口为 `circle.exe`。
