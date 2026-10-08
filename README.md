# 🤖 Three-Agent Model Development, Validation & Audit

A Python notebook that simulates a model review conversation between a **Model Developer**, **Model Validator**, and **Model Auditor** using a supplied Model Development Document (MDD).

The developer and validator use OpenAI APIs, while the auditor runs locally through Ollama. Each agent receives a role-specific prompt and conversation history, with responses displayed as Markdown in the notebook.

Built with Python, the OpenAI SDK, Ollama, and Jupyter.

---

## 🚀 Features

### 📄 Document-Based Discussion
- Reads `Marketing_Model_Development_Document.txt` as UTF-8 text.
- Includes the document in the initial developer message, making it available in each agent's history.
- Instructs the developer to answer from the document and acknowledge missing information.

### 🧑‍💻 Model Developer
- Explains the model development document.
- Responds to validator questions and auditor messages included in its context.
- Uses a prompt requesting concise answers.

### 🔍 Model Validator
- Questions the developer's methodology and the development document.
- Responds to auditor questions and is instructed to defer uncertain points to the developer.
- Is prompted to ask at most two questions per turn.

### 🧾 Model Auditor
- Challenges the validator and highlights potential gaps in the document or review.
- Uses a local `llama3.2` model through an OpenAI-compatible Ollama endpoint.
- Is prompted to ask at most two questions per turn.

### 🔄 Sequential Conversation
- Runs five rounds by default.
- Generates responses in the order **Developer → Validator → Auditor**.
- Keeps separate message lists for each agent.
- Displays each response with a labeled Markdown heading.

---

## 🧩 Tech Stack

| Component | Tool / configuration |
|---|---|
| Programming language | Python |
| Developer client | OpenAI SDK; configured model: `gpt-5.4-nano` |
| Validator client | OpenAI SDK; configured model: `gpt-5-nano` |
| Auditor client | OpenAI SDK connected to Ollama; model: `llama3.2` |
| Local endpoint | `http://localhost:11434/v1` |
| API key configuration | `python-dotenv` and `.env` |
| Response display | `IPython.display.Markdown` |
| Execution interface | Jupyter notebook / JupyterLab |

The OpenAI model names above reproduce the supplied code. Configure model IDs available to your account before running it.

---

## 🛠️ Installation

### 1. Prepare the project files

Save the supplied code in a notebook, for example `model_review.ipynb`, and place these files in the same project folder:

| File | Purpose |
|---|---|
| `model_review.ipynb` | Your three-agent conversation code; suggested filename |
| `Marketing_Model_Development_Document.txt` | Document to review; required filename in the code |
| `environment.yml` | Conda environment definition |
| `.env` | OpenAI API key |

### 2. Set up Python dependencies

Using the accompanying Conda configuration:

```bash
conda env create -f environment.yml
conda activate model-review-agents
```

Alternatively, activate your existing virtual environment and install:

```bash
python -m pip install openai python-dotenv ipython jupyterlab ipykernel
```

### 3. Configure your API key

Create a `.env` file containing:

```dotenv
OPENAI_API_KEY=your_api_key_here
```

Add `.env` to your `.gitignore` before committing the project. The notebook loads the key with `load_dotenv(override=True)`, so values from the file override existing environment variables.

### 4. Set up Ollama

Install [Ollama](https://ollama.com), then download the auditor model:

```bash
ollama pull llama3.2
```

Ensure Ollama is running at `localhost:11434`. If it is not already running, start its server:

```bash
ollama serve
```

The auditor's `api_key="ollama"` is a placeholder for the OpenAI-compatible client, not your OpenAI API key.

---

## ▶️ Usage

1. Add your model development document to the required text file.
2. Launch JupyterLab from the project folder:

   ```bash
   jupyter lab
   ```

3. Open your notebook and select the Python environment containing the installed packages.
4. Run the cells in order.
5. Review the labeled developer, validator, and auditor responses.

Change `range(5)` in the final loop to adjust the number of rounds. Five rounds produce **15 model requests**: ten to OpenAI and five to Ollama. OpenAI requests incur API usage charges.

To start a fresh conversation, rerun the cells that initialize the three message lists before rerunning the loop.

---

## 🔄 Message Flow

| Step in each round | Agent action | Context |
|---|---|---|
| 1 | Developer responds | Completed history, including the previous validator and auditor messages |
| 2 | Validator responds | Completed history plus the newly generated developer response |
| 3 | Auditor responds | Completed history plus the newly generated developer and validator responses |

The auditor's response becomes part of the developer's and validator's context in the next round. The first developer turn sees the document and introductory messages rather than substantive review questions.

---

## 📝 Current Limitations

- Agent roles and question limits are prompt instructions; the code does not enforce or verify them.
- All agents see the document through conversation history. There is no retrieval system, tool calling, or external evidence lookup.
- Auditor messages reach the developer directly through its history, even though the role prompt describes indirect questioning.
- The histories grow each round, increasing context usage. The `zip(...)` loops include only aligned entries; the validator and auditor functions append newer messages explicitly.
- The code has no retry handling, structured findings export, or final consolidated report.
- Responses are generated review suggestions and require human assessment before use in model risk decisions.

---

## 🔮 Possible Enhancements

- **Structured findings:** Capture the issue, document evidence, missing information, and proposed follow-up in a consistent schema.
- **Evidence references:** Require each agent to reference document sections supporting its statements.
- **Final review report:** Summarize unresolved questions and agreed findings after the last round.
- **Conversation persistence:** Export the discussion to Markdown or JSON.
- **Explicit message routing:** Route auditor questions through the validator when indirect developer communication is required.
- **Real use case MDD:** - This is a toy MDD. A real life use case MDD can be passed to analyze gaps through cross questioning.
- **Review interface:** Add a document upload and conversation viewer using Gradio or Streamlit.
