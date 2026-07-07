# Skincare AI Agent — Inventory Optimization & Personalized Recommendation System

> **POC Project** 

A multi-agent AI system that solves two concurrent business problems in cosmetics retail:
- **Inventory stagnation** caused by rapid product launches
- **Low customer retention** from generic, untargeted marketing

By matching customers to products based on natural language skin analysis and real-time stock levels, the system enables personalized 1:1 recommendations at scale.

---

## Problem Statement

| Stakeholder | Pain Point |
|-------------|-----------|
| Brand (Amorepacific) | Overstocked SKUs from frequent new launches; inventory turnover lag |
| Platform | Generic recommendation logic → low CTR and repurchase rate |
| Customer | Irrelevant product suggestions that ignore individual skin profiles |

---

## Solution Architecture

A **3-agent sequential pipeline** built with CrewAI:

```
User Natural Language Input
        ↓
┌─────────────────────┐
│  Skin Analyst Agent  │  → Extracts skin type, concerns, sensitivities,
│  (피부 과학 분석가)   │    environmental factors; outputs ingredient guidelines
└─────────────────────┘
        ↓
┌──────────────────────────┐
│  Inventory Manager Agent  │  → Reads amore_inventory.csv; matches products
│  (재고 최적화 전문가)      │    by efficacy; prioritizes overstocked items
└──────────────────────────┘
        ↓
┌──────────────────────────────┐
│  Marketing Specialist Agent   │  → Generates empathetic, personalized
│  (뷰티 콘텐츠 마케터)          │    recommendation report in Korean
└──────────────────────────────┘
        ↓
  Personalized Recommendation Report
```

### Key Design Decisions

- **Inventory-weighted matching** — overstocked products with similar efficacy are prioritized, directly targeting inventory turnover KPIs
- **Privacy-preserving state management** — intermediate agent reasoning and raw CSV data are explicitly blocked from leaking into final outputs via task-level output constraints
- **Natural language interface** — customers describe skin concerns conversationally (e.g., *"요즘 피부가 푸석해"*); no forms or dropdowns required

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Agent Orchestration | [CrewAI](https://github.com/joaomdmoura/crewAI) |
| LLM | GPT-4o (OpenAI) |
| Data Processing | Pandas |
| Inventory Tool | `crewai_tools.FileReadTool` |
| Language | Python 3.x |

---

## Project Structure

```
skincare-ai-agent-poc/
├── notebooks/
│   ├── recommendation_agent.ipynb   # Main 3-agent pipeline
│   └── generate_inventory.ipynb     # Inventory CSV generation script
├── data/
│   ├── amore_inventory.csv          # Product inventory (brand, ingredients, stock status)
│   └── users_100_example.csv        # Sample customer profiles (100 users)
├── .env.example                     # Environment variable template
├── .gitignore
└── README.md
```

---

## Quickstart

### 1. Clone & install dependencies

```bash
git clone https://github.com/<your-username>/skincare-ai-agent-poc.git
cd skincare-ai-agent-poc

pip install crewai crewai-tools pandas python-dotenv openai
```

### 2. Set up your API key

```bash
cp .env.example .env
# Edit .env and add your OpenAI API key:
# OPENAI_API_KEY=sk-proj-...
```

### 3. Run the notebook

Open `notebooks/recommendation_agent.ipynb` in Jupyter and run all cells.

```python
# Example usage
user_request = "나는 건성에 성분에 민감한 타입이고 요즘 피부가 푸석해"
response = get_realtime_recommendation(user_request)
print(response)
```

**Sample output:**
```
✨ 아모레퍼시픽 AI 카운셀러의 응답:
- 제품명: 수퍼 모이스처라이징 크림
- 고객 피부 타입 및 적합 성분: 건조하며 푸석함과 민감성으로 고민이시군요.
  히알루론산, 글리세린, 세라마이드 성분이 피부의 수분을 보존하고 보호막을 강화합니다.
- 해당 제품의 효과: 지속적인 사용으로 건조함과 푸석함이 개선되고,
  촉촉하고 탄력 있는 피부 상태를 유지하실 수 있습니다.
```

---

## Inventory Data Schema

`data/amore_inventory.csv`

| Column | Description |
|--------|-------------|
| `제품명` | Product name |
| `브랜드` | Brand (설화수, 아이오페, 라네즈, etc.) |
| `제품유형` | Product type (에센스, 크림, 앰플, etc.) |
| `핵심성분` | Key active ingredients |
| `성분특징` | Ingredient efficacy description |
| `재고상태` | Stock status: `재고 충분` / `재고 적정` / `재고 부족` |
| `추천피부` | Recommended skin type |
| `타입효과` | Primary skin benefit |

---

## Future Roadmap

- **Vector DB integration** — Qdrant for semantic similarity search between abstract customer descriptions (*"dry and flaky"*) and product ingredient embeddings, replacing keyword-based matching
- **Batch processing** — Process `users_100_example.csv` at scale using CrewAI batch mode for B2B campaign generation
- **Cloud deployment** — AWS Lambda (agent execution) + S3 (inventory data) + API Gateway (customer-facing endpoint)
- **Real-time inventory sync** — Replace static CSV with live inventory API integration

---

## License

This project is for educational/portfolio purposes.

---

*[한국어 README →](README_KR.md)*
