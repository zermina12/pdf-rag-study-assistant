# PDF RAG Study Assistant

An AI-powered study assistant that allows users to ask questions about PDF documents and receive answers grounded in the document's content.

This is my first implementation of a Retrieval-Augmented Generation (RAG) pipeline, built to understand how PDF extraction, text chunking, embeddings, information retrieval, and large language models work together to create a document question-answering system.

##  Project Objective

The goal is to make learning from PDF documents easier by allowing users to ask questions in natural language and retrieve relevant information without manually searching through entire documents.

Instead of relying entirely on an LLM's pretrained knowledge, the application retrieves relevant passages from the uploaded document and uses them as context to generate answers.

##  Features

-  **PDF Text Extraction:** Extracts readable text from digital PDF documents.
-  **OCR-Assisted Extraction:** Attempts to extract text from scanned pages using OCR when digital text is   insufficient.
-  **Text Chunking:** Splits extracted text into smaller, overlapping chunks.
-  **Text Embeddings:** Converts text chunks into numerical representations for semantic search.
-  **FAISS Vector Search:** Retrieves document chunks based on vector similarity.
-  **Hybrid Retrieval:** Combines BM25 keyword search with FAISS semantic search.
-  **Context-Grounded Answers:** Generates answers using retrieved document passages.
-  **Source References:** Preserves page metadata and displays relevant source excerpts.
-  **Gradio Interface:** Provides an interface for uploading PDFs and asking questions.

##  Technology Stack

| Technology | Purpose | Why I Used It |
|---|---|---|
| Python | Programming language | To implement the RAG pipeline. |
| Google Colab | Development environment | To build and test each component incrementally. |
| PyMuPDF | PDF text extraction | To extract existing digital text from PDF pages. |
| Tesseract OCR / `pytesseract` | Optical character recognition | To attempt text extraction from scanned or image-based pages. |
| Pillow (PIL) | Image processing | To prepare page images for OCR. |
| LangChain | RAG framework components | To connect document processing, retrieval, prompts, and LLM inference. |
| `RecursiveCharacterTextSplitter` | Text chunking | To divide long documents into manageable, overlapping chunks. |
| Hugging Face Sentence Transformers | Embeddings | To represent text chunks as vectors for semantic retrieval. |
| FAISS | Vector indexing and search | To find semantically relevant document chunks efficiently. |
| BM25 | Keyword retrieval | To retrieve passages containing relevant words and terms. |
| `EnsembleRetriever` | Hybrid retrieval | To combine keyword-based and vector-based retrieval. |
| Groq | LLM inference | To access a hosted language model for answer generation. |
| Gradio | User interface | To build a simple PDF question-answering interface. |
| Hugging Face Spaces | Deployment | To explore hosting the application as a public web app. |

##  How the RAG Pipeline Works

The application follows these steps:

### 1. PDF Upload and Text Extraction

The user uploads a PDF containing study material, lecture notes, or other documents.

The system first attempts to extract digital text directly from each page. Since some PDFs contain scanned images rather than readable text, OCR can be applied to pages with little or no extractable text.

This hybrid approach helps avoid unnecessary OCR processing on pages that already contain readable text.

**Tools:** PyMuPDF, Tesseract OCR, `pytesseract`, Pillow.

### 2. Text Cleaning and Chunking

After extraction, the text is cleaned and split into smaller chunks using LangChain's `RecursiveCharacterTextSplitter`.

The current configuration uses:

- **Chunk size:** 800 characters
- **Chunk overlap:** 150 characters

Chunking makes retrieval more focused, while overlap helps preserve context between neighboring chunks.

### 3. Embedding Generation

Each chunk is converted into a numerical vector using:

`sentence-transformers/all-MiniLM-L6-v2`

Embeddings help the system identify passages that are semantically related to a question, even when the wording is different.

### 4. FAISS Vector Search

The generated embeddings are indexed using FAISS.

When a question is asked, the vector retriever searches for the most relevant chunks based on vector similarity.

### 5. Hybrid Retrieval with BM25 and FAISS

Vector search is useful for understanding semantic similarity, while keyword search helps identify exact terms, names, and technical concepts.

The application combines:

- **BM25:** Keyword-based retrieval.
- **FAISS:** Semantic vector retrieval.
- **EnsembleRetriever:** Combines the results of both retrievers.

The current retrieval configuration uses:

- BM25 weight: `0.4`
- FAISS weight: `0.6`
- Retrieved chunks per retriever: `3`

These are configuration choices that can be adjusted and evaluated as the project develops.

### 6. Answer Generation

The retrieved chunks are passed to a language model along with the user's question.

The application uses LangChain's `ChatGroq` integration and the configured model:

`openai/gpt-oss-20b`

The prompt instructs the model to answer using only the supplied document context, avoid guessing, and explain information in clear language.

If the answer cannot be found in the retrieved context, the application is instructed to return a predefined message.

Relevant source excerpts and page references are included with the answer.

##  What I Learned

Building this project helped me understand the core components of a RAG system and how they work together.

Key learning areas include:

- Why some PDFs return zero extractable characters.
- The difference between digital text extraction and OCR.
- Why text chunk size and overlap matter.
- How embeddings support semantic search.
- How FAISS performs vector similarity retrieval.
- How BM25 differs from vector search.
- How hybrid retrieval combines complementary search methods.
- How retrieved context helps an LLM answer document-specific questions.
- Why page metadata and source excerpts are useful for tracing answers.

##  Development and Testing

I developed the project in Google Colab and tested the question-answering workflow on educational PDF material, including a Grade 9 Computer Science textbook.

I also explored OCR-assisted processing for scanned and mixed-content PDFs.

The core notebook workflow has been tested successfully in Google Colab. Public deployment and interface testing are separate steps that are still being worked on.

##  Future Improvements

- Improve extraction from complex layouts, tables, and image-heavy documents.
- Evaluate OCR accuracy on different types of scanned PDFs.
- Experiment with chunking and retrieval parameters.
- Evaluate retrieval quality and answer faithfulness.
- Improve source citations and page navigation.
- Support multiple documents in a single study session.
- Add document summarization, notes, quizzes, and flashcards.
- Improve the user interface and deployment process.
- Explore advanced RAG techniques and retrieval evaluation.

##  Running the Project

### Option 1: Google Colab

1. Open the project notebook in Google Colab.
2. Install the required Python dependencies.
3. Configure the required API key securely.
4. Upload a PDF document.
5. Run the extraction, chunking, embedding, and indexing steps.
6. Ask questions about the document and inspect the retrieved sources.

### Option 2: Run the Gradio Application Locally

Clone the repository:

```bash
git clone https://github.com/YOUR_USERNAME/pdf-rag-study-assistant.git
cd pdf-rag-study-assistant
```

Install the dependencies listed in `requirements.txt`:

```bash
pip install -r requirements.txt
```

Configure your Groq API key using an environment variable named `GROQ_API_KEY`. Install any required system dependencies, including the Tesseract OCR executable if OCR is enabled.

Start the application:

```bash
python app.py
```

**Note:** The repository must contain `app.py`, `requirements.txt`, and any required supporting files for these instructions to work. Replace `YOUR_USERNAME` with your GitHub username.

## 🔐 API Key Security

The application uses a Groq API key for language-model inference.

- For local development, configure `GROQ_API_KEY` as an environment variable.
- For Hugging Face Spaces, add it under **Settings → Variables and secrets → Secrets**.
- Never commit API keys, credentials, or private documents to a public repository.

## 📌 Project Status

**Status:** Learning project — core RAG pipeline implemented and tested in Google Colab; public deployment and interface testing are ongoing.

This repository documents my hands-on learning journey in Retrieval-Augmented Generation, PDF data extraction, hybrid search, and LLM-powered document question answering.

## 👩‍💻 Author

Built as a hands-on project to learn and experiment with RAG systems, document processing, information retrieval, and LLM-powered applications.

