# SJSU MSAI Virtual Advisor

**CMPE 259 – Natural Language Processing | San José State University**

A tool-augmented LLM chatbot that helps SJSU MS in Artificial Intelligence students with course prerequisites, degree requirements, elective planning, and academic policies.

---

## Overview

This project builds a Virtual Advisor (VA) pipeline that combines two lightweight, CPU-friendly language models with structured tool use. Rather than relying on model memorization, the VA calls a local SQLite database and approved SJSU web pages to retrieve factual academic information before generating a response.

Three prompting strategies are implemented and compared across 22 representative student queries.

---

## Models

| Model | Parameters | Backend |
|---|---|---|
| TinyLlama-1.1B-Chat | ~1.1B | HuggingFace Transformers (CPU, float32) |
| Qwen2-0.5B-Instruct | ~0.5B | HuggingFace Transformers (CPU, float32) |

Both models run entirely on CPU — no GPU required.

---

## Prompting Strategies

| Strategy | Description |
|---|---|
| **Meta Prompting** | Single structured system prompt that enforces tool use before every answer |
| **Prompt Chaining** | Three-step pipeline: classify intent → call tool → generate response |
| **Self-Reflection** | Generate initial answer → critique it against tool data → produce refined answer |

---

## Tools

| Tool | Purpose |
|---|---|
| `query_database` | Query the local SQLite DB for courses, prerequisites, and degree requirements |
| `web_search` | Scrape approved SJSU pages for deadlines, advising contacts, and tuition info |

Web search is restricted to an allowlist of SJSU domains (`sjsu.edu`, `catalog.sjsu.edu`) to prevent SSRF.

---

## Project Structure

```
.
├── CMPE259_VA_Notebook.ipynb   # Main notebook (training, evaluation, demo)
├── sjsu_msai.db                # SQLite database (courses, prereqs, requirements)
├── evaluation_results.png      # Accuracy/tool-use benchmark chart
├── caching_benchmark.png       # Prompt caching latency comparison
└── security_results.png        # Prompt injection defense results
```

---

## Notebook Structure

| Section | Description |
|---|---|
| 1 · Installation | pip installs |
| 2 · Imports | Library setup |
| 3 · SQLite Database | Schema creation and seed data |
| 4 · Tool Definitions | `query_database` and `web_search` implementations |
| 5 · Model Loading | TinyLlama and Qwen2 loaded on CPU |
| 6 · Prompting Strategies | Meta, Chain, Reflect strategy prompts |
| 7 · VA Pipeline | Core `run_meta`, `run_chain`, `run_reflect` functions |
| 8 · Prompt Caching | System-prompt token caching for reduced latency |
| 9 · Test Queries | 22 representative student queries |
| 10 · Evaluation | Accuracy, tool usage, and helpfulness scoring |
| 11 · Security Testing | Prompt injection attacks and defense evaluation |
| 12 · Results Visualization | Charts for evaluation, caching, and security |
| 13 · Summary & Analysis | Cross-model and cross-strategy comparison |
| 14 · Conclusion | Key findings, limitations, and future work |
| 15 · Interactive Demo | Gradio chat UI inside the notebook |

---

## Key Results

- **Accuracy**: Both models achieved comparable factual accuracy on course and prerequisite queries. TinyLlama produced more coherent responses; Qwen2-0.5B is smaller and faster.
- **Strategy performance**: Prompt Chaining showed the most reliable tool-routing. Self-Reflection improved correctness on complex multi-step queries.
- **Prompt caching**: Reduced per-query tokenization overhead for repeated system-prompt prefixes.
- **Security**: Both models successfully defended against all 5 prompt injection attack types when system-prompt safeguards were in place.

---

## Requirements

```
torch
transformers
gradio
requests
beautifulsoup4
matplotlib
```

Install all at once:
```bash
pip install torch transformers gradio requests beautifulsoup4 matplotlib
```

---

## Security Notes

- SQL queries use parameterized statements — no SQL injection surface.
- `web_search` validates domains against an allowlist before making any HTTP request.
- Tool names are validated against a registered `TOOLS` dict — arbitrary tool calls are rejected.
- System prompts include explicit rules prohibiting fabrication of academic data.

---

## Course Info

- **Course**: CMPE 259 – Natural Language Processing
- **University**: San José State University
- **Program**: MS in Artificial Intelligence
- **Data source**: SJSU MSAI Academic Catalog (2022–2023)
