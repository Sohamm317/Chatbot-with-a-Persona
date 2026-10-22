# Persona Chat (V1)

A Flask chatbot that takes on any persona you describe. It runs on open-source models through an OpenAI-compatible API (Groq, OpenRouter, Hugging Face, Ollama, and others).

## Features

- Custom persona: your description becomes the system prompt
- Multi-turn memory: history is stored server-side and resent on every call
- History trimming: only the last 20 messages are sent to the model
- Basic prompt-injection defense: the persona is wrapped in tags and sits under fixed base rules
- Friendly error handling for rate limits, bad keys, timeouts, and provider errors
- Input limits: persona up to 1000 characters, messages up to 2000
- Responsive UI with quick-start personas, typing indicator, and dark mode

## Project structure

```
persona-chat/
├── app.py
├── test_api.py          # standalone API sanity check
├── .env                 # your secrets (not committed)
├── .gitignore
└── templates/
    └── index.html
```

## Requirements

- Python 3.9 or newer
- An API key from an OpenAI-compatible provider (for example Groq)

Python packages:

| Package | Used for |
|---|---|
| `flask` | Web server, routes, and sessions |
| `openai` | Client for OpenAI-compatible APIs (works with Groq, OpenRouter, Ollama, and others) |
| `python-dotenv` | Loading secrets from the `.env` file |

## Setup

Create and activate a virtual environment, then install the packages:

```bash
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install flask openai python-dotenv
```

Using Pipenv instead:

```bash
pipenv install flask openai python-dotenv
pipenv shell
```

Create a `.env` file:

```
FLASK_SECRET_KEY=your-random-string
LLM_API_KEY=your-provider-key
LLM_BASE_URL=https://api.groq.com/openai/v1
LLM_MODEL=exact-model-id-from-your-provider
```

- Generate a secret key: `python -c "import secrets; print(secrets.token_hex(32))"`
- Get the exact model ID from your provider's model list. Names and free tiers change often.
- Other base URLs: OpenRouter `https://openrouter.ai/api/v1`, Ollama `http://localhost:11434/v1`

## Run

Test the API connection first:

```bash
python test_api.py
```

Then start the app and open http://127.0.0.1:5000:

```bash
python app.py
```

## Routes

| Route | Method | Purpose |
|---|---|---|
| `/` | GET | Chat page |
| `/persona` | POST | Set persona and reset the conversation |
| `/chat` | POST | Send a message and get a reply |

## Known limitations

- Chat history is stored in memory, so it resets when the server restarts.
- Trimming counts messages, not tokens, so very long messages can still exceed the context window.
- Replies appear all at once (no streaming), and markdown is shown as plain text.
- Small open models may drift out of character in long chats or ignore injection defenses.
- Never commit `.env`. If a key leaks, regenerate it.

## Planned for V2

- Retry with exponential backoff on 429 errors
- Rate limiting with Flask-Limiter
- Token-based history trimming
- Persona builder returning schema-validated JSON (Pydantic)
- Saved personas in SQLite
- Streaming responses
