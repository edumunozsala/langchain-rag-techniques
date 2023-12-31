# Retrieval Augmented Generation Techniques in Langchain
 This repository collects multiple notebooks and .py files with techniques and solutions to RAG problems. Some simple solutions to more advanced ones to approach the retrieval, augmentation and generation of answers or information about data/documents provided. 
 
#### This repository is still in progress.


 ### Dense X Retrieval

 Paper: ["Dense X Retrieval: What Retrieval Granularity Should We Use?"](https://arxiv.org/abs/2312.06648) by Tong Chen, Hongwei Wang, Sihao Chen, Wenhao Yu, Kaixin Ma, Xinran Zhao, Hongming Zhang, and Dong Yu from the University of Washington, Tencent AI Lab, University of Pennsylvania, and Carnegie Mellon University.

 Dense retrieval has emerged as a crucial method for obtaining relevant context or knowledge in open-domain NLP tasks. However, the choice of the retrieval unit, i.e., the pieces of text in which the corpus is indexed, such as a document, passage, or sentence, is often overlooked when a learned dense retriever is applied to a retrieval corpus at inference time. The researchers found that the choice of retrieval unit significantly influences the performance of both retrieval and downstream tasks.

 
 ![Image from the original paper "Dense X Retrieval"](images/retriever_diagram.png)

It suggests that the choice of retrieval unit can significantly impact retrieval performance, highlighting the potential of propositions as a novel retrieval unit. This research contributes to the ongoing efforts to improve the performance of dense retrieval in open-domain NLP tasks and offers valuable insights for researchers and professionals in the field.

In summary, the authors introduce the Propositionizer as a text generation model fine-tuned through a two-step distillation process, involving the use of GPT-4 and Flan-T5-large. The approach combines the capabilities of pre-trained language models with task-specific fine-tuning, demonstrating a comprehensive strategy for parsing passages into propositions. The use of 1-shot demonstrations and distillation processes adds depth to the training methodology, showcasing a nuanced and effective approach in the realm of natural language processing.

In conclusion, the study underscores the potential of proposition-based retrieval as a superior approach, offering improved performance in both retrieval tasks and downstream QA applications. The compact yet context-rich nature of propositions appears to be a valuable asset in addressing the challenges posed by limited token length in language models

### Hybrid Search and Reciprocal Rerank Fusion 

A relatively old idea that you could take the best from both worlds — keyword-based old school search — sparse retrieval algorithms like tf-idf or search industry standard BM25 — and modern semantic or vector search and combine it in one retrieval result.
The only trick here is to properly combine the retrieved results with different similarity scores — this problem is usually solved with the help of the [Reciprocal Rank Fusion](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf) algorithm, reranking the retrieved results for the final output.

The retrieved documents will be reranked according to the `Reciprocal Rerank Fusion` algorithm demonstrated in this [paper](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf). It provides an effecient method for rerranking retrieval results without excessive computation or reliance on external models.

*"Inthe search for such a method we came up with Reciprocal Rank Fusion (RRF) to serve as a baseline. We found that RRF, when used to combine the results of IR methods (including learning to rank), almost invariably improved on the best of the combined results. We also found that RRF consistently equaled or bettered other methods we tried, including established metaranking standards Condorcet Fuse and CombMNZ"*

Hybrid or fusion search usually provides better retrieval results as two complementary search algorithms are combined, taking into account both semantic similarity and keyword matching between the query and the stored documents.

## Multiquery Retrieval

The `MultiQueryRetriever` automates the process of prompt tuning by using an LLM to generate multiple queries from different perspectives for a given user input query. For each query, it retrieves a set of relevant documents and takes the unique union across all queries to get a larger set of potentially relevant documents. By generating multiple perspectives on the same question, the `MultiQueryRetriever` might be able to overcome some of the limitations of the distance-based retrieval and get a richer set of results.

## Per User Retrieval

When building a retrieval app, you often have to build it with multiple users in mind. This means that you may be storing data not just for one user, but for many different users, and they should not be able to see eachother’s data. This means that you need to be able to configure your retrieval chain to only retrieve certain information.

This generally involves two steps.

- Step 1: Make sure the retriever you are using supports multiple users, for example, each vectorstore and retriever may have their own mechanism, and may be called different things (namespaces, multi-tenancy, etc). For vectorstores, this is generally exposed as a keyword argument that is passed in during similarity_search.

- Step 2: Add that parameter as a configurable field for the chain. This will let you easily call the chain and configure any relevant flags at runtime.

- Step 3: Call the chain with that configurable field

## Parent Document Retriever

When splitting documents for retrieval, there are often conflicting desires:

- You may want to have small documents, so that their embeddings can most accurately reflect their meaning. If too long, then the embeddings can lose meaning.
- You want to have long enough documents that the context of each chunk is retained.

**Context Enrichment**

The concept here is to retrieve smaller chunks for better search quality, but add up surrounding context for LLM to reason upon.
There are two options — to expand context by sentences around the smaller retrieved chunk or to split documents recursively into a number of larger parent chunks, containing smaller child chunks.

The `ParentDocumentRetriever` strikes that balance by splitting and storing small chunks of data. During retrieval, it first fetches the small chunks but then looks up the parent ids for those chunks and returns those larger documents.


# Content

- langchain-dense-x-retrieval: a notebook with the code from Langchain to build a propositional retrieval designed in the paper: ["Dense X Retrieval: What Retrieval Granularity Should We Use?"](https://arxiv.org/abs/2312.06648) by Tong Chen, Hongwei Wang, Sihao Chen, Wenhao Yu, Kaixin Ma, Xinran Zhao, Hongming Zhang, and Dong Yu from the University of Washington, Tencent AI Lab, University of Pennsylvania, and Carnegie Mellon University. Langchain provides a template in this [link](https://templates.langchain.com/new?integration_name=propositional-retrieval)

- Rerank-Fusion-Ensemble-Hybrid-Search: a notebook where we build a simple RAG chain using an Emsemble Retriever, Hybrid Search, and the Reciprocal Rerank Fusion, based on the [paper](https://plg.uwaterloo.ca/~gvcormac/cormacksigir09-rrf.pdf). 

- Multiquery-retrieval: in this notebook we show you how to use a multiquery retriever in a RAG chain.

- Per-user-retrieval: notebook implementing a per user retrieval in a RAG chain.

- parent-document-retriever: an example notebbok implementing a context enrichment strategy using Parent Document Retriever in Langchain

# License

Copyright 2023 Eduardo Muñoz

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
