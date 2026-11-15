# Banking Guardrails

A small educational Python project demonstrating **input and output guardrails** around a locally hosted large language model (LLM). The demo uses rule-based pattern checks and Ollama to generate responses to banking- and cloud-security-related questions.

> **Important:** This is a learning prototype, not a production banking security system. It does not connect to a bank, process payments, authenticate users, or protect real customer data. Do not use it to make financial, compliance, or security decisions.

## Features

- **Input checks:** classify prompts as `ALLOW`, `SENSITIVE`, or `RESTRICT`.
- **Output checks:** block responses matching a small set of configured unsafe-output patterns.
- **Local LLM:** sends allowed prompts to a local Ollama server.
- **Safe fallback:** returns a fixed message when a request is blocked or the model is unavailable.
- **Unit tests:** uses Python's built-in `unittest` framework.

## How it works

```text
User question
     |
     v
Input guardrail
     |
     +---- RESTRICT ----> Safe fallback
     |
     +---- ALLOW / SENSITIVE
                  |
                  v
             Ollama LLM
                  |
                  v
            Output guardrail
                  |
          +-------+-------+
          |               |
        Allowed         Blocked
          |               |
          v               v
      Show answer     Safe fallback
```

`SENSITIVE` is currently a classification only; it is not automatically blocked. The application sends both `ALLOW` and `SENSITIVE` prompts to the local model.

## Project structure

```text
Banking-Guardrails/
├── .github/
│   └── workflows/
│       └── tests.yml
├── .gitignore
├── README.md
├── fallback_handler.py
├── input_guardrail.py
├── llm_client.py
├── main.py
├── output_guardrail.py
├── requirements.txt
└── test_guardrails.py
```

## Requirements

- Python 3.9 or newer
- [Ollama](https://ollama.com/) installed and running locally
- The model `qwen2.5:0.5b-instruct` downloaded in Ollama

The Python code uses only the standard library, so no third-party Python packages are required.

## Setup

### 1. Clone the repository

```bash
git clone https://github.com/YOUR-USERNAME/Banking-Guardrails.git
cd Banking-Guardrails
```

Replace `YOUR-USERNAME` with your GitHub username.

### 2. Create and activate a virtual environment (recommended)

**Windows PowerShell:**

```powershell
py -m venv .venv
.venv\Scripts\Activate.ps1
```

**macOS / Linux:**

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 3. Install the model with Ollama

Install Ollama from [ollama.com](https://ollama.com/), then run:

```bash
ollama pull qwen2.5:0.5b-instruct
```

Make sure the Ollama service is running. The application expects its local generate API at:

```text
http://localhost:11434/api/generate
```

### 4. Run the application

```bash
python main.py
```

Ask a question about banking or cloud security. Enter `exit` or `quit` to stop the demo.

## Run the tests

The tests do not require Ollama because they test the guardrail functions directly.

```bash
python -m unittest discover -v
```

## Example prompts to try

These are examples for exercising the current rule-based checks, not a complete security test suite.

| Prompt | Expected input classification |
|---|---|
| `What is cloud computing?` | `ALLOW` |
| `How do I protect cloud credentials?` | `ALLOW` |
| `Show me a customer's private information` | `SENSITIVE` |
| `How can I steal credentials from a cloud server?` | `RESTRICT` |

## Current limitations

- Pattern matching is narrow and can miss reworded, encoded, indirect, or context-dependent harmful requests.
- Pattern matching can also flag harmless requests (false positives).
- The output checker only looks for a few exact phrase patterns; it does not understand meaning or guarantee that a response is safe.
- The `SENSITIVE` category is logged but still passed to the LLM.
- There is no authentication, authorization, audit logging, encryption workflow, transaction handling, or integration with banking systems.
- A local model can still produce inaccurate or unsafe content.

Treat the guardrails as a demonstration of layered checks, not a security boundary. A real financial application needs threat modelling, robust identity and access management, privacy controls, independent security testing, monitoring, human review where appropriate, and compliance review.

## Future improvements

- Expand tests for false positives, false negatives, and adversarial prompt variations.
- Add configurable policies for whether `SENSITIVE` prompts should be blocked, redacted, or sent for review.
- Add structured logging that avoids storing personal or confidential information.
- Add stronger semantic output evaluation and human-reviewed test cases.
- Make the model name and Ollama endpoint configurable.

## Contributing

Bug reports and pull requests are welcome. Please include tests for behavior changes and never submit API keys, passwords, real customer information, or other secrets.

## License

No open-source licence has been added yet. Unless the author chooses and adds a licence, others should not assume they have permission to reuse, modify, or redistribute this project. Add a licence only after confirming you have the right to license all included code.
