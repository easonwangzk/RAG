# 项目文件结构详解

## 📁 项目概览

```
project/
├── 核心代码 (2个Python文件)
│   ├── rag.py                     # 主RAG系统
│   └── eval_ragas.py              # 评估脚本
│
├── 配置文件 (4个)
│   ├── .env                       # 本地配置 (包含API密钥)
│   ├── .env.example              # 配置模板
│   ├── requirements.txt          # Python依赖
│   └── .gitignore               # Git忽略规则
│
├── 文档 (1个)
│   └── README.md                 # 项目文档
│
├── 数据文件 (15个，在data/目录)
│   ├── mastersprograminanalytics.pdf
│   └── page_*.html (14个HTML文件)
│
└── 自动生成 (运行时创建)
    ├── .chroma/                  # 向量数据库
    └── __pycache__/              # Python缓存
```

---

## 📄 核心代码文件

### 1. `rag.py` (18KB, ~600行)

**作用**: RAG系统的核心实现文件

**主要功能**:
- ✅ PDF和HTML文档解析
- ✅ Token-based文本分块 (600 tokens/chunk)
- ✅ 向量数据库构建和管理
- ✅ 查询扩展 (Query Expansion)
- ✅ 智能检索和去重
- ✅ LLM答案生成

**关键函数**:
```python
# 数据处理
extract_pdf_text_with_metadata()     # 提取PDF内容
extract_html_text_with_metadata()    # 提取HTML内容
create_documents_from_pdf()          # PDF转Document对象
create_documents_from_html()         # HTML转Document对象

# 向量数据库
load_or_build_vectordb()             # 加载或构建向量DB

# 检索优化
expand_query()                       # 查询扩展
retrieve_with_scores()               # 检索并计算相似度
deduplicate_and_merge_chunks()       # 去重和合并

# 答案生成
answer_question()                    # 完整RAG流程
```

**命令行使用**:
```bash
python rag.py "What are the core courses?"
python rag.py --rebuild "question"     # 重建向量DB
python rag.py -v "question"            # 详细输出
python rag.py --top_k 10 "question"    # 检索10个chunks
python rag.py --min_sim 0.15 "question" # 降低相似度阈值
```

**核心优化**:
1. **查询扩展**: 自动为"core courses"添加课程名称关键词
2. **按源文件去重**: 消除重复HTML页面 (page_00001和page_00012)
3. **Token-based分块**: 比字符级更精确
4. **相似度修复**: `sim = 1 - (dist/2)` 而非 `1 - dist`

---

### 2. `eval_ragas.py` (4.2KB, ~130行)

**作用**: 使用RAGAS框架评估RAG系统性能

**评估指标**:
- **Faithfulness** (忠实度): 答案是否基于检索到的内容
- **Answer Relevancy** (相关性): 答案是否回答了问题

**测试问题集**:
1. What are the core courses?
2. How long does the program typically take to complete?
3. Are there any capstone or practicum components?
4. Tell me about the Time Series Analysis course
5. What is the Machine Learning I course about?

**使用方式**:
```bash
python eval_ragas.py
```

**输出**:
- 每个问题的检索上下文
- 生成的答案
- Faithfulness和Answer Relevancy分数
- 总体评估报告

---

## ⚙️ 配置文件

### 3. `.env` (本地配置，不在Git中)

**作用**: 存储本地配置和API密钥

**内容**:
```env
# 数据源
PDF_PATH=data/mastersprograminanalytics.pdf
HTML_DIR=data

# 向量数据库
CHROMA_DIR=.chroma

# OpenAI模型
EMBED_MODEL=text-embedding-3-large
CHAT_MODEL=gpt-4o-mini

# 分块参数
CHUNK_TOKENS=600
OVERLAP_TOKENS=150

# 检索参数
TOP_K=5
MIN_SIM=0.20

# 生成参数
TEMPERATURE=0.2
MAX_TOKENS=800

# API密钥 (敏感信息!)
OPENAI_API_KEY=sk-proj-xxxxx...
```

**注意**: 
- ⚠️ 包含API密钥，已在 `.gitignore` 中排除
- 🔒 不要提交到Git仓库

---

### 4. `.env.example` (配置模板)

**作用**: 配置文件模板，提交到Git供其他人参考

**内容**: 与 `.env` 相同，但API密钥部分为:
```env
OPENAI_API_KEY=your-api-key-here
```

**使用方式**:
```bash
cp .env.example .env
# 编辑 .env 添加真实的API密钥
```

---

### 5. `requirements.txt` (Python依赖)

**作用**: 列出项目所需的Python包

**核心依赖**:
```
# OpenAI和Token处理
openai>=1.43.0
tiktoken>=0.7.0

# LangChain RAG框架
langchain>=0.3.2
langchain-community>=0.3.1
langchain-openai>=0.1.24

# 向量数据库
chromadb>=0.5.4

# 文档处理
pypdf>=5.0.0                # PDF解析
beautifulsoup4>=4.12.0      # HTML解析
lxml>=5.0.0                 # XML/HTML解析器

# 评估框架
ragas>=0.1.9
datasets>=2.20.0
pandas>=2.2.2
numpy>=2.1.2

# 工具
python-dotenv>=1.0.1        # 环境变量管理
```

**高级特性 (可选)**:
```
rank-bm25>=0.2.2            # BM25关键词检索
sentence-transformers>=2.2.2 # Cross-encoder重排序
```

**安装**:
```bash
pip install -r requirements.txt
```

---

### 6. `.gitignore` (Git忽略规则)

**作用**: 告诉Git哪些文件不需要提交

**主要内容**:
```
# Python
__pycache__/
*.pyc

# 环境变量 (包含API密钥)
.env

# 向量数据库 (太大，应本地生成)
.chroma/

# IDE
.vscode/
.idea/

# 系统文件
.DS_Store
```

---

## 📚 文档文件

### 7. `README.md` (6KB)

**作用**: 项目使用文档

**内容结构**:
1. **快速开始** - 3步开始使用
2. **系统性能** - 性能指标
3. **使用示例** - 各种查询示例
4. **配置说明** - 参数详解
5. **核心技术** - 技术栈介绍
6. **项目结构** - 文件说明
7. **RAGAS评估** - 评估方法
8. **技术栈** - 依赖说明
9. **性能指标** - 基准数据
10. **最佳实践** - 使用建议
11. **故障排查** - 常见问题

**快速查阅**:
```bash
# 查看README
cat README.md
# 或在GitHub上查看渲染后的版本
```

---

## 📦 数据文件 (data/目录)

### 8. `data/mastersprograminanalytics.pdf` (209KB)

**作用**: UChicago MS Applied Data Science项目的PDF手册

**内容**:
- 项目概述
- 课程列表和描述
- 录取要求
- 项目时长
- Capstone项目
- 课程代码 (如ADSP 31006)

**提取结果**: 20 chunks (8个页面)

**特点**:
- 官方文档
- 结构化内容
- 包含课程代码

---

### 9-22. `data/page_*.html` (14个HTML文件)

**作用**: UChicago Data Science网站的网页快照

**文件列表**:
```
page_00000.html (1 chunk)   - 首页
page_00001.html (29 chunks) - 课程详情
page_00002.html (31 chunks) - 课程详情
page_00003.html (1 chunk)   - 简短页面
page_00004.html (38 chunks) - 项目结构 ⭐ 最重要
page_00005.html (20 chunks) - FAQ
page_00006.html (6 chunks)  - 学生故事
page_00007.html (2 chunks)  - 简短页面
page_00008.html (3 chunks)  - 简短页面
page_00009.html (22 chunks) - 课程描述
page_00010.html (1 chunk)   - 简短页面
page_00011.html (2 chunks)  - Capstone信息
page_00012.html (29 chunks) - 课程详情 (与page_00001重复)
page_00013.html (2 chunks)  - 简短页面
```

**总计**: 187 chunks

**内容**:
- 详细的课程描述
- 项目时间线
- 学生故事
- FAQ
- Capstone项目信息

**处理方式**:
- 移除噪音: script, style, nav, footer (71.3%过滤率)
- 保留结构: h1-h5, p, li, td
- 提取元数据: title, description, URL

---

## 🗄️ 自动生成文件

### `.chroma/` (向量数据库目录)

**作用**: 存储文档的向量嵌入

**大小**: ~20-30MB

**内容**:
- 207个文档chunks的embeddings
- ChromaDB索引文件
- 元数据

**生成方式**:
```bash
python rag.py --rebuild "test"
```

**注意**:
- ⚠️ 已在 `.gitignore` 中排除
- 🔄 首次运行或数据变化时需要重建
- 🗑️ 删除后会自动重建

---

### `__pycache__/` (Python缓存)

**作用**: Python编译后的字节码缓存

**内容**:
- `rag.cpython-310.pyc`
- 自动生成的 `.pyc` 文件

**注意**:
- ⚠️ 已在 `.gitignore` 中排除
- 🔄 Python自动管理
- 🗑️ 可以安全删除

---

## 📊 文件大小统计

| 类型 | 文件数 | 总大小 |
|------|--------|--------|
| Python代码 | 2 | 22KB |
| 配置文件 | 4 | 2KB |
| 文档 | 1 | 6KB |
| PDF数据 | 1 | 209KB |
| HTML数据 | 14 | ~1.7MB |
| 向量数据库 | - | ~25MB |
| **总计** | 22+ | ~2MB (不含.chroma) |

---

## 🔑 关键文件说明

### 必须的文件 (6个)
1. ✅ `rag.py` - 核心代码
2. ✅ `eval_ragas.py` - 评估脚本
3. ✅ `requirements.txt` - 依赖列表
4. ✅ `.env` - 配置 (需本地创建)
5. ✅ `README.md` - 文档
6. ✅ `data/` - 数据目录

### 推荐的文件 (3个)
1. ✅ `.env.example` - 配置模板
2. ✅ `.gitignore` - Git规则
3. ✅ `FILE_STRUCTURE.md` - 本文档

### 自动生成 (2个)
1. 🔄 `.chroma/` - 向量数据库
2. 🔄 `__pycache__/` - Python缓存

---

## 🚀 快速开始流程

```bash
# 1. 安装依赖
pip install -r requirements.txt

# 2. 配置API密钥
cp .env.example .env
vim .env  # 添加OPENAI_API_KEY

# 3. 运行RAG系统
python rag.py "What are the core courses?"

# 4. 评估系统
python eval_ragas.py

# 5. 查看文档
cat README.md
```

---

## 💡 文件修改指南

### 想要修改检索参数？
编辑 `.env`:
```env
TOP_K=10          # 检索更多chunks
MIN_SIM=0.15      # 降低阈值
```

### 想要添加新的查询扩展规则？
编辑 `rag.py` 中的 `expand_query()` 函数

### 想要更换模型？
编辑 `.env`:
```env
EMBED_MODEL=text-embedding-3-small  # 更小的embedding
CHAT_MODEL=gpt-4o                   # 更强的LLM
```

### 想要添加新的数据源？
1. 放入 `data/` 目录
2. 如果是PDF，更新 `.env` 中的 `PDF_PATH`
3. 如果是HTML，确保在 `data/` 目录下
4. 运行 `python rag.py --rebuild "test"`

---

**文档生成时间**: 2025-11-05
**项目版本**: V2.0 (优化版)
