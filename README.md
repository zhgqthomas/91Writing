# 📚 91AI小说生成器

> 一个基于 Vue 3 + Element Plus 的智能小说创作工具，集成多种 AI 模型，助力作者高效创作

[![Vue](https://img.shields.io/badge/Vue-3.3.8-4FC08D?style=flat-square&logo=vue.js)](https://vuejs.org/)
[![Element Plus](https://img.shields.io/badge/Element%20Plus-2.4.2-409EFF?style=flat-square&logo=element)](https://element-plus.org/)
[![Vite](https://img.shields.io/badge/Vite-4.5.0-646CFF?style=flat-square&logo=vite)](https://vitejs.dev/)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)

<img src=".\image\image-20250611102429512.png" />

## 🎉 演示

- https://xiezuo.91hub.vip

## ✨ 项目特色

- 🤖 **AI 智能创作** - 集成多种 AI 模型，支持大纲生成、章节续写、人物创建
- 📝 **富文本编辑** - 基于 WangEditor 的专业写作环境
- 🎨 **模板系统** - 内置 7 种小说类型模板，支持自定义模板
- 👥 **人物管理** - 智能人物档案管理，AI 辅助角色设定
- 🌍 **世界观构建** - 系统化的世界观设定工具
- 📊 **写作统计** - 实时字数统计、阅读时间估算
- 💾 **数据管理** - 本地存储，支持导入导出
- 🎯 **语料库** - 个人写作素材库管理

## 🚀 功能亮点

### 🎭 多样化模板支持
- **玄幻小说** - 修仙异世界，宏大叙事
- **都市言情** - 现代爱情，细腻温馨
- **悬疑推理** - 烧脑情节，逻辑严密
- **科幻未来** - 科技想象，未来探索
- **历史穿越** - 古韵悠长，历史厚重
- **校园青春** - 青春活力，纯真美好
- **武侠江湖** - 豪情万丈，侠义精神

### 🤖 AI 智能助手
- **大纲生成** - 根据关键词智能生成小说大纲
- **章节续写** - AI 辅助章节内容创作
- **人物生成** - 自动创建角色档案和性格特征
- **世界观构建** - AI 辅助世界观设定创建
- **智能对话** - 与 AI 实时交流创作灵感

### 📊 专业写作工具
- **实时统计** - 字数、段落、阅读时间统计
- **章节管理** - 分章节组织，便于管理长篇作品
- **富文本编辑** - 支持格式化、图片插入等
- **导入导出** - 支持多种格式的文档导入导出

## 🛠️ 技术栈

- **前端框架**: Vue 3.3.8 (Composition API)
- **UI 组件库**: Element Plus 2.4.2
- **状态管理**: Pinia 2.1.7
- **路由管理**: Vue Router 4.2.5
- **构建工具**: Vite 4.5.0
- **富文本编辑器**: WangEditor 5.1.23
- **HTTP 客户端**: Axios 1.6.0
- **代码高亮**: Highlight.js 11.9.0
- **Markdown 解析**: Marked 9.1.6

## 📦 快速开始

### 环境要求
- Node.js >= 16.0.0
- npm >= 8.0.0 或 pnpm >= 7.0.0

### 安装依赖
```bash
# 使用 npm
npm install

# 或使用 pnpm (推荐)
pnpm install
```

### 启动开发服务器
```bash
# 使用 npm
npm run dev

# 或使用 pnpm
pnpm dev
```

### 构建生产版本
```bash
# 使用 npm
npm run build

# 或使用 pnpm
pnpm build
```

## ⚙️ 配置说明

### API 配置
1. 点击右上角「API配置」按钮
2. 选择 AI 服务提供商（OpenAI、Claude、国产大模型等）
3. 填入 API Key 和相关配置
4. 测试连接确保配置正确

### 支持的 AI 服务
- OpenAI GPT 系列
- Anthropic Claude
- 国产大模型（DeepSeek、通义千问、文心一言等）
- 自定义 API 接口
- 推荐使用Gemini2.5pro、Claude3.7/4

## 📖 使用指南

### 1. 创建新小说
1. 选择合适的小说模板
2. 输入关键词和基本设定
3. 使用 AI 生成大纲
4. 开始章节创作

### 2. 人物管理
1. 点击「人物管理」添加角色
2. 填写人物基本信息
3. 使用 AI 生成人物特征
4. 在创作中引用人物设定

### 3. 世界观构建
1. 添加世界观设定
2. 描述世界规则和背景
3. 使用 AI 完善设定细节
4. 确保创作一致性

### 4. AI 辅助创作
1. 在编辑器中选择需要续写的位置
2. 点击「AI 续写」按钮
3. AI 会根据上下文生成内容
4. 选择满意的内容插入

## 🎯 项目亮点

### 🧠 智能化程度高
- 深度集成 AI 能力，覆盖创作全流程
- 上下文感知，生成内容更贴合故事发展
- 多模型支持，适应不同创作需求

### 🎨 用户体验优秀
- 现代化 UI 设计，操作直观简洁
- 响应式布局，支持多设备使用
- 实时保存，防止创作内容丢失

### 🔧 功能完整丰富
- 从构思到成文的完整创作链路
- 专业的写作辅助工具集
- 灵活的模板和设定系统

### 🚀 技术架构先进
- Vue 3 + Composition API，代码组织清晰
- Vite 构建，开发体验流畅
- 模块化设计，易于扩展维护

## 🤝 贡献指南

我们欢迎所有形式的贡献！

### 如何贡献
1. Fork 本仓库
2. 创建特性分支 (`git checkout -b feature/AmazingFeature`)
3. 提交更改 (`git commit -m 'Add some AmazingFeature'`)
4. 推送到分支 (`git push origin feature/AmazingFeature`)
5. 开启 Pull Request

### 贡献类型
- 🐛 Bug 修复
- ✨ 新功能开发
- 📝 文档改进
- 🎨 UI/UX 优化
- 🔧 代码重构
- 🌐 国际化支持

## 📄 开源协议

本项目基于 [MIT License](LICENSE) 开源协议。

## 🙏 致谢

感谢以下开源项目的支持：
- [Vue.js](https://vuejs.org/) - 渐进式 JavaScript 框架
- [Element Plus](https://element-plus.org/) - Vue 3 组件库
- [WangEditor](https://www.wangeditor.com/) - 富文本编辑器
- [Vite](https://vitejs.dev/) - 下一代前端构建工具

## 📞 联系我们

- 🐛 [提交 Issue](../../issues)
- 💡 [功能建议](../../discussions)
- 📧 邮箱：[1090879115@qq.com]
- 🐧 QQ群

<img src=".\image\qrcode_1749609318081.jpg" style="zoom: 25%;" />

---

⭐ 如果这个项目对你有帮助，请给我们一个 Star！

🚀 让 AI 助力你的创作之旅！