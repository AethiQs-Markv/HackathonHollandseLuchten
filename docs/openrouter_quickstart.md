
## openrouter and other API keys

### Quick start: test an agent with OpenRouter
Oprouter free offers 50 requests/day. Use this if needed for testing and demoing your solution.
OpenRouter provides an OpenAI-compatible API that can connect an agent to models from different providers.

1. Create an account at [OpenRouter](https://openrouter.ai/).
2. Create an API key on the [Keys page](https://openrouter.ai/keys). Treat the key like a password: store it in an environment variable or a local `.env` file, and never commit it to Git or share it publicly.
3. Choose a model marked as free on the [Models page](https://openrouter.ai/models). Free models and their availability, rate limits, and usage conditions can change; check the current details before testing.
4. In your agent framework, select the OpenAI-compatible provider and configure the API key, base URL, and chosen model:

	| Setting | Value |
	|---|---|
	| API key | Your `OPENROUTER_API_KEY` value |
	| Base URL | `https://openrouter.ai/api/v1` |
	| Model | The exact model ID shown on OpenRouter |

Start with a small test prompt and check the model's current usage limits. Requests are sent to OpenRouter and the selected model provider, so do not include secrets, personal information, or other sensitive data. A free model may have limited capacity or may not be suitable for every task; verify an agent's analysis against the underlying data and include its limitations in the audit trail.