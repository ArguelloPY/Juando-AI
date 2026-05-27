#  Local AI Environment & Juando Chatbot

A fully private, offline AI environment running open-source large language models (LLMs) on local hardware.
No subscriptions and no data leaving your machine. Includes a custom-built chatbot frontend called **Juando**.

---

##  Project Overview

This project replicates a self-hosted AI stack using free, open-weight models. It provides a ChatGPT-style browser interface, a custom AI chatbot (Juando), AI-assisted coding in VS Code, and an AI-powered terminal — all running locally on a Windows machine via WSL2 (Linux).

---

##  Tech Stack

| Tool | Purpose |
|------|---------|
| [WSL2](https://learn.microsoft.com/en-us/windows/wsl/) | Linux environment on Windows |
| [Ollama](https://ollama.com/) | Local LLM runtime and model manager |
| [Open WebUI](https://github.com/open-webui/open-webui) | Browser-based chat interface (self-hosted) |
| [Continue](https://www.continue.dev/) | AI coding assistant inside VS Code |
| [ShellGPT](https://github.com/TheR1D/shell_gpt) | AI assistant in the Linux terminal |
| HTML/CSS/JavaScript | Custom Juando chatbot frontend |

---

##  Models Used

| Model | Size | Purpose |
|-------|------|---------|
| [Qwen2 7B](https://ollama.com/library/qwen2) | 4.4 GB | Chat and coding base model |
| [Phi-3 Mini](https://ollama.com/library/phi3) | 2.2 GB | Fast shell assistant |
| [Mistral 7B Instruct](https://ollama.com/library/mistral) | 4.4 GB | Alternative fast model |
| [TranslateGemma 4B](https://ollama.com/library/translategemma) | 3.3 GB | Offline translation |
| [Nomic Embed Text](https://ollama.com/library/nomic-embed-text) | 274 MB | Vector embeddings for RAG |

---

##  Custom Model Aliases

Custom Ollama aliases were created with optimized parameters for each use case:

| Alias | Base Model | Use Case | Temperature |
|-------|-----------|----------|-------------|
| `chat-ai` | Qwen2 7B | General conversation | 0.5 |
| `code-ai` | Qwen2 7B | Code completion (FIM support) | 0.2 |
| `translate-ai` | TranslateGemma 4B | Faithful translation | 0.1 |
| `sh-ai` | Phi-3 Mini | Shell command generation | 0.1 |

---

## Architecture

```
Windows (Host)
│
├── VS Code + Continue Extension
│     └── connects to Ollama via WSL2 IP
│
├── Firefox — Juando chatbot (juando.html served on port 8888)
│     └── streams responses from Ollama API
│
└── WSL2 (Ubuntu)
      ├── Ollama (model server on 0.0.0.0:11434)
      │     ├── chat-ai
      │     ├── code-ai
      │     ├── translate-ai
      │     └── sh-ai
      ├── Open WebUI (browser UI on port 8080)
      └── ShellGPT (terminal AI assistant)
```

---

##  Setup

### Prerequisites
- Windows 10/11
- NVIDIA GPU with CUDA support
- WSL2 installed (`wsl --install` in PowerShell as Admin)

### 1. Install Ollama
```bash
curl -fsSL https://ollama.com/install.sh | sh
```

### 2. Pull Models
```bash
ollama pull qwen2:7b
ollama pull phi3:mini
ollama pull mistral:7b-instruct
ollama pull translategemma:4b
ollama pull nomic-embed-text
```

### 3. Create Model Aliases
```bash
ollama create chat-ai -f ~/.config/ollama/modelfiles/Modelfile.chat-ai
ollama create code-ai -f ~/.config/ollama/modelfiles/Modelfile.code-ai
ollama create translate-ai -f ~/.config/ollama/modelfiles/Modelfile.translate-ai
ollama create sh-ai -f ~/.config/ollama/modelfiles/Modelfile.sh-ai
```

### 4. Start Ollama (accessible from Windows)
```bash
sudo pkill ollama && OLLAMA_HOST=0.0.0.0:11434 ollama serve
```

### 5. Start Open WebUI
```bash
source ~/.venvs/openwebui/bin/activate && open-webui serve
```
Then open **http://127.0.0.1:8080** in your browser.

### 6. Start Juando Chatbot
```bash
cd ~ && python3 -m http.server 8888
```
Then open **http://localhost:8888/juando.html** in your browser.

### 7. ShellGPT
```bash
source ~/.venvs/shellgpt/bin/activate
sgpt "Your question here"
sgpt --shell "List files larger than 100MB"
```

### 8. VS Code + Continue
Install the [Continue extension](https://marketplace.visualstudio.com/items?itemName=Continue.continue) and configure `~/.continue/config.yaml` to point to your Ollama instance.

---

## 💡 Usage Examples

**Juando chatbot:**
Open `http://localhost:8888/juando.html`, select a model from the dropdown, and start chatting. Responses stream in real time from your local Ollama instance.

**Chat in browser (Open WebUI):**
Open `http://127.0.0.1:8080`, select a model, and start chatting.

**AI code assistant in VS Code:**
- `Ctrl+L` — open AI chat about your code
- `Ctrl+I` — edit selected code with AI

**AI in terminal:**
```bash
sgpt "Explain what this error means"
sgpt --shell "Find all log files modified in the last 24 hours"
```

**Translation:**
```bash
ollama run translate-ai "Translate to Spanish: Hello, how are you?"
cat README.md | ollama run translate-ai "Translate to French, keep markdown:"
```

---

## 🔒 Privacy

All models run entirely on local hardware. No data is sent to external servers. No accounts or API keys required.

---

## 📚 References

- [Ollama Documentation](https://ollama.com)
- [Open WebUI GitHub](https://github.com/open-webui/open-webui)
- [Continue Documentation](https://docs.continue.dev)
- [ShellGPT GitHub](https://github.com/TheR1D/shell_gpt)
