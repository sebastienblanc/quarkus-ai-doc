# Redis Document Store for Retrieval Augmented Generation (RAG)

When implementing Retrieval Augmented Generation (RAG), a capable document store is necessary. This guide will explain how to leverage a Redis database as the document store.

## Leveraging the Redis Document Store

To utilize the Redis document store, you’ll need to include the following dependency:

```xml
<dependency>
    <groupId>io.quarkiverse.langchain4j</groupId>
    <artifactId>quarkus-langchain4j-redis</artifactId>
    <version>{project-version}</version>
</dependency>
```

This extension relies on the Quarkus Redis client. Ensure the default Redis datasource is configured appropriately. For detailed guidance, refer to the [Quarkus Redis Quickstart](https://quarkus.io/guides/redis) and the [Quarkus Redis Reference](https://quarkus.io/guides/redis-reference).

**❗ IMPORTANT**\
The Redis document store’s functionality is built on the Redis JSON and Redis Search modules. Ensure these modules are installed, or consider using the Redis Stack. When the `quarkus-langchain4j-redis` extension is present, the default image used for Redis is `redis-stack:latest` but this can be changed by setting `quarkus.redis.devservices.image-name=someotherimage` in your `application.properties` file.

**❗ IMPORTANT**\
The Redis document store requires the dimension of the vector to be set. Add the `quarkus.langchain4j.redis.dimension` property to your `application.properties` file and set it to the dimension of the vector. The dimension depends on the embedding model you use.
For example, `AllMiniLmL6V2QuantizedEmbeddingModel` produces vectors of dimension 384. OpenAI’s `text-embedding-ada-002` produces vectors of dimension 1536.

Upon installing the extension, you can utilize the Redis document store using the following code:

```java
```

## Configuration Settings

By default, the extension utilizes the default Redis datasource for storing and indexing the documents. Customize the behavior of the extension by exploring various configuration options:

## Under the Hood

Each ingested document is saved as a JSON document in Redis, containing the _embedding_ stored as a vector. The document store also generates an index for each ingested document. To retrieve relevant documents, the extension employs the Redis _search_ command.
