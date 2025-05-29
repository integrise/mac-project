# MAC Project Demo

This project demonstrates how to integrate some of the [MuleSoft AI Chain project](https://mac-project.ai/)'s modules and connectors for your Mule projects.

## In this Project

### Agentforce

Connect to your Agentforce Agent via API.

- [**Conversation**](https://mac-project.ai/docs/agentforce/supported-operations/conversation)
  - Start
  - Continue
  - End

### Einstein AI

Use the Einstein AI trust layer to make use of different models from Einstein.

- [**Agent**](https://docs.mulesoft.com/einstein-ai-connector/latest/configuring-agent-operations)
  - Define Prompt Template
- [**Chat**](https://docs.mulesoft.com/einstein-ai-connector/latest/configuring-chat-operations)
  - Answer Prompt
  - Generate From Messages
- [**Embeddings**](https://docs.mulesoft.com/einstein-ai-connector/latest/configuring-embeddings-operations)
  - Adhoc File Query
  - Generate From File
  - Generate From Text
- [**RAG**](https://docs.mulesoft.com/einstein-ai-connector/latest/configuring-rag-operations)
  - Adhoc Load Document

> [!TIP]
> **Agent**, **Chat** and **RAG** make use of LLMs to give an answer in natural language, whereas **Embeddings** will give numeric responses since no LLM is being used.
>
> **Tools - Use AI Service** operation is not included in this project.

### MuleSoft AI Chain

Build a unification layer for LLMs, vector stores (in-memory), and AI Services.

- [**Agent**](https://mac-project.ai/docs/mulechain-ai/supported-operations/agent)
  - Define Prompt Template
- [**Chat**](https://mac-project.ai/docs/mulechain-ai/supported-operations/chat)
  - Answer Prompt
  - Answer Prompt with Memory
- [**Embeddings**](https://mac-project.ai/docs/mulechain-ai/supported-operations/embeddings)
  - New Store
  - Add Document to Store
  - Add Folder To Store
  - Query From Store
  - Get Info from Store
- [**Image Generation**] (https://mac-project.ai/docs/mulechain-ai/supported-operations/image-generation)
  - Read
  - Generate
- [**RAG**](https://mac-project.ai/docs/mulechain-ai/supported-operations/rag)
  - Load Document
- [**Sentiment Analysis**] (https://mac-project.ai/docs/mulechain-ai/supported-operations/sentiment-analysis)
  - Analyzer
- [**Tools**] (https://mac-project.ai/docs/mulechain-ai/supported-operations/tools)
  - Use AI Service

### MuleSoft Inference

Long-term successor of MuleSoft AI Chain Connector

- [**Tools**] (https://mac-project.ai/docs/ms-inference/supported-operations/tools)
  - Use Native Templates
- [**Toxicity**] (https://mac-project.ai/docs/ms-inference/supported-operations/toxicity)
  - Detection by

### MuleSoft WebCrawler

A connector toolkit that provide web crawling capabilities to extract data from web pages.

- [**Crawl**] (https://mac-project.ai/docs/ms-webcrawler/supported-operations/crawl)
  - Website (Full Scan)
  - Get Sitemap
- [**Page**] (https://mac-project.ai/docs/ms-webcrawler/supported-operations/page)
  - Download Document
  - Download Image
  - Get Content
  - Get Insights
  - Get Meta Tags
- [**Search**] (https://mac-project.ai/docs/ms-webcrawler/supported-operations/search)
  - Google

### MuleSoft Whisperer

Support Speech-to-text and text-to-speech to enhance other MuleSoft AI Chain connectors.

- [**Speech to Text**] (https://mac-project.ai/docs/ms-whisperer/supported-operations/speech-to-text)
- [**Text to Speech**] (https://mac-project.ai/docs/ms-whisperer/supported-operations/text-to-speech)


# Prerequisites

- **Salesforce org** - Create a new dev account [here](https://www.salesforce.com/form/developer-signup/?d=pb)
- **Anypoint Platform account** - Create a free trial account [here](https://anypoint.mulesoft.com/)
- **MuleSoft IDE** - Anypoint Studio or Anypoint Code Builder
- Mule Runtime 4.9.x
- Maven 3.x
- Java 17
- **OpenAI API key** - Create OpenAI account [here](https://auth.openai.com/create-account), generate OpenAI API key.

## Project Structure

```
├── api-spec/         # API Spec files to be published in your Exchange
├── src/                  # Source code directory
├── postman/              # Postman collections + envs for testing
├── mule-artifact.json    # Mule runtime and Java versions
└── pom.xml               # Maven dependencies
```

## Salesforce Configuration (for Agentforce/Einstein AI)

### 1. Salesforce Connected App

> [!NOTE]
> Based on [these instructions](https://developer.salesforce.com/docs/einstein/genai/guide/agent-api-get-started.html).

1. Open your Salesforce org and to go **Setup**.
2. From setup, use **Quick Find** to search for **App Manager**.
3. Click **New Connected App** and then select **Create a Connected App**.
4. Specify a Connected App Name like `mac_project`.
5. Add your contact email. Make sure it's a real email, you'll need it later.
6. Check **Enable OAuth Settings** ✅
7. Callback URL: `https://login.salesforce.com`
8. Selected OAuth Scopes. **Do NOT select more scopes or you'll get a scope error**:
   - Access chatbot services (chatbot_api)
   - Access the Salesforce API Platform (sfap_api)
   - Manage user data via APIs (api)
   - Perform requests at any time (refresh_token, offline_access)
9. Deselect/select the following settings:
   - Require Proof Key for Code Exchange (PKCE) Extension for Supported Authorization Flows ◻️
   - Require Secret for Web Server Flow ◻️
   - Require Secret for Refresh Token Flow ◻️
   - Enable Client Credentials Flow ✅
   - Enable Authorization Code and Credentials Flow ◻️
   - Enable Token Exchange Flow ◻️
   - Enable Refresh Token Rotation ◻️
   - Issue JSON Web Token (JWT)-based access tokens for named users ✅
   - Introspect All Tokens ◻️
10. Save the app and click **Continue**.
11. Once you're in the **Manage Connected Apps** page, click **Manage**.
12. Click **Edit Policies**.
    - Permitted Users: **All users may self-authorize**
    - Enable Single Logout ◻️
    - Run As `<select your user, in my case, Alex Martinez>`
    - Issue JSON Web Token (JWT)-based access tokens ✅
    - Token Timeout: **30 Minutes**
13. Go back to the connected app and click **Manage Consumer Details**.
14. Add your verification code from your email.
15. Click **generate** and then **apply**.
16. Copy the consumer key/secret from the top. You will use these in MuleSoft.

### 2. Agentforce Setup

1. In Setup, go to **Einstein Setup** and turn on Einstein.
2. Wait a few seconds and **refresh the site**. You might not see the following options if you don't refresh.
3. Look for **Agentforce Agents** and turn on Agentforce.
4. Enable the Agentforce default Agent.
5. In **Agentforce Agents**, click on **New Agent**.
6. Create from a Template > **Agentforce Service Agent**
7. Remove all the topics to start from scratch
   - (optional) Add the Off Topic topic with the information from [off-topic.md](/topics/off-topic.md)
8. Add the following details:
    - Name: `Agentforce MuleSoft Agent`
    - API Name: `Agentforce_MuleSoft_Agent`
    - Description: `Use custom topics and actions to connect to your MuleSoft APIs.`
    - Role: `An AI customer service agent who connects to MuleSoft APIs for custom functionality.`
    - Company: `This company is used for demo purposes.`
9.  Don't add Data Cloud resources for this example.
10. Once the agent is created and you have it open, click **Connections**.
11. Click on **Add** to add a new connection.
12. Select **API**, add an integration name like `MAC Project Integration` and select the connected app you previously created (`mac_project`).
13. Once you add the connection, make sure to click on **Activate** at the top-right of the screen.

> [!IMPORTANT]
> The Mule API cannot call Agents that are deactivated. Make sure that it's **Active** in Salesforce. BUT anytime that you need to modify the agent, you have to deactivate it first.

## Mule Configuration

The project uses secure configuration properties for sensitive information. Make sure to configure the following:

- Create and publish an API Spec based on the files in `api-spec/`

  - Make sure to match your own **org ID** and RAML version in `pom.xml` and `mac-api.xml`

- `OPENAI_API_KEY` environment variable in your OS (if running locally)

- `properties.yaml` file under src/main/resources

- `secure-properties.yaml` file under src/main/resources
  
  - Configure your environment and Salesforce credentials:

    ```yaml
    domain: YOUR_SALESFORCE_ORG_DOMAIN
    clientId: YOUR_CONNECTED_APP_CLIENT_ID
    clientSecret: YOUR_CONNECTED_APP_CLIENT_SECRET
    ```

    > Your Client ID / Secret is the same from your Connected App's Consumer Key / Secret. You can obtain your Salesforce Org's domain from Salesforce Setup > **My Domain** > **Current My Domain URL**. For example: `orgfarm-6acf46e05z-dev-ed.develop.my.salesforce.com`

- Anypoint Platform credentials in your own `.m2/settings.xml` file

    ```xml
    <servers>
        <server>
            <username>your_AP_username</username>
            <password>your_AP_password</password>
            <id>anypoint-exchange-v3</id>
        </server>
    </servers>
    ```

## Running the project

The project includes Postman collections and environments for testing the APIs. You can find them in the `postman/` directory.

## Troubleshooting

- **Authentication errors:** Double-check your Client ID, Client Secret, and Connected App settings.
- **Scope errors:** Make sure the scopes in your Connected App are set up correctly. If you add more/less scopes than the mentioned in the settings here, you might get scope errors.
- **Network issues:** Ensure your Mule server can reach Salesforce.

## References

- [Salesforce OAuth Scopes Documentation](https://help.salesforce.com/s/articleView?id=sf.remoteaccess_oauth_scopes.htm)
- [Salesforce Agent API - Get Started](https://developer.salesforce.com/docs/einstein/genai/guide/agent-api-get-started.html)
- [Agentforce Supported Operations - Conversation](https://mac-project.ai/docs/agentforce/supported-operations/conversation)
- [MuleSoft Whisperer Getting Started] (https://mac-project.ai/docs/ms-whisperer/getting-started)
- [MuleSoft WebCrawler Getting Started] (https://mac-project.ai/docs/ms-webcrawler/getting-started)
- [MuleSoft AI Chain Getting Started] (https://mac-project.ai/docs/mulechain-ai/getting-started)
- [MuleSoft Inference Getting Started] (https://mac-project.ai/docs/ms-inference/getting-started)