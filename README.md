# Langchain_OpenSearch_Hybrid_search

## Implementation of Hybrid Search 

I have taken LangChain's OpenSearch integration to the next level by adding hybrid search capabilities. Building on the existing OpenSearchVectorSearch class, I have implemented Hybrid Search functionality (which combines the best of both keyword and semantic search). This new functionality allows users to harness the power of OpenSearch's advanced hybrid search features without leaving the familiar LangChain ecosystem. By blending traditional text matching with vector-based similarity, the enhanced class delivers more accurate and contextually relevant results. It's designed to seamlessly fit into existing LangChain workflows, making it easy for developers to upgrade their search capabilities.

In implementing hybrid search for OpenSearch within the LangChain framework, I also incorporated filtering capabilities. It's important to note that according to the OpenSearch hybrid search documentation, only post-filtering is supported for hybrid queries. This means that the filtering is applied after the hybrid search results are obtained, rather than during the initial search process.

**Note:** For the implementation of hybrid search, I strictly followed the official OpenSearch Hybrid search documentation. 

### Instructions to follow for Performing Hybrid Search:

**Step-1: Instantiating OpenSearchVectorSearch Class:**
```python
opensearch_vectorstore = OpenSearchVectorSearch(
    index_name=os.getenv("INDEX_NAME"),
    embedding_function=embedding_model,
    opensearch_url=os.getenv("OPENSEARCH_URL"),
    http_auth=(os.getenv("OPENSEARCH_USERNAME"),os.getenv("OPENSEARCH_PASSWORD")),
    use_ssl=False,
    verify_certs=False,
    ssl_assert_hostname=False,
    ssl_show_warn=False
)
```

**Parameters:**
1. **index_name:** The name of the OpenSearch index to use.
2. **embedding_function:** The function or model used to generate embeddings for the documents. It's assumed that embedding_model is defined elsewhere in the code.
3. **opensearch_url:** The URL of the OpenSearch instance.
4. **http_auth:** A tuple containing the username and password for authentication.
5. **use_ssl:** Set to False, indicating that the connection to OpenSearch is not using SSL/TLS encryption.
6. **verify_certs:** Set to False, which means the SSL certificates are not being verified. This is often used in development environments but is not recommended for production.
7. **ssl_assert_hostname:** Set to False, disabling hostname verification in SSL certificates.
8. **ssl_show_warn:** Set to False, suppressing SSL-related warnings.

**Step-2: Create Search Pipeline:**

To initiate hybrid search functionality, you need to create a search pipeline first. 

**Implementation Details:**

This method creates a search pipeline in OpenSearch that:
1. Normalizes the scores from both keyword and vector searches using the min-max technique.
2. Applies the specified weights to the normalized scores.
3. Calculates the final score using an arithmetic mean of the weighted, normalized scores.


**Parameters:**

* **pipeline_name (str):** A unique identifier for the search pipeline. It's recommended to use a descriptive name that indicates the weights used for keyword and vector searches.
* **keyword_weight (float):** The weight assigned to the keyword search component. This should be a float value between 0 and 1. In this example, 0.3 gives 30% importance to traditional text matching.
* **vector_weight (float):** The weight assigned to the vector search component. This should be a float value between 0 and 1. In this example, 0.7 gives 70% importance to semantic similarity.

```python
opensearch_vectorstore.create_search_pipelines(
    pipeline_name="search_pipeline_keyword_0.3_vector_0.7",
    keyword_weight=0.3,
    vector_weight=0.7,
)
```

**Step-3: Performing Hybrid Search:**

After creating the search pipeline, you can perform a hybrid search using the `similarity_search()` method (or) any methods that are supported by `langchain`. This method combines both `keyword-based and semantic similarity` searches on your OpenSearch index, leveraging the strengths of both traditional information retrieval and vector embedding techniques.

**parameters:**
* **query:** The search query string.
* **k:** The number of top results to return (in this case, 3).
* **search_type:** Set to `hybrid_search` to use both keyword and vector search capabilities.
* **search_pipeline:** The name of the previously created search pipeline.

```python
query = "what are the country named in our database?"

top_k = 3

pipeline_name = "search_pipeline_keyword_0.3_vector_0.7"

matched_docs = opensearch_vectorstore.similarity_search_with_score(
                query=query,
                k=top_k,
                search_type="hybrid_search",
                search_pipeline = pipeline_name
            )

matched_docs
```

## Implementation of Full-Text-Search: 

I have enhanced LangChain's OpenSearch integration by implementing full-text search capabilities within the OpenSearchVectorSearch class. This addition complements the existing vector search functionality, creating a more versatile and powerful search solution. By incorporating full-text search, users can now leverage both traditional keyword-based matching and semantic similarity within the same framework. This implementation allows for more precise and flexible querying, enabling users to find relevant documents based on exact text matches as well as conceptual similarity. The enhanced OpenSearchVectorSearch class seamlessly integrates this full-text search capability with the existing vector search features, providing a comprehensive search experience without requiring users to leave the LangChain ecosystem. This improvement significantly expands the search capabilities available to developers using LangChain, allowing for more nuanced and effective information retrieval across a wide range of use cases.

### Instructions to follow for Performing full-text-search:

**Step-1: Instantiating OpenSearchVectorSearch Class:**
```python
opensearch_vectorstore = OpenSearchVectorSearch(
    index_name=os.getenv("INDEX_NAME"),
    embedding_function=embedding_model,
    opensearch_url=os.getenv("OPENSEARCH_URL"),
    http_auth=(os.getenv("OPENSEARCH_USERNAME"),os.getenv("OPENSEARCH_PASSWORD")),
    use_ssl=False,
    verify_certs=False,
    ssl_assert_hostname=False,
    ssl_show_warn=False
)
```

**Parameters:**
1. **index_name:** The name of the OpenSearch index to use. It's retrieved from an environment variable "INDEX_NAME".
2. **embedding_function:** The function or model used to generate embeddings for the documents. It's assumed that embedding_model is defined elsewhere in the code.
3. **opensearch_url:** The URL of the OpenSearch instance. It's retrieved from an environment variable "OPENSEARCH_URL".
4. **http_auth:** A tuple containing the username and password for authentication. Both are retrieved from environment variables "OPENSEARCH_USERNAME" and "OPENSEARCH_PASSWORD".
5. **use_ssl:** Set to False, indicating that the connection to OpenSearch is not using SSL/TLS encryption.
verify_certs: Set to False, which means the SSL certificates are not being verified. This is often used in development environments but is not recommended for production.
6. **ssl_assert_hostname:** Set to False, disabling hostname verification in SSL certificates.
7. **ssl_show_warn:** Set to False, suppressing SSL-related warnings.


**Step-2: Performing Full-text-search:**

After instantiation of the OpenSearchVectorSearch class, you can perform a `full-text search` using the `similarity_search()` method (or) any methods that are supported by `langchain`. This method allows you to execute keyword-based searches on your OpenSearch index, leveraging traditional information retrieval techniques.

**parameters:**
* **query:** The search query string.
* **k:** The number of top results to return (set to 3).
* **search_type:** Set to "full_text_search", indicating we want to use keyword-based search rather than vector search.

```python
query = "what are the country named in our database?"

top_k = 3

matched_docs = opensearch_vectorstore.similarity_search_with_score(
                query=query,
                k=top_k,
                search_type="full_text_search"
            )

matched_docs
```

 


