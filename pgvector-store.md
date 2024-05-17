# Pgvector Document Store for Retrieval Augmented Generation (RAG)

When implementing Retrieval Augmented Generation (RAG), a capable document store is necessary. This guide will explain how to leverage a pgvector database as the document store.

## Leveraging the pgvector Document Store

To utilize the Redis document store, you’ll need to include the following dependency:

```xml
<dependency>
    <groupId>io.quarkiverse.langchain4j</groupId>
    <artifactId>quarkus-langchain4j-pgvector</artifactId>
    <version>{project-version}</version>
</dependency>
```

This extension will check for a default datasource, ensure you have defined at least one datasource. For detailed guidance, refer to the [CONFIGURE DATA SOURCES IN QUARKUS](https://quarkus.io/guides/datasource).

**❗ IMPORTANT**\
The pgvector store requires the dimension of the vector to be set. Add the `quarkus.langchain4j.pgvector.dimension` property to your `application.properties` file and set it to the dimension of the vector. The dimension depends on the embedding model you use.
For example, `AllMiniLmL6V2QuantizedEmbeddingModel` produces vectors of dimension 384. OpenAI’s `text-embedding-ada-002` produces vectors of dimension 1536.

Upon installing the extension, you can utilize the pgvector store using the following code:

```java
```

## Configuration Settings

Customize the behavior of the extension by exploring various configuration options:

## Under the Hood

Each ingested document is saved as a row in a Postgres table, containing the embedding column stored as a vector.
