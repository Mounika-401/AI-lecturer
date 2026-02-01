# AI Lecturer

An end-to-end application that dynamically generates interactive lectures on-demand using large language models, text-to-speech, and AI-driven avatars. Designed to produce lecture notes, slides, quizzes, and synchronized avatar videos, AI Lecturer offers an engaging learning experience for students and educators alike.

---

## Table of Contents

* [Features](#features)
* [Architecture Overview](#architecture-overview)
* [Getting Started](#getting-started)
* [Usage](#usage)
* [Project Structure](#project-structure)
* [Customization](#customization)
* [Troubleshooting & FAQs](#troubleshooting--faqs)
* [Documentation](#Documentation)
* [License](#license)
* [Contact](#contact)

---

## Features

* **Lecture Generation**: Uses OpenAI API to generate structured lecture content in markdown/JSON.
* **Slide Creation**: Converts generated content into a Reveal.js presentation with custom theming (`moon` theme).
* **Text-to-Speech**: Supports multiple TTS engines (OpenAI TTS, ElevenLabs, Coqui, Bark) for natural-sounding voiceovers.
* **Avatar Video**: Renders AI avatar videos with synchronized lip-sync and gesture animation via EchoMimicV2 (Meng et al., 2024) [arXiv:2411.10061](https://arxiv.org/abs/2411.10061).
* **Interactive Quiz**: Automatically generates quizzes based on lecture content to assess student understanding.
* **Chatbot Tutor**: Prompt-driven chatbot that answers student questions on-topic and gracefully handles out-of-scope queries.
* **RAG Integration**: Optionally fetches content from ArXiv or other knowledge sources for enhanced reference material.

---

## Architecture Overview

```text
+-----------------+      +-----------------------+      +-----------------+
|  React Frontend | <--> |     FastAPI Backend   | <--> |   OpenAI API    |
+-----------------+      +-----------------------+      +-----------------+
         |                         |                           |
         v                         v                           v
    React UI              Lecture Generator             OpenAI Models
      (iframe)                    |                           |
                                   v                           
                             Avatar & TTS Pipeline            
                                   |                           
                                   v                           
                             Quiz & Chatbot Module            
```

---

## Getting Started

### Prerequisites

* **Backend**:

  * Python 3.10+
  * `pip` package manager
  * `ffmpeg` installed and on your PATH
  * OpenAI API key (for LLM and TTS)
* **Frontend**:

  * Node.js >=16.x and npm or yarn
* **Avatar Pipeline (Optional GPU)**:

  * CUDA-compatible GPU
  * PyTorch dependencies as specified in `echomimic_v2/requirements.txt`

### Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/<your-org>/ai-lecturer.git
   cd ai-lecturer
   ```

2. **Backend Setup**

   ```bash
   cd ai-lecturer/backend
   python -m venv venv
   source venv/bin/activate    # macOS/Linux
   venv\Scripts\activate     # Windows
   pip install -r requirements.txt
   ```

3. **Frontend Setup**

   ```bash
   cd ../frontend
   npm install                # or `yarn install`
   ```

4. **echomimic\_v2 Setup (Avatar-Enabled Lecture)**
   This folder hosts a standalone Gradio app for GPU-based avatar videos.

   ```bash
   cd ../../echomimic_v2
   pip install pip -U
   pip uninstall -y torch torchvision torchaudio
   pip install torch==2.5.1 torchvision==0.20.1 torchaudio==2.5.1 xformers==0.0.28.post3 --index-url https://download.pytorch.org/whl/cu124
   pip install torchao --index-url https://download.pytorch.org/whl/nightly/cu124
   pip install -r requirements.txt
   pip install --no-deps facenet_pytorch==2.6.0
   pip install huggingface-hub>=0.26.2
   pip install click>=8.1.7
   python download_models.py
   ```

5. **Configure API Key**

   * Open `ai-lecturer/backend/gradio_implementation.py` in your favorite editor.
   * At the top of the file, locate the `OPENAI_API_KEY` variable and set it to your OpenAI key:

     ```python
     OPENAI_API_KEY = "<YOUR_OPENAI_API_KEY>"
     ```

---

## Usage

1. **Start the FastAPI Backend & Gradio Interface**

   ```bash
   cd ai-lecturer/backend
   source venv/bin/activate  # if not active
   uvicorn main:app --reload  # serves API and Gradio on port 7861
   ```

2. **Launch the React Frontend**

   ```bash
   cd ../frontend
   npm start                 # Runs on port 3000
   ```

   * Sign up or log in via the React UI.
   * The lecture generator (Gradio) is embedded in an iframe.

3. **Run Avatar-Enabled Lecture App (Optional)**

   ```bash
   cd ../../echomimic_v2
   python app.py              # Standalone Gradio on default port
   ```

4. **Access the Applications**

   * React app: `http://localhost:3000`
   * Backend/Gradio (standalone): `http://localhost:7861`
   * Avatar Gradio: (port printed in console)

---

## Project Structure

```
├── ai-lecturer
│   ├── backend
│   │   ├── gradio_implementation.py  # Gradio workflows: topic input to video, quiz, chat
│   │   ├── main.py                  # FastAPI entry point
│   │   ├── requirements.txt         # Backend deps
│   │   └── .env.example
│   └── frontend
│       ├── public
│       ├── src
│       ├── package.json
│       └── ...                      # React app files
├── echomimic_v2                     # GPU-required avatar Gradio app
│   ├── app.py                       # Standalone Gradio entry
│   ├── download_models.py
│   ├── requirements.txt
│   └── ...                          # Model and pipeline files
└── README.md                        # This file
```

---

## Configuration

No separate `.env` file is needed. Simply edit the `OPENAI_API_KEY` variable in `backend/gradio_implementation.py` to include your OpenAI key.

\----------------------|----------------------------------------------------|
\| `OPENAI_API_KEY`     | API key for OpenAI (LLM & TTS)                     |
\| `ELEVENLABS_API_KEY` | API key for ElevenLabs TTS (optional)              |
\| `FFMPEG_PATH`        | Path to FFmpeg executable (if not in `$PATH`)      |

---

## Customization

* **Themes**: Modify Reveal.js theme under `frontend/src/assets/revealjs`.
* **TTS Engines**: Adjust TTS logic in `gradio_implementation.py` (backend).
* **Avatar Pipelines**: Configure in `echomimic_v2` folder for different models.
* **Auth & UX**: Update React components under `frontend/src` for flows or styling.

---

## Troubleshooting & FAQs

* **⚠️ React Build Issues**: Delete `node_modules` and rerun `npm install`.
* **⚠️ Avatar App Errors**: Confirm GPU drivers, CUDA version, and PyTorch/cu124 wheels.

## Documentation

- [User Guide](docs/user_doc.md): How to use the application and its features
- [Developer Guide](docs/developer_doc.md): Architecture, scaling, and optimization
  
## License

This project is released under the MIT License. See [LICENSE](LICENSE) for details.

---

## Acknowledgements & Citation

* **EchoMimicV2**: Rang Meng, Xingyu Zhang, Yuming Li, and Chenguang Ma. "EchoMimicV2: Towards Striking, Simplified, and Semi-Body Human Animation." arXiv:2411.10061 (2024).
@misc{meng2024echomimicv2,
  title={EchoMimicV2: Towards Striking, Simplified, and Semi-Body Human Animation},
  author={Rang Meng, Xingyu Zhang, Yuming Li, Chenguang Ma},
  year={2024},
  eprint={2411.10061},
  archivePrefix={arXiv}
}
* We acknowledge the Ant Group EchoMimic open-source project (https://github.com/antgroup/echomimic_v2) for their foundational avatar synthesis pipeline.

Happy lecturing! 📚
