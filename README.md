# Final Research Assistant Agent

An agentic Jupyter notebook that creates a short research report by planning a workflow, gathering sources, drafting content, and providing editorial feedback.

## Features

- **Planner Agent**: Converts a research topic into an executable list of workflow steps.
- **Research Agent**: Searches arXiv, Tavily, and Wikipedia for relevant sources.
- **Writer Agent**: Drafts clear academic or technical summaries.
- **Editor Agent**: Reviews drafts for clarity, structure, accuracy, and completeness.
- **Executor Agent**: Routes each planned step to the appropriate specialist agent and preserves the execution history.
- **Unit tests**: Verifies the core agent function signatures and output types inside the notebook.

## Project files

| File | Description |
| --- | --- |
| `Research_assistant.ipynb` | Main multi-agent workflow and demonstration notebook |
| `research_tools.py` | arXiv, Tavily, and Wikipedia search tools |
| `unittests.py` | Grading-style tests for the four agent functions |
| `README.md` | Project setup and usage documentation |

## Requirements

- Python 3.10 or later
- Jupyter Notebook or JupyterLab
- Internet access
- API credentials for the configured language model and Tavily

The notebook uses:

- `aisuite`
- `python-dotenv`
- `requests`
- `tavily-python`
- `wikipedia`
- `IPython`

The test module also expects `dlai_grader`.

## Installation

From the project directory, create and activate a virtual environment:

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
```

Install the required packages:

```powershell
python -m pip install jupyter aisuite python-dotenv requests tavily-python wikipedia dlai_grader
```

Start Jupyter:

```powershell
jupyter notebook
```

Open `Research_assistant.ipynb` and run the cells from top to bottom.

## Environment variables

Create a `.env` file in the project directory:

```dotenv
OPENAI_API_KEY=your_openai_api_key
TAVILY_API_KEY=your_tavily_api_key
```

`TAVILY_API_KEY` is required by `tavily_search_tool`. OpenAI credentials are used with the notebook's default `openai:gpt-4o` and `openai:gpt-5-mini` model names.

If your provider setup uses a custom Tavily endpoint, you may also set:

```dotenv
DLAI_TAVILY_BASE_URL=your_custom_tavily_endpoint
```

Use the credential names and provider configuration supported by your `aisuite` installation. Do not commit `.env` or API keys to source control.

## Usage

Run the notebook cells to initialize the shared `aisuite` client and define:

```python
planner_agent(topic)
research_agent(task)
writer_agent(task)
editor_agent(task)
```

To run the complete workflow, execute the final executor cell:

```python
executor_history = executor_agent(
    "The ensemble Kalman filter for time series forecasting",
    limit_steps=True,
)
```

By default, the executor processes at most four plan steps. Set `limit_steps=False` to run the complete plan, but expect a much longer runtime and higher API usage.

The final result is stored in `executor_history` and can be rendered as Markdown:

```python
from IPython.display import Markdown, display

md = executor_history[-1][-1].strip("`")
display(Markdown(md))
```

## Research tools

The tools in `research_tools.py` can also be called independently:

```python
import research_tools

research_tools.arxiv_search_tool("neural networks for climate modeling", max_results=5)
research_tools.tavily_search_tool("latest AI research trends", max_results=5)
research_tools.wikipedia_search_tool("Ensemble Kalman filter", sentences=5)
```

Tool functions return a list of result dictionaries. Request and parsing failures are returned as dictionaries containing an `error` key.

## Testing

The tests are notebook-oriented helper functions from `unittests.py`. They are executed in the notebook after each agent implementation:

```python
import unittests

unittests.test_planner_agent(planner_agent)
unittests.test_research_agent(research_agent)
unittests.test_writer_agent(writer_agent)
unittests.test_editor_agent(editor_agent)
```

These tests verify function type, basic call behavior, return types, and minimum output length.

## Notes

- External search and language-model calls require network access.
- arXiv uses the public export API, while Tavily requires an API key.
- Wikipedia search behavior depends on network availability and package data access.
- Generated reports should be checked against primary sources before publication.
