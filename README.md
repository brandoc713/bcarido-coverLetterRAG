# Automated RAG-Based Cover Letter Generator

## Overview
This repository contains a Jupyter Notebook (`BDC_rag.ipynb`) demonstrating an end-to-end Retrieval-Augmented Generation (RAG) Pipeline. The project is designed to ingest user's already made cover letters and project descriptions, process them into a vector database, and dynamically generate highly personalized, ATS-friendly cover letters based on a specific job description and resume.

This project showcases practical, hands-on experience in **Natural Language Processing (NLP)**, **Vector Database Management**, and **Prompt Engineering** using modern LLM frameworks.

## Relevance to AI Chatbot Development & Data Science
This notebook serves as a practical demonstration of several core competencies required for AI and LLM-based development:
- **RAG Architecture:** Builds a complete RAG pipeline to ensure that LLM generates accurate, contextually relevant, and stylistically consistent text based on retrieved personal data.
- **Vector Embeddings & Databases:** Utilizes **Chroma** (a vector database) and **Hugging Face** embeddings to index, store, and retrieve documents.
- **Data Processing & Chunking:** Implements semantic text chunking using LangChain's `RecursiveCharacterTextSplitter` to ensure source documents are optimally sized for the embedding model.
- **Framework Proficiency:** Extensively uses **LangChain** for orchestration, API integration, and prompt management.
- **Prompt Engineering:** Designs rigid system prompts using `ChatPromptTemplate` to dictate output length, tone, and keyword matching.

## Technical Stack
- **Language:** Python
- **LLM Orchestration:** LangChain (`langchain_community`,`langchain_core`)
- **Vector Database:** Chroma
- **Embeddings:** Hugging Face (`sentence-transformers/all-MiniLM-L6-v2`)
- **Data Ingestion:** `PyPDFLoader`, `TextLoader`
- **LLM API:** ChatDartmouth (using `qwen.qwen3-vl-32b-instruct-fp8`)
- **Environment:** Google Colab

## Pipeline Architecture

### 1. Data Ingestion & Chunking
The pipeline mounts to Google Drive and iterates through a directory of already made cover letters (supporting both `.pdf` and `.txt` formats). To ensure high-quality retrieval, the documents are processed using LangChain's `RecursiveCharacterTextSplitter`:
- **Chunk Size:** 800 Characters
- **Chunk Overlap:** 150 Characters

### 2. Embedding & Vector Storage
The text chunks are embedded using the lightweight and efficient Hugging Face `all-MiniLM-L6-v2` model. These embeddings are then stored persistently in a local **Chroma** vector database (`/content/chroma_letters`).

### 3. Retrieval Strategy
When a new job description and resume are provided, the system queries the Chroma database. It retrieves the top 6 (`k=6`) most semantically similar chunks from the user's past documents to serve as the stylistic and historical context for the generation phase.

### 4. Prompt Engineering & Generation
A string prompt template is defined to guide to the LLM. It instructs the model to:
- Act as an ATS-friendly cover letter drafter.
- Use *only* the retrieved context, the provided resume, and the target job description.
- Strictly prevent hallucinations (no inventing experiences/technologies).
- Output exactly 3-4 paragraphs.

The context, resume, and job description are passed to the `ChatDartmouth` API (running the Qwen 32B model) to generate the final artifact.

*Note on Generated Output*: The sample cover letter displayed at the end of the notebook was generated using a test corpus of diverse project descriptions and historical cover letters. It is intended solely to demonstrate the RAG pipeline's ability to accurately retrieve, synthesize, and format context. The specific companies and roles listed in the output reflect this test dataset rather than my personal employment history.

## Setup and Usage
1. **Environment Setup:** Open the notebook in Google Colab.
2. **Install Dependencies:** The first cell installs all required libraries.
3. **Mount Drive:** Ensure your documents are located in Google Drive at `/content/drive/MyDrive/colab_rag-letters`.
4. **API Keys:** You will need to add your Dartmouth API key to your Colab Secrets (`dartmouth_api_key`). Note: If adapting for standard OpenAI/Anthropic API, swap the `ChatDartmouth` module for `langchain_openai` or equivalent.
5. **Execution:** Define the `job-desc` and `resume_text` strings in the final cell, then run `rag_answer()` to generate the cover letter.
