# Multilingual MAJU Information Chatbot — Generative AI Course Project

Developed as a student course project for the Generative AI course at Muhammad Ali Jinnah University (MAJU). This project is not an official MAJU service.

A Python notebook that answers MAJU university questions in English and Urdu using retrieval-augmented generation (RAG). It retrieves information from a text dataset, generates answers through Groq, and includes speech-to-text and text-to-speech experiments.

[Open in Google Colab](https://colab.research.google.com/github/MuhammadZain010/Multilingual-AI-Chatbot-Python/blob/main/Multilingual%20AI%20Chatbot%20Python.ipynb)

## Features

- English and Urdu text questions and responses.
- Paragraph-based text chunking with a maximum of 600 characters per chunk.
- Sentence Transformer embeddings and FAISS similarity search over the dataset.
- Comparison of two retrievers and two language models.
- Speech transcription with local Whisper Base or Groq-hosted Whisper Large v3.
- Spoken answers through gTTS or Edge TTS.
- A set of 20 English and Urdu evaluation questions, with answers displayed in pandas tables.

## How it works

```text
Text question / uploaded audio
              |
       Speech transcription (for audio)
              |
       Question embedding
              |
       FAISS: retrieve 5 text chunks
              |
       Groq Llama: generate an answer
              |
       English / Urdu text + optional speech
```

The answer prompt instructs the model to use only the retrieved context, keep responses short, and return `Information not found in the dataset.` when the context does not contain the answer. These are prompt instructions rather than a guarantee of factual accuracy.

## Models and tools

| Component | Implemented options |
| --- | --- |
| Embeddings | `sentence-transformers/all-MiniLM-L6-v2`, `BAAI/bge-m3` |
| Vector search | FAISS `IndexFlatL2` |
| Answer generation via Groq | `llama-3.1-8b-instant`, `llama-3.3-70b-versatile` |
| Speech-to-text | Whisper `base` locally, `whisper-large-v3` via Groq |
| Text-to-speech | gTTS, Edge TTS |
| Edge voices configured in the notebook | `en-US-AriaNeural`, `ur-PK-AsadNeural` |

These are the identifiers configured in the notebook; API execution depends on their availability in your account.

## Run in Google Colab

The notebook uses `google.colab.userdata` and `google.colab.files`, so Google Colab is the intended environment.

1. Open the notebook using the Colab link above.
2. Run the installation cells at the top. They install `sentence-transformers`, `faiss-cpu`, `groq`, `gTTS`, `openai-whisper`, `edge-tts`, and `nest_asyncio`. The notebook also imports NumPy, pandas, and IPython.
3. Upload your UTF-8 dataset as **`maju_dataset_cleaned.txt`** to the runtime working directory before running the dataset-loading cell. This dataset is **not included in this repository**. You must supply it or change the filename in the loading cell.
4. In Colab's **Secrets** panel, create a secret named **`GROQ_API_KEY`**, paste your Groq API key, and enable notebook access. The API setup cell reads this secret.
5. Run the chunking, embedding/index, retrieval, API setup, language detection, and answer-generation cells in order. The embedding models are downloaded when first loaded.
6. Run the text examples or evaluation cells. Each evaluation block makes one answer-generation request per question; the notebook contains two blocks of 20 questions.
7. For speech examples, run the TTS definitions and Whisper-loading cell, then use the audio upload cells to select an English or Urdu recording. Run the transcription and speech chatbot cells afterward.

Internet access is needed for package/model downloads, Groq requests, and the TTS services. Local Whisper audio decoding requires FFmpeg; if decoding fails because FFmpeg is missing, install it in the runtime. A GPU can help with local model execution.

## Usage examples

After running the setup and function-definition cells:

```python
answer = generate_answer(
    "Where is MAJU located?",
    retriever_name="BAAI",
    llm_name="Llama 3.3 70B",
)
print(answer)
```

```python
question = "ماجو کہاں واقع ہے؟"
answer = generate_answer(question, retriever_name="BAAI")
print(answer)
tts_gtts(answer, lang=detect_language(question))
```

After loading Whisper Base, defining the speech functions, and uploading an audio file:

```python
speech_chatbot(audio_file, stt_model="base", tts_model="gtts")
speech_chatbot(audio_file, stt_model="large-v3", tts_model="edge")
```

## Defaults and limitations

- `generate_answer()` defaults to the **MiniLM** retriever and **Llama 3.3 70B**. `retrieve_context()` called directly defaults to **BAAI**. The speech chatbot calls `generate_answer()` with its defaults.
- Language detection checks for characters in the Arabic Unicode block and treats matching input as Urdu; otherwise it selects English. It does not specifically detect Roman Urdu or distinguish other languages using that script.
- The evaluation tables contain generated responses, not automatic accuracy scores or a measured benchmark.
- Answers depend on the supplied dataset and retrieved chunks. Review responses against the source information.
- This is a notebook demonstration with uploaded audio, rather than a deployed chat interface or live microphone application.
- Running outside Colab requires replacing Colab-specific secret access and upload helpers with local equivalents.

## Repository contents

| File | Purpose |
| --- | --- |
| [Multilingual AI Chatbot Python.ipynb](Multilingual%20AI%20Chatbot%20Python.ipynb) | Complete implementation, evaluation examples, and saved outputs |
| `README.md` | Project overview and setup instructions |
