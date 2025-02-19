📌 示例 KMDF HID 过滤器驱动程序
📦 项目代码来源
源项目: https://github.com/hedgar2017/loki-hidriver 代码改动不大，主要是为了适配 Windows 11 环境。

❓ 常见问题解答 (FAQ)

Q: 编译时找不到 mshidkmdf.sys？

A: 使用 Everything 软件搜索 mshidkmdf.sys，并复制到 \loki-hidriver\x64\Debug\KMDFDriver 目录下。

Q: 编译时提示需要安装 Spectre 缓解？

A: 在项目属性页中，依次点击 配置属性 -> C/C++ -> 代码生成，找到 Spectre Mitigation 并设置为 Disabled。

Q: 编译时需要证书？

A: 请参考 注意 部分，编译前需要生成证书并启用测试模式。

⚠️ 注意事项：

编译前请在 HIDRIVER.inf 文件中为 "ClassGuid=" 生成 GUID 格式 (例如 {xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}).

编译前请务必 生成驱动证书 并 启用 Windows 测试模式。 📝

📝 描述
这是一个 示例 内核模式驱动程序框架 (KMDF) HID 过滤器驱动程序。它旨在演示 Windows HID 驱动程序开发的基础知识，涵盖以下关键方面：

🛠️ 设备创建: 创建过滤器设备对象，用于拦截和处理 HID 请求。

🔄 请求处理: 处理标准 HID IOCTL，例如 IOCTL_HID_GET_DEVICE_DESCRIPTOR、IOCTL_HID_GET_DEVICE_ATTRIBUTES、IOCTL_HID_GET_REPORT_DESCRIPTOR、IOCTL_HID_READ_REPORT、IOCTL_HID_WRITE_REPORT 等。

📜 报告描述符: 定义一个简单的 HID 报告描述符，用于模拟鼠标和键盘的输入/输出报告。

🗄️ 手动请求队列: 使用手动队列来模拟由输出报告触发的输入报告。

⌨️🖱️ 基础输入/输出报告模拟: 驱动程序模拟响应特定的输出报告来发送输入报告。 注意： 这仅为演示目的，并非真实 HID 过滤器驱动程序的典型行为。真实的过滤器驱动程序通常会过滤实际 HID 硬件的数据流。

❗ 重要提示: 本驱动程序 仅用于教育和演示目的。它 不 与任何物理 HID 硬件交互，而是在软件层面模拟 HID 行为。 ⚠️

⚙️ 编译
编译此驱动程序，您需要准备以下环境：

Windows 驱动程序工具包 (WDK): 请确保已安装与目标 Windows 版本兼容的 WDK 版本。 ✅

Visual Studio: 使用集成了 WDK 的 Visual Studio 来构建驱动程序项目。 ✅

编译步骤:

以管理员身份打开 “适用于 VS XXXX 的开发者命令提示符” (XXXX 代表您的 Visual Studio 版本). 🚀

导航到包含驱动程序源代码的目录。 📂

使用 msbuild 命令构建驱动程序项目。 例如:

msbuild hidriver.vcxproj /p:Configuration="Debug" /p:Platform="x64"
Use code with caution.
Cmd
将 hidriver.vcxproj 替换为您的实际项目文件名 (如果不同)。

Debug 替换为 Release 以构建发布版本。

x64 替换为您需要的目标平台 (Win32, ARM64 等).

编译成功后，驱动程序文件 (hidriver.sys, mshidkmdf.sys, hidriver.inf, hidriver.cat) 将位于输出目录 (例如 x64\Debug 或 x64\Release) 中。 🎉

💾 安装
安装前准备:

⚠️ 禁用驱动程序签名强制 (测试环境): 为了测试，您可能需要在 Windows 中禁用驱动程序签名强制。 🚨 强烈不建议在生产环境中使用！ 请参考 Microsoft 文档了解如何操作 (通常通过启动选项)。

安装步骤:

找到编译输出目录中的驱动程序文件 (hidriver.sys, mshidkmdf.sys, hidriver.inf, hidriver.cat). 🔍

打开 设备管理器。 🖱️

在设备管理器中，右键单击树顶部的计算机名称，选择 “添加过时硬件”。 ➕

点击 “下一步”。 ➡️

选择 “安装我手动从列表选择的硬件(高级)”，点击 “下一步”。 ➡️

选择 “显示所有设备”，点击 “下一步”。 ➡️

点击 “从磁盘安装...”。 💿

点击 “浏览...”，导航到驱动程序文件目录。 选择 hidriver.inf 文件，点击 “打开”。 📂

在 “从磁盘安装” 窗口中点击 “确定”。 ✅

选择设备 (设备名称由 version.inf 中的 DeviceName 定义，默认是 “VARIABLE_1”)，点击 “下一步”。 ➡️

按照屏幕提示完成驱动程序安装。 ⚠️ 如果启用了驱动程序签名强制，可能会出现安全警告。 🛡️

安装成功后，驱动程序应该会作为 HID 类下的过滤器驱动程序加载。 ✅

🎮 使用方法 / 调用方式
❗ 重要提示: 由于是 过滤器驱动程序，应用程序 不会直接调用此驱动程序的功能。 它会拦截和处理原本发往底层 HID 设备的请求。 🚫

如何与示例驱动程序交互 (演示目的):

确保已安装驱动程序。 ✅

发送输出报告 (模拟输入): 要触发驱动程序发送模拟输入报告，需要使用标准 HID API (例如 HidD_SetOutputReport 或带有 IOCTL 的 WriteFile) 向驱动程序发送 输出报告。 📤

输出报告 ID:

REPORT_ID_MOUSE_OUTPUT (0x02): 发送此 ID 的输出报告会触发驱动程序生成并发送 模拟鼠标输入报告。 🖱️

REPORT_ID_KEYBOARD_OUTPUT (0x04): 发送此 ID 的输出报告会触发驱动程序生成并发送 模拟键盘输入报告。 ⌨️

示例 (概念性): 假设要模拟鼠标输入。 构建一个输出报告缓冲区，包含：

第一个字节: REPORT_ID_MOUSE_OUTPUT (0x02)

后续字节: 输出报告数据 (本例中输出报告内容未在模拟逻辑中使用).

使用 HID API (例如用户模式下的 HidD_SetOutputReport 或内核模式下带有 IOCTL_HID_SET_OUTPUT_REPORT 的 WdfRequestSend) 将此输出报告发送到驱动程序。

接收输入报告 (间接方式): 发送输出报告 (例如 REPORT_ID_MOUSE_OUTPUT) 后，驱动程序内部处理并生成相应的 输入报告 (例如 REPORT_ID_MOUSE_INPUT)。 这些模拟的输入报告将提供给正在从该驱动程序过滤的 HID 设备堆栈读取输入报告的应用程序。 📥

应用程序通常使用 HID API，如 HidD_GetInputReport 或带有 IOCTL_HID_GET_INPUT_REPORT 的 ReadFile 来接收输入报告。

总结： 要 触发 本示例驱动程序的输入报告，需要向其 发送带有特定报告 ID 的输出报告。 💡 这是一种简化的演示方式，并非真实 HID 过滤器驱动程序的典型用法。

🛠️ 自定义
您可以修改以下内容来自定义驱动程序：

供应商 ID, 产品 ID, 版本号: 位于 device.h (VENDOR_ID, PRODUCT_ID, VERSION_NUMBER) 中。 根据需要修改。 ⚙️

字符串资源: 修改 version.inf 的 [Strings] 部分，更改设备管理器中显示的设备名称、制造商名称、服务名称等。 🏷️

报告描述符: device.c 中的 g_reportDescriptor 数组定义了 HID 报告描述符。 您可以修改此数组以更改模拟 HID 设备的功能 (例如添加更多按钮、轴、按键等)。 需要了解 HID 报告描述符语法才能进行有效修改。 📜

输入/输出报告模拟逻辑: queue_manual.c 中的 QueueManualSendReport 函数包含基于输出报告模拟输入报告的逻辑。 您可以修改此函数以更改模拟行为。 ✍️

⚠️ 免责声明
本示例驱动程序 “按原样” 提供，仅用于 教育和演示目的。 不适用于生产环境，可能不够健壮或功能不完善。 使用风险自负。 在关键系统上部署驱动程序前，请务必在受控环境中进行 充分测试。 🧪
