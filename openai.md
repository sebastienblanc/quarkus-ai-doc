# OpenAI

OpenAI stands as a pioneering AI research organization, famous for its groundbreaking Large Language Models (LLMs) like GPT-3 and GPT-4, setting new benchmarks in natural language understanding and generation.

OpenAI’s LLMs offer extensive support for:

* Tools facilitating seamless interaction between the LLM and applications.
* Document retrievers enabling the transmission of pertinent content to the LLM.

## Using OpenAI Models

To employ OpenAI LLMs, integrate the following dependency into your project:

```xml
<dependency>
    <groupId>io.quarkiverse.langchain4j</groupId>
    <artifactId>quarkus-langchain4j-openai</artifactId>
    <version>{project-version}</version>
</dependency>
```

If no other LLM extension is installed, [AI Services](ai-services.adoc) will automatically utilize the configured OpenAI model.

### Configuration

Configuring OpenAI models mandates an API key, obtainable by creating an account on the OpenAI platform.

The API key can be set in the `application.properties` file:

```properties
quarkus.langchain4j.openai.api-key=sk-...
```

**💡 TIP**\
Alternatively, leverage the `QUARKUS_LANGCHAIN4J_OPENAI_API_KEY` environment variable.

Several configuration properties are available:

## Document Retriever

When utilizing OpenAI models, the recommended practice involves leveraging the `OpenAiEmbeddingModel`. If no other LLM extension is installed, retrieve the embedding model as follows:

```java
@Inject EmbeddingModel model; // Injects the OpenAIEmbeddingModel
```

**❗ IMPORTANT**\
The `OpenAIEmbeddingModel` transmits the document to OpenAI for embedding computation.

## Azure OpenAI

Applications can leverage the [Azure’s](https://learn.microsoft.com/en-us/azure/ai-services/openai/overview) version of OpenAI services simply by using the `quarkus-langchain4j-azure-openai` extension instead of the `quarkus-langchain4j-openai` extension.

When this extension is used, the following configuration properties are required:

```properties
quarkus.langchain4j.azure-openai.resource-name=
quarkus.langchain4j.azure-openai.deployment-name=

# And one of the below depending on your scenario
quarkus.langchain4j.azure-openai.api-key=
quarkus.langchain4j.azure-openai.ad-token=
```

In the case of Azure, the `api-key` and `ad-token` properties are mutually exclusive. The `api-key` property should  be used when the Azure OpenAI service is configured to use API keys, while the `ad-token` property should be used when the Azure OpenAI service is configured to use Azure Active Directory tokens.

In both cases, the key will be placed in the Authorization header when communicating with the Azure OpenAI service.

## Advanced usage

`quarkus-langchain4j-openai` and `quarkus-langchain4j-azure-openai` extensions use a REST Client under the hood to make the REST calls required by LangChain4j.
This client is however available for use in a Quarkus application in the same way as any other REST client.

An example usage is the following:

```java
import java.net.URI;
import java.net.URISyntaxException;

import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;

import org.jboss.resteasy.reactive.RestStreamElementType;

import dev.ai4j.openai4j.chat.ChatCompletionChoice;
import dev.ai4j.openai4j.chat.ChatCompletionResponse;
import dev.ai4j.openai4j.chat.Delta;
import dev.ai4j.openai4j.chat.Message;
import dev.ai4j.openai4j.completion.CompletionChoice;
import dev.ai4j.openai4j.completion.CompletionResponse;
import io.quarkiverse.langchain4j.openai.OpenAiRestApi;
import io.quarkus.rest.client.reactive.QuarkusRestClientBuilder;
import io.smallrye.mutiny.Multi;


@Path("restApi")
@ApplicationScoped
public class QuarkusRestApiResource {

    private final OpenAiRestApi restApi;
    private final String token;

    public QuarkusRestApiResource() throws URISyntaxException {
        this.restApi = QuarkusRestClientBuilder.newBuilder()
                .baseUri(new URI("https://api.openai.com/v1/"))
                .build(OpenAiRestApi.class);
        this.token = "sometoken";

    }

    @GET
    @Path("language/streaming")
    @RestStreamElementType(MediaType.TEXT_PLAIN)
    public Multi<String> languageStreaming() {
        return restApi.streamingCompletion(
                createCompletionRequest("Write a short 1 paragraph funny poem about Enterprise Java"), token, null)
                .map(r -> {
                    if (r.choices() != null) {
                        if (r.choices().size() == 1) {
                            CompletionChoice choice = r.choices().get(0);
                            var text = choice.text();
                            if (text != null) {
                                return text;
                            }
                        }
                    }
                    return "";
                });
    }
}
```

This example allows for streaming OpenAIs response back to the user’s browser as Server Sent Events.

<dl><dt><strong>📌 NOTE</strong></dt><dd>

We used `null` as the `apiVersion` parameter in the call to `streamingCompletion` as this parameter is optional when using the vanilla OpenAI APIs.
This parameter is only required when using Azure OpenAI.
</dd></dl>
