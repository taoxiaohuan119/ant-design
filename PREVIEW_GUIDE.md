# 预览代码指南

## 如何预览拍照批改作文组件

### 方法一：启动 Ant Design 文档站点（推荐）

1. **安装依赖**

   ```bash
   npm install
   # 或
   yarn install
   ```

2. **启动开发服务器**

   ```bash
   npm start
   # 或
   yarn start
   ```

3. **访问组件页面**
   - 开发服务器启动后，通常会在 `http://localhost:8001` 或类似端口运行
   - 在浏览器中访问：`http://localhost:8001/components/upload-cn/`
   - 在页面中找到 "拍照批改作文" 的 demo 示例
   - 或者直接访问：`http://localhost:8001/components/upload-cn/#components-upload-demo-essay-correction`

### 方法二：使用 CodeSandbox / StackBlitz（在线预览）

1. 复制 `components/upload/demo/essay-correction.md` 中的代码
2. 在 CodeSandbox 或 StackBlitz 中创建一个新的 React 项目
3. 安装 antd 依赖：`npm install antd`
4. 将代码粘贴到组件文件中
5. 运行项目即可预览

### 方法三：创建独立的 HTML 文件（快速预览）

创建一个简单的 HTML 文件来快速预览：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>拍照批改作文预览</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/antd@3.26.20/dist/antd.css" />
  </head>
  <body>
    <div id="root"></div>
    <script src="https://unpkg.com/react@16/umd/react.development.js"></script>
    <script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js"></script>
    <script src="https://unpkg.com/antd@3.26.20/dist/antd.js"></script>
    <script>
      // 将 essay-correction.md 中的 JSX 代码转换为浏览器可运行的代码
      // 注意：需要将 JSX 转换为 React.createElement 调用，或使用 Babel 在线编译
    </script>
  </body>
</html>
```

### 文件位置

组件 demo 文件位于：

```
/workspace/components/upload/demo/essay-correction.md
```

### 注意事项

1. **移动端预览**：由于使用了 `capture="environment"` 属性，建议在移动设备或浏览器的移动模式下预览，以测试相机功能
2. **上传接口**：当前使用的是 mock 接口 `https://www.mocky.io/v2/5cc8019d300000980a055e76`，实际使用时需要替换为真实的上传接口
3. **依赖版本**：确保使用的 Ant Design 版本是 3.x（当前项目是 3.26.20）

### 快速启动命令

```bash
# 一键安装并启动
npm install && npm start
```

启动成功后，打开浏览器访问显示的本地地址即可。
