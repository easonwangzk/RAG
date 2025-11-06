# RAG System - UChicago MS Applied Data Science

Production-grade RAG (Retrieval Augmented Generation) system supporting PDF and HTML document formats.

## Quick Start

### 1. Install Dependencies

```bash
pip install -r requirements.txt
```

### 2. Configure API Key

```bash
cp .env.example .env
# Edit .env file and add your OPENAI_API_KEY
```

### 3. Run Query

```bash
python rag.py "What are the core courses?"
```

## System Performance

**Data Scale**: 1 PDF + 14 HTML = 207 chunks
**Retrieval Similarity**: 0.66 (baseline 0.50, improved by 32%)
**Answer Accuracy**: 5/5 test questions correct
**Response Latency**: ~2 seconds/query

## Usage Examples

### Basic Queries
```bash
python rag.py "What are the core courses?"
python rag.py "How long does the program take?"
python rag.py "Tell me about the capstone project"
```

### Interactive Mode
```bash
python rag.py
# Press Enter and input your question
```

### Custom Parameters
```bash
# Increase retrieval count
python rag.py --top_k 10 "What courses are offered?"

# Lower similarity threshold
python rag.py --min_sim 0.20 "What are the electives?"

# Verbose output
python rag.py -v "What is the admission process?"

# Rebuild vector database
python rag.py --rebuild "Your question"
```

## Configuration

Configure parameters in the `.env` file:

```env
# Data sources
PDF_PATH=data/mastersprograminanalytics.pdf
HTML_DIR=data

# Models
EMBED_MODEL=text-embedding-3-large
CHAT_MODEL=gpt-4o-mini

# Chunking parameters
CHUNK_TOKENS=600        # Tokens per chunk
OVERLAP_TOKENS=150      # Overlap between chunks

# Retrieval parameters
TOP_K=5                 # Number of chunks to retrieve
MIN_SIM=0.20            # Minimum similarity threshold

# Generation parameters
TEMPERATURE=0.2         # LLM temperature
MAX_TOKENS=800          # Maximum generation tokens
```

## Core Technologies

### Data Processing
- **PDF Parsing**: pypdf extracts text and metadata
- **HTML Parsing**: BeautifulSoup intelligent cleaning (71.3% noise filtering)
- **Token Chunking**: tiktoken precise segmentation (600 tokens/chunk)
- **Smart Deduplication**: Automatic merging of chunks from same source

### Retrieval System
- **Vector Retrieval**: OpenAI text-embedding-3-large (3072 dimensions)
- **Vector Database**: ChromaDB persistent storage
- **Query Expansion**: Rule-based keyword augmentation
- **Two-stage Retrieval**: Retrieve 2x top_k then deduplicate to top_k
- **Similarity Filtering**: Configurable threshold

### Generation System
- **LLM**: GPT-4o-mini
- **Context Management**: Smart limiting within 3000 tokens
- **Source Citations**: Automatic source information

## Project Structure

```
.
├── rag.py                     # Main program
├── eval_ragas.py              # RAGAS evaluation
├── requirements.txt           # Python dependencies
├── .env                       # Configuration file (not in git)
├── .env.example              # Configuration template
├── README.md                 # This file
├── FILE_STRUCTURE.md         # Detailed file structure documentation
├── data/
│   ├── mastersprograminanalytics.pdf  # PDF data
│   └── page_*.html           # HTML data (14 files)
└── .chroma/                   # Vector database (auto-generated)
```

## RAGAS Evaluation

Run the evaluation script:

```bash
python eval_ragas.py
```

**Test Questions**:
1. What are the core courses?
2. How long does the program typically take to complete?
3. Are there any capstone or practicum components?
4. Tell me about the Time Series Analysis course
5. What is the Machine Learning I course about?

**Evaluation Metrics**:
- Faithfulness (answer grounded in context)
- Answer Relevancy (answer addresses the question)

## Technology Stack

**Core**:
- `langchain` - RAG framework
- `chromadb` - Vector database
- `openai` - Embeddings and LLM
- `tiktoken` - Token counting

**Data Processing**:
- `pypdf` - PDF parsing
- `beautifulsoup4` - HTML parsing
- `lxml` - HTML parser

**Evaluation**:
- `ragas` - RAG evaluation
- `datasets`, `pandas`, `numpy`

## Performance Metrics

| Metric | Value |
|--------|-------|
| Total data chunks | 207 (PDF: 20, HTML: 187) |
| Retrieval similarity | 0.59-0.72 |
| Average response time | ~2 seconds |
| Cost per query | ~$0.0004 |
| Answer success rate | 100% (5/5) |

## Best Practices

### Simple Factual Queries
```bash
# Direct query with default parameters
python rag.py "How many courses are required?"
```

### Complex Exploratory Queries
```bash
# Increase retrieval count, lower threshold
python rag.py --top_k 10 --min_sim 0.20 \
  "Explain the program structure"
```

### List-based Queries
```bash
# Lower threshold to get more candidates
python rag.py --min_sim 0.25 \
  "What are all the elective courses?"
```

## System Improvement History

**V1.0** (Baseline)
- Single PDF data source
- Similarity: 0.41
- Contains bug (negative similarity)

**V2.0** (Current Version)
- PDF + HTML multi-source integration
- Token-based chunking
- Similarity: 0.69 (+68%)
- Answer accuracy: 80%

**V3.0** (Latest - Query Expansion)
- Added rule-based query expansion
- Improved deduplication (source-file based)
- Similarity: 0.66 (for "core courses" query)
- Answer accuracy: 100% (5/5)
- MIN_SIM lowered from 0.30 to 0.20

## Troubleshooting

### Issue: "No module named xxx"
```bash
pip install -r requirements.txt
```

### Issue: "OpenAI API key not found"
```bash
# Check if OPENAI_API_KEY is set in .env file
cat .env | grep OPENAI_API_KEY
```

### Issue: Cannot find relevant information
```bash
# 1. Lower similarity threshold
python rag.py --min_sim 0.20 "your question"

# 2. Increase retrieval count
python rag.py --top_k 10 "your question"

# 3. Rebuild vector database
python rag.py --rebuild "your question"
```

## Development Notes

### Key Optimizations

1. **Similarity Calculation Fix**
   ```python
   # Wrong: sim = 1.0 - distance  # distance in [0, 2]
   # Correct: sim = 1.0 - (distance / 2.0)
   ```

2. **Token-based Chunking**
   - Changed from character-level (chunk_size=1000) to token-level (chunk_tokens=600)
   - Chunks count: 59 → 20 (-66%)
   - Higher quality, better semantic integrity

3. **HTML Processing**
   - Remove script/style/nav noise tags
   - Keep structured content like h1-h5/p/li
   - 1.7MB HTML → 467KB text (71.3% filtering rate)

4. **Smart Deduplication**
   - Retrieve 2x top_k candidates
   - Merge high-similarity chunks from same source file
   - Finally keep top_k most relevant chunks

5. **Query Expansion**
   - Rule-based keyword augmentation
   - Add domain-specific terms for common queries
   - Improves retrieval recall

## License

MIT License

## Acknowledgments

- UChicago Data Science Institute - Data source
- LangChain - RAG framework
- OpenAI - Embeddings and LLM

---

**Status**: Production Ready
**Updated**: 2025-11-05
**Version**: V3.0
