# 知途Agent

> 个人知识体系梳理与学习规划的垂直 Agent

## 核心功能

1. **知识框架生成** - 输入学习目标，AI 自动生成三级结构化知识体系
2. **素材智能分析** - 上传学习素材（txt/pdf/md），AI 比对知识框架，识别已掌握/薄弱/缺口
3. **学习计划输出** - 按优先级排序（P0 必学/P1 加强/P2 精进），含建议资源和预估时间
4. **知识图谱可视化** - 力导向图展示知识结构，颜色编码掌握程度

## 技术栈

| 层级 | 技术 | 说明 |
|------|------|------|
| 前端 | Next.js 14 + Tailwind CSS 4 + Framer Motion | 极简三步流 UI |
| 后端 | Vercel Edge Functions | 冷启动快，流式响应 |
| AI | DeepSeek API (Chat + Embedding) | 框架生成 + 素材比对 |
| RAG | 向量化 + 余弦相似度检索 | 碎片素材分块→嵌入→比对 |
| 可视化 | SVG 力导向图 | 无额外依赖 |
| 部署 | Vercel Git 部署 | .env 管理 API Key |

## 快速开始

### 1. 安装依赖

```bash
npm install
```

### 2. 配置环境变量

```bash
cp .env.example .env.local
# 编辑 .env.local，填入你的 DeepSeek API Key
# 获取地址：https://platform.deepseek.com/api_keys
```

### 3. 启动开发服务器

```bash
npm run dev
```

访问 http://localhost:3000

### 4. 未配置 API Key 时

项目内置了模拟数据模式，未配置 `DEEPSEEK_API_KEY` 时会自动返回演示数据，可直接体验 UI 流程。

## 项目结构

```
知途Agent/
├── src/
│   ├── app/
│   │   ├── layout.tsx              # 根布局
│   │   ├── page.tsx                # 主页面（三步操作流）
│   │   ├── globals.css             # 全局样式
│   │   └── api/
│   │       ├── generate-framework/ # 知识框架生成 API
│   │       ├── analyze/            # 素材分析比对 API
│   │       ├── generate-plan/      # 学习计划生成 API
│   │       └── chat/               # 通用对话 API
│   ├── components/
│   │   ├── Header.tsx              # 顶部导航
│   │   ├── GoalInput.tsx           # 学习目标输入
│   │   ├── MaterialUpload.tsx      # 素材上传区
│   │   ├── KnowledgeTree.tsx       # 知识框架树展示
│   │   ├── AnalysisResult.tsx      # 比对分析结果
│   │   ├── LearningPlan.tsx        # 学习计划展示
│   │   └── KnowledgeGraph.tsx      # 知识图谱可视化
│   ├── lib/
│   │   ├── deepseek.ts             # DeepSeek API 封装
│   │   ├── rag.ts                  # RAG 向量化与检索
│   │   └── types.ts                # 完整类型定义
│   └── hooks/
│       └── useAnalysis.ts          # 分析流程 Hook
├── .env.example
├── package.json
├── next.config.ts
├── tailwind.config.ts
├── tsconfig.json
└── README.md
```

## 部署到 Vercel

### 1. 推送代码到 Git

```bash
git init
git add .
git commit -m "feat: init 知途Agent"
git remote add origin <your-repo-url>
git push -u origin main
```

### 2. 在 Vercel 导入项目

1. 访问 [vercel.com](https://vercel.com)
2. 点击 "Import Project"
3. 选择你的 Git 仓库
4. 配置环境变量：`DEEPSEEK_API_KEY`
5. 部署

### 3. 环境变量

| 变量名 | 必填 | 说明 |
|--------|------|------|
| `DEEPSEEK_API_KEY` | ✅ | DeepSeek API 密钥 |
| `DEEPSEEK_BASE_URL` | ❌ | API 代理地址（默认 https://api.deepseek.com） |

## API 接口

### POST /api/generate-framework
生成知识框架
- 请求：`{ goal: string }`
- 响应：`{ success: true, framework: KnowledgeFramework }`

### POST /api/analyze
素材分析比对
- 请求：`{ goal: string, framework: KnowledgeFramework, materials: Material[] }`
- 响应：`{ success: true, analysis: AnalysisResult }`

### POST /api/generate-plan
生成学习计划
- 请求：`{ framework: KnowledgeFramework, analysis: AnalysisResult }`
- 响应：`{ success: true, plan: LearningPlan }`

### POST /api/chat
通用对话（支持流式）
- 请求：`{ messages: ChatMessage[], stream?: boolean, context?: {...} }`
- 响应：`{ success: true, message: ChatMessage }` 或 SSE 流

## 数据安全

- API Key 仅存于服务端环境变量，前端无法访问
- 素材内容仅在分析请求时处理，不持久化存储
- RAG 向量索引使用内存 Map，请求结束后自动释放
- Edge Function 无状态，无数据泄露风险

## License

MIT
