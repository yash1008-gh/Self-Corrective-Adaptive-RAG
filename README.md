# Self-Corrective Adaptive RAG

A practice implementation of an advanced Retrieval-Augmented Generation (RAG) system that dynamically decides how to handle a query and evaluates its own intermediate results before producing a final answer.

Unlike a basic `retrieve → generate` pipeline, this system uses routing, document grading, query rewriting, web search, and answer validation to make the retrieval process more reliable.

The project was initially developed as a notebook and later converted into a Python script using LangChain and LangGraph.

## Overview

The system first determines whether a question is best answered using the local knowledge base or an external web search.

For questions related to the knowledge base, relevant documents are retrieved from a FAISS vector store and passed through a relevance grader. If the retrieved documents are not useful, the query is rewritten and retrieval is attempted again.

After generating an answer, the system performs another evaluation to check whether the response is supported by the retrieved information and whether it actually answers the original question.

This creates a feedback loop rather than blindly trusting the first retrieval and generation attempt.

## Key Features

### Query Routing
An LLM-based router decides between:

- **Vector Store** for questions related to the local knowledge base.
- **Web Search** for questions outside the available knowledge base.

### Document Grading
Each retrieved document is evaluated for relevance to the question. Irrelevant documents are filtered out before generation.

### Query Transformation
When retrieval fails to produce relevant documents, the original question is rewritten into a more retrieval-friendly version and sent through the retrieval process again.

### Web Search
Tavily is used when the router determines that external information is more appropriate than the local knowledge base.

### Context-Aware Generation
The final response is generated using the retrieved context rather than relying solely on the LLM's internal knowledge.

### Self-Correction
The generated answer is evaluated in two stages:

- **Hallucination Check:** Determines whether the answer is grounded in the retrieved documents.
- **Answer Check:** Determines whether the response actually addresses the user's question.

Depending on the result, the graph can retry generation or transform the query before trying again.

## Workflow

![alt text](image.png)