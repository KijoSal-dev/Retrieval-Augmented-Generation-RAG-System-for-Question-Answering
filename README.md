# Retrieval-Augmented-Generation-RAG-System-for-Question-Answering
# Project Overview
This project demonstrates a basic Retrieval-Augmented Generation (RAG) system that allows users to ask questions about a PDF document.
The system extracts text from the document, converts it into embeddings, stores the embeddings in a vector database, and retrieves relevant sections when a user asks a question. A language model then generates answers using the retrieved document context.
This project was developed as part of the Cyber Shujaa Program – Data and Artificial Intelligence track to practice applying generative AI techniques to real documents

# Key Features
- Extracts text from a PDF document
- Cleans and preprocesses document text
- Splits documents into smaller chunks for efficient retrieval
- Generates embeddings using Sentence Transformers
- Stores embeddings in a FAISS vector database
- Retrieves relevant context for user queries
- Generates responses using a Hugging Face language model

# Technologies Used
- Python
- Google Colab
- LangChain
- Hugging Face Transformers
- Sentence Transformers
- FAISS (Vector Database)
- PyPDF

# Installation

Install the required libraries:
- !pip install langchain
- !pip install sentence-transformers
- !pip install faiss-cpu
- !pip install transformers
- !pip install pypdf

# How to Run the Project
1. Open the notebook in Google Colab
2. Install the required libraries
3. Upload the PDF document
4. Run the notebook cells sequentially
5. Enter a query to test the RAG system

# Learning Outcomes
Through this project, I gained practical experience with:
1. Building a basic Retrieval-Augmented Generation pipeline
2. Using FAISS for vector similarity search
3. Generating embeddings with Sentence Transformers
4. Integrating Hugging Face language models
5. Processing and chunking documents for AI applications

# Screenshots
# 1. Document Loading / PDF Processing
This shows the system successfully loading and reading the PDF.

# 2. Document Chunking
This proves that the system correctly splits the document into smaller chunks for retrieval.

# 4. Query Example
This is the most important screenshot because it shows the system working.
   
