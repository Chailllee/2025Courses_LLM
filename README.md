# 2025Courses_LLM Portfolio

A collection of independent projects spanning LLM applications, data analysis, visualization, and agent/tooling demos. Curated for PhD applications and data/AI roles, emphasizing end-to-end problem solving, reproducibility, and interpretable results.

## Highlights
- **LLM & Agent Practice:** Prompt engineering, function calling, retrieval/embedding workflows, and local model serving (DeepSeek, Ollama, Qwen). Includes web search, function-enabled chat, and streaming clients.
- **Data Science & ML:** Feature engineering, model training (CatBoost, XGBoost), and submission-ready pipelines for tabular problems (used-car price prediction, ensemble stacking).
- **Full-Stack Visual Analytics:** Flask + ECharts dashboards (hospital bed usage, epidemic trends) with caching, API design, and front-end interactivity; Excel data prep notebooks for fast inspection.
- **Reproducible Notebooks:** Stepwise notebooks for each case, with minimal dependencies and runnable scripts for automation.

## Project Map
- **1-LLM Fundamentals & API Usage**
  - Function calling demos, sentiment analysis, weather tool, table extraction, ops incident handling, web search (Qwen/DeepSeek). Notebooks + Python scripts show prompt design and tool invocation patterns.
- **2-DeepSeek & Prompt Engineering**
  - Prompt engineering exercises, DeepSeek model usage, local/remote model calls, Ollama streaming & FastAPI clients. Emphasis on instruction tuning style prompts and latency-aware streaming.
- **3-Cursor Programming: Zero to Pro**
  - **Case1 Excel Merge:** Excel-based data joins and cleaning for HR performance data.
  - **Case2 Bed Usage Dashboard:** Flask + ECharts; precompute cache, exploratory charts, and server routes for hospital bed utilization.
    <div align="center">
      <img src="3-Cursor-Programming-Zero-to-Pro/case_img/case2.gif" alt="Case2 Demo" width="520" />
    </div>
  - **Case3 Epidemic Dashboard:** Time series + geo visualizations with risk stratification; API layer, geojson name mapping, and tooltip enrichment. Front-end and back-end both included.
    <div align="center">
      <img src="3-Cursor-Programming-Zero-to-Pro/case_img/case3.gif" alt="Case3 Demo" width="520" />
    </div>
  - Shared combined notebook for running cases sequentially, with unified path handling.
- **4-Embeddings & Vector Databases**
  - ChatPDF + FAISS PDF QA pipeline; chunking, embedding, and retrieval demo (Case-ChatPDF-Faiss).
  - Vector DB workflows: embedding computation with metadata and FAISS querying (CASE-向量数据库).
  - Hotel recommendation via similarity search on a structured hotel dataset (hotel_recommendation).
  - Word2Vec and NLP embedding practice with classic corpora and similarity scripts (word2vec).
- **19-AI Algorithms Across Domains**
  - Used-car price prediction end-to-end: EDA, feature engineering, CatBoost/XGBoost training, ensembling, and submission artifacts.


## How to Run (quick start)
1) Create/activate a Python env (3.10+ recommended).
2) Install per-case requirements (e.g., `pip install -r 3-Cursor-Programming-Zero-to-Pro/Case3_dashboard_epidemic/requirements.txt`).
3) For dashboards: run the Flask app (`python app.py --port 5000`) inside each case folder; open http://127.0.0.1:5000/.
4) For notebooks: open in VS Code/ Jupyter; follow the stepwise cells (path setup cells provided).
