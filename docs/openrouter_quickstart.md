# Quick start: OpenRouter (AI models via one API)

[OpenRouter](https://openrouter.ai/) gives you one OpenAI-compatible API for models from many providers. Use it to add an LLM to your solution (for example for challenge 3) without needing your own GPU, provider account and/or making a lot of costs. **You don't have to use it**: any other model or provider is fine, as long as you [disclose it](../README.md#use-of-tools-and-generative-ai).

> [!NOTE]
> Good tutorials already exist, so this page only covers what you need to get going. The official docs are the source of truth: [Quickstart](https://openrouter.ai/docs/quickstart), [Limits](https://openrouter.ai/docs/api-reference/limits), [Models](https://openrouter.ai/models).

## 1. Get an API key
1. Create an account at [openrouter.ai](https://openrouter.ai/).
2. Create a key on the [Keys page](https://openrouter.ai/keys).

## 2. Pick a model
Browse the [Models page](https://openrouter.ai/models) and filter on **free** models (the model ID ends in `:free`). Copy the exact model ID.

- Free models are rate limited. **Share one key within your team sparingly, and keep your demo/jury run in mind.** See the [limits page](https://openrouter.ai/docs/api-reference/limits).
- Availability, limits and conditions of free models change. Check them before the hackathon day, and have a fallback model in mind.

## 3. Store the key safely
Create a `.env` file in your project (and add `.env` to `.gitignore`):

```
OPENROUTER_API_KEY=sk-or-...
```

Or set it in your terminal for the current session:

```powershell
# Windows PowerShell
$env:OPENROUTER_API_KEY = "sk-or-..."
```
```bash
# macOS / Linux
export OPENROUTER_API_KEY="sk-or-..."
```

## 4. Make your first call
Install the packages: `pip install openai python-dotenv`

```python
import os
from dotenv import load_dotenv
from openai import OpenAI

load_dotenv()  # reads .env
client = OpenAI(
    base_url="https://openrouter.ai/api/v1",
    api_key=os.environ["OPENROUTER_API_KEY"],
)

response = client.chat.completions.create(
    model="<exact model ID from the Models page>",
    messages=[{"role": "user", "content": "Explain PM2.5 in two sentences."}],
)
print(response.choices[0].message.content)
```

## Responsible use
- Prompts are sent to OpenRouter and the model provider, and free models may log or train on prompts. **Do not send secrets or personal information.** The hackathon data is open data, but do not add anything else that is sensitive.
- LLMs can be confidently wrong. Verify an agent's analysis against the underlying data and describe limitations in your audit trail.
- We encourage you to consider for user facing AI, which models you use from a responsible AI perspective and why. Take this with you in your submission.