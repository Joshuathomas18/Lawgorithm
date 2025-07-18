# Petition Bot Fine-tuning System

This system fine-tunes the OPT-1.3B model on Indian court petition data to create a specialized petition bot.

## Project Structure

```
.
├── preprocessing/
│   └── data_processor.py    # Data preprocessing module
├── training/
│   └── fine_tuner.py       # Fine-tuning module
├── inference/
│   └── petition_bot.py     # Inference module
├── petition_data/
│   ├── raw/               # Raw petition data
│   └── processed/         # Processed data for training
├── fine_tuned_model/      # Fine-tuned model output
├── conversations/         # Saved conversations
└── requirements.txt       # Project dependencies
```

## Setup

1. Install dependencies:
```bash
pip install -r requirements.txt
```

2. Prepare your data:
   - Place your petition data in CSV format in `petition_data/processed/petitions.csv`
   - The CSV should have columns: petition_id, title, court, date, judges, citations, url

## Usage

### 1. Preprocess Data

```bash
python preprocessing/data_processor.py
```

This will:
- Load the petition data
- Create instruction-response pairs
- Generate embeddings
- Save processed data to `petition_data/processed/finetuning_data.json`

### 2. Fine-tune Model

```bash
python training/fine_tuner.py
```

This will:
- Load the processed data
- Fine-tune the OPT-1.3B model using PEFT (LoRA)
- Save the fine-tuned model to `fine_tuned_model/`

### 3. Use the Petition Bot

```python
from inference.petition_bot import PetitionBot

# Initialize bot
bot = PetitionBot()

# Generate response
response = bot.generate_response(
    instruction="What is the title of the petition with ID 154721154?",
    query_type="basic"
)

print(response['response'])
```

## Features

- Parameter-Efficient Fine-Tuning (PEFT) using LoRA
- Automatic disclaimer addition for legal queries
- Conversation history saving
- Error handling and logging
- GPU acceleration support

## The RAG Process: A Step-by-Step Explanation

RAG is fundamentally a technique that combines the strengths of two powerful AI models:

1.  **Large Language Models (LLMs):** Like the one I'm built on, LLMs excel at generating human-like text,
    understanding context, and performing complex reasoning tasks *given a prompt*. However, their knowledge is
    limited to the data they were trained on – a snapshot in time.
2.  **Information Retrieval Systems:** These systems are designed to efficiently search and retrieve relevant
    documents from a vast database.

RAG overcomes the limitations of LLMs by providing them with real-time access to external knowledge. Here's how it
works:

**1. User Prompt:** You provide the AI with a prompt – for example: "Draft a legal argument challenging the
validity of the Land Acquisition Act, 2013, based on principles of natural justice."

**2. Query Generation:**  The LLM analyzes your prompt and *generates a more structured query* – this is often
called a "search query."  Instead of just feeding the prompt directly, the LLM breaks it down into keywords and
concepts. In our example, the query might become: "Land Acquisition Act 2013, validity, natural justice,
principles of fairness."

**3. Information Retrieval (The "Retrieval" Step):**  This is where the magic happens. The generated query is fed
into a specialized information retrieval system (like a vector database).  Here's the breakdown:
    * **Vector Database:** This database stores *embeddings* – numerical representations of documents. An
      embedding captures the semantic meaning of a document – how closely related it is to other documents.
    * **Embedding Model:** A separate model (often a dense vector model) converts the search query into an
      embedding.
    * **Similarity Search:** The system then performs a similarity search – it finds the documents in the database
      whose embeddings are most similar to the query's embedding.  This identifies the most relevant legal precedents,
      statutes, and commentary. Let's say the system returns documents related to the Supreme Court case of *Krishna
      Kant v. Mysoor City Municipality*.

**4. Context Augmentation:** The retrieved documents (in our example, the *Krishna Kant* judgment) are *added as
context* to the original prompt.  The augmented prompt now looks like this: "Draft a legal argument challenging
the validity of the Land Acquisition Act, 2013, based on principles of natural justice.  *Consider the legal
arguments presented in the Krishna Kant v. Mysoor City Municipality judgment.*"

**5. Response Generation:** The LLM now uses *both* the original prompt *and* the retrieved context to generate
its response. It synthesizes the legal arguments, referencing the relevant case law.

**Key Components & Technical Details:**

*   **Embeddings Models:**  Popular choices include Sentence Transformers, OpenAI Embeddings, or Google's PaLM
    Embeddings.
*   **Vector Databases:** Pinecone, Chroma, Weaviate, Milvus – specialized databases designed for efficient
    similarity search.
*   **Indexing:** The documents are indexed in the vector database, allowing for fast retrieval.
*   **Hybrid Search:** Combining semantic similarity search (using embeddings) with keyword search can further
    improve accuracy.

**Diagram:**

```mermaid
graph TD;
    A["User Prompt"] --> B{"LLM (Query Generation)"};
    B --> C["Vector Database (Similarity Search)"];
    C --> D["Retrieved Context Documents"];
    D --> E["Augmented Prompt"];
    E --> F{"LLM (Response Generation)"};
```

## Model Details

- Base Model: OPT-1.3B
- Fine-tuning Method: LoRA
- Context Length: 2048 tokens
- Training: Mixed precision (FP16)

## Safety Features

- Legal disclaimers
- Source citations
- Confidence indicators
- Input validation

## Requirements

- Python 3.8+
- CUDA-capable GPU (recommended)
- 16GB+ RAM
- 50GB+ free disk space

## Notes

- The fine-tuned model is for informational purposes only
- Always verify information with official sources
- Not intended for legal advice
- Model responses should be validated by legal professionals #   L a w g o r i t h m 
 
 
