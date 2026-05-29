

# 💊 PrescriptAI

> Multimodal AI system that reads handwritten Indian prescriptions, identifies drugs, checks for dangerous combinations, and explains everything in plain English.

**B.Tech Major Project | Final Year | 2025–26**

---

## 🎯 Problem

- 80% of Indian prescriptions are handwritten and illegible to patients
- Patients miss dangerous drug interactions
- No existing system targets Indian brand names + handwriting

## 🚀 Demo

<img width="1470" height="883" alt="Screenshot 2026-05-29 at 4 41 09 PM" src="https://github.com/user-attachments/assets/3bd6e3d6-19bc-4c74-b7cc-e21194d886e9" />


Upload a prescription photo → AI reads it → explains in plain English → checks for dangerous drug combinations → reads aloud via TTS.

---

## 🏗️ Architecture

```
Prescription Image
        ↓
Stage 1: Vision Extraction    [Gemini 2.5 Flash]
        ↓ drug names (JSON)
Stage 2: RAG Correction       [Sentence Transformers + RapidFuzz]
        ↓ verified drug info
Stage 2.5: Interaction Check  [25 Rules + LLM Fallback]
        ↓ warnings
Stage 3: Explanation          [Gemini 2.5 Flash]
        ↓
Patient-friendly output + TTS
```

---

## ✨ Features

- 📷 **Handwriting OCR** — reads messy Indian doctor handwriting via Gemini Vision
- 🔍 **RAG-based correction** — semantic + fuzzy matching against 960-drug corpus
- ⚠️ **Drug interaction detection** — 25 rule-based checks for dangerous combinations
- 📝 **Plain-language explanation** — 8th-grade reading level output
- 🔊 **Text-to-Speech** — reads explanation aloud for elderly/low-literacy patients
- ♿ **Accessibility-first UI** — Atkinson Hyperlegible font, high contrast, large text
- 🇮🇳 **Indian-specific corpus** — 960 drugs including regional brands (Nadoxin, Saslic, Aziderm)

---

## 📊 Evaluation

Tested on 26 real handwritten prescriptions from [Kaggle public dataset](https://www.kaggle.com/datasets/mehaksingal/illegible-medical-prescription-images-dataset):

| Metric | Value |
|--------|-------|
| Hard match (≥0.55) | 53.6% (15/28) |
| Coverage (matched + low_conf) | 85.7% (24/28) |
| Average confidence score | 0.559 |
| Corpus size | 960 drugs |
| Drug interaction rules | 25 |

---

## 🛠️ Tech Stack

| Component | Technology |
|-----------|-----------|
| Vision OCR | Gemini 2.5 Flash |
| Semantic search | sentence-transformers/all-mpnet-base-v2 |
| Fuzzy matching | RapidFuzz (WRatio) |
| UI | Streamlit + ngrok |
| TTS | gTTS |
| Drug corpus | Custom JSON (960 Indian drugs) |
| Runtime | Google Colab (free tier) |

---

## 📁 Project Structure

```
prescriptai/
├── notebooks/
│   ├── 00_foundation_final.ipynb          # Gemini setup + key rotation
│   ├── 01_5_corpus_expansion_final.ipynb  # Wikipedia drug expansion
│   ├── 01_6_derm_brands.ipynb             # Indian derm brands
│   ├── 01_7_pediatric_kaggle_expansion.ipynb  # Pediatric + Kaggle drugs
│   ├── 03_llm_agents_final.ipynb          # RAG + LLM agent pipeline
│   ├── 03_5_interaction_checker.ipynb     # Drug interaction module
│   ├── 04_streamlit_ui.ipynb              # Streamlit GUI launcher
│   └── 05_evaluation_kaggle.ipynb         # Evaluation on Kaggle dataset
├── data/
│   ├── drugs/
│   │   ├── indian_drugs.json              # Main corpus (960 drugs)
│   │   └── derm_brands.json               # Derm brand additions
│   └── output/                            # Evaluation results
├── app.py                                 # Streamlit application
├── requirements.txt
└── README.md
```

---

## 🚀 Setup & Run

### Prerequisites

- Google Colab account
- Google Drive mounted
- 3 Gemini API keys (free tier from [Google AI Studio](https://aistudio.google.com))
- ngrok account + auth token ([ngrok.com](https://ngrok.com))

### Step 1: Clone & Setup

```bash
git clone https://github.com/YOUR_USERNAME/prescriptai.git
```

Upload to Google Drive at: `MyDrive/prescriptai/`

### Step 2: Configure API keys

Create `.env` file in `MyDrive/prescriptai/`:

```
GEMINI_API_KEY=AIzaSy...your_key_1
GEMINI_API_KEY_2=AIzaSy...your_key_2
GEMINI_API_KEY_3=AIzaSy...your_key_3
```

### Step 3: Run pipeline

Open notebooks in order:

```
00_foundation_final.ipynb      → verify Gemini connection
01_7_pediatric_kaggle_expansion.ipynb → build drug corpus + embeddings
04_streamlit_ui.ipynb          → launch GUI
```

### Step 4: Launch GUI

Run all cells in `04_streamlit_ui.ipynb`. Copy the ngrok URL printed in output. Open in browser.

---

## 📓 Module Overview

| Module | Description | Status |
|--------|-------------|--------|
| Module 0 | Foundation — Gemini setup, multi-key rotation | ✅ |
| Module 1 | Drug corpus — 800 base drugs | ✅ |
| Module 1.5 | Wikipedia expansion — +76 drugs | ✅ |
| Module 1.6 | Indian derm brands (Nadoxin, Saslic, Aziderm) | ✅ |
| Module 1.7 | Pediatric + Kaggle expansion — +28 drugs | ✅ |
| Module 2 | RAG layer — semantic + fuzzy matching | ✅ |
| Module 3 | LLM agent — verifier + explanation | ✅ |
| Module 3.5 | Drug interaction checker — 25 rules | ✅ |
| Module 4 | Streamlit GUI — TTS + accessibility | ✅ |
| Module 5 | Evaluation — 26 Kaggle prescriptions | ✅ |

---

## 🔬 How RAG Works

**Problem:** OCR reads "OFLAZEST OZ" but corpus has "Ofloxacin"

**Solution — 3 steps:**

1. **Semantic search** — embed query, find top-5 similar drugs by meaning
2. **Fuzzy match** — string similarity to catch typos and abbreviations
3. **Hybrid score** — `0.6 × semantic + 0.4 × fuzzy`

**Decision logic:**
- Score ≥ 0.55 → matched (trust RAG directly)
- Score 0.40–0.55 → ask LLM verifier
- Score < 0.40 → low confidence (flag to patient)

---

## ⚠️ Drug Interaction Rules (sample)

| Combination | Severity | Risk |
|-------------|----------|------|
| Warfarin + NSAIDs | 🔴 SEVERE | Major bleeding |
| SSRIs + Tramadol | 🔴 SEVERE | Serotonin syndrome |
| Macrolides + QT drugs | 🔴 SEVERE | Fatal arrhythmia |
| Benzodiazepines + Opioids | 🔴 SEVERE | Respiratory failure |
| NSAIDs + ACE inhibitors | 🟠 MODERATE | Kidney damage |
| Steroids + NSAIDs | 🟠 MODERATE | Stomach ulcers |

---

## 📋 Requirements

```
google-generativeai
sentence-transformers
rapidfuzz
streamlit
gtts
Pillow
torch
numpy
pandas
pyngrok
```

Install: `pip install -r requirements.txt`

---

## ⚠️ Disclaimer

PrescriptAI is a research prototype for educational purposes. It is **not** a medical device and should **not** be used as a substitute for professional medical advice. Always confirm prescriptions with your doctor or pharmacist.

---

## 🙏 Acknowledgements

- [Kaggle — Illegible Medical Prescription Images Dataset](https://www.kaggle.com/datasets/mehaksingal/illegible-medical-prescription-images-dataset)
- [Sentence Transformers — SBERT](https://www.sbert.net)
- [Google Gemini API](https://ai.google.dev)
- [Streamlit](https://streamlit.io)
