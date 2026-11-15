# Circle

新版 Circle 原型由手写 x86-64 机器码实现，支持 Windows x64。当前可运行和编译整数加法、多个局部变量、显式 `u8`、单数字输入以及标准输出。

```powershell
.\machine\circle.exe --help
.\machine\circle.exe run examples\names.cir
.\machine\circle.exe build examples\names.cir -o names.exe
```

编译产物在启动时检查 `%LOCALAPPDATA%\Circle\circle.exe`。双语图形安装器仍在开发中。十六进制源码、复现方式和当前限制见 [机器码说明](machine/README.md)，后续工作见 [开发任务清单](ROADMAP.md)。

## 旧版原型

> **重做进行中。** 新版示例使用 `.cir`，手写机器码入口已实测支持 `circle help`、`circle --help` 和 `circle version`。源码执行和中英文图形安装仍在开发中。以下介绍的是曾用 Python 构建的旧原型，其安装程序使用命令行。新版文件与复现方式见 [machine/README.md](machine/README.md)。

Circle 是一个简单的整数栈语言。这个旧原型支持 Windows x86-64，示例文件扩展名已更改为 `.cir`。旧解释器和安装器运行时使用 Windows 系统 API，不需要安装 Python、编译器或第三方运行库。

## 安装

运行 [CircleSetup.exe](CircleSetup.exe)。它会将内置的 `cc.exe` 安装到当前用户的 `%LOCALAPPDATA%\Circle`，并将该目录加入当前用户的 `PATH`。不需要管理员权限。安装器和 `cc.exe` 均嵌入了项目的 Circle logo 图标。

安装完成后，**关闭并重新打开终端**，然后运行：

```powershell
cc --help
cc --version
cc path\to\program.cir
```

例如，在本项目目录运行 `cc examples\count.cir`，会依次输出 5 到 1。如果系统已有其他名为 `cc` 的命令，命令搜索顺序可能使它优先运行；此时可使用 `%LOCALAPPDATA%\Circle\cc.exe` 的完整路径，或调整 PATH 顺序。这里的 `cc` 是旧原型入口，不是计划中的 `circle` 命令。

如果希望自行管理 PATH，可以在终端运行 `CircleSetup.exe --no-path`；这会安装文件，但不修改 PATH。卸载时，删除 `%LOCALAPPDATA%\Circle`，并从当前用户的 PATH 中移除该目录。

## 不安装直接运行

在项目目录的 PowerShell 中：

```powershell
.\cc.exe --help
.\cc.exe examples\count.cir
cmd /c "cc.exe - < examples\count.cir"
```

`cc -` 从标准输入读取整个程序。不带参数时显示帮助。成功时退出码为 0；参数、文件、程序或输入输出错误时退出码为 1。

`cc.hex` 是 `cc.exe` 完整文件的十六进制表示，包括机器指令、PE 文件头、导入表、图标和数据。可以用 Windows 自带工具还原并核对：

```powershell
certutil -f -decodehex cc.hex cc-restored.exe 4
Get-FileHash cc.exe, cc-restored.exe -Algorithm SHA256
```

## 语言规则

程序从左向右执行。整数放入栈顶；运算符从栈顶取出操作数，再将结果放回栈顶。两个数的运算中，先入栈的是左操作数。例如，`8 3 - .` 输出 `5`。源码是 ASCII 文本；空格和换行被忽略，数字之间需要空白或运算符分隔。

| 写法 | 作用 |
| --- | --- |
| `0` 至 `2147483647` | 将十进制整数压栈。负数可用 `0 5 -` 构造。 |
| `+` `-` `*` | 加、减、乘。 |
| `/` `%` | 有符号整数除法、余数；商向零取整。 |
| `=` `<` `>` | 比较两个有符号数，结果为真时压入 `1`，否则压入 `0`。 |
| `d` | 复制栈顶。 |
| `x` | 丢弃栈顶。 |
| `s` | 交换栈顶两个数。 |
| `.` | 弹出并以十进制输出栈顶，末尾附换行。 |
| `[ ... ]` | 在 `[` 处检查栈顶：为零则跳到匹配的 `]` 之后；非零则执行循环体。`]` 跳回匹配的 `[`。检查本身不弹出栈顶，循环可以嵌套。 |

例如 `5 [ d . 1 - ] x`：先放入 5；每轮复制并打印计数器，将其减一；当计数器为 0 时退出并丢弃它。更多示例见 `examples/`。

整数计算使用 32 位补码：加、减、乘超出范围时按 32 位回绕。除以零以及 `-2147483648 / -1` 或对应的余数运算会报错。栈最多容纳 256 个数；程序最多 65,536 字节。栈不足、栈溢出、字面量过大、非法字符、括号不匹配和输入输出失败都会报错。括号在开始执行前检查；其他错误发生前的输出不会撤销。

## 当前范围和性能

这是一个可运行的最小原型，暂不提供变量、字符串、函数、注释、交互式输入或错误位置。循环跳转通过扫描匹配括号实现，适合学习和小程序。解释器直接运行原生机器码，但尚未进行性能基准测试，不能据此声称它比其他语言更快。
