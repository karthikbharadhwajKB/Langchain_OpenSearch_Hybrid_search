# Langchain_OpenSearch_Hybrid_search

## Implementation of Hybrid Search 

I have taken LangChain's OpenSearch integration to the next level by adding hybrid search capabilities. Building on the existing OpenSearchVectorSearch class, I have implemented Hybrid Search functionality (which combines the best of both keyword and semantic search). This new functionality allows users to harness the power of OpenSearch's advanced hybrid search features without leaving the familiar LangChain ecosystem. By blending traditional text matching with vector-based similarity, the enhanced class delivers more accurate and contextually relevant results. It's designed to seamlessly fit into existing LangChain workflows, making it easy for developers to upgrade their search capabilities.

In implementing hybrid search for OpenSearch within the LangChain framework, I also incorporated filtering capabilities. It's important to note that according to the OpenSearch hybrid search documentation, only post-filtering is supported for hybrid queries. This means that the filtering is applied after the hybrid search results are obtained, rather than during the initial search process.

**Note:** For the implementation of hybrid search, I strictly followed the official OpenSearch Hybrid search documentation. 

**Step-1: Create Search Pipeline**

```python
opensearch_vectorstore.create_search_pipelines(
    pipeline_name="search_pipeline_keyword_0.3_vector_0.7",
    keyword_weight=0.3,
    vector_weight=0.7,
)
```

**Step-2: Hybrid Search**
```
query = "what are the country named in our database?"

top_k = 3

pipeline_name = "search_pipeline_keyword_0.3_vector_0.7"

matched_docs = opensearch_vectorstore.similarity_search_with_score(
                query=query,
                k=top_k,
                search_type="hybrid_search",
                search_pipeline = pipeline_name,
                vector_field="vector_field",
                text_field="text",
                metadata_field="metadata"
            )

matched_docs
```