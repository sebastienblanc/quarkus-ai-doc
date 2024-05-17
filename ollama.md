# Ollama

[Ollama](https://ollama.com/) provides a way to run large language models (LLMs) locally.
You can run many [models](https://ollama.com/library) such as LLama3, Mistral, CodeLlama and many others on your machine, with full CPU and GPU support.

## Prerequisites

To use Ollama, you need to have a running Ollama installed.
Ollama is available for all major platforms and its installation is quite easy, simply visit [Ollama download page](https://ollama.com/download) and follow the instructions.

Once installed, check that Ollama is running using:

```shell
> ollama --version
```

### Dev Service

Quarkus LangChain4j automatically handles the pulling of the models configured by the application, so there is no need for users to do so manually.

**⚠️ WARNING**\
Models are huge. For example Llama3 is 4.7Gb, so make sure you have enough disk space.

**📌 NOTE**\
Due to model’s large size, pulling them can take time

## Using Ollama

To integrate with models running on Ollama, add the following dependency into your project:

```xml
<dependency>
    <groupId>io.quarkiverse.langchain4j</groupId>
    <artifactId>quarkus-langchain4j-ollama</artifactId>
    <version>{project-version}</version>
</dependency>
```

If no other LLM extension is installed, [AI Services](../ai-services.adoc) will automatically utilize the configured Ollama model.

By default, the extension uses `llama3`, the model we pulled in the previous section.
You can change it by setting the `quarkus.langchain4j.ollama.chat-model.model-id` property in the `application.properties` file:

```properties
quarkus.langchain4j.ollama.chat-model.model-id=mistral
```

### Configuration

Several configuration properties are available:

If the Ollama server is running behind an authentication proxy, then the authentication header can easily be added to each HTTP request by adding
a [ClientRequestFilter](https://quarkus.io/guides/rest-client#customizing-the-request) like so:

```java
@Provider
public class OllamaClientAuthHeaderFilter implements ClientRequestFilter {

    @Override
    public void filter(ClientRequestContext requestContext) {
        requestContext.getHeaders().add("Authentication", "Bearer someToken");
    }
}
```

This class is a CDI bean which gives the ability to utilize other CDI beans if necessary.

## Document Retriever and Embedding

Ollama also provides embedding models.
By default, it uses `nomic-embed-text`.

You can change the default embedding model by setting the `quarkus.langchain4j.ollama.embedding-model.model-id` property in the `application.properties` file:

```properties
quarkus.langchain4j.log-requests=true
quarkus.langchain4j.log-responses=true

quarkus.langchain4j.ollama.chat-model.model-id=mistral
quarkus.langchain4j.ollama.embedding-model.model-id=mistral
```

If no other LLM extension is installed, retrieve the embedding model as follows:

```java
@Inject EmbeddingModel model; // Injects the embedding model
```
