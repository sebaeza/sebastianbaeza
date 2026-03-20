---
layout: page
title: Policy Convergence in Japan's Aging Society
description: Tracking lexical and conceptual policy alignment across national, regional, and local scales using computational text analysis.
img: assets/img/projects_img/3_policy_convergence/thumbnail.png
importance: 2
category: research
related_publications: false
github: https://github.com/sebaeza/japan_policy_convergence
---

Advanced economies facing demographic decline experience a profound tension between traditional growth-oriented strategies and the reality of shrinking populations. This project investigates the dynamics of policy translation in Japan, asking a critical question: Do local governments formulate strategies tailored to their diverse demographic realities, or do they simply adopt the rhetoric of the central government?

By analyzing a massive corpus of Japanese policy documents across three administrative scales (national, prefectural, and municipal), we trace how policy concepts cascade and mutate. This helps distinguish genuine local adaptation from "rhetorical compliance"—where localities align with central indicators to secure funding while ignoring their own specific needs.

## Methodology: A Dual-Layered NLP Approach

To capture both superficial mimicry and deep conceptual alignment, the research employs a dual-layered computational text analysis framework:

1.  **Lexical Convergence (Surface-Level):** We measure how quickly local governments adopt the specific vocabulary and "buzzwords" introduced by the central government.
2.  **Semantic Proximity (Deep-Level):** We evaluate whether the underlying policy structures and concepts align, regardless of the exact terminology used.

---

## Core Implementation

### 1. Lexical Analysis (TF-IDF)
Measures the direct adoption of central policy vocabulary by regional governments using a custom Sudachi tokenizer for Japanese text.

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from sklearn.metrics.pairwise import cosine_similarity
from sudachipy import Dictionary, SplitMode

# 1. Custom tokenizer filtering specific POS and stopwords
sudachi_tokenizer = Dictionary(config="Dictionary/sudachi_config.json").create(mode=SplitMode.C)

def sudachi_for_bertopic(text):
    # Extracts meaningful parts of speech (Nouns, Verbs, Adjectives, etc.)
    # Excludes geographic names and custom stopwords
    pass 

# 2. Vectorize documents
vectorizer = TfidfVectorizer(
    tokenizer=sudachi_for_bertopic, 
    ngram_range=(1, 3), 
    max_features=10000
)
vectorizer.fit(all_docs)

# 3. Compute cosine similarity between local and central documents
central_tfidf = vectorizer.transform(central_docs)
regional_tfidf = vectorizer.transform(regional_docs)
similarity_matrix = cosine_similarity(regional_tfidf, central_tfidf)
```

