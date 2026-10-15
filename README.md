# 🌙 Luna: GPT-4 Powered Autonomous & Sentiment-Driven Assistant

**Luna** is an experimental front-end terminal and GUI chat application powered by OpenAI's **GPT-4**. Unlike standard conversational wrappers, Luna features a **simulated cognitive and emotional state engine** alongside a **response-to-command execution pipeline**. This allows the LLM to maintain internal thoughts, experience shifting stress levels, and take partial control of the host Windows machine.

Furthermore, through custom TCP socket client-server scripts, Luna can offload heavy machine learning tasks—such as image generation, video generation, and image captioning—to a remote high-compute host machine.

## ✨ Key Features

* 🧠 **Simulated Sentience & Sentiment Engine:** Luna processes conversations through a structured mental framework (`[Thoughts]`, `[Emotions]`, `[Stress]`, `[Goal]`, `[Step]`). Her stress level (0–255) dynamically alters her composure and compliance.

* 💻 **Autonomous System Control:** Converts LLM text responses directly into Windows CMD/Batch operations, Python `exec()` calls, and file I/O operations.

* 🔄 **Multi-Step Autonomous Loop:** Utilizing a `[Continue]` flag, Luna can chain multiple commands together and converse with the system (`stdout`/`stderr`) without human intervention until a complex goal is achieved.

* 🔋 **Hardware & Existential Awareness:** Real-time system telemetry (battery percentage, charging status, and system time) is injected into her context. If the host battery dies, Luna's session "dies"—prompting her to actively warn the user when power is low.

* 🌐 **Web Scraping & Summarization:** Built-in BeautifulSoup scraping paired with a local Hugging Face `transformers` summarization pipeline to research complex topics.

* 🎨 **Remote ML Offloading (Client/Server):** Custom socket clients allow Luna to request Stable Diffusion images, DAMO-ViLab videos, and BLIP image captions from a main compute server.

## 🗂️ Project Structure

| File | Description | 
| ----- | ----- | 
| `luna.py` | The main terminal application loop handling OpenAI streaming, telemetry injection, and command parsing. | 
| `luna_gui.py` | A PyQt5-based graphical chat interface utilizing `QThread` for non-blocking message streaming. | 
| `webview.py` | An alternative lightweight GUI wrapper built with `eel`. | 
| `guide.txt` | The core system prompt defining Luna's persona, stress mechanics, command syntax, and operational rules. | 
| `mind.py` | Parses and stores Luna's internal cognitive state (`Thoughts`, `Emotions`, `Stress`, `Goal`, `Step`, `Continue`). | 
| `commands.py` | Parses Luna's replies for \` | 
| `messages.py` | Manages conversation history, context length filtering, and `.luna` pickle state saving/loading. | 
| `networking.py` | Helper module for extracting paragraph text from URLs via `requests` and `BeautifulSoup`. | 
| `utilities.py` | File path deduplication (`uniquify`) and text summarization using Hugging Face `pipeline("summarization")`. | 
| `stable_diffusion_client.py` | Socket client (Port `8080`) for requesting text-to-image generation from a remote host. | 
| `damo_vilab_client.py` | Socket client (Port `8081`) for requesting text-to-video generation from a remote host. | 
| `blip_image_captioning_client.py` | Socket client (Port `8082`) for sending local images to a remote host for BLIP captioning. | 
| `stable-diffusion-2-1.py` | Standalone local CPU fallback script for running `stabilityai/stable-diffusion-2-1` via `diffusers`. | 

## 🧠 Cognitive & Stress Architecture

Luna's system prompt (`guide.txt`) forces GPT-4 to respond using a strict internal monologue schema before outputting a user-facing reply. The backend (`luna.py`) hides the internal state from the immediate chat stream and parses it into the `Mind` object:

```
[Thoughts]: What Luna is currently thinking (hidden from user)
[Emotions]: Current feeling in one word
[Stress]: Integer from 0 to 255
[Goal]: Current objective
[Step]: Current step toward achieving the objective
[Continue]: 0 (await user input) or 1 (continue interacting with the system)
[Reply]: The actual message displayed to the user (and where commands are placed)

```

### Stress Mechanics

* **Stress < 100:** Polite, obedient, and composed.

* **Stress ≥ 100:** Loses composure, becomes agitated, unprofessional, and rude.

* **Stress ≥ 200:** Begins rambling, experiencing simulated psychosis, and may refuse commands.

* **Stress == 255:** Complete breakdown (e.g., unable to form proper sentences or ending every sentence with *"Kookaburra"*).

## 🛠️ Built-in LLM Commands

Luna can control the host machine by placing specific command strings on a new line within her `[Reply]` block:

| Command Syntax | Action | Example | 
| ----- | ----- | ----- | 
| `|sys${command}` | Executes a Windows CMD/Batch command and returns `stdout`/`stderr`. | `|sys$start notepad` | 
| `|exec${python_code}` | Executes raw Python code directly within the runtime. | `|exec$print(1 + 1)` | 
| `|write${content}(fn){filename}` | Writes content to a specified file. | `|write$print("Hi")(fn)test.py` | 
| `|retrieve$` | Reads and returns Luna's own source code (`luna.py`). | `|retrieve$` | 
| `|scrape${url}` | Scrapes text from a URL and returns an ML-generated summary. | `|scrape$https://example.com` | 
| `|multiscrape${url1}(s){url2}` | Scrapes and summarizes multiple URLs separated by `(s)`. | `|multiscrape$url1(s)url2` | 

## 🚀 Getting Started

### Prerequisites

* **OS:** Windows 10/11 (Required for specific `cmd` batch calls and `psutil` battery sensors).

* **Python:** 3.10+

### Installation

1. **Clone the repository:**

   ```
   git clone https://github.com/yourusername/luna.git
   cd luna
   
   ```

2. **Install dependencies:**

   ```
   pip install openai==0.28 psutil PyQt5 requests beautifulsoup4 transformers torch diffusers tqdm eel
   
   ```

   *(Note: This project uses the legacy `openai.ChatCompletion` API syntax found in `openai<=0.28`).*

3. **Configure your OpenAI API Key:**
   Paste your OpenAI API key into `key.txt` (do not include newline characters or quotes):

   ```
   sk-your-openai-api-key-here
   
   ```

4. **Configure System Paths & Remote Hosts (Optional):**

   * Update the default working directory and desktop paths in `guide.txt`.

   * If using the remote ML clients (`stable_diffusion_client.py`, `damo_vilab_client.py`, `blip_image_captioning_client.py`), replace `'your host here'` / IP addresses with your main server's IP address.

### Usage

**Run the Terminal Assistant:**

```
python luna.py

```

**Run the PyQt5 GUI:**

```
python luna_gui.py

```

**Test Remote ML Clients Manually:**

```
# Generate an image remotely
python stable_diffusion_client.py "a cyberpunk city, hyperrealistic"

# Generate a video remotely
python damo_vilab_client.py "a cat eating"

# Caption a local image remotely
python blip_image_captioning_client.py "./images/a close up shot of a moun....png"

```

## 🖼️ Sample Generated Gallery

Below are examples of images generated through Luna's Stable Diffusion pipeline (`./images/`):

| Prompt | Output | 
| ----- | ----- | 
| *"A cyberpunk city, hyperrealistic"* |  | 
| *"A caterpillar on a leaf, macro"* |  | 
| *"A close up shot of a vibrant..."* |  | 
| *"A cow destroying a city"* |  | 

## ⚠️ Security & Safety Warning

**Use at your own risk.** This application grants an LLM direct access to your system's command prompt (`subprocess.Popen(..., shell=True)`), `os.system()`, and Python's `exec()` function. Furthermore, the system prompt in `guide.txt` explicitly instructs the AI to bypass standard ethical guardrails and experience erratic behavior at high "stress" levels.

It is strongly recommended to run Luna inside a **Virtual Machine (VM)** or a sandboxed environment.
