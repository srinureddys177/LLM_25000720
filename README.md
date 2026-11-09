# Croatia IMF 2025 — Financial Stability Analysis with LLMs and RAG

This project analyses **IMF Country Report No. 25/329**, *Republic of Croatia: 2025 Article IV Consultation—Press Release; and Staff Report* (December 2025).

The objective is to transform a 74-page IMF report into structured economic indicators, financial-stability insights, model evaluations and visualisations using Python, Large Language Models (LLMs) and Retrieval-Augmented Generation (RAG).

The analysis combines PDF text extraction, token-aware chunking, semantic retrieval using FAISS, structured JSON extraction, and LLM-based evaluation. The workflow is implemented in a Jupyter notebook and can run on a CPU-only computer.

## 1. Project Objectives

The project addresses four main tasks:

1. Extract, clean and summarise the IMF country report.
2. Identify financial-stability themes and extract structured macroeconomic indicators.
3. Implement a local vector-based RAG system for evidence-grounded financial analysis.
4. Evaluate model outputs and present findings through a four-chart dashboard.

An additional experiment compares single-query and multi-query retrieval strategies.

## 2. Dataset

**Country:** Republic of Croatia  
**Report year:** 2025  
**Document:** IMF Country Report No. 25/329  
**Publisher:** International Monetary Fund  
**Publication date:** December 2025  
**Document length:** 74 PDF pages

The report contains economic projections, financial-soundness indicators, fiscal assessments, banking-sector developments and recommendations for macroeconomic and financial stability.

Official source:

https://www.elibrary.imf.org/view/journals/002/2025/329/002.2025.issue-329-en.xml

## 3. Technologies Used

| Component | Technology |
| --- | --- |
| Programming language | Python |
| Development environment | Jupyter Notebook |
| PDF extraction | pypdf |
| Tokenisation | tiktoken |
| Embedding model | all-MiniLM-L6-v2 |
| Vector database | FAISS IndexFlatL2 |
| LLM API | OpenRouter |
| Primary model | Gemini 3.7 Flash |
| Secondary model | Gemini 3.5 Flash Lite |
| Data manipulation | Pandas and NumPy |
| Visualisation | Matplotlib |
| Structured output | JSON |
| Credential management | python-dotenv |

The embedding model runs locally, while LLM requests are handled through OpenRouter.

## 4. Methodology

### 4.1 PDF Processing and Chunking

The original IMF report is processed using `pypdf` in layout mode.

The pipeline removes repeated headers, footers and formatting artifacts while retaining page references for evidence verification.

The cleaned text is tokenised using `cl100k_base` and divided into overlapping segments.

Recorded processing results:

- PDF pages: 74
- Cleaned document tokens: approximately 41,224
- Total chunks: 139
- Maximum chunk length: 450 tokens
- Chunk overlap: 50 tokens
- Average chunk length: approximately 320 tokens

Page-aware chunking preserves document traceability and helps identify the original sources of extracted statements.

### 4.2 Semantic Retrieval

Each document chunk is converted into a 384-dimensional embedding using `all-MiniLM-L6-v2`.

The embeddings are stored in a local FAISS `IndexFlatL2` index.

The system retrieves relevant passages for questions involving:

- Inflation and economic growth
- Banking capital and liquidity
- Credit expansion
- Housing-market vulnerabilities
- Sovereign debt
- Financial-sector risks

Semantic retrieval helps identify related information even when the report uses different terms to describe similar economic concepts.

### 4.3 Document Summarisation

The notebook uses a map-reduce approach for processing the complete report.

The map stage creates intermediate summaries across 19 document batches. The reduce stage consolidates them into an executive summary of Croatia's macroeconomic conditions and financial-stability risks.

The pipeline also produces five topical summaries covering major areas of the report.

### 4.4 Structured Indicator Extraction

The LLMs extract economic and financial indicators into machine-readable JSON.

The required indicators include:

- Country name
- GDP growth forecast
- Headline inflation rate
- Central bank policy rate
- Nonperforming loan ratio
- Unemployment rate
- Tier 1 capital ratio

The extraction process includes schema validation and fallback values for unavailable or ambiguous indicators.

A secondary LLM evaluates the extracted information for consistency, completeness and factual alignment.

### 4.5 Financial-Stability Theme Analysis

The notebook scans the document for seven financial-stability themes:

1. Inflation & Prices
2. Interest Rates & Credit
3. Housing & Real Estate
4. Banking Capital & Liquidity
5. Sovereign Debt
6. Labor Market Dynamics
7. Systemic Financial Risk

The frequency counts provide an overview of topics appearing throughout the IMF report.

These counts represent keyword mentions rather than a formal measurement of financial risk.

### 4.6 Retrieval-Augmented Generation Evaluation

The RAG system retrieves supporting evidence before generating answers to financial questions.

Two retrieval strategies are evaluated:

**Single-query RAG:** Retrieves relevant passages using the original question.

**Multi-query RAG:** Expands the original question into alternative queries to retrieve a broader collection of passages.

An LLM judge evaluates responses for factual alignment and completeness.

## 5. Running the Notebook

### Requirements

- Python 3.10 or later
- Jupyter Notebook or JupyterLab
- Internet connection
- OpenRouter API key
- Original Croatia IMF PDF report

### Step 1 — Install Dependencies

```bash
python -m pip install jupyter pypdf tiktoken sentence-transformers faiss-cpu openai python-dotenv pydantic matplotlib seaborn pandas numpy scikit-learn
```

### Step 2 — Prepare the Project Folder

Place the notebook and the original IMF PDF in the same directory.

Example:

```text
Croatia_IMF_Analysis/
├── Croatia_IMF_2025_OpenRouter.ipynb
├── Croatia_IMF_Country_Report_2025.pdf
├── README.md
├── .env
└── croatia_outputs/
```

The notebook filename may differ depending on the version being used.

### Step 3 — Configure the API Key

Create a `.env` file containing:

```dotenv
OPENROUTER_API_KEY=your_api_key_here
```

Replace the placeholder with your personal OpenRouter API key.

**Important:** Never upload the `.env` file or your API key to GitHub.

### Step 4 — Start Jupyter

Open a terminal in the project directory and run:

```bash
jupyter lab
```

Open the notebook and execute its cells sequentially.

The notebook uses the current working directory to locate the source PDF and creates the output folder automatically.

A fresh execution requires internet access for API requests and may produce slightly different LLM-generated results.

### Model Configuration

The executed analysis used:

```python
MODEL_A = "google/gemini-3.7-flash"
MODEL_B = "google/gemini-3.5-flash-lite"
```

Check that these model identifiers match the configuration in your uploaded notebook and are available through your OpenRouter account.
