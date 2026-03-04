# 👁️ Multimodal Vision AI Chatbot

<div align="center">

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Gemini](https://img.shields.io/badge/Gemini_2.5_Flash_Lite-4285F4?style=for-the-badge&logo=google&logoColor=white)
![Pillow](https://img.shields.io/badge/Pillow-Image_Processing-FFD43B?style=for-the-badge)
![API Resilience](https://img.shields.io/badge/Exponential_Backoff-Rate_Limit_Safe-00C853?style=for-the-badge)

**A multimodal AI system that accepts any image + natural language query and returns structured visual analysis.**  
Built with production-grade API resilience — handles rate limits gracefully with exponential backoff.

</div>

---

## ⚡ Results at a Glance

| Metric | Detail |
|---|---|
| 🤖 Model | `gemini-2.5-flash-lite` (Google GenAI SDK) |
| 🖼️ Image Preprocessing | Pillow thumbnail resize — max 1024×1024 before API call |
| 🔄 Retry Logic | Up to 5 attempts with exponential backoff (`2^n + 2` seconds) |
| 🎯 Capabilities | Object detection, OCR, scene reasoning — natively, no extra libraries |
| 📝 Input | Any image (JPEG, PNG) + plain English query |
| 🚫 External OCR | None needed — Gemini handles text extraction natively |

---

## 🧠 What This System Does

Most "vision AI" demos pipe an image into a model and return a single label. This system treats vision as a **conversational interface** — you can ask anything about any image in plain English:

```
Input:  sample_image.jpg  +  "What is happening in this image?
                               List all visible objects and any text you find."

Output: Structured analysis covering:
        → Object detection (20+ items identified in test case)
        → OCR (embedded watermarks and metadata extracted)
        → Scene-level reasoning (spatial composition, groupings, context)
```

No fine-tuning. No external OCR library. No custom detection model. The multimodal fusion happens natively inside `gemini-2.5-flash-lite`.

---

## 💻 Core Implementation

```python
def chat_with_vision(image_path, user_query):
    img = PIL.Image.open(image_path)
    img.thumbnail((1024, 1024))          # Resize before sending — reduces token cost

    for attempt in range(5):
        try:
            response = client.models.generate_content(
                model="gemini-2.5-flash-lite",
                contents=[user_query, img]  # Interleaved text + image payload
            )
            return response.text

        except exceptions.ResourceExhausted:
            wait = (2 ** attempt) + 2      # 4s → 6s → 10s → 18s → 34s
            print(f"Rate limit hit. Retrying in {wait}s...")
            time.sleep(wait)

    return "Error: Maximum retries exceeded due to rate limits."
```

The key design choice: **image and query are passed as a list** (`contents=[user_query, img]`). This is the interleaved multimodal payload format — Gemini processes both modalities jointly, not sequentially.

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Multimodal LLM | `gemini-2.5-flash-lite` (Google GenAI SDK) |
| Image Processing | Pillow (PIL) — open, resize, thumbnail |
| API Resilience | Custom exponential backoff on `ResourceExhausted` (429) |
| Error Handling | `google.api_core.exceptions` for typed exception catching |
| Language | Python 3.10+ |

---

## 🔄 Backoff Schedule

| Attempt | Wait Time | Formula |
|---|---|---|
| 1 | 4 seconds | `2^0 + 2` |
| 2 | 6 seconds | `2^1 + 2` |
| 3 | 10 seconds | `2^2 + 2` |
| 4 | 18 seconds | `2^3 + 2` |
| 5 | 34 seconds | `2^4 + 2` |

After 5 failed attempts, the function returns a clean error string rather than crashing — safe for integration into larger pipelines.

---

## 📊 Inference Results: Complex Scene Test

**Test image:** High-complexity still-life food composition — designed to stress-test detection breadth and OCR simultaneously.

| Input Image | Model Analysis |
|---|---|
| ![Input](images/sample_image.jpg) | ![Results](images/sample_result.png) |

**Findings:**
- **Object Detection:** 20+ distinct items correctly identified and categorized (raw proteins, grains, produce)
- **Native OCR:** Extracted `"gettyimages"` watermark and numerical metadata `861188910` — zero external libraries
- **Scene Reasoning:** Described spatial groupings and visual composition — not just labels, but context

---

## 🔑 Key Engineering Decisions

- **Why `img.thumbnail((1024, 1024))`?** Larger images consume significantly more tokens. Resizing to 1024×1024 before the API call reduces latency and cost with negligible quality loss for analysis tasks.
- **Why `contents=[user_query, img]` as a list?** This is the correct interleaved format for the `google-genai` SDK's multimodal endpoint — passing them separately would break the joint vision-language fusion.
- **Why `2^n + 2` backoff instead of `2^n`?** The `+2` offset ensures a minimum 4-second floor on the first retry, avoiding immediate re-hammering of a rate-limited endpoint.
- **Why catch `ResourceExhausted` specifically?** Typed exception handling lets other errors (network issues, invalid image format) surface immediately rather than being silently retried.

---

## 🚀 Quick Start

```bash
# 1. Clone
git clone https://github.com/Rahilshah01/multimodal-vision-chatbot.git
cd multimodal-vision-chatbot

# 2. Install
pip install google-genai pillow

# 3. Add your API key
# Get one at: https://aistudio.google.com/
# Replace "YOUR API_KEY" in main.py (or move to .env)

# 4. Add an image
# Place any .jpg or .png in the project root as sample_image.jpg

# 5. Run
python main.py
```

---

## 📁 Repository Structure

```
multimodal-vision-chatbot/
├── main.py              # Core vision pipeline
├── sample_image.jpg     # Test image
├── images/
│   ├── sample_image.jpg # Input used in demo
│   └── sample_result.png # Terminal output screenshot
└── README.md
```

---

> 💡 **Note:** The API key is currently hardcoded in `main.py`. For any shared or production use, move it to a `.env` file and load with `python-dotenv`.

---

*Built by [Rahil Shah](https://rahil-shah-portfolio.vercel.app/) · MS Data Science @ Stevens Institute of Technology*
