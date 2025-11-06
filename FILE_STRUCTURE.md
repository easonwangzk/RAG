# Project File Structure Documentation

## Project Overview

```
project/
├── Core Code (2 Python files)
│   ├── rag.py                     # Main RAG system
│   └── eval_ragas.py              # Evaluation script
│
├── Configuration Files (4 files)
│   ├── .env                       # Local configuration (contains API keys)
│   ├── .env.example              # Configuration template
│   ├── requirements.txt          # Python dependencies
│   └── .gitignore               # Git ignore rules
│
├── Documentation (2 files)
│   ├── README.md                 # Project documentation
│   └── FILE_STRUCTURE.md         # This file
│
├── Data Files (15 files in data/ directory)
│   ├── mastersprograminanalytics.pdf
│   └── page_*.html (14 HTML files)
│
└── Auto-generated (created at runtime)
    ├── .chroma/                  # Vector database
    └── __pycache__/              # Python cache
```

---

## Core Code Files

### 1. `rag.py` (20KB, ~633 lines)

**Purpose**: Core implementation file for the RAG system

**Main Features**:
- PDF and HTML document parsing
- Token-based text chunking (600 tokens/chunk)
- Vector database construction and management
- Query expansion for improved retrieval
- Smart retrieval and deduplication
- LLM answer generation

**Key Functions**:
```python
# Data processing
extract_pdf_text_with_metadata()     # Extract PDF content
extract_html_text_with_metadata()    # Extract HTML content
create_documents_from_pdf()          # PDF to Document objects
create_documents_from_html()         # HTML to Document objects

# Vector database
load_or_build_vectordb()             # Load or build vector DB

# Retrieval optimization
expand_query()                       # Query expansion
retrieve_with_scores()               # Retrieve and calculate similarity
deduplicate_and_merge_chunks()       # Deduplication and merging

# Answer generation
answer_question()                    # Complete RAG pipeline
```

**Command Line Usage**:
```bash
python rag.py "What are the core courses?"
python rag.py --rebuild "question"     # Rebuild vector DB
python rag.py -v "question"            # Verbose output
python rag.py --top_k 10 "question"    # Retrieve 10 chunks
python rag.py --min_sim 0.15 "question" # Lower similarity threshold
```

**Core Optimizations**:
1. **Query Expansion**: Automatically adds course name keywords for "core courses"
2. **Source-based Deduplication**: Eliminates duplicate HTML pages (page_00001 and page_00012)
3. **Token-based Chunking**: More precise than character-level
4. **Similarity Fix**: `sim = 1 - (dist/2)` instead of `1 - dist`

---

### 2. `eval_ragas.py` (4.3KB, ~136 lines)

**Purpose**: Evaluate RAG system performance using RAGAS framework

**Evaluation Metrics**:
- **Faithfulness**: Whether answer is based on retrieved content
- **Answer Relevancy**: Whether answer addresses the question

**Test Question Set**:
1. What are the core courses?
2. How long does the program typically take to complete?
3. Are there any capstone or practicum components?
4. Tell me about the Time Series Analysis course
5. What is the Machine Learning I course about?

**Usage**:
```bash
python eval_ragas.py
```

**Output**:
- Retrieved context for each question
- Generated answers
- Faithfulness and Answer Relevancy scores
- Overall evaluation report

---

## Configuration Files

### 3. `.env` (Local configuration, not in Git)

**Purpose**: Store local configuration and API keys

**Content**:
```env
# Data sources
PDF_PATH=data/mastersprograminanalytics.pdf
HTML_DIR=data

# Vector database
CHROMA_DIR=.chroma

# OpenAI models
EMBED_MODEL=text-embedding-3-large
CHAT_MODEL=gpt-4o-mini

# Chunking parameters
CHUNK_TOKENS=600
OVERLAP_TOKENS=150

# Retrieval parameters
TOP_K=5
MIN_SIM=0.20

# Generation parameters
TEMPERATURE=0.2
MAX_TOKENS=800

# API key (sensitive information!)
OPENAI_API_KEY=sk-proj-xxxxx...
```

**Note**:
- Contains API keys, excluded in `.gitignore`
- Do NOT commit to Git repository

---

### 4. `.env.example` (Configuration template)

**Purpose**: Configuration file template, committed to Git for reference

**Content**: Same as `.env`, but API key section is:
```env
OPENAI_API_KEY=your-api-key-here
```

**Usage**:
```bash
cp .env.example .env
# Edit .env to add real API key
```

---

### 5. `requirements.txt` (Python dependencies)

**Purpose**: List required Python packages

**Core Dependencies**:
```
# OpenAI and Token processing
openai>=1.43.0
tiktoken>=0.7.0

# LangChain RAG framework
langchain>=0.3.2
langchain-community>=0.3.1
langchain-openai>=0.1.24

# Vector database
chromadb>=0.5.4

# Document processing
pypdf>=5.0.0                # PDF parsing
beautifulsoup4>=4.12.0      # HTML parsing
lxml>=5.0.0                 # XML/HTML parser

# Evaluation framework
ragas>=0.1.9
datasets>=2.20.0
pandas>=2.2.2
numpy>=2.1.2

# Utilities
python-dotenv>=1.0.1        # Environment variable management
```

**Advanced Features (optional)**:
```
rank-bm25>=0.2.2            # BM25 keyword retrieval
sentence-transformers>=2.2.2 # Cross-encoder reranking
```

**Installation**:
```bash
pip install -r requirements.txt
```

---

### 6. `.gitignore` (Git ignore rules)

**Purpose**: Tell Git which files not to commit

**Main Content**:
```
# Python
__pycache__/
*.pyc

# Environment variables (contains API keys)
.env

# Vector database (too large, should be generated locally)
.chroma/

# IDE
.vscode/
.idea/

# System files
.DS_Store
```

---

## Documentation Files

### 7. `README.md` (6KB)

**Purpose**: Project usage documentation

**Content Structure**:
1. **Quick Start** - 3 steps to get started
2. **System Performance** - Performance metrics
3. **Usage Examples** - Various query examples
4. **Configuration** - Parameter explanation
5. **Core Technologies** - Technology stack introduction
6. **Project Structure** - File descriptions
7. **RAGAS Evaluation** - Evaluation methods
8. **Technology Stack** - Dependency descriptions
9. **Performance Metrics** - Benchmark data
10. **Best Practices** - Usage recommendations
11. **Troubleshooting** - Common issues

**Quick Reference**:
```bash
# View README
cat README.md
# Or view rendered version on GitHub
```

---

### 8. `FILE_STRUCTURE.md` (This file)

**Purpose**: Comprehensive documentation of all project files

**Content**:
- Detailed file descriptions
- Code examples and usage patterns
- File size statistics
- Quick start guide
- Modification instructions

---

## Data Files (data/ directory)

### 9. `data/mastersprograminanalytics.pdf` (214KB)

**Purpose**: UChicago MS Applied Data Science program PDF handbook

**Content**:
- Program overview
- Course list and descriptions
- Admission requirements
- Program duration
- Capstone project
- Course codes (e.g., ADSP 31006)

**Extraction Result**: 20 chunks (8 pages)

**Characteristics**:
- Official documentation
- Structured content
- Contains course codes

---

### 10-23. `data/page_*.html` (14 HTML files)

**Purpose**: Web page snapshots from UChicago Data Science website

**File List**:
```
page_00000.html (1 chunk)   - Home page
page_00001.html (29 chunks) - Course details
page_00002.html (31 chunks) - Course details
page_00003.html (1 chunk)   - Short page
page_00004.html (38 chunks) - Program structure (Most important)
page_00005.html (20 chunks) - FAQ
page_00006.html (6 chunks)  - Student stories
page_00007.html (2 chunks)  - Short page
page_00008.html (3 chunks)  - Short page
page_00009.html (22 chunks) - Course descriptions
page_00010.html (1 chunk)   - Short page
page_00011.html (2 chunks)  - Capstone information
page_00012.html (29 chunks) - Course details (duplicate of page_00001)
page_00013.html (2 chunks)  - Short page
```

**Total**: 187 chunks

**Content**:
- Detailed course descriptions
- Program timeline
- Student stories
- FAQ
- Capstone project information

**Processing Method**:
- Remove noise: script, style, nav, footer (71.3% filtering rate)
- Keep structure: h1-h5, p, li, td
- Extract metadata: title, description, URL

---

## Auto-generated Files

### `.chroma/` (Vector database directory)

**Purpose**: Store document vector embeddings

**Size**: ~20-30MB

**Content**:
- Embeddings for 207 document chunks
- ChromaDB index files
- Metadata

**Generation**:
```bash
python rag.py --rebuild "test"
```

**Note**:
- Excluded in `.gitignore`
- Needs to be rebuilt on first run or when data changes
- Automatically rebuilt after deletion

---

### `__pycache__/` (Python cache)

**Purpose**: Python compiled bytecode cache

**Content**:
- `rag.cpython-310.pyc`
- Auto-generated `.pyc` files

**Note**:
- Excluded in `.gitignore`
- Automatically managed by Python
- Safe to delete

---

## File Size Statistics

| Type | File Count | Total Size |
|------|-----------|-----------|
| Python code | 2 | 24KB |
| Configuration files | 4 | 2KB |
| Documentation | 2 | 15KB |
| PDF data | 1 | 214KB |
| HTML data | 14 | ~1.7MB |
| Vector database | - | ~25MB |
| **Total** | 23+ | ~2MB (excluding .chroma) |

---

## Key Files Explanation

### Required Files (6)
1. `rag.py` - Core code
2. `eval_ragas.py` - Evaluation script
3. `requirements.txt` - Dependency list
4. `.env` - Configuration (must be created locally)
5. `README.md` - Documentation
6. `data/` - Data directory

### Recommended Files (3)
1. `.env.example` - Configuration template
2. `.gitignore` - Git rules
3. `FILE_STRUCTURE.md` - This documentation

### Auto-generated (2)
1. `.chroma/` - Vector database
2. `__pycache__/` - Python cache

---

## Quick Start Process

```bash
# 1. Install dependencies
pip install -r requirements.txt

# 2. Configure API key
cp .env.example .env
vim .env  # Add OPENAI_API_KEY

# 3. Run RAG system
python rag.py "What are the core courses?"

# 4. Evaluate system
python eval_ragas.py

# 5. View documentation
cat README.md
```

---

## File Modification Guide

### Want to modify retrieval parameters?
Edit `.env`:
```env
TOP_K=10          # Retrieve more chunks
MIN_SIM=0.15      # Lower threshold
```

### Want to add new query expansion rules?
Edit `expand_query()` function in `rag.py`

### Want to change models?
Edit `.env`:
```env
EMBED_MODEL=text-embedding-3-small  # Smaller embedding
CHAT_MODEL=gpt-4o                   # Stronger LLM
```

### Want to add new data sources?
1. Place in `data/` directory
2. If PDF, update `PDF_PATH` in `.env`
3. If HTML, ensure it's in `data/` directory
4. Run `python rag.py --rebuild "test"`

---

**Documentation Generated**: 2025-11-05
**Project Version**: V3.0 (Optimized)
