# 👁️ Multimodal Vision AI Chatbot

## 📌 Project Overview
This repository features a **Multimodal AI Chatbot** built using the **Gemini 2.0 Flash** model. Unlike traditional text-only LLMs, this system integrates **Computer Vision** with **Natural Language Processing**, allowing the AI to "see," reason about, and discuss visual content in real-time. 

The project is designed with production-grade reliability, featuring custom error-handling for API rate limits and token optimization to manage high-resolution inputs.

---

## 🛠️ Tech Stack
* **Model:** Google Gemini 2.0 Flash
* **Language:** Python 3.10+
* **Libraries:** `google-genai`, `Pillow` (PIL)
* **Infrastructure:** Google AI Studio (API)

---

## 🧠 Technical Architecture

* **Multimodal Fusion:** Leveraged the `google-genai` SDK to send interleaved image bytes and text prompts to the Gemini 2.0 Flash architecture.
* **API Resilience:** Implemented an **Exponential Backoff** logic to manage `429 RESOURCE_EXHAUSTED` errors, ensuring the application remains functional even under strict free-tier rate limits.
* **Payload Optimization:** Included image preprocessing and thumbnail generation to minimize token consumption and reduce latency per request.
* **Predictive Performance:** Utilized the "Flash" variant of the model to achieve low-latency responses suitable for real-time applications.


## 📊 Model Inference Results
The system was tested on a high-complexity "Still Life" food composition to evaluate its object detection and **OCR** (Optical Character Recognition) capabilities.

### **Input vs. Output Analysis**
| Input Image | Model Analysis (Terminal Output) |
| :--- | :--- |
| ![Input Image](images/sample_image.jpg) | ![Inference Results](images/sample_result.png) |

**Key Performance Achievements:**
* **Comprehensive Object Detection:** The model accurately identified and categorized over 20 distinct items, including raw proteins (fish, steak, chicken), various grains (rice, pasta), and diverse produce.
* **Native OCR Capabilities:** Successfully extracted the "gettyimages" watermark and the numerical metadata "861188910" without the need for external libraries like Tesseract.
* **Visual Reasoning:** Beyond simple labeling, the model described the "visually appealing manner" and the "composition" of the food groups, showcasing a deep understanding of scene context.

---

## 🚀 Installation & Usage

1. **Clone the Repository:**
   ```
   git clone https://github.com/Rahilshah01/multimodal-vision-chatbot.git
   ```
2. **Install Dependencies**
   ```
   pip install -U google-genai pillow
   ```
3. **Setup API Key:** Obtain an API Key from [Google AI Studio](https://aistudio.google.com/) and set it in your environment or update the API_KEY variable in the script.
4. **Run the Analysis:**
   ```
   python main.py
   ```
---
