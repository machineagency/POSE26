# jubiLLM

An AI coding assistant for [Science Jubilee](https://github.com/machineagency/science-jubilee) lab automation. You describe what you want your robot to do in plain English, and the assistant writes executable Python code directly in your Jupyter notebooks.

Science Jubilee is a powerful platform, but its Python API has a lot of moving parts — tool lifecycles, coordinate conventions, unit differences between tools, calibration prerequisites. jubiLLM encodes this domain knowledge so the AI assistant doesn't hallucinate generic robotics code — it writes code that actually works with *your* machine.

## Example Interaction

You open a notebook and tell the assistant:

> "I want to do serial dilution across all 4 rows, starting with 20/40/60/80% dye. Max 2.5 mL per well."

The assistant reads your lab config, checks the science-jubilee API, and writes the code directly into your notebook cells — with the correct units (mL for syringe), proper tool lifecycle (pickup → use → park), and safe Z movements.

## Choose Your AI Assistant

| | Continue.dev + Ollama | Claude Code |
|---|---|---|
| **Runs where** | Locally on your laptop | Cloud (Anthropic) |
| **Cost** | Free | Claude subscription |
| **Can browse source code** | Only with `@file` / `@codebase` | Yes, autonomously |
| **Code quality** | Variable — may hallucinate wrong APIs | Excellent — follows domain knowledge reliably |
| **Safety** | May generate unsafe GCode or wrong method names | Respects safety rules in CLAUDE.md |
| **Best for** | Tab autocomplete while typing | Complex workflows, debugging, anything safety-critical |

**Important:** Local models (even 14B) sometimes ignore the science-jubilee Python API and fall back to generic GCode or other robotics libraries (Opentrons, Hamilton) from their training data. This can produce code that looks reasonable but uses wrong method names, wrong units, or wrong coordinate conventions — which is dangerous with a physical robot. For any code that will actually run on your machine, we recommend Claude Code or reviewing local model output carefully against the `examples/` notebooks.

---

## Prerequisites

This guide assumes you've already followed the [installation instructions](../README.md#installation) in the main README. You should have:
- VS Code installed with Python and Jupyter extensions
- Both this repo and `science-jubilee` cloned side by side
- Python environment set up with science-jubilee installed

## Configure for your lab

Edit these files to match your physical setup:

- **`lab_config/deck_config.yaml`** — your machine's IP address, tool indices, and default slot layout
- **`lab_config/tool_config.yaml`** — calibration offsets for each tool

To find your tool indices, connect to your machine in Python and run:
```python
from science_jubilee.Machine import Machine
m = Machine(address='YOUR_IP')
print(m.configured_tools)
```

## Open the workspace

Open `jubiLLM.code-workspace` in VS Code (File → Open Workspace from File). This workspace includes both this repo and science-jubilee so the AI can access the source code.

---

## Option A: Continue.dev + Ollama (Local)

Runs entirely on your laptop with no cloud dependency. **Useful mainly for tab autocomplete** — as you type code in a notebook, the local model suggests completions based on what's on screen. For chat-based code generation (asking questions, writing new workflows), local models often hallucinate wrong APIs and should not be trusted without careful review against the `examples/` notebooks.

### 1. Install Ollama

Download from [ollama.com](https://ollama.com), install, and **launch the app** (it runs in the menu bar as a llama icon). Ollama must be running whenever you use Continue.

Pull a model based on your RAM:

| Available RAM | Model | Command |
|--------------|-------|---------|
| 8 GB | `qwen2.5-coder:3b` | `ollama pull qwen2.5-coder:3b` |
| 16 GB | `qwen2.5-coder:7b` | `ollama pull qwen2.5-coder:7b` |
| 32 GB+ | `qwen2.5-coder:14b` (recommended) | `ollama pull qwen2.5-coder:14b` |

Qwen is a code-focused model that works well for code completion. However, it has not been extensively tested with science-jubilee. **Always verify generated code against the `examples/` notebooks before running it on a Jubilee.**

### 2. Install Continue.dev

In VS Code, search "Continue" in the Extensions panel and install it. The Continue icon will show up in the sidebar.

### 3. Configure Continue to use Ollama

Continue reads its config from `~/.continue/config.yaml` (in your **home directory**, not the project folder). Copy the config from this repo into place:

```bash
cp continue-config.yaml ~/.continue/config.yaml
```

If you pulled a different model size, edit `~/.continue/config.yaml` and change the model name.

Then reload VS Code: `Cmd+Shift+P` → **"Developer: Reload Window"**.

You should now see your Qwen model in the model dropdown at the top of the Continue chat panel.

### 4. Using Continue.dev

**Tab autocomplete** is the main benefit of local models — it works inline as you type in notebooks and scripts, suggesting completions based on the code on screen. No setup needed beyond the steps above.

**Chat mode** (asking questions in the Continue sidebar) is less reliable with local models. They often ignore the science-jubilee API and hallucinate wrong method names or GCode commands. If you use chat, always start with `@file CLAUDE.md` to give it context, and verify any generated code against the `examples/` notebooks before running it on your machine.

### Optional: Claude API through Continue.dev

For better quality without switching to Claude Code:

1. Get an API key at [console.anthropic.com](https://console.anthropic.com)
2. Edit `~/.continue/config.yaml`
3. Replace `YOUR_ANTHROPIC_API_KEY` with your key
4. Switch to "Claude Sonnet 4.6 (Cloud)" in the model dropdown

---

## Option B: Claude Code

Claude Code can autonomously read files, edit notebooks, and browse the science-jubilee source code. It reads `CLAUDE.md` automatically for domain knowledge.

**Install:** Search "Claude Code" in the VS Code Extensions panel. You'll need a Claude subscription at [claude.ai](https://claude.ai).

Or install the CLI:
```bash
npm install -g @anthropic-ai/claude-code
```

**Use:** Open the workspace and start chatting. Describe what you want in plain English — Claude will read the relevant source files, understand your lab config, and write code directly into your notebook.

If you prefer not to use the workspace file:
```bash
cd jubiLLM
claude --add-dir ../science-jubilee
```

---

## Test Your Setup

Try these queries with either tool:

**Code understanding** — open [DemoOfAllTools.ipynb](../DemoOfAllTools.ipynb
) and ask:
> "What does the serial dilution section do? What parameter controls the dilution ratio?"

**Code generation** — ask:
> "Write a loop to photograph every well in a 96-wellplate in slot 3 using the camera on tool 1."

Note that this is not rigorously verified. Please double-check the code jubiLLM generated before running it on the machine!

---

## Repo Structure

```
CLAUDE.md                    # Safety rules, conventions, gotchas for the AI
continue-config.yaml         # Continue.dev config — copy to ~/.continue/config.yaml
lab_config/
  deck_config.yaml           # Your machine's IP, tools, slot layout
  tool_config.yaml           # Calibration parameters for each tool
examples/                    # Tutorial notebooks organized by tool/topic
DemoOfAllTools.ipynb         # Full multi-tool demo notebook
```

## Customizing

- **New labware**: Create a JSON definition following the Opentrons format and place it in `science_jubilee/labware/labware_definition/`
- **New tool configs**: Copy an example from `science_jubilee/tools/configs/examples/` to `configs/user/` and edit
- **Add workflows**: Create new notebooks to do your lab automation work!
