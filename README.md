Q-Agent 🧠
Graph-aware Question Answering Agent for Knowledge Graphs
Q-Agent is a powerful, LLM-powered system that seamlessly bridges natural language queries with structured data in a Neo4j knowledge graph. By leveraging Azure OpenAI for language and embedding models, LangChain, and LangGraph, it transforms user questions into precise Cypher queries, executes multi-hop traversals, and delivers context-rich, structured responses. The system ensures reliability through guardrails, query validation, and correction, making it ideal for enterprise-grade applications.

🚀 Features

Semantic Query Routing: Classifies and routes user queries to the appropriate domain or subgraph (e.g., components, policies, workspaces).
Dynamic Cypher Generation: Converts natural language into optimized Cypher queries using few-shot learning and semantic similarity example selection.
Multi-hop Traversal: Supports complex reasoning across connected nodes for deeper insights into graph relationships.
Guardrails & Constraints: Ensures queries are relevant, safe, and aligned with the graph schema.
Query Validation & Correction: Validates Cypher queries for syntax and semantic accuracy, with deterministic correction for errors.
Structured JSON Outputs: Delivers machine-readable responses for integration into downstream workflows.
LangGraph Orchestration: Manages stateful workflows for query processing, validation, execution, and answer generation.
Vector-Augmented RAG: Integrates Azure OpenAI embeddings for hybrid symbolic and semantic reasoning.
Visualization Support: Generates graph visualizations to enhance interpretability (via Graphviz or similar tools).


🏗️ Architecture
User Query ─► Guardrails ─► Cypher Generation ─► Validation/Correction ─► Neo4j Execution
                                  │                            │
                                  ▼                            ▼
                     Structured JSON Response ◄─── Final Answer Generation
                                  │
                                  ▼
                     Knowledge Graph Visualization


LLM Core: Azure OpenAI GPT models for chat and embeddings.
Pipeline: LangGraph for workflow orchestration, LangChain for chain management.
Storage: Neo4j for graph data, with optional PostgreSQL + pgvector for vector-augmented RAG.
Augmentation: Semantic similarity-based example selection for robust Cypher query synthesis.


📦 Quick Start
1. Clone the Repository
```
git clone https://github.com/DerrickKirimi/q-agent.git
cd q-agent
```
2. Install Dependencies
```
pip install --upgrade --quiet langchain langchain-neo4j langchain-openai langgraph
```

3. Set Up Environment
Create a .env file with the following variables:
```
AZURE_OPENAI_ENDPOINT=https://<your-endpoint>.openai.azure.com/
AZURE_OPENAI_API_KEY=<your-api-key>
AZURE_OPENAI_API_VERSION=2024-12-01-preview
AZURE_OPENAI_CHAT_DEPLOYMENT_NAME=gpt-4-32k
AZURE_OPENAI_EMBEDDING_ENDPOINT=https://<your-embedding-endpoint>.cognitiveservices.azure.com/
AZURE_OPENAI_EMBEDDING_API_KEY=<your-embedding-api-key>
AZURE_OPENAI_EMBEDDING_DEPLOYMENT_NAME=text-embedding-3-large
NEO4J_URI=bolt://<neo4j-host>:7687
NEO4J_USERNAME=neo4j
NEO4J_PASSWORD=<your-neo4j-password>
```
4. Configure Neo4j Database

Ensure a Neo4j database is running (local or cloud-hosted, e.g., Neo4j Aura).
The database should include nodes (Component, SourceSystem, Chunk) and relationships (HAS_CHILD, SOURCED_FROM, numbered relationships like :3, :4, etc.) as per the schema.

5. Run the Agent
```
python main.py
```

# Example query
```
query = "What policies are available?"

response = agent.answer(query)

print(response)
```

```
Output:
{
  "answer": "The available policies are:\n\n1. IT Compute Infrastructure Management Policy\n2. IT Asset Management Policy\n3. IT Connectivity Infrastructure Management Policy\n...",
  "steps": ["guardrail", "generate_cypher", "validate_cypher", "execute_cypher", "generate_final_answer"],
  "cypher_statement": "MATCH (c:Component {type: \"Policy\"}) RETURN c.name, c.description"
}
```


Another Example:
```
query = "What is Compute Infrastructure Management (Physical Server)?"
response = agent.answer(query)
print(response)
```

```
Output:
{
  "answer": "Compute Infrastructure Management (Physical Server) is a Policy Section.",
  "steps": ["guardrail", "generate_cypher", "validate_cypher", "execute_cypher", "generate_final_answer"],
  "cypher_statement": "MATCH (c:Component {name: \"Compute Infrastructure Management (Physical Server)\"}) RETURN c.type, c.description, c.rag_content"
}
```

📜 License
This project is licensed under the MIT License. See the LICENSE file for details.
