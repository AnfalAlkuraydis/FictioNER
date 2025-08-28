<div align="center">

# ✨ **FictioNER — Named Entity Recognition for Fiction**

![Python](https://img.shields.io/badge/Python-3.9%2B-3776AB?logo=python&logoColor=white)
![Transformers](https://img.shields.io/badge/HuggingFace-Transformers-ffcc00?logo=huggingface&logoColor=black)
![spaCy](https://img.shields.io/badge/spaCy-NLP-09A3D5?logo=spacy&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-EE4C2C?logo=pytorch&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green)

Domain-adapted NER for **fiction & narrative text** — detect **characters**, **locations**, **organizations**, and custom literary entities.

</div>

---

## 🧭 Abstract
**FictioNER** is a lightweight, production-friendly pipeline for **Named Entity Recognition** on **fiction**. General-domain NER models struggle with narrative style, epithets, nicknames, and coreference hints. FictioNER fine-tunes transformer encoders on **story-centric annotations** (CoNLL/IOB), adds optional **spaCy** postprocessing, and ships ready-to-run scripts for training, evaluation, and inference.

---

## 🔎 Overview
- **Goal:** robust entity extraction from novels, short stories, scripts, and fan‑fic.
- **Entities (configurable):** `PER`, `LOC`, `ORG`, `GPE`, `FAC`, `VEH` (extend via `config/labels.txt`).
- **Backbones:** Hugging Face Transformers (e.g., `bert-base-cased`, `roberta-base`) with token‑classification heads.
- **Extras:** optional **spaCy** pipeline for sentence segmentation, rule hints, and cleaner spans.

---

## 🔄 Pipeline

```mermaid
flowchart LR
  A["Data Ingestion<br/>CoNLL/IOB TSV · JSONL · spaCy DocBin"] --> B["Preprocess<br/>split · normalize · label schema"]
  B --> C["Tokenization<br/>HF tokenizer + alignment to IOB"]
  C --> D["Fine‑Tune<br/>Transformer token‑classifier (PyTorch)"]
  D --> E["Evaluate<br/>precision · recall · F1 (seqeval)"]
  E --> F["Export & Serve<br/>HF format · spaCy model · ONNX (optional)"]
  F --> G["Inference<br/>CLI · notebook · API"]
```

---

## 📦 Data
- Accepts **CoNLL/IOB** (`token label` per line, blank line between sentences), **JSONL** span annotations, or **spaCy DocBin**.
- Train/validation/test splits are configurable; stratification by entity presence supported.
- Tips for fiction:
  - Add aliases and epithets to the same **PERSON** id during annotation.
  - Keep punctuation tokenization consistent (quotes, em‑dashes).
  - Consider a small **rules layer** for titles/honorifics (e.g., “Lord”, “Detective”).

> (If you have a public dataset link, drop it here.)

---

## 📈 Results (Validation)
From the final epoch of the run you shared:

| Label | F1 | Precision | Recall | Support |
|---|---:|---:|---:|---:|
| **PER** | **0.8298** | 0.8128 | 0.8476 | 932 |
| **GPE** | 0.7895 | 0.7500 | 0.8333 | 144 |
| **FAC** | 0.6933 | 0.6783 | 0.7091 | 220 |
| **ORG** | 0.5455 | 0.5000 | 0.6000 | 5 |
| **LOC** | 0.5111 | 0.4742 | 0.5542 | 83 |
| **VEH** | 0.5714 | 0.5000 | 0.6667 | 12 |

**Aggregates**  
- **Micro F1:** **0.7807** (P=0.7583, R=0.8044, N=1396)  
- **Macro F1:** 0.6568  
- **Weighted F1:** 0.7820

> *Note:* Micro F1 was used as the checkpoint selection criterion in this run.

---

## ⚙️ Installation

```bash
git clone https://github.com/AnfalAlkuraydis/FictioNER.git
cd FictioNER

# (optional) create a virtual env
python -m venv .venv
# Windows:
.venv\Scripts\activate
# macOS/Linux:
source .venv/bin/activate

pip install -r requirements.txt
```

---

## 🚀 Quickstart

### 1) Train
```bash
python src/train.py \
  --train data/train.conll \
  --dev data/dev.conll \
  --labels config/labels.txt \
  --model_name roberta-base \
  --output_dir models/roberta-fic-ner \
  --epochs 3 --batch_size 16 --lr 3e-5
```

### 2) Evaluate
```bash
python src/evaluate.py \
  --test data/test.conll \
  --labels config/labels.txt \
  --model_dir models/roberta-fic-ner
```

### 3) Inference (CLI)
```bash
python src/infer.py \
  --text "Arya met the Faceless Man in Braavos near the House of Black and White." \
  --model_dir models/roberta-fic-ner
```

### 4) Inference (Python)
```python
from transformers import AutoTokenizer, AutoModelForTokenClassification, pipeline
model_dir = "models/roberta-fic-ner"
tok = AutoTokenizer.from_pretrained(model_dir)
model = AutoModelForTokenClassification.from_pretrained(model_dir)
ner = pipeline("token-classification", model=model, tokenizer=tok, aggregation_strategy="simple")
ner("The White Witch ruled Narnia from Cair Paravel.")
```

---

## 🧰 Project Structure (suggested)
```
FictioNER/
├─ src/
│  ├─ train.py          # fine-tuning script
│  ├─ evaluate.py       # metrics with seqeval
│  ├─ infer.py          # CLI inference
│  ├─ data_utils.py     # readers (CoNLL, JSONL, DocBin) & aligners
│  └─ postprocess.py    # optional spaCy/rules cleanup
├─ config/
│  ├─ labels.txt        # entity set (one per line)
│  └─ params.yaml       # optional config
├─ data/
│  ├─ train.conll
│  ├─ dev.conll
│  └─ test.conll
├─ models/
│  └─ roberta-fic-ner/
├─ notebooks/
└─ requirements.txt
```

---

## 🛣️ Roadmap
- Add **coreference‑aware** heuristics to link aliases/epithets to canonical names.
- Release **pretrained checkpoints** on the Hub.
- Streamlit demo that highlights entities on full chapters.
- Export to **spaCy** pipeline for easy integration.

---

## 📜 License
Released under the **MIT License**. Check any third‑party datasets for their own terms.

---

<div align="center">
Made with ❤️ — bringing NER to the world of fiction.
</div>
