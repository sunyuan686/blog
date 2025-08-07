# [macOS 下修复 Holmes 书签搜索插件：Chrome Manifest V3 兼容教程](https://github.com/sunyuan686/blog/issues/26)



在新版 Chrome 中恢复被禁用的 Holmes 插件（Manifest V3 兼容）【Mac 教程】

> 原插件名：[**Holmes**](https://chromewebstore.google.com/detail/holmes/gokficnebmomagijbakglkcmhdbchbhn)
>  插件功能：**在 Chrome 地址栏中快速搜索书签** Chrome Bookmark Search Extension
>  插件版本：**3.3.1**
>  错误提示：`This extension was turned off because it is no longer supported`
>  适用用户：插件被禁用，但希望继续使用的 Chrome 用户（macOS）

<p>
<img width="404" height="203" alt="Image" src="https://github.com/user-attachments/assets/db682b23-3f20-48f8-8c87-15ecf2a06267" />

------

## 📌 背景介绍

2025 年起，Google Chrome **全面弃用 Manifest V2 插件**，许多经典插件如 **Holmes** 被强制禁用，即使你在开发者模式中加载 `.crx` 或源码，也会提示不再支持。

------

## 🎯 本文目标

- 🛠️ 教你如何找到 Holmes 插件的本地源码
- 🧱 将插件升级为 Manifest V3
- 🔧 手动修复 `manifest.json` 及必要文件
- ✅ 成功加载并使用插件（兼容 Chrome 新版本）

------

## 🖥️ 环境信息

- 系统：macOS
- 浏览器：Chrome Version 138.0.7204.184 (Official Build) (arm64)（禁用 Manifest V2 插件）
- 插件状态：被禁用，不再支持

------

## ✅ 操作步骤

### 第一步：找到 Holmes 插件本地文件

1. 打开 Finder，按下快捷键：

   ```
   Command + Shift + G
   ```

2. 输入路径并回车：

   ```
   ~/Library/Application Support/Google/Chrome/Default/Extensions/
   ```

3. 找到 Holmes 插件的 ID 文件夹（Tab栏搜索: chrome://extensions/ 打开extensions页面）：

找到Holmes对应的　ID,如

<img width="1208" height="900" alt="Image" src="https://github.com/user-attachments/assets/52eb58e8-596a-4736-816d-6f7f2cbb3cfa" />

   ```
   gokficnebmomagijbakglkcmhdbchbhn
   ```

4. 打开 `3.3.1` 子目录：

   ```
   gokficnebmomagijbakglkcmhdbchbhn/3.3.1
   ```

5. 将该文件夹复制到桌面或任意可写目录，如：

   ```
   ~/Desktop/Holmes/
   ```

------

### 第二步：重写 manifest.json 为 Manifest V3

将原来的 `manifest.json` 替换为以下内容：

```json
{
  "manifest_version": 3,
  "name": "Holmes",
  "version": "3.3.1",
  "description": "Chrome Bookmark Search Extension",
  "permissions": ["bookmarks", "tabs", "storage"],
  "action": {
    "default_icon": {
      "16": "images/icon_19.png",
      "48": "images/icon_48.png",
      "128": "images/icon_128.png"
    },
    "default_title": "Holmes",
    "default_popup": "holmes.html"
  },
  "omnibox": {
    "keyword": "*"
  },
  "icons": {
    "16": "images/icon_19.png",
    "48": "images/icon_48.png",
    "128": "images/icon_128.png"
  },
  "commands": {
    "_execute_action": {
      "suggested_key": {
        "default": "Alt+Shift+H"
      },
      "description": "Trigger Holmes popup"
    }
  },
  "host_permissions": ["chrome://favicon/"],
  "content_security_policy": {
    "extension_pages": "script-src 'self'; object-src 'self'"
  }
}
```


------

### 第三步：加载插件

1. 打开 Chrome 地址栏，访问：

   ```
   chrome://extensions/
   ```

2. 开启右上角的 **开发者模式**

3. 点击「加载已解压的扩展程序」

4. 选择你刚刚修改好的 `~/Desktop/Holmes/3.3.1` 目录

5. 若未报错，即表示成功

------

## 🧪 插件测试方式

- 打开地址栏，输入：

  ```
  *空格（或者Tab键）要搜索的内容
  ```

- 插件会自动搜索你的书签内容，显示匹配结果

- 也可以点击扩展图标唤起 popup 页面（holmes.html）

------


------

## ✅ 成功复活插件

<img width="414" height="216" alt="Image" src="https://github.com/user-attachments/assets/46a0bc37-6aa5-4904-88a2-9f5fd6b51ea7" />

<img width="1187" height="214" alt="Image" src="https://github.com/user-attachments/assets/520efce5-588d-4457-bca8-0c158fe78fc2" />

------


------

## 🧠 总结

即便 Google 强制禁用了 Manifest V2 扩展，我们依然可以通过手动方式：

- ✅ 找回本地插件源码
- ✅ 升级 `manifest.json` 为 V3 格式
- ✅ 成功恢复插件功能
