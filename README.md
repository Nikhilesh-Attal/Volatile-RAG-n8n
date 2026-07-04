Volatile RAG (Zero-Maintenance Unified RAG Automation Pipeline)
<img width="707" height="612" alt="image" src="https://github.com/user-attachments/assets/26bc790b-20d5-4ce9-b305-f4cbe97870f7" />

An elegant, single-workflow Retrieval-Augmented Generation (RAG) pipeline built entirely within n8n. This system serves as a conversational AI agent that can ingest web URLs on the fly, scrape and chunk their contents into temporary in-memory vector storage, and instantly answer context-aware questions—all with zero database maintenance and completely free resources.

🚀 Key Features
Unified Architecture: Combines both ingestion (scraping/embedding) and retrieval (QA/chatting) into a single, cohesive workflow using an intelligent routing mechanism.

Zero-Database Overhead: Utilizes n8n's native In-Memory Vector Store. Webpage embeddings are saved directly to your server's RAM and isolated by session ID, automatically wiping clean to prevent memory bloat.

Pop-up Proof Scraping: Uses the Jina AI Reader API to bypass cookie walls, pop-ups, and scripts, returning clean, LLM-ready Markdown.

Blazing-Fast Inference: Powered by Groq (Llama 3) for near-instant responses at zero cost.

Session Isolation: Dynamically maps embeddings to individual sessionId keys so multiple chat sessions never cross-contaminate data.

🛠️ The Tech Stack
Automation Platform: n8n (Advanced AI Nodes)

LLM Orchestration: AI Agent Node with Window Buffer Memory

Inference Engine: Groq Cloud (llama3-8b-8192)

Embeddings Model: Hugging Face Inference (sentence-transformers/all-MiniLM-L6-v2)

Web Scraper: Jina AI Reader (No API key required)

Vector Storage: Native n8n In-Memory Vector Store

📋 Prerequisites
Before importing the workflow, make sure you have:

A local or cloud instance of n8n (v1.19.0+ recommended for advanced AI features).

A free Groq API Key from the Groq Console.

A free Hugging Face Access Token from your Hugging Face Settings.

📥 Installation & Setup
Copy the Workflow JSON: Copy the complete project JSON code provided in your setup notes.

Import into n8n:

Open your n8n instance and create a New Workflow.

Click anywhere on the blank canvas and press Ctrl + V (or Cmd + V on Mac) to paste the nodes directly.

Configure Credentials:

Open the Groq Chat Model node, click on Credential Package, and add your Groq API key.

Open the Embeddings (Insert) and Embeddings (Retrieve) nodes, click on Credential Package, and paste your Hugging Face Access Token.

Save & Activate: Click Save in the top right corner.

🔄 How It Works (Under the Hood)
               +------------------+
               |   Chat Trigger   |
               +--------+---------+
                        |
                        v
             +----------+----------+
             | Is URL or Question? |
             +----+-----------+----+
                  |           |
        (If URL)  |           | (If Question)
                  v           v
    +-------------+----+    +-------------+----+
    |  Scrape Jina AI  |    |     AI Agent     |
    +-------------+----+    +------+------+----+
                  |                | (Uses Tool)
                  v                v
    +-------------+----+    +------+------+----+
    | Insert to Memory |<---|  Search Tool     |
    +------------------+    +------------------+
The Router: When you interact with the chat interface, an IF Node evaluates your input using a regular expression.

Ingestion Route: If the message contains a URL, the workflow routes to Jina AI, cleans the webpage content, splits it recursively into 1000-character chunks via the Recursive Character Text Splitter, vectorizes it via Hugging Face, and inserts it into n8n's memory.

Retrieval Route: If the message is a standard text question, it paths to the AI Agent. The agent invokes the webpage_search tool to dynamically retrieve relevant fragments from your temporary store and feeds them to Groq to synthesize a factual response.

💬 Usage Guide
Open the n8n chat window interface by clicking the Chat icon on the Chat Trigger node.

Step 1 (Ingest): Simply paste a URL.

Example: [https://en.wikipedia.org/wiki/Artificial_intelligence](https://en.wikipedia.org/wiki/Artificial_intelligence)

AI Response: "I've read and memorized that webpage! What would you like to know about it?"

Step 2 (Query): Ask any question based on that link.

Example: What are the core sub-fields mentioned in the history section?

Step 3 (Reset): Paste a completely new link at any point. The pipeline will automatically flush the previous session data and memorize the new page.
