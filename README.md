# auditor-chatbot

Detailed Component Overview

1. User Query via Streamlit UI

Interface: A simple, interactive UI built with Streamlit that accepts user queries about financial reports and transactions.
Input Example: “Does the GL agree with the trial balance for account X?”


2. Preprocessing Module

Tasks:
OCR & Text Extraction: Process images or PDFs (e.g., scanned GL listings, trial balances) using tools like Tesseract or Donut.
Basic Cleaning: Normalize text data for consistency.
Reasoning: This ensures that data coming from various sources is in a uniform format before further processing.


3. Multimodal Data Processing

Separation of Data:
Text Data: GL listings, trial balances, reports.
Tabular Data: Aging reports, counterparty-specific summaries.
Image Data: Supporting documents, management estimate sheets.
Purpose: Allows you to apply specialized processing (or summarization if needed) to each data type while preserving critical details.


4. Chunking & Embedding Generation

Chunking Strategy:
Financial Reports: Chunk by account or transaction.
Aging Reports: Chunk by debtor/creditor or date ranges.
Supporting Documents: Chunk by logical sections (e.g., assumptions, calculations).
Context Consideration: Maintain enough context in each chunk to ensure precision—avoid over-summarization that might strip away key details.
Embeddings: Generate embeddings for each chunk (using a pre-trained sentence transformer) for similarity search.


5. Vector Store Retrieval (FAISS) and Metadata Retrieval (PostgreSQL)

FAISS:
Role: Performs fast similarity search on the generated embeddings.
Advantage: Speed, especially for local testing.
PostgreSQL:
Role: Stores metadata (e.g., document type, date, account info) and raw text chunks.
Integration: Query results from FAISS (by vector IDs) are matched with metadata from PostgreSQL for more precise filtering.
Design Note: This decouples fast vector retrieval from rich metadata querying.


6. Query Parser & Fusion Engine

Purpose:
Interprets the user’s query using both keyword matching and semantic search.
Fuses information from both the FAISS results and PostgreSQL metadata to decide which chunks best answer the query.
Outcome: Ensures that the answer is both contextually relevant and precise—vital for financial auditing.


7. Groq Inference (LLaMA 2/Mistral)

Role:
Runs inference on the selected chunks.
Uses the lightweight model (LLaMA 2 or Mistral) initially, with plans to upscale later.
Why Groq? Leverages fast, specialized hardware acceleration to generate responses in real time.

8. Response Generator

Function:
Synthesizes the processed data and generates a clear, detailed response.
Ensures consistency (e.g., GL listings and trial balance agreement, aging report details).
Output: The final answer that is displayed in the Streamlit UI.


9. Final Answer Display in Streamlit UI

Interface: The result is presented in an easy-to-read format with supporting visuals if needed
