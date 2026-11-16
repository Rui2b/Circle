# Circle

Circle 是一个面向系统编程的语言原型。编译器和安装器的新版实现直接维护 x86-64 十六进制机器码，当前支持 Windows x64。

## 安装与运行

双击 [CircleSetup.exe](CircleSetup.exe)，选择中文或 English，然后确认安装。安装器将内置的 `circle.exe` 安装到当前用户的 `%LOCALAPPDATA%\Circle`，无需管理员权限。

安装器会将安装目录加入当前用户的 PATH。安装完成后打开一个新终端，即可运行：

```powershell
circle --help
circle run examples\assignment-input.cir
circle build examples\repeat.cir -o repeat.exe
.\repeat.exe
```

如果安装器报告 PATH 更新失败，可用完整路径 `%LOCALAPPDATA%\Circle\circle.exe`，也可手动把安装目录加入用户 PATH。编译产物在启动时检查安装目录，找不到 `circle.exe` 会返回 126。

要卸载，在安装器所在的目录运行 `.\CircleSetup.exe --uninstall`，选择语言并确认。卸载只移除安装的 `circle.exe` 和匹配的用户 PATH 项。

## 当前能力

支持 `.cir` 源码、`u8`、`u16`、`u32`、局部变量和赋值、整数加法、单数字输入、标准输出、条件表达式及固定步长的计数循环。当前语法和限制见 [机器码说明](machine/README.md)，后续工作见 [开发任务清单](ROADMAP.md)。

`cc.exe`、`cc.hex` 和栈语言示例来自早期原型。新版入口为 `circle.exe`。
