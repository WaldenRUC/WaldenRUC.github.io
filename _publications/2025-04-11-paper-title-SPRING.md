---
title: "One Token Can Help! Learning Scalable and Pluggable Virtual Tokens for Retrieval-Augmented Large Language Models"
collection: publications
permalink: /publication/2025-04-11-paper-title-SPRING
excerpt: 'AAAI 2025'
date: 2025-04-11
venue: 'AAAI'
paperurl: 'https://ojs.aaai.org/index.php/AAAI/article/view/34813'
citation: "Zhu, Y., Huang, Z., Dou, Z., & Wen, J.-R. (2025). One Token Can Help! Learning Scalable and Pluggable Virtual Tokens for Retrieval-Augmented Large Language Models. Proceedings of the AAAI Conference on Artificial Intelligence, 39(24), 26166-26174. https://doi.org/10.1609/aaai.v39i24.34813"
---

## Abstract

Retrieval-augmented generation (RAG) is a promising way to improve large language models (LLMs) for generating more factual, accurate, and up-to-date content. Existing methods either optimize prompts to guide LLMs in leveraging retrieved information or directly fine-tune LLMs to adapt to RAG scenarios. Although fine-tuning can yield better performance, it often compromises the LLMs' general generation capabilities by modifying their parameters. This limitation poses challenges in practical applications, especially when LLMs are already deployed, as parameter adjustments may affect their original functionality. To address this, we propose a novel method that involves learning scalable and pluggable virtual tokens for RAG. By maintaining the LLMs' original parameters and fine-tuning only the embeddings of these pluggable tokens, our approach not only enhances LLMs' performance but also preserves their general generation capabilities. Furthermore, we design several training strategies to improve the scalability, flexibility, and generalizability of our method. Comprehensive experiments across 12 question-answering tasks demonstrate the superiority of our approach.

[Download paper here](/files/2025.AAAI.pdf)