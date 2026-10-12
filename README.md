## How to run?

1. conda create -n llmapp python=3.14 -y 

2. conda activate llmapp 

3. pip install -r requirements.txt

## LangServe demo

With the project's `.venv` activated and `OPENAI_API_KEY` configured in `.env`:

```powershell
python -m pip install -r requirements.txt
python .\10_langserve_demo.py
```

Open `http://localhost:8000/docs` for the API documentation or
`http://localhost:8000/chain/playground/` to try the translator.
The `langserve[server]` dependency includes FastAPI and SSE support; Uvicorn
runs the server. Translation requests require OpenAI API credits.

## LangGraph web search setup

The project uses LangChain 1.4.4 and LangGraph 1.2.14, with compatible
connector versions pinned in `requirements.txt`. After installing the
requirements, restart the notebook kernel to avoid mixing old and new packages.
The agent example uses `from langchain.agents import create_agent` instead of
the deprecated `langgraph.prebuilt.create_react_agent`. Invoke the agent with
`{"messages": [...]}` and read the returned `messages` list.
JSON parser examples import from `langchain_core.output_parsers`, and Pydantic
schemas import `BaseModel` and `Field` directly from `pydantic`.

The Tavily search example in `11_langgraph_demo.ipynb` requires a separate
Tavily API key; an OpenAI key cannot be used for Tavily.
Create a key in your Tavily account and set `TAVILY_API_KEY` in your local
`.env` file. Do not share or save the key in notebook cells or output.
Run the environment setup cell first, then the search cell. The search cell
reloads `.env`, so it can pick up a newly added Tavily key.

## VS Code import snippets

Project snippets are defined in `.vscode/langchain.code-snippets`.
In a Python notebook code cell or a `.py` file, type a prefix without quotes
and press Tab. Alternatively, press Ctrl+Space and select the snippet.

- `lc-strparser`: `StrOutputParser` import and instance.
- `lc-passthrough`: `RunnablePassthrough` import.
- `lc-fewshot`: `ChatPromptTemplate` and `FewShotChatMessagePromptTemplate` imports.
- `lc-openai-completion`: `OpenAI` import (not `ChatOpenAI`).
- `lc-pydantic`: `BaseModel` and `Field` from Pydantic.
- `lc-rag-imports`: the chat model, prompt, text parser and passthrough imports together.

Existing prefixes, including `lc-openai`, `lc-jsonparser` and `lc-rag`, remain available.
Snippet prefixes are editor shortcuts, not executable Python code.

## OpenAI notebook setup

Create a new API key in your OpenAI project and set `OPENAI_API_KEY` in your
local `.env` file. Never share the key or save it in notebook cells.
The notebook reloads this file with `override=True` so an old key in the
kernel environment is replaced. Restart the `llmapp` kernel and run all cells
in order after changing the key; existing client objects retain their old key.

Both examples use `gpt-4o-mini` and the chat completions endpoint.
For LangChain, `response.content` gives the text. For the OpenAI SDK, use
`response.choices[0].message.content`.

An HTTP 401 `token_invalidated` means the key has been revoked and must be
replaced; reloading cannot reactivate it. API credits are required separately
from a ChatGPT subscription.

## Optional local model setup (Ollama)

As an alternative, `ChatOllama` with `llama3.2:1b` runs locally and does not
require an OpenAI API key or API credits.

1. Install Ollama on Windows:

   ```powershell
   winget install --id Ollama.Ollama --exact
   ```

2. Open Ollama from the Start menu. Open a new terminal after installation
   so that the `ollama` command is available, then download the model:

   ```powershell
   ollama pull llama3.2:1b
   ```

3. Replace the OpenAI examples with `ChatOllama(model="llama3.2:1b")` imported
   from `langchain_ollama`. Skip the API key setup cell. Select the `llmapp`
   Python kernel, restart it, and run your local-model cells in order.

Ollama must be running at `http://localhost:11434`. If it is not running,
start it from the Start menu or run `ollama serve` in a separate terminal.
If the model is not found, run the pull command above.
`ChatOllama.invoke()` returns a message; `.content` displays its text.

The Groq and Ollama packages remain available for other examples.
OpenAI's `credit_balance_exhausted` error
requires adding API credits; switching to this local model avoids that dependency.