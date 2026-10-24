# Lumina Vision

A full-stack neural image synthesis studio powered by React, FastAPI, OpenCV, and Natural Language Processing (NLP). Lumina Vision provides prompt enhancement, real-time computer vision post-processing filters, and image generation with a dark glassmorphic design system.

---

## Gallery Showcase

| Cyberpunk City | Majestic Dragon | Futuristic Ronin | Cosmic Nebula |
| :---: | :---: | :---: | :---: |
| ![Cyberpunk City](assets/gallery/cyberpunk.png) | ![Majestic Dragon](assets/gallery/dragon.png) | ![Futuristic Ronin](assets/gallery/samurai.png) | ![Cosmic Nebula](assets/gallery/galaxy.png) |

---

## Key Features

- **Dual User Interfaces**:
  - **Studio Web Application**: React 19, Vite, and Tailwind CSS v4 featuring real-time health pulse monitoring, preset gallery integration, custom dimensions, seed controls, and artifact download.
  - **Gradio Interactive App**: Standalone local interface with glassmorphic styling, seed controls, prompt expansion, and gallery vault.
- **NLP Prompt Enhancement**:
  - **Transformer Mode**: Expands visual concept prompts using `distilgpt2` via Hugging Face Transformers (CUDA GPU-accelerated when available, with resilient fallback).
  - **NLTK Keyword Mode**: Strips stopwords and structures prompt tokens using NLTK (`punkt`, `stopwords`) to boost prompt clarity.
- **Real-Time Computer Vision Filters**:
  - Post-processing powered by OpenCV (`Grayscale`, `Canny Edge Detection`, and `Gaussian Blur`).
- **Neural Synthesis Engine**:
  - Integrated cloud image synthesis powered by Pollinations AI with automatic exponential backoff retry handling.
- **Production Ready**:
  - Configured for decoupled deployment: frontend SPA on Vercel (`frontend/vercel.json`), backend FastAPI on Render/Heroku (`Procfile`, `requirements-render.txt`).

---

## Architecture & Tech Stack

```
                     +---------------------------------------+
                     |      React 19 + Vite Frontend         |
                     |  (Tailwind CSS v4, Lucide & Axios)    |
                     +-------------------+-------------------+
                                         |
                            REST API     |  (Health, Generate, Enhance)
                                         v
                     +---------------------------------------+
                     |         FastAPI Backend (main.py)     |
                     +---------+-------------------+---------+
                               |                   |
            +------------------+                   +------------------+
            v                                                         v
+-----------------------+                                 +-----------------------+
|  NLP Prompt Engine    |                                 | OpenCV Post-Processor |
|  - distilgpt2 (Torch) |                                 | - Grayscale / Canny   |
|  - NLTK Tokenizer     |                                 | - Gaussian Blur       |
+-----------+-----------+                                 +-----------+-----------+
            |                                                         |
            +------------------+                   +------------------+
                               v                   v
                     +---------------------------------------+
                     |        Pollinations AI Engine         |
                     +---------------------------------------+
```

### Stack Breakdown
- **Frontend**: React 19, Vite 7, Tailwind CSS v4, Axios
- **Backend**: Python 3.10+, FastAPI, Uvicorn, Pydantic
- **AI & NLP**: Hugging Face Transformers (`distilgpt2`), PyTorch, NLTK
- **Computer Vision**: OpenCV (`opencv-python` / `opencv-python-headless`), Pillow, NumPy
- **Alternative UI**: Gradio

---

## Project Structure

```
Lumina-Vision/
├── assets/
│   └── gallery/              # Curated preset preview images
├── frontend/
│   ├── public/
│   │   ├── assets/gallery/   # Static gallery presets for the web app
│   │   └── favicon.png       # Web app favicon
│   ├── src/
│   │   ├── App.jsx           # Main studio UI interface
│   │   ├── index.css         # Theme tokens and glassmorphic styling
│   │   └── main.jsx          # React DOM entrypoint
│   ├── index.html            # Web HTML shell & metadata
│   ├── package.json          # Node scripts and dependencies
│   ├── vercel.json           # Vercel SPA routing rewrite rules
│   └── vite.config.js        # Vite bundler configuration
├── app.py                    # Standalone Gradio desktop/web interface
├── main.py                   # FastAPI backend application
├── requirements.txt          # Complete backend dependencies
├── requirements-render.txt   # Lightweight backend dependencies for Render
├── Procfile                  # Uvicorn entrypoint for PaaS deployment
├── LICENSE                   # MIT License
└── README.md                 # Project documentation
```

---

## Prerequisites

- **Python**: Version 3.10 or higher
- **Node.js**: Version 18 or higher
- **npm**: Version 9 or higher

---

## Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/nfsprogramming/Lumina-Vision.git
cd Lumina-Vision
```

### 2. Backend Setup (FastAPI)

1. Create and activate a Python virtual environment:
   ```bash
   # Windows (PowerShell)
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1

   # Linux / macOS
   python3 -m venv .venv
   source .venv/bin/activate
   ```

2. Install backend dependencies:
   ```bash
   pip install -r requirements.txt
   ```
   *(For headless/lightweight servers without GPU/transformers, install `pip install -r requirements-render.txt`)*

3. Start the FastAPI backend server:
   ```bash
   python main.py
   ```
   The backend API will start on `http://localhost:8001`. Verify via `http://localhost:8001/health`.

### 3. Frontend Setup (React + Vite)

1. Navigate to the frontend directory:
   ```bash
   cd frontend
   ```

2. Install dependencies:
   ```bash
   npm install
   ```

3. Launch the development server:
   ```bash
   npm run dev
   ```
   The application will be accessible at `http://localhost:5173` (or the port displayed in your terminal).

### 4. Alternative: Standalone Gradio UI

To run the standalone Gradio web interface:
```bash
python app.py
```
Access the Gradio studio directly at `http://127.0.0.1:7860`.

---

## Configuration & Environment Variables

| Variable | Scope | Default | Description |
| :--- | :--- | :--- | :--- |
| `PORT` | Backend | `8001` | Server listening port |
| `POLLINATIONS_API_KEY` | Backend | None (optional) | Optional authorization token for Pollinations AI |
| `VITE_API_URL` | Frontend | `http://localhost:8001` | Backend API URL for the React client |

---

## API Endpoints

### `GET /health`
Returns connection status for client polling.
```json
{
  "status": "online"
}
```

### `POST /enhance`
Enriches a prompt using NLP.
- **Request Body**:
  ```json
  {
    "prompt": "futuristic samurai"
  }
  ```
- **Response**:
  ```json
  {
    "enhanced": "futuristic samurai, ultra-detailed digital masterpiece, 8k resolution..."
  }
  ```

### `POST /generate`
Synthesizes and applies computer vision filters to the output image.
- **Request Body**:
  ```json
  {
    "prompt": "A cyberpunk city in the rain, neon lights, 8k",
    "nlp_mode": "Transformer (Creative)",
    "filter": "Canny Edge",
    "width": 1024,
    "height": 1024,
    "seed": 42
  }
  ```
- **Response**:
  ```json
  {
    "image": "data:image/jpeg;base64,...",
    "final_prompt": "...",
    "logs": "Enhanced prompt: ..."
  }
  ```

---

## Testing & Validation

```bash
# Check Python syntax and compilation
python -m py_compile main.py app.py

# Run ESLint on the frontend
npm --prefix frontend run lint

# Build production bundle for frontend
npm --prefix frontend run build
```

---

## Contributing

1. Fork the repository.
2. Create your feature branch (`git checkout -b feature/amazing-feature`).
3. Commit your changes (`git commit -m 'Add amazing feature'`).
4. Push to the branch (`git push origin feature/amazing-feature`).
5. Open a Pull Request.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
