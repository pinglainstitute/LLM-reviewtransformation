# LLM-reviewtransformation
Although Large Language Models (LLMs) have demonstrated significant advancements in natural language processing tasks,
their effectiveness in the classification and transformation of abusive text into non-abusive versions remains an area ripe for explo-
ration. In this study, we aim to use LLMs to transform abusive text (tweets and reviews) featuring hate speech and swear words
into non-abusive text where the message is retained, i.e. the semantics and also the sentiment. We evaluate the performance of two
state-of-the-art LLMs, such as Gemini and Groq, on their ability to identify abusive text. We then use two additional LLMs to trans-
form them so that we obtain a text that is clean from abusive and inappropriate content but maintains a similar level of sentiment
and semantics, i.e. the transformed text needs to maintain its message. Next, we evaluate the raw and transformed datasets with
sentiment analysis and semantic analysis. Our results show Groq have vastly different results when compared with other modelsand
we’ve identified many similarities between GPT-4o and DeepSeek-V3
