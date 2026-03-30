---
name: "screenshot-receiver"
description: "截图接收工具：弹出 Tkinter 窗口，用户通过 ⌘V 粘贴剪贴板截图或浏览文件，图片保存到临时目录供 AI 读取后自动清理。当用户说「给你截图」「贴图」「看截图」「传图片」时使用。"
inclusion: manual
---

# Screenshot Receiver

当用户需要给你传递截图或图片时，使用此 skill。

## 使用流程

1. 运行 Python 脚本弹出接收窗口：
   ```bash
   python3 ~/.kiro/skills/screenshot-receiver/receive.py
   ```

2. 脚本会弹出一个 Tkinter 窗口，用户可以：
   - **⌘V** 粘贴剪贴板中的截图（macOS）
   - **Browse** 按钮选择本地图片文件
   - 支持多次粘贴/选择
   - 点击 **Done** 或按 **Esc** 完成

3. 脚本退出后，stdout 输出图片路径（每行一个），如果没有图片则输出 `NO_IMAGES`

4. 用 `fs_read` 的 `Image` mode 读取图片路径

5. **读取完毕后，必须清理临时目录**：
   ```bash
   rm -rf /tmp/kiro-screenshot-*
   ```

## 注意事项

- 临时文件在 `/tmp/kiro-screenshot-*/` 下，读取后务必删除
- 粘贴功能依赖 macOS 的 `osascript`（AppleScript），仅支持 macOS
- 需要系统安装了 Python 3 + tkinter（macOS 自带）
