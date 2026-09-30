# trinity-verse-ai-phi-3
Trinity Verse AI — A multilingual AI spiritual assistant that retrieves relevant Bible, Bhagavad Gita, and Quran verses and generates compassionate explanations using local open-source AI models without external LLM APIs.

Trinity Verse AI is an AI-powered spiritual assistant designed to help users find relevant verses from the **Bible, Bhagavad Gita, and Quran** based on their questions, emotions, or situations.

Instead of relying on paid external LLM APIs, the current version uses **open-source Hugging Face models locally** for semantic retrieval, response generation, and multilingual translation.

The system retrieves relevant spiritual verses, uses **Microsoft Phi-3 Mini** to generate a compassionate explanation, and uses **NLLB-200** for multilingual translation.

---

## 🌟 Key Features

* 📖 **Multi-scripture support**

  * Bible
  * Bhagavad Gita
  * Quran

* 🔎 **Semantic verse retrieval**

  * Finds verses relevant to the user's question rather than relying only on exact keyword matching.

* 🧠 **AI-generated explanations**

  * Uses Microsoft Phi-3 Mini to generate contextual and compassionate responses.

* 🌍 **Multilingual support**

  * Supports translation into multiple languages using NLLB-200.

* 🔐 **No external LLM API required**

  * The current AI inference pipeline runs using locally loaded open-source models.

* 🤖 **Embedding-based search**

  * Uses BGE embeddings to represent verses and user queries semantically.

* 💻 **Local model inference**

  * Models are loaded using Hugging Face Transformers.

* 🎯 **Context-aware responses**

  * Retrieved verses are supplied to the language model as context before generating the response.

---

# 🏗️ System Architecture

```text
                   ┌──────────────────────┐
                   │      User Query      │
                   │ "I feel hopeless"   │
                   └──────────┬───────────┘
                              │
                              ▼
                   ┌──────────────────────┐
                   │   BGE Embedding      │
                   │     Model            │
                   └──────────┬───────────┘
                              │
                              ▼
                   ┌──────────────────────┐
                   │ Semantic Retrieval   │
                   │ Relevant Verses      │
                   └──────────┬───────────┘
                              │
                              ▼
              ┌───────────────────────────────┐
              │ Bible / Gita / Quran Context │
              └───────────────┬───────────────┘
                              │
                              ▼
                   ┌──────────────────────┐
                   │   Microsoft Phi-3    │
                   │      Mini 4K         │
                   └──────────┬───────────┘
                              │
                              ▼
                   ┌──────────────────────┐
                   │ AI Generated         │
                   │ Spiritual Response   │
                   └──────────┬───────────┘
                              │
                              ▼
                   ┌──────────────────────┐
                   │    NLLB-200          │
                   │   Translation Model  │
                   └──────────┬───────────┘
                              │
                              ▼
                   ┌──────────────────────┐
                   │   Final Response     │
                   │   in User Language   │
                   └──────────────────────┘
```

---

# 🧠 Models Used

The current version uses three major AI components.

## 1. BGE Base English

**Model:**

```text
BAAI/bge-base-en
```

### Purpose

BGE is used for **semantic embeddings**.

The user's question and the available verses are converted into numerical vector representations. The system can then identify verses that are semantically related to the user's query.

For example:

```text
User:
"I am afraid about my future."

        ↓

BGE Embedding

        ↓

Find semantically related verses
```

This allows the system to retrieve relevant content even when the exact words do not appear in the verse.

---

# 2. Microsoft Phi-3 Mini

**Model:**

```text
microsoft/Phi-3-mini-4k-instruct
```

Phi-3 Mini is the primary **language generation model**.

It receives the user's question together with the retrieved spiritual context and generates the final explanation.

Conceptually:

```text
User Question
      +
Retrieved Verses
      ↓
   Phi-3 Mini
      ↓
Generated Explanation
```

The notebook uses the Hugging Face tokenizer and model-loading workflow for Phi-3.

The generation configuration includes a maximum of:

```python
max_new_tokens = 180
```

This limits the amount of newly generated text.

---

# 3. NLLB-200

**Model:**

```text
facebook/nllb-200-distilled-600M
```

NLLB-200 is used for **multilingual translation**.

The generated response can be translated into supported target languages so that users can interact with the system in their preferred language.

Conceptually:

```text
Phi-3 Generated Response
          ↓
       NLLB-200
          ↓
Translated Response
```

---

# 🔤 Are Tokens Used?

Yes.

Although Trinity Verse AI does **not require an external LLM API key**, the models still use **tokens internally**.

The tokenizer converts text into tokens before the models process it.

```text
Text
 ↓
Tokenizer
 ↓
Tokens
 ↓
AI Model
 ↓
Generated Tokens
 ↓
Tokenizer
 ↓
Text
```

For example, Phi-3 uses:

```python
AutoTokenizer.from_pretrained(...)
```

and tokenizes the prompt before passing it to the model.

### Important distinction

**Tokens ≠ API tokens.**

The project uses tokens as part of normal local model processing.

It does **not** mean that the project is sending requests to an external LLM API.

---

# 🚫 No External LLM API

One of the main changes in the current version is the move away from an API-dependent architecture.

### Previous approach

```text
Application
     ↓
External AI API
     ↓
Cloud LLM
     ↓
Response
```

### Current approach

```text
Application
     ↓
Local Hugging Face Models
     ↓
Phi-3 / BGE / NLLB
     ↓
Response
```

This means the AI inference pipeline does not require an OpenAI/Gemini/etc. LLM API key.

---

# 💻 Hardware

The notebook is configured to use a **GPU**, with the Colab configuration specifying an NVIDIA T4 GPU.

Because the project uses multiple transformer models, GPU acceleration can significantly improve inference speed compared with CPU-only execution.

---


# ⚙️ Installation

## 1. Clone the repository

```bash
git clone https://github.com/YOUR-USERNAME/trinity-verse-ai.git
```

```bash
cd trinity-verse-ai
```

## 2. Create a virtual environment

```bash
python -m venv venv
```

### Windows

```bash
venv\Scripts\activate
```

### Linux / macOS

```bash
source venv/bin/activate
```

## 3. Install dependencies

```bash
pip install -r requirements.txt
```

---

# 📦 Main Dependencies

The project uses the Python ecosystem around:

```text
torch
transformers
sentence-transformers
accelerate
numpy
pandas
scikit-learn
```

The exact requirements should match the packages actually imported by the final notebook/code.

---

# 🚀 Running the Project

Open the notebook:

```text
phi3.ipynb
```

and run the cells sequentially.

The models are loaded through Hugging Face's `from_pretrained()` mechanism.

The first execution may take longer because model files need to be downloaded and loaded.

---

# 🔄 How Trinity Verse AI Works

## Step 1 — User enters a question

Example:

```text
I am feeling scared and need strength.
```

---

## Step 2 — Query embedding

The question is converted into an embedding using:

```text
BAAI/bge-base-en
```

---

## Step 3 — Semantic retrieval

The system searches the available spiritual content and identifies relevant verses.

---

## Step 4 — Context construction

The retrieved verses are combined with the user's question.

Conceptually:

```text
Question
+
Relevant Verse 1
+
Relevant Verse 2
+
Relevant Verse 3
```

---

## Step 5 — Response generation

The context is provided to:

```text
microsoft/Phi-3-mini-4k-instruct
```

Phi-3 generates a natural-language explanation.

---

## Step 6 — Translation

If the user selects another language, the generated response is passed to:

```text
facebook/nllb-200-distilled-600M
```

---

## Step 7 — Final response

The user receives a contextualized spiritual response in the requested language.

---


# 🧩 Technologies Used

| Technology                | Purpose                                |
| ------------------------- | -------------------------------------- |
| Python                    | Main programming language              |
| PyTorch                   | Deep-learning framework                |
| Hugging Face Transformers | Loading and running transformer models |
| BGE                       | Semantic embeddings                    |
| Phi-3 Mini                | AI response generation                 |
| NLLB-200                  | Translation                            |
| Jupyter / Google Colab    | Development environment                |
| GPU / CUDA                | Accelerated inference                  |

---

# ⭐ Key Technical Concepts

This project demonstrates concepts from:

* Natural Language Processing
* Large Language Models
* Transformer architectures
* Embeddings
* Semantic Search
* Information Retrieval
* Retrieval-Augmented Generation (RAG)
* Text Generation
* Machine Translation
* Multilingual NLP
* Local AI inference

---

# 🧠 RAG-Based Architecture

Trinity Verse AI follows a retrieval + generation approach.

Instead of asking the language model to answer entirely from its internal knowledge, the system first retrieves relevant spiritual content.

```text
         User Question
               │
               ▼
        Query Embedding
               │
               ▼
       Semantic Retrieval
               │
               ▼
      Relevant Verse Context
               │
               ▼
            Phi-3
               │
               ▼
       Generated Response
```

This approach helps ground the generated response in retrieved source material.

---

# 🌍 Multilingual AI

The translation component allows the project to serve users beyond English.

The overall pipeline can be represented as:

```text
User Question
      ↓
Semantic Retrieval
      ↓
Relevant Verses
      ↓
Phi-3
      ↓
English Explanation
      ↓
NLLB-200
      ↓
Selected Language
```

---

# ⚠️ Limitations

Trinity Verse AI is an experimental AI project and should not be treated as a replacement for religious scholars, clergy, spiritual counselors, or authoritative religious texts.

Potential limitations include:

* AI-generated explanations may contain inaccuracies.
* Semantic retrieval may occasionally return imperfect matches.
* Translation quality can vary by language.
* Local model performance depends on available hardware.
* Large models may require substantial RAM/VRAM.
* Generated responses should be checked against the original source text.

---

# 🔮 Future Improvements

Possible future development includes:

* 🎯 Better verse-ranking algorithms
* 🗃️ Vector database integration
* 💬 Conversational memory
* 🌐 Web application interface
* 📱 Mobile application
* 🔊 Text-to-speech
* 🎤 Speech-to-text
* 🌍 More Indian languages
* 📚 Improved scripture metadata
* 🔍 Source citation for every retrieved verse
* ⚡ Model quantization for lower-end hardware
* 🧠 Improved RAG pipeline
* 📊 Evaluation metrics for retrieval and generation
* 🔐 Additional privacy controls

---

# 📌 Model Summary

| Component   | Model                              | Role                                |
| ----------- | ---------------------------------- | ----------------------------------- |
| Embedding   | `BAAI/bge-base-en`                 | Semantic representation & retrieval |
| Generation  | `microsoft/Phi-3-mini-4k-instruct` | Response generation                 |
| Translation | `facebook/nllb-200-distilled-600M` | Multilingual translation            |

---

# 📜 Disclaimer

Trinity Verse AI is an educational and experimental AI project.

The generated responses are AI-generated interpretations based on retrieved source material and should not be considered authoritative religious guidance.

Users should consult the original scriptures and qualified religious authorities for theological interpretation.

---

# 👩‍💻 Project

**Trinity Verse AI**

Built as an exploration of:

> **AI + Semantic Search + RAG + Multilingual NLP + Spiritual Text Retrieval**

The project demonstrates how open-source AI models can be combined to create a multilingual, context-aware spiritual assistant without depending on an external LLM API.

---

## ❤️ Acknowledgements

This project uses open-source models and libraries from the Hugging Face ecosystem and the broader open-source AI community.

Special acknowledgement to the developers and researchers behind:

* BGE
* Microsoft Phi-3
* NLLB-200
* PyTorch
* Hugging Face Transformers
* Jupyter / Google Colab

---
<img width="985" height="424" alt="image" src="https://github.com/user-attachments/assets/50510563-1e8d-4fc8-917e-568d88e00327" />
<img width="985" height="487" alt="image" src="https://github.com/user-attachments/assets/0820863d-d702-4eb0-9502-0dec6880c475" />
                                            
RETRIEVAL BGE MODEL 	
Metrics 	Value :- Average-0.84 , Max-0.845 , Min-0.835 

Explanation Metrics:- Models-Phi-3-Mini, Alignment-0.8255, Faithfulness-0.8599
Translation Metrics:- Combined score-0.8427, Semantic similarity-0.7802, Retrieval Time-26.45sec
