<div align="center">

# 🔍 JitViewer

**跨框架文档预览 SDK**

一次集成，随处预览 —— 支持 Vue3、React、原生 HTML

[![npm version](https://img.shields.io/npm/v/jit-viewer.svg)](https://www.npmjs.com/package/jit-viewer)
[![npm downloads](https://img.shields.io/npm/dm/jit-viewer.svg)](https://www.npmjs.com/package/jit-viewer)
[![License](https://img.shields.io/npm/l/jit-viewer.svg)](https://github.com/jitOffice/jit-viewer-sdk/blob/main/LICENSE)

[在线演示](https://jitword.com/jit-viewer.html) · [文档](https://jitword.com/jit-viewer.html) · [GitHub](https://github.com/jitOffice/jit-viewer-sdk)

</div>

---

## ✨ 特性

- 📄 **多格式支持** - PDF、DOCX、XLSX、PPTX、OFD、TXT、Markdown
- 🔧 **跨框架兼容** - Vue3、React、原生 HTML 无缝切换
- 🎨 **内置工具栏** - 缩放、旋转、分页、打印、下载开箱即用
- 🌓 **主题系统** - 浅色/深色主题，支持自定义配色
- 🌐 **国际化** - 中文、英文内置，可扩展更多语言
- 📦 **零依赖** - 打包所有依赖，无需额外安装
- 🚀 **轻量高效** - 按需加载，性能优化

## 📦 安装

```bash
# npm
npm install jit-viewer

# yarn
yarn add jit-viewer

# pnpm
pnpm add jit-viewer
```

## 🚀 快速开始

### Vue3

```vue
<template>
  <Viewer
    :file="file"
    theme="light"
    :toolbar="true"
    width="100%"
    height="600px"
    @ready="onReady"
    @load="onLoad"
    @error="onError"
  />
</template>

<script setup>
import { Viewer } from 'jit-viewer'
import 'jit-viewer/style.css'

const file = 'https://example.com/document.pdf'

function onReady() { console.log('Viewer ready') }
function onLoad() { console.log('File loaded') }
function onError(error) { console.error('Error:', error) }
</script>
```

### React

```tsx
import { useEffect, useRef, useState } from 'react'
import { createViewer, type ViewerInstance } from 'jit-viewer'
import 'jit-viewer/style.css'

function DocumentViewer() {
  const containerRef = useRef<HTMLDivElement>(null)
  const [viewer, setViewer] = useState<ViewerInstance | null>(null)

  useEffect(() => {
    if (containerRef.current) {
      const instance = createViewer({
        target: containerRef.current,
        theme: 'light',
        toolbar: true
      })
      instance.mount()
      setViewer(instance)
    }
    return () => viewer?.destroy()
  }, [])

  const handleFileChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0]
    if (file && viewer) {
      viewer.setFile(file, file.name)
    }
  }

  return (
    <div>
      <input type="file" onChange={handleFileChange} />
      <div ref={containerRef} style={{ width: '100%', height: '600px' }} />
    </div>
  )
}
```

### 原生 HTML (IIFE)

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="https://unpkg.com/jit-viewer/dist/iife/jit-viewer.min.css">
</head>
<body>
  <div id="viewer"></div>
  <input type="file" id="fileInput">
  
  <script src="https://unpkg.com/jit-viewer/dist/iife/jit-viewer.min.js"></script>
  <script>
    const { createViewer } = JitViewer;
    
    const viewer = createViewer({
      target: '#viewer',
      theme: 'light',
      toolbar: true
    });
    viewer.mount();
    
    document.getElementById('fileInput').addEventListener('change', (e) => {
      const file = e.target.files[0];
      if (file) viewer.setFile(file, file.name);
    });
  </script>
</body>
</html>
```

## 📖 API 文档

### createViewer(options)

创建预览器实例。

#### Options

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| target | `HTMLElement \| string` | - | 挂载目标元素或选择器 |
| file | `FileSource` | - | 文件源（URL、File对象、Blob、ArrayBuffer） |
| type | `FileType` | 自动检测 | 文件类型 |
| filename | `string` | - | 文件名 |
| toolbar | `boolean \| ToolbarConfig` | `true` | 工具栏配置 |
| theme | `'light' \| 'dark' \| ThemeConfig` | `'light'` | 主题 |
| locale | `'zh-CN' \| 'en'` | `'zh-CN'` | 语言 |
| width | `string \| number` | `'100%'` | 宽度 |
| height | `string \| number` | `'100%'` | 高度 |
| onReady | `() => void` | - | 准备就绪回调 |
| onLoad | `() => void` | - | 文件加载完成回调 |
| onError | `(error: Error) => void` | - | 错误回调 |
| onDestroy | `() => void` | - | 销毁回调 |

### ViewerInstance 方法

| 方法 | 说明 |
|------|------|
| `mount(target?)` | 挂载到指定元素 |
| `destroy()` | 销毁实例 |
| `setFile(file, filename?)` | 设置文件 |
| `getFile()` | 获取当前文件信息 |
| `zoom(scale)` | 设置缩放比例 |
| `rotate(degree)` | 旋转角度 |
| `reset()` | 重置缩放和旋转 |
| `fullscreen(enable?)` | 全屏切换 |
| `prevPage()` | 上一页 |
| `nextPage()` | 下一页 |
| `gotoPage(page)` | 跳转到指定页 |
| `getPageInfo()` | 获取分页信息 |
| `print()` | 打印 |
| `download()` | 下载文件 |
| `setTheme(theme)` | 设置主题 |
| `setLocale(locale)` | 设置语言 |
| `setToolbar(config)` | 设置工具栏 |
| `on(event, handler)` | 监听事件 |
| `off(event, handler)` | 取消监听 |
| `getState()` | 获取当前状态 |

### 支持的文件格式

| 格式 | 扩展名 | MIME Type |
|------|--------|----------|
| PDF | `.pdf` | `application/pdf` |
| Word | `.docx` | `application/vnd.openxmlformats-officedocument.wordprocessingml.document` |
| Excel | `.xlsx`, `.xls` | `application/vnd.openxmlformats-officedocument.spreadsheetml.sheet` |
| PowerPoint | `.pptx`, `.ppt` | `application/vnd.openxmlformats-officedocument.presentationml.presentation` |
| OFD | `.ofd` | `application/ofd` |
| Text | `.txt` | `text/plain` |
| Markdown | `.md`, `.markdown` | `text/markdown` |

## 📁 项目结构

```
jit-viewer/
├── packages/
│   └── core/              # 核心 SDK
│       ├── src/           # 源代码
│       └── dist/          # 构建产物
│           ├── index.js   # ESM 格式
│           ├── index.cjs  # CommonJS 格式
│           └── iife/      # IIFE 格式（浏览器直引）
├── demo/
│   ├── html-api-docs/     # HTML Demo + API 文档
│   ├── vue3/              # Vue3 Demo
│   └── react/             # React Demo
└── docs/                  # VitePress 文档
```

## 🛠️ 开发

```bash
# 克隆项目
git clone https://github.com/jitOffice/jit-viewer-sdk.git
cd jit-viewer-sdk

# 安装依赖
pnpm install

# 构建 SDK
cd packages/core && pnpm build:all

# 运行 Vue3 Demo
cd demo/vue3 && pnpm dev

# 运行 React Demo
cd demo/react && pnpm dev

# 运行 HTML Demo
cd demo/html-api-docs && python3 -m http.server 3000
```

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

## 如何联系

wechat：cxzk_168

## 📄 许可证

[Apache-2.0](LICENSE) © JitOffice

---

<div align="center">

**[⬆ 返回顶部](#-jitviewer)**

Made with ❤️ by [JitOffice](https://github.com/jitOffice)

</div>
