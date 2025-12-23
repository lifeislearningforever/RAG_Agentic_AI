# LangChain Cheat Sheet: Building GenAI Applications

## Installation & Setup

bash
# Core installation
pip install langchain langchain-community langchain-core

# LLM providers
pip install langchain-openai langchain-anthropic langchain-google-genai

# Vector stores
pip install langchain-chroma langchain-pinecone langchain-qdrant

# Additional utilities
pip install langchain-experimental pypdf python-dotenv


## 1. LLM Integration

### Basic LLM Setup
python
from langchain_openai import ChatOpenAI
from langchain_anthropic import ChatAnthropic
from langchain_core.messages import HumanMessage, SystemMessage

# OpenAI
llm = ChatOpenAI(model="gpt-4", temperature=0.7)

# Anthropic Claude
llm = ChatAnthropic(model="claude-3-5-sonnet-20241022", temperature=0.7)

# Basic invocation
response = llm.invoke("What is data engineering?")
print(response.content)

# Chat with messages
messages = [
    SystemMessage(content="You are a data engineering expert"),
    HumanMessage(content="Explain ETL pipelines")
]
response = llm.invoke(messages)


### Streaming Responses
python
for chunk in llm.stream("Explain data lakes"):
    print(chunk.content, end="", flush=True)


## 2. Prompt Templates

### Basic Templates
python
from langchain_core.prompts import ChatPromptTemplate, PromptTemplate

# Simple template
prompt = PromptTemplate.from_template(
    "Explain {concept} in the context of {domain}"
)
formatted = prompt.format(concept="normalization", domain="databases")

# Chat template
chat_prompt = ChatPromptTemplate.from_messages([
    ("system", "You are an expert in {domain}"),
    ("human", "{question}"),
    ("ai", "{ai_response}"),
    ("human", "{follow_up}")
])

# Format
messages = chat_prompt.format_messages(
    domain="data engineering",
    question="What is a data warehouse?",
    ai_response="A data warehouse is...",
    follow_up="How does it differ from a data lake?"
)


### Few-Shot Prompting
python
from langchain_core.prompts import FewShotPromptTemplate

examples = [
    {"input": "SELECT * FROM users", "output": "Query retrieves all user records"},
    {"input": "INSERT INTO logs VALUES", "output": "Query inserts log entries"}
]

example_prompt = PromptTemplate(
    input_variables=["input", "output"],
    template="SQL: {input}\nExplanation: {output}"
)

few_shot_prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_prompt,
    prefix="Explain SQL queries:",
    suffix="SQL: {input}\nExplanation:",
    input_variables=["input"]
)


## 3. Chains

### Simple Sequential Chain
python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough

# LCEL (LangChain Expression Language)
prompt = ChatPromptTemplate.from_template("Explain {topic} briefly")
chain = prompt | llm | StrOutputParser()

result = chain.invoke({"topic": "data pipelines"})


### Complex Chain with Multiple Steps
python
from operator import itemgetter

# Multi-step chain
chain = (
    {"context": itemgetter("docs"), "question": itemgetter("question")}
    | prompt
    | llm
    | StrOutputParser()
)

result = chain.invoke({
    "docs": "ETL stands for Extract, Transform, Load...",
    "question": "What does ETL mean?"
})


### Router Chain
python
from langchain.chains.router import MultiPromptChain
from langchain.chains import LLMChain

sql_prompt = PromptTemplate(
    template="Answer this SQL question: {input}",
    input_variables=["input"]
)

python_prompt = PromptTemplate(
    template="Answer this Python question: {input}",
    input_variables=["input"]
)

prompt_infos = [
    {"name": "sql", "description": "Good for SQL queries", "prompt_template": sql_prompt},
    {"name": "python", "description": "Good for Python code", "prompt_template": python_prompt}
]


## 4. Document Loaders

### Common Data Sources
python
from langchain_community.document_loaders import (
    TextLoader, CSVLoader, UnstructuredExcelLoader,
    PyPDFLoader, DirectoryLoader, JSONLoader
)

# Text files
loader = TextLoader("data/document.txt")
docs = loader.load()

# CSV with metadata
csv_loader = CSVLoader(
    file_path="data/dataset.csv",
    csv_args={"delimiter": ",", "quotechar": '"'}
)

# PDF
pdf_loader = PyPDFLoader("report.pdf")
pages = pdf_loader.load_and_split()

# Directory (batch loading)
dir_loader = DirectoryLoader(
    "data/",
    glob="**/*.txt",
    loader_cls=TextLoader
)

# JSON with specific extraction
json_loader = JSONLoader(
    file_path="data.json",
    jq_schema=".data[]",
    text_content=False
)

# Database
from langchain_community.document_loaders import SQLDatabaseLoader

db_loader = SQLDatabaseLoader(
    query="SELECT * FROM logs WHERE date > '2024-01-01'",
    db=db_connection
)


## 5. Text Splitting

### Strategic Chunking
python
from langchain.text_splitter import (
    RecursiveCharacterTextSplitter,
    CharacterTextSplitter,
    TokenTextSplitter
)

# Recursive splitter (recommended)
text_splitter = RecursiveCharacterTextSplitter(
    chunk_size=1000,
    chunk_overlap=200,
    length_function=len,
    separators=["\n\n", "\n", " ", ""]
)

chunks = text_splitter.split_documents(docs)

# Token-based splitting
token_splitter = TokenTextSplitter(
    chunk_size=512,
    chunk_overlap=50
)

# Code-specific splitting
from langchain.text_splitter import Language, RecursiveCharacterTextSplitter

python_splitter = RecursiveCharacterTextSplitter.from_language(
    language=Language.PYTHON,
    chunk_size=500,
    chunk_overlap=50
)


## 6. Embeddings & Vector Stores

### Embedding Models
python
from langchain_openai import OpenAIEmbeddings
from langchain_community.embeddings import HuggingFaceEmbeddings

# OpenAI embeddings
embeddings = OpenAIEmbeddings(model="text-embedding-3-small")

# Local embeddings (free)
embeddings = HuggingFaceEmbeddings(
    model_name="sentence-transformers/all-MiniLM-L6-v2"
)

# Embed text
vector = embeddings.embed_query("What is a data warehouse?")
vectors = embeddings.embed_documents(["doc1", "doc2", "doc3"])


### Vector Store Operations
python
from langchain_community.vectorstores import Chroma, FAISS, Qdrant

# Chroma (persistent)
vectorstore = Chroma.from_documents(
    documents=chunks,
    embedding=embeddings,
    persist_directory="./chroma_db"
)

# FAISS (in-memory, fast)
vectorstore = FAISS.from_documents(chunks, embeddings)
vectorstore.save_local("faiss_index")
vectorstore = FAISS.load_local("faiss_index", embeddings)

# Qdrant (production-ready)
from langchain_qdrant import Qdrant
from qdrant_client import QdrantClient

client = QdrantClient(path="./qdrant_db")
vectorstore = Qdrant(
    client=client,
    collection_name="documents",
    embeddings=embeddings
)

# Add documents
vectorstore.add_documents(chunks)

# Similarity search
results = vectorstore.similarity_search("data pipeline best practices", k=4)

# Search with scores
results_with_scores = vectorstore.similarity_search_with_score(
    "ETL vs ELT", k=3
)

# MMR (Maximum Marginal Relevance) for diversity
results = vectorstore.max_marginal_relevance_search(
    "data modeling", k=4, fetch_k=20
)


## 7. Retrievers

### Basic Retrieval
python
# Simple retriever
retriever = vectorstore.as_retriever(
    search_type="similarity",
    search_kwargs={"k": 4}
)

docs = retriever.invoke("What is data normalization?")

# MMR retriever (diverse results)
retriever = vectorstore.as_retriever(
    search_type="mmr",
    search_kwargs={"k": 4, "fetch_k": 20, "lambda_mult": 0.5}
)

# Threshold retriever
retriever = vectorstore.as_retriever(
    search_type="similarity_score_threshold",
    search_kwargs={"score_threshold": 0.8, "k": 4}
)


### Advanced Retrievers
python
from langchain.retrievers import ContextualCompressionRetriever
from langchain.retrievers.document_compressors import LLMChainExtractor

# Compression retriever (extract relevant parts)
compressor = LLMChainExtractor.from_llm(llm)
compression_retriever = ContextualCompressionRetriever(
    base_compressor=compressor,
    base_retriever=retriever
)

# Multi-query retriever (generate multiple search queries)
from langchain.retrievers.multi_query import MultiQueryRetriever

multi_retriever = MultiQueryRetriever.from_llm(
    retriever=vectorstore.as_retriever(),
    llm=llm
)

# Ensemble retriever (combine multiple retrievers)
from langchain.retrievers import EnsembleRetriever

ensemble_retriever = EnsembleRetriever(
    retrievers=[retriever1, retriever2],
    weights=[0.5, 0.5]
)


## 8. RAG (Retrieval Augmented Generation)

### Basic RAG Chain
python
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough

def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)

rag_prompt = ChatPromptTemplate.from_template("""
Answer the question based only on the following context:

{context}

Question: {question}

Answer:
""")

rag_chain = (
    {"context": retriever | format_docs, "question": RunnablePassthrough()}
    | rag_prompt
    | llm
    | StrOutputParser()
)

answer = rag_chain.invoke("What is dimensional modeling?")


### RAG with Sources
python
from langchain.chains import RetrievalQAWithSourcesChain

qa_chain = RetrievalQAWithSourcesChain.from_chain_type(
    llm=llm,
    retriever=retriever,
    return_source_documents=True
)

result = qa_chain.invoke({"question": "Explain star schema"})
print(result["answer"])
print(result["source_documents"])


### Conversational RAG
python
from langchain.chains import ConversationalRetrievalChain
from langchain.memory import ConversationBufferMemory

memory = ConversationBufferMemory(
    memory_key="chat_history",
    return_messages=True
)

conversational_chain = ConversationalRetrievalChain.from_llm(
    llm=llm,
    retriever=retriever,
    memory=memory,
    verbose=True
)

# Multi-turn conversation
response1 = conversational_chain.invoke({"question": "What is OLAP?"})
response2 = conversational_chain.invoke({"question": "How does it differ from OLTP?"})


## 9. Agents & Tools

### Creating Custom Tools
python
from langchain.tools import Tool
from langchain.agents import AgentExecutor, create_react_agent
from langchain import hub

def query_database(query: str) -> str:
    """Execute SQL query and return results."""
    # Your database logic
    return f"Results for: {query}"

def calculate_metrics(data: str) -> str:
    """Calculate data quality metrics."""
    # Your metrics logic
    return "Completeness: 95%, Accuracy: 92%"

tools = [
    Tool(
        name="DatabaseQuery",
        func=query_database,
        description="Useful for querying the data warehouse. Input should be a SQL query."
    ),
    Tool(
        name="DataQualityMetrics",
        func=calculate_metrics,
        description="Calculate data quality metrics for a dataset."
    )
]

# Create agent
prompt = hub.pull("hwchase17/react")
agent = create_react_agent(llm, tools, prompt)
agent_executor = AgentExecutor(agent=agent, tools=tools, verbose=True)

result = agent_executor.invoke({
    "input": "Query the sales table and calculate data quality metrics"
})


### SQL Database Agent
python
from langchain_community.agent_toolkits import create_sql_agent
from langchain_community.utilities import SQLDatabase

db = SQLDatabase.from_uri("postgresql://user:pass@localhost/dbname")

agent_executor = create_sql_agent(
    llm=llm,
    db=db,
    agent_type="openai-tools",
    verbose=True
)

result = agent_executor.invoke(
    "What are the top 5 products by revenue in Q4 2024?"
)


## 10. Memory

### Conversation Memory Types
python
from langchain.memory import (
    ConversationBufferMemory,
    ConversationBufferWindowMemory,
    ConversationSummaryMemory,
    ConversationTokenBufferMemory
)

# Full history
buffer_memory = ConversationBufferMemory()

# Last N messages
window_memory = ConversationBufferWindowMemory(k=5)

# Summarized history
summary_memory = ConversationSummaryMemory(llm=llm)

# Token-limited
token_memory = ConversationTokenBufferMemory(
    llm=llm,
    max_token_limit=2000
)

# Using memory in chain
from langchain.chains import ConversationChain

conversation = ConversationChain(
    llm=llm,
    memory=buffer_memory,
    verbose=True
)

conversation.predict(input="Explain data lakes")
conversation.predict(input="What are their advantages?")


## 11. Output Parsers

### Structured Output
python
from langchain_core.output_parsers import PydanticOutputParser
from pydantic import BaseModel, Field

class DataPipelineAnalysis(BaseModel):
    pipeline_name: str = Field(description="Name of the data pipeline")
    bottlenecks: list[str] = Field(description="Identified bottlenecks")
    recommendations: list[str] = Field(description="Optimization recommendations")
    estimated_improvement: str = Field(description="Expected performance improvement")

parser = PydanticOutputParser(pydantic_object=DataPipelineAnalysis)

prompt = ChatPromptTemplate.from_template(
    """Analyze this data pipeline and provide structured feedback.

{format_instructions}

Pipeline Description: {description}
"""
)

chain = prompt | llm | parser

result = chain.invoke({
    "description": "ETL pipeline processing 10GB daily from S3 to Redshift",
    "format_instructions": parser.get_format_instructions()
})

print(result.pipeline_name)
print(result.bottlenecks)


### JSON Output
python
from langchain_core.output_parsers import JsonOutputParser

json_parser = JsonOutputParser()

prompt = ChatPromptTemplate.from_template(
    """Extract key metrics from this log:
    
{log_entry}

Return as JSON with fields: timestamp, severity, component, error_count
"""
)

chain = prompt | llm | json_parser
result = chain.invoke({"log_entry": "2024-01-15 ERROR DataLoader: Failed 3 times"})


## 12. Callbacks & Monitoring

### Token Usage Tracking
python
from langchain.callbacks import get_openai_callback

with get_openai_callback() as cb:
    result = chain.invoke({"question": "What is data lineage?"})
    print(f"Tokens used: {cb.total_tokens}")
    print(f"Cost: ${cb.total_cost}")


### Custom Callbacks
python
from langchain.callbacks.base import BaseCallbackHandler

class DataEngineeringCallback(BaseCallbackHandler):
    def on_llm_start(self, serialized, prompts, **kwargs):
        print(f"LLM started with prompts: {prompts}")
    
    def on_llm_end(self, response, **kwargs):
        print(f"LLM ended with response: {response}")
    
    def on_chain_start(self, serialized, inputs, **kwargs):
        print(f"Chain started with inputs: {inputs}")

callback = DataEngineeringCallback()
chain.invoke({"question": "Explain ETL"}, config={"callbacks": [callback]})


## 13. Production Patterns

### Caching
python
from langchain.cache import InMemoryCache, SQLiteCache
from langchain.globals import set_llm_cache

# In-memory cache
set_llm_cache(InMemoryCache())

# Persistent cache
set_llm_cache(SQLiteCache(database_path=".langchain.db"))


### Error Handling & Retries
python
from langchain_core.runnables import RunnableRetry

chain_with_retry = chain | RunnableRetry(
    max_attempts=3,
    wait_exponential_jitter=True
)

try:
    result = chain_with_retry.invoke({"input": "query"})
except Exception as e:
    print(f"Failed after retries: {e}")


### Batch Processing
python
# Batch invocation
inputs = [
    {"question": "What is ETL?"},
    {"question": "What is data modeling?"},
    {"question": "What is data governance?"}
]

results = chain.batch(inputs)

# Async batch
import asyncio

async def process_batch():
    results = await chain.abatch(inputs)
    return results

results = asyncio.run(process_batch())


## 14. Common Data Engineering Use Cases

### Document Q&A System
python
# Complete pipeline
documents = DirectoryLoader("./docs", glob="**/*.md").load()
chunks = RecursiveCharacterTextSplitter(chunk_size=1000).split_documents(documents)
vectorstore = Chroma.from_documents(chunks, embeddings)
retriever = vectorstore.as_retriever(search_kwargs={"k": 4})

qa_chain = (
    {"context": retriever | format_docs, "question": RunnablePassthrough()}
    | rag_prompt
    | llm
    | StrOutputParser()
)


### SQL Query Generation
python
sql_prompt = ChatPromptTemplate.from_template("""
Given the database schema:
{schema}

Generate a SQL query to answer: {question}

Return only the SQL query, no explanation.
""")

sql_chain = sql_prompt | llm | StrOutputParser()

query = sql_chain.invoke({
    "schema": "Tables: users(id, name, email), orders(id, user_id, amount)",
    "question": "Find total revenue by user in December 2024"
})


### Data Quality Report Generator
python
report_prompt = ChatPromptTemplate.from_template("""
Analyze this data quality report and provide insights:

Metrics:
{metrics}

Generate:
1. Summary of data quality
2. Critical issues
3. Recommendations
""")

report_chain = report_prompt | llm | StrOutputParser()


### Log Analysis Agent
python
from langchain.agents import create_openai_functions_agent

def analyze_logs(log_file: str) -> str:
    """Analyze log file for errors and patterns."""
    # Log analysis logic
    return "Found 15 errors, 3 critical"

def suggest_fixes(error_type: str) -> str:
    """Suggest fixes for common errors."""
    # Fix suggestion logic
    return "Increase memory allocation"

log_tools = [
    Tool(name="AnalyzeLogs", func=analyze_logs, description="Analyze log files"),
    Tool(name="SuggestFixes", func=suggest_fixes, description="Suggest error fixes")
]

log_agent = create_openai_functions_agent(llm, log_tools, prompt)
log_executor = AgentExecutor(agent=log_agent, tools=log_tools)


## 15. Best Practices

### Prompt Engineering
python
# Be specific and provide context
good_prompt = """
You are a data engineer reviewing pipeline performance.
Given these metrics: {metrics}
Identify the top 3 bottlenecks and suggest specific optimizations.
Format: Bottleneck | Impact | Solution
"""

# Use few-shot examples for consistency
# Use output parsers for structured data
# Test prompts with different inputs


### Chunking Strategy
python
# For technical docs: smaller chunks (500-1000 tokens)
# For narrative content: larger chunks (1000-2000 tokens)
# Always use overlap (10-20% of chunk size)
# Consider semantic boundaries (paragraphs, sections)


### Vector Store Selection
python
# Development: FAISS (fast, in-memory)
# Production: Qdrant, Pinecone, Weaviate (scalable, managed)
# Self-hosted: Chroma, Milvus (full control)


### Cost Optimization
python
# Use caching for repeated queries
# Choose appropriate models (GPT-3.5 vs GPT-4)
# Implement query rewriting to reduce tokens
# Monitor with callbacks
# Use local embeddings when possible


## Quick Reference Commands

python
# Installation
pip install langchain langchain-openai langchain-community chromadb

# Basic setup
from langchain_openai import ChatOpenAI
llm = ChatOpenAI(model="gpt-4")

# Quick RAG
from langchain_community.vectorstores import Chroma
from langchain_community.document_loaders import DirectoryLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter

docs = DirectoryLoader("./data").load()
chunks = RecursiveCharacterTextSplitter(chunk_size=1000).split_documents(docs)
vectorstore = Chroma.from_documents(chunks, embeddings)
retriever = vectorstore.as_retriever()

# Query
chain = retriever | format_docs | llm
answer = chain.invoke("Your question")


---

## Additional Resources

- **Documentation**: https://python.langchain.com/
- **API Reference**: https://api.python.langchain.com/
- **LangSmith** (monitoring): https://smith.langchain.com/
- **Community**: https://github.com/langchain-ai/langchain
- **Templates**: https://github.com/langchain-ai/langchain/tree/master/templates