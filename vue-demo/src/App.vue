<template>
  <div class="app">
    <header class="header">
      <div class="header-content">
        <div class="header-title">
          <h1>JitViewer 文档预览 Demo</h1>
          <p>支持 DOCX, XLSX, PDF, PPTX, TXT, Markdown, OFD, HTML, 图片格式</p>
        </div>
        <div class="header-actions">
          <a href="https://jitword.com" target="_blank" class="btn-office">体验在线Office编辑</a>
          <a href="https://know.jitword.com" target="_blank" class="btn-ai">AI知识库</a>
        </div>
      </div>
    </header>
    
    <main class="main">
      <div class="sidebar">
        <div class="panel">
          <h3>选择文件</h3>
          <input 
            type="file" 
            id="fileInput"
            accept=".docx,.xlsx,.xls,.pdf,.pptx,.ppt,.txt,.md,.ofd,.html,.htm,.jpg,.jpeg,.png,.gif,.webp,.svg,.bmp,.tiff,.tif,.ico"
            @change="handleFileChange"
          />
        </div>
        
        <div class="panel">
          <h3>或输入URL</h3>
          <input 
            v-model="urlInput"
            type="text"
            placeholder="输入文件URL"
          />
          <button class="btn-primary" @click="loadUrl">加载</button>
        </div>
        
        <div class="panel" v-if="fileInfo">
          <h3>文件信息</h3>
          <p><strong>文件名:</strong> {{ fileInfo.name }}</p>
          <p><strong>类型:</strong> {{ fileInfo.type }}</p>
        </div>
        
        <div class="panel">
          <h3>配置选项</h3>
          <div class="form-group">
            <label>主题:</label>
            <div class="btn-group">
              <button 
                :class="{ active: theme === 'light' }" 
                @click="setTheme('light')"
              >浅色</button>
              <button 
                :class="{ active: theme === 'dark' }" 
                @click="setTheme('dark')"
              >深色</button>
            </div>
          </div>
          
          <div class="form-group">
            <label>语言:</label>
            <div class="btn-group">
              <button 
                :class="{ active: locale === 'zh-CN' }" 
                @click="setLocale('zh-CN')"
              >中文</button>
              <button 
                :class="{ active: locale === 'en' }" 
                @click="setLocale('en')"
              >English</button>
            </div>
          </div>
          <div class="form-group">
            <label>PDF 渲染方式:</label>
            <div class="btn-group">
              <button
                :class="{ active: pdfRenderMode === 'inset' }"
                @click="setPdfRender('inset')"
              >内置渲染</button>
              <button
                :class="{ active: pdfRenderMode === 'native' }"
                @click="setPdfRender('native')"
              >浏览器原生</button>
            </div>
          </div>
          <div class="form-group">
            <label>工具栏:</label>
            <div class="btn-group">
              <button
                :class="{ active: showToolbar }"
                @click="toggleToolbar(true)"
              >显示</button>
              <button
                :class="{ active: !showToolbar }"
                @click="toggleToolbar(false)"
              >隐藏</button>
            </div>
          </div>
        </div>
        
        <div class="panel">
          <h3>操作控制</h3>
          <div class="btn-group vertical">
            <button @click="zoomIn">放大</button>
            <button @click="zoomOut">缩小</button>
            <button @click="reset">重置</button>
            <button @click="print">打印</button>
            <button @click="download">下载</button>
          </div>
        </div>
        
        <div class="panel">
          <h3>示例文件</h3>
          <div class="demo-links">
            <button @click="loadDemo('docx')">DOCX示例</button>
            <button @click="loadDemo('pdf')">PDF示例</button>
            <button @click="loadDemo('excel')">Excel示例</button>
          </div>
        </div>
      </div>
      
      <div class="content">
        <div ref="viewerContainer" class="viewer-wrapper"></div>
        <div v-if="!currentFile" class="empty-state">
          <p>请选择文件或输入URL开始预览</p>
          <button class="btn-primary" @click="initViewer">初始化预览器</button>
        </div>
      </div>
    </main>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue'
import { createViewer } from 'jit-viewer'
import type { ViewerInstance, FileSource, Theme, Locale } from 'jit-viewer'

// Viewer 实例
const viewerInstance = ref<ViewerInstance | null>(null)
const viewerContainer = ref<HTMLElement | null>(null)

// 状态
const currentFile = ref<FileSource | null>(null)
const fileInfo = ref<{ name: string; type: string } | null>(null)
const urlInput = ref('')
const theme = ref<Theme>('light')
const locale = ref<Locale>('zh-CN')
const pdfRenderMode = ref<'native' | 'inset'>('inset')
const showToolbar = ref(true)

// 示例文件URL
const demoFiles: Record<string, string> = {
  docx: 'http://static.jitword.com/test6.docx',
  pdf: 'http://static.jitword.com/test.pdf',
  excel: 'http://static.jitword.com/demo/excel.xlsx'
}

// 初始化 Viewer
function initViewer() {
  if (!viewerContainer.value) return
  
  // 如果已存在，先销毁
  if (viewerInstance.value) {
    viewerInstance.value.destroy()
  }
  
  // 创建新的 Viewer 实例
  viewerInstance.value = createViewer({
    target: viewerContainer.value,
    file: currentFile.value || undefined,
    theme: theme.value,
    locale: locale.value,
    pdfRender: pdfRenderMode.value,
    toolbar: showToolbar.value,
    width: '100%',
    height: '600px',
    onReady: () => {
      console.log('Viewer ready')
    },
    onLoad: () => {
      console.log('File loaded')
    },
    onError: (error) => {
      console.error('Load error:', error)
      alert('加载失败: ' + error.message)
    }
  })
  
  viewerInstance.value.mount()
}

// 处理文件选择
function handleFileChange(e: Event) {
  const target = e.target as HTMLInputElement
  const file = target.files?.[0]
  if (!file) return
  
  currentFile.value = file
  fileInfo.value = {
    name: file.name,
    type: file.type || 'unknown'
  }
  
  // 重新初始化以加载新文件
  setTimeout(initViewer, 0)
}

// 加载URL
function loadUrl() {
  if (!urlInput.value.trim()) {
    alert('请输入URL')
    return
  }
  
  const url = urlInput.value.trim()
  const filename = url.split('/').pop() || 'unknown'
  
  currentFile.value = url
  fileInfo.value = {
    name: filename,
    type: 'url'
  }
  
  // 重新初始化以加载新文件
  setTimeout(initViewer, 0)
}

// 加载示例文件
function loadDemo(type: string) {
  const url = demoFiles[type]
  if (!url) {
    alert('没有该类型的示例文件')
    return
  }
  
  currentFile.value = url
  fileInfo.value = {
    name: `示例${type.toUpperCase()}文件`,
    type: 'demo'
  }
  
  // 重新初始化以加载新文件
  setTimeout(initViewer, 0)
}

// 设置主题
function setTheme(t: Theme) {
  theme.value = t
  viewerInstance.value?.setTheme(t)
}

// 设置语言
function setLocale(l: Locale) {
  locale.value = l
  viewerInstance.value?.setLocale(l)
}

function setPdfRender(mode: 'native' | 'inset') {
  pdfRenderMode.value = mode
  // 重新初始化以应用新的渲染方式
  setTimeout(initViewer, 0)
}

function toggleToolbar(show: boolean) {
  showToolbar.value = show
  // 重新初始化以应用新的工具栏设置
  setTimeout(initViewer, 0)
}

// 放大
function zoomIn() {
  const state = viewerInstance.value?.getState()
  if (state) {
    viewerInstance.value?.zoom(state.zoom + 0.1)
  }
}

// 缩小
function zoomOut() {
  const state = viewerInstance.value?.getState()
  if (state) {
    viewerInstance.value?.zoom(state.zoom - 0.1)
  }
}

// 重置
function reset() {
  viewerInstance.value?.reset()
}

// 打印
function print() {
  viewerInstance.value?.print()
}

// 下载
async function download() {
  await viewerInstance.value?.download()
}

// 生命周期
onMounted(() => {
  // 可以在这里自动初始化
})

onUnmounted(() => {
  if (viewerInstance.value) {
    viewerInstance.value.destroy()
  }
})
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: #f5f5f5;
}

.app {
  min-height: 100vh;
}

.header {
  background: #1677ff;
  color: white;
  padding: 20px;
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  max-width: 1400px;
  margin: 0 auto;
}

.header-title {
  text-align: left;
}

.header h1 {
  margin-bottom: 8px;
}

.header p {
  opacity: 0.9;
  font-size: 14px;
}

.header-actions {
  display: flex;
  gap: 12px;
}

.header-actions .btn-office {
  display: inline-flex;
  align-items: center;
  padding: 10px 20px;
  background: #ffffff;
  color: #1677ff;
  text-decoration: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 500;
  transition: all 0.3s;
  box-shadow: 0 2px 8px rgba(0,0,0,0.15);
}

.header-actions .btn-office:hover {
  background: #f0f7ff;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(0,0,0,0.2);
}

.header-actions .btn-ai {
  display: inline-flex;
  align-items: center;
  padding: 10px 20px;
  background: linear-gradient(135deg, #1677ff 0%, #4096ff 100%);
  color: white;
  text-decoration: none;
  border-radius: 6px;
  font-size: 14px;
  font-weight: 500;
  transition: all 0.3s;
  box-shadow: 0 2px 8px rgba(22, 119, 255, 0.3);
}

.header-actions .btn-ai:hover {
  background: linear-gradient(135deg, #4096ff 0%, #69b1ff 100%);
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(22, 119, 255, 0.4);
}

.main {
  display: flex;
  max-width: 1400px;
  margin: 20px auto;
  gap: 20px;
  padding: 0 20px;
}

.sidebar {
  width: 280px;
  flex-shrink: 0;
}

.panel {
  background: white;
  border-radius: 8px;
  padding: 16px;
  margin-bottom: 16px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
}

.panel h3 {
  font-size: 14px;
  color: #333;
  margin-bottom: 12px;
  padding-bottom: 8px;
  border-bottom: 1px solid #f0f0f0;
}

.panel input[type="file"],
.panel input[type="text"] {
  width: 100%;
  padding: 8px;
  border: 1px solid #d9d9d9;
  border-radius: 4px;
  margin-bottom: 8px;
}

.btn-primary {
  width: 100%;
  padding: 8px 16px;
  background: #1677ff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.btn-primary:hover {
  background: #4096ff;
}

.form-group {
  margin-bottom: 12px;
}

.form-group label {
  display: block;
  font-size: 13px;
  color: #666;
  margin-bottom: 6px;
}

.btn-group {
  display: flex;
  gap: 8px;
}

.btn-group button {
  flex: 1;
  padding: 6px 12px;
  border: 1px solid #d9d9d9;
  background: #fff;
  border-radius: 4px;
  cursor: pointer;
  font-size: 13px;
}

.btn-group button:hover {
  border-color: #1677ff;
  color: #1677ff;
}

.btn-group button.active {
  background: #1677ff;
  color: white;
  border-color: #1677ff;
}

.btn-group.vertical {
  flex-direction: column;
}

.btn-group.vertical button {
  width: 100%;
}

.demo-links {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.demo-links button {
  padding: 8px;
  background: #f5f5f5;
  border: 1px solid #d9d9d9;
  border-radius: 4px;
  cursor: pointer;
}

.demo-links button:hover {
  background: #e6e6e6;
}

.content {
  flex: 1;
  background: white;
  border-radius: 8px;
  min-height: 600px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.08);
  overflow: hidden;
  position: relative;
}

.viewer-wrapper {
  width: 100%;
  height: 600px;
}

.empty-state {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  color: #999;
  gap: 16px;
}

.empty-state p {
  font-size: 16px;
}
</style>
