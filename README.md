#  Tag Search Engine

![Python](https://img.shields.io/badge/python-3.11-blue) ![FastAPI](https://img.shields.io/badge/FastAPI-0.100+-green) ![Docker](https://img.shields.io/badge/docker-ready-2496ED)

A plug-and-play scoring API designed to work with any service that needs tag-based page ranking.

##  How it works

Send your pre-filtered list of pages from your database as JSON, and the API will score and rank them based on how relevant they are to the user's query.

The algorithm uses:
- **RapidFuzz** — fuzzy matching for typo tolerance
- **NLTK SnowballStemmer** — stemming to detect morphological variations of words
- **Double scoring** — tags + title for higher precision

##  Supported languages

`arabic` `danish` `dutch` `english` `finnish` `french` `german` `hungarian` `italian` `norwegian` `portuguese` `romanian` `russian` `spanish` `swedish`

##  Installation

### Without Docker
```bash
pip install -r requirements.txt
python3 -m uvicorn main:app --reload
```

### With Docker
```bash
docker build -t tag-search-engine .
docker run -p 8000:8000 tag-search-engine
```

##  Docker Compose

```yaml
services:
  search-engine:
    build: .
    restart: unless-stopped
    ports:
      - 8000:8000
```

##  Configuration

All parameters are configurable via `config.json`:

| Parameter | Description | Default |
|-----------|-------------|---------|
| `languages` | Stemmer languages | `["french"]` |
| `limit` | Max number of results returned | `15` |
| `score_threshold` | Minimum score to appear in results | `65` |
| `title_weight` | Weight of title score in final score | `0.2` |

##  Usage

Send a `POST` request to `/search` with a JSON body containing:
- `query` — the search string entered by the user
- `items` — the list of pages to score, each with an `id`, `tags` and `title`

> 💡 Recommended: use a 300ms debounce on the frontend to avoid spamming the API.

**Example request:**
```json
{
  "query": "alternance javascript",
  "items": [
    {"id": 1, "tags": ["alternance", "javascript"], "title": "My internship experience"},
    {"id": 2, "tags": ["cooking", "recipes"], "title": "My favorite recipes"}
  ]
}
```

**Example response:**
```json
[
  {"id": 1, "tags": ["alternance", "javascript"], "title": "My internship experience", "score": 97.0}
]
```

Swagger docs available at `http://localhost:8000/docs`

##  Roadmap

This project is likely to evolve. Future improvements may include:
- [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) scoring
- [BM25](https://en.wikipedia.org/wiki/Okapi_BM25) ranking algorithm
