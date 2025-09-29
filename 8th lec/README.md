## What are the two RAG notebooks doing? (8th lec/rag-2.ipynb and 8th lec/rag-3.ipynb)

Both notebooks implement and demo a Retrieval-Augmented Generation (RAG) pipeline that can switch between two LLM providers (Groq and Anthropic/Claude) while using a single embedding model and vector database.

High-level flow:
- Load documents: from PDF files, a directory of PDFs, or web URLs using LangChain loaders.
- Split text: chunk the content with RecursiveCharacterTextSplitter (configurable size/overlap).
- Embed chunks: use OpenAI embeddings (text-embedding-3-small) to turn chunks into vectors.
- Store/Retrieve: persist vectors in a Chroma DB collection and expose a retriever (k=6 by default).
- Prompt + Generate: build provider-specific prompts and answer questions using either:
  - Groq (model: llama-3.1-70b-versatile), or
  - Anthropic Claude (model: claude-3-haiku-20240307).
- Compare providers: run the same question through both providers to compare responses.

Key components defined in both notebooks:
- class MultiProviderRAG:
  - _initialize_llm(provider): selects ChatGroq or ChatAnthropic based on provider.
  - load_documents(source, source_type): supports 'pdf', 'web', 'directory'.
  - setup_vector_store(documents, persist_directory): splits, embeds, and creates a Chroma store; builds a retriever.
  - load_vector_store(persist_directory): reloads an existing Chroma store.
  - query(question): retrieves context and generates an answer using the current provider.
  - compare_providers(question): gathers answers from both providers (with error handling) and restores the original provider.
  - get_system_info(): returns current provider/model and vector-store status.
- Provider-specific prompts: tailored instruction templates for Groq vs Claude.
- demo(): example routine that loads a sample PDF, builds the vector DB, asks a test question, switches providers, compares outputs, and prints system info.

Environment variables required:
- GROQ_API_KEY (for Groq LLM)
- ANTHROPIC_API_KEY (for Claude LLM)
- OPENAI_API_KEY (for OpenAI embeddings)

Minor differences between rag-2 and rag-3:
- Both files contain nearly identical code (including two demo() definitions each). In rag-3.ipynb, one demo uses an absolute sample PDF path (/home/ojas/Downloads/sample.pdf); in rag-2.ipynb the demo uses sample.pdf in the working directory. Otherwise, logic and configuration are the same.

Practical use:
1) Put your PDFs in a folder or point to a URL.
2) Set the required API keys in your environment (.env supported via python-dotenv).
3) Run demo() inside the notebook (or import the class and use it in your own script) to build the vector DB and ask questions.
4) Use switch_provider("groq"|"claude") to change the LLM at runtime and compare outputs with compare_providers().
