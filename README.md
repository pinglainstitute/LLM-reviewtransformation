# Detoxify: A Framework for Abusive Text Transformation Using LLMs

This repository contains the code, data pipeline, and evaluation notebooks for **Detoxify**, a framework that uses Large Language Models (LLMs) to detect abusive text (tweets and reviews) and transform it into non-abusive, respectful text — while preserving the original meaning, intent, and sentiment.

Although LLMs have advanced rapidly on general NLP tasks, their ability to reliably identify abusive content and *rewrite* it without losing the original message is still not well understood. This project investigates that gap by systematically comparing four state-of-the-art LLMs — **Gemini**, **GPT-4o**, **DeepSeek**, and **Groq (Llama 3)** — on abuse detection and text detoxification, and by evaluating their outputs using BERT-based sentiment and semantic similarity models.

## Overview

The study addresses three core research goals:

1. **Abuse detection** — how well each LLM identifies abusive vs. non-abusive text.
2. **Abuse transformation** — how well each LLM rewrites abusive text into a polite, respectful version.
3. **Preservation evaluation** — how well the transformed text retains the sentiment and semantic meaning of the original.

Two datasets of abusive text were used:

- **IIT-abuse dataset**: ~160,000 entries scraped from Reddit, 4chan, and Twitter (IIT Guwahati), labelled as abusive (1) or non-abusive (0).
- **Twitter (X) dataset**: 4,265 tweets from 52 users, categorised into five classes — Religion, NSFW, Racism, Discrimination, and Non-abusive.

All web-scraped data was restricted to publicly available material, and user-generated content was anonymised at ingestion (usernames, locations, and other identifying metadata removed) to minimise privacy risk.

## Framework

The Detoxify pipeline consists of seven stages:

1. **Data acquisition** — collect abusive/non-abusive reviews and tweets from Reddit, 4chan, and Twitter.
2. **Preprocessing** — lowercasing, contraction expansion, URL/symbol removal, tokenisation, stopword removal, and lemmatisation.
3. **LLM API configuration** — set up Gemini, Groq, GPT-4o, and DeepSeek APIs with consistent prompts and settings.
4. **Abuse detection** — each LLM classifies text as abusive or non-abusive.
5. **Text transformation** — each LLM rewrites abusive text into a polite, non-abusive version using an identical prompt across all models.
6. **Sentiment analysis** — a fine-tuned SenWave-BERT model compares sentiment distributions before and after transformation.
7. **Semantic analysis** — an MPNet-based embedding model computes cosine similarity / BERTScore between original and transformed text, plus UMAP visualisation of the embedding space.

## Key Findings

- **Detection performance**: Gemini slightly outperformed Groq in abuse detection (mean accuracy 81.5% vs. 78.0%; higher F1 and IoU scores).
- **Transformation success rates**: GPT-4o had the highest successful transformation rate (396/400), followed by DeepSeek (393/400) and Gemini (385/400), while Groq had the most refusals/failures (371/400 successful).
- **Hate-speech reduction**: All four models substantially reduced hateful content post-transformation, with only a small number of cases where hate was retained or newly introduced.
- **Style differences**: Groq consistently produced the longest, most heavily rewritten outputs, often over-correcting toward excessive positive/optimistic phrasing, sometimes losing the original context. GPT-4o stayed closest to the original wording, censoring only explicit terms while preserving structure and meaning.
- **Semantic similarity**: GPT-4o and DeepSeek produced the most semantically similar transformed outputs to each other and to the originals (~80% BERTScore/cosine similarity), while Groq showed the lowest similarity to the original text — consistent with its tendency to restructure sentences.
- **Sentiment shift**: Prior to transformation, the dominant sentiment was "Annoyed." After transformation, all models sharply reduced "Annoyed" and "Joking" sentiment in favour of "Optimistic," with Groq showing the largest shift.
- **Over-sanitisation**: In false-positive cases (originally non-abusive text), LLMs still tended to rewrite text into a more neutral or polished form rather than preserving it exactly — indicating a general tendency toward stylistic normalisation even absent abusive content.

## Repository Structure

```
├── Data_Preprocessing.ipynb              # Text cleaning: lowercasing, contraction expansion,
│                                          # URL/symbol removal, tokenisation, stopword removal, lemmatisation
├── transform__reviews.ipynb              # LLM-based abuse detection & transformation of reviews (IIT-abuse dataset)
├── twitter_transform.ipynb               # LLM-based abuse detection & transformation of tweets (Twitter/X dataset)
├── twitter_analysis.ipynb                # N-gram (bigram/trigram) and category-level analysis of Twitter data
├── sentiment.ipynb                       # SenWave-BERT sentiment analysis on original vs. transformed text
├── semantic.ipynb                        # MPNet embeddings, cosine similarity, BERTScore, and UMAP analysis
├── LLM_Safety_Filter_Analysis.ipynb      # Analysis of transformation failures/refusals and safety-filter behaviour across LLMs
├── Results.ipynb                         # Consolidated results and evaluation metrics
├── Results_graphics.ipynb                # Figures and plots used in the paper
└── Transform_abusive_reviews_using_LLMs___evaluate.pdf   # Full research paper (preprint)
```

## Models Evaluated

| Model | Provider | Notes |
|---|---|---|
| Llama 3.1 8B Instant | Groq | Text-only, low latency (LPU-based inference) |
| Gemini 1.5 / 2.5 Flash | Google AI Studio | Multimodal, configurable safety thresholds |
| GPT-4o | OpenAI | No configurable safety filters via API |
| DeepSeek-V3 | DeepSeek | Open-source, no configurable safety filters via API |

## Evaluation Methods

- **Abuse detection**: Precision, Recall, F1, and IoU against ground-truth labels.
- **Transformation success**: Rate of successful (non-refused) rewrites per model, per batch, and per abuse category.
- **Hate-speech scoring**: HateBERT-based classification of hateful content before/after transformation, plus keyword/log-odds analysis (SWAD and Dynamically Generated Hate Speech datasets).
- **Sentiment preservation**: Multi-label SenWave-BERT sentiment classification (10 categories) on original vs. transformed text.
- **Semantic preservation**: MPNet-based sentence embeddings, pairwise cosine similarity, BERTScore, KL divergence, and UMAP dimensionality reduction with cluster validation (Silhouette and Davies–Bouldin scores).

## Limitations

- A significant portion of scraped tweets were incomplete sentences, making context recovery difficult for LLMs.
- Twitter-specific slang, emojis, and sarcasm were not always well handled by the models.
- Only text was analysed; images, links, and other multimodal content attached to tweets were excluded.
- Due to API rate limits, only a subset of tweets/reviews (100–500 per model) was used for transformation experiments.
- Semantic evaluation relied on cosine similarity alone, which does not fully capture information loss, semantic drift, or overcorrection.
- Human evaluation was limited; results are primarily based on automated metrics.

## Future Work

- Extending the framework to additional LLMs (e.g., Claude, Mistral).
- Evaluating robustness on more diverse and multilingual datasets (e.g., Hindi, Spanish).
- Incorporating multimodal inputs (text + images) for more context-aware abuse detection.
- Large-scale human evaluation via crowdsourcing to validate automated metrics.
- Comparing against external toxicity validators such as Perspective API and Detoxify.

## Citation

If you use this repository or find this work useful, please cite:

> Chandra, R., Choi, J., Sonawane, J. *Detoxify: A framework for abusive text transformation using LLMs.* Centre for Artificial Intelligence and Innovation, Pingla Institute; School of Mathematics and Statistics, UNSW Sydney.

The full paper is included in this repository (`Transform_abusive_reviews_using_LLMs___evaluate.pdf`).

## Data Availability

Datasets and transformed outputs referenced in the paper are available at:
`https://github.com/pinglainstitute/LLM-reviewtransformation`

## Acknowledgements

We thank Neha Chaudhary and Tanuj Chaudhary from IIT Guwahati for providing the abuse dataset used in this study.

## License

Please refer to the LICENSE file in this repository for usage terms. Note that datasets used in this study contain abusive and offensive language collected for research purposes only; users should handle this content responsibly and in accordance with applicable ethical and legal guidelines.
