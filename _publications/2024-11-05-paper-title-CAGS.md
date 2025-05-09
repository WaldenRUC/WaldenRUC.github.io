---
title: "CAGS: Context-Aware Document Ranking with Contrastive Graph Sampling"
collection: publications
permalink: /publication/2024-11-05-paper-title-CAGS
excerpt: '[TKDE 2024](https://ieeexplore.ieee.org/xpl/RecentIssue.jsp?punumber=69) published 12 issues with a total of 666 papers.'
date: 2024-11-05
venue: 'TKDE'
paperurl: 'https://ieeexplore.ieee.org/document/10742917'
citation: "Z. Huang, Z. Dou, Y. Zhu and J. -R. Wen, CAGS: Context-Aware Document Ranking with Contrastive Graph Sampling, in IEEE Transactions on Knowledge and Data Engineering, doi: 10.1109/TKDE.2024.3491996. keywords: {Data augmentation;Context modeling;Data models;Search engines;Contrastive learning;Training;Search problems;Encoding;Bidirectional control;Predictive models;Context-aware document ranking;contrastive learning;graph sampling}"
---

## Abstract
In search sessions, a series of interactions in the context has been proven to be advantageous in capturing users' search intents. Existing studies show that designing pre-training tasks and data augmentation strategies for session search improves the robustness and generalizability of the model. However, such data augmentation strategies only focus on changing the original session structure to learn a better representation. Ignoring information from outside the session, users' diverse and complex intents cannot be learned well by simply reordering and deleting historical behaviors, proving that such strategies are limited and inadequate. In order to solve the problem of insufficient modeling under complex user intents, we propose exploiting information outside the original session. More specifically, in this paper, we sample queries and documents from the global click-on and follow-up session graph, alter an original session with these samples, and construct a new session that shares a similar user intent with the original one. Specifically, we design four data augmentation strategies based on session graphs in view of both one-hop and multi-hop structures to sample intent-associated query/document nodes. Experiments conducted on three large-scale public datasets demonstrate that our model outperforms the existing ad-hoc and context-aware document ranking models.

## Meta Review
Considering that the authors have addressed satisfactorily all the previous concerns from the reviewers, and hence, I recommend that the manuscript could be accepted in its current form.


[Download paper here](https://ieeexplore.ieee.org/document/10742917)