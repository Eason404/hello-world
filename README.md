# maven-project

Simple Maven Project

rchitecture Blueprint: Zero-Cost Local RAG System (Vanilla JS Edition)
1. Executive Summary
This blueprint details a lightweight, zero-egress semantic search engine designed to serve as an intelligent directory for internal (and eventually external) product documentation. It eliminates the need for expensive API calls, cloud vector databases, and heavy frontend bundlers. By leveraging browser-based AI models and native math calculations, it delivers highly accurate search results while strictly adhering to corporate data security policies.

2. System Architecture (The "No-Build" Stack)
This architecture is optimized for a frontend served "as-is" by a basic HTTP server, integrating perfectly with existing Tailwind UI and Vanilla JS setups.

Tier 1: Offline ETL Pipeline (Node.js)

Runs locally or via CI/CD before deploying the static site.

Reads Markdown (.md) files, splits them by headings, and extracts custom enterprise dictionary terms (e.g., specific API routes or business jargon).

Uses @xenova/transformers locally to generate vector embeddings for each document chunk.

Outputs a single, flat wiki-index.json file containing metadata, text snippets, and vector arrays.

Tier 2: Frontend AI Engine (CDN & ES Modules)

Loads Transformers.js directly via a jsDelivr CDN link using <script type="module">.

Downloads the 20MB all-MiniLM-L6-v2 quantized model and caches it in the browser's IndexedDB upon initial interaction.

Calculates similarity using a pure Vanilla JavaScript Cosine Similarity function, completely removing the need for WebAssembly vector databases like Voy.

Tier 3: UI Injection & Rendering (Tailwind + Mermaid.js)

Attaches event listeners to the existing Tailwind-styled search input.

Injects results dynamically into the DOM using template literals, preserving the current application theme.

Hooks into Mermaid.js to trigger mermaid.init() on newly injected Markdown chunks, ensuring diagrams render correctly in the search results.

3. Implementation Guide (For Claude / Copilot Prompting)
You can provide the following requirements directly to your AI coding assistant to generate the exact scripts needed.

Phase 1: The Node.js Indexer (build-index.js)
Task: Write a Node.js script using fs and glob to read all .md files in a docs/ folder.

Logic: Split the content by H2 (##) tags. For each chunk, use the @xenova/transformers pipeline (feature-extraction, Xenova/all-MiniLM-L6-v2) to generate an embedding.

Output: Write the results to an array of objects { id, title, url, snippet, vector } and save it as wiki-index.json in the public server directory.

Phase 2: The Vanilla JS Search Engine (app.js)
Task: Create an ES module that imports pipeline from [https://cdn.jsdelivr.net/npm/@xenova/transformers@2.16.0](https://cdn.jsdelivr.net/npm/@xenova/transformers@2.16.0).

Initialization: Fetch wiki-index.json and initialize the pipeline with { quantized: true }.

Math: Write a pure JavaScript cosineSimilarity(vecA, vecB) function.

Search Logic: Create a searchWiki(query) function that embeds the user query, loops through the JSON to calculate scores, and returns the top 3 results sorted by score.

Phase 3: DOM Manipulation & Theming
Task: Attach an input event listener to the existing search bar ID.

UI Update: When searchWiki returns results, map them into HTML strings using existing Tailwind classes (e.g., text-blue-600, hover:bg-gray-50) and inject them into the results container.

Mermaid Hook: Immediately after updating the DOM's innerHTML, check if window.mermaid exists and run mermaid.init(undefined, document.querySelectorAll('.mermaid')).



Here is the complete, upgraded English blueprint for the Hybrid Search Architecture. You can copy this directly to your other device to guide Claude or GitHub Copilot in generating the new code.

Architecture Blueprint v2.0: Zero-Cost Hybrid RAG System (Vanilla JS Edition)
1. Executive Summary
This upgraded blueprint transitions our client-side Retrieval-Augmented Generation (RAG) system from a "Pure Semantic Search" to a Hybrid Search Architecture. By combining semantic vector embeddings (for contextual understanding) with an in-memory lexical search engine (for exact keyword/acronym matching), we solve the issue of low match accuracy (<60%) on enterprise-specific terminology. The system remains completely serverless, zero-cost, and runs entirely in the browser using Vanilla JS and Tailwind CSS.

2. System Architecture (The Hybrid Pipeline)
Tier 1: The Offline ETL Pipeline (Node.js)
Task: Parse Markdown files (.md) and prepare dual-index data.

Output 1 (Semantic): Uses @xenova/transformers (all-MiniLM-L6-v2) to generate vector embeddings for each chunk.

Output 2 (Lexical): Extracts the raw text and titles of each chunk.

Output 3 (Dictionary): A synonyms.json file containing enterprise-specific acronyms (e.g., {"Apollo": "API Gateway", "Auth": "Authentication"}).

Final Artifact: A unified wiki-index.json containing { id, title, url, snippet, full_text, vector }.

Tier 2: The Frontend Hybrid Engine (CDN & ES Modules)
Semantic Engine: Loads Transformers.js via jsDelivr to run local vector embeddings and calculates Cosine Similarity using pure Vanilla JS.

Lexical Engine [NEW]: Loads MiniSearch (a lightweight <10KB full-text search library) via CDN to perform exact keyword matching with fuzzy tolerance.

Query Rewriting [NEW]: Intercepts the user's query and replaces internal jargon with standard terms using the synonyms.json map before searching.

Reciprocal Rank Fusion (RRF) [NEW]: A custom JavaScript algorithm that merges the results from both engines. If a document ranks high in both semantic meaning and exact keyword match, it is boosted to the absolute top of the results.

Tier 3: UI Injection & Rendering (Tailwind + Mermaid)
DOM Manipulation: Injects results dynamically using existing Tailwind classes.

Transparency [NEW]: Optionally displays a small tag on the results indicating why it was matched (e.g., <span class="text-xs text-blue-500">Semantic Match</span> or <span class="text-xs text-green-500">Exact Keyword</span>).

Mermaid Integration: Triggers mermaid.init() on newly injected Markdown chunks.

3. Implementation Guide (For Claude / Copilot Prompting)
Feed these exact prompts to your AI assistant to generate the Vanilla JS implementation:

Phase 1: The Dual-Index Node.js Script
Prompt for AI: "Write a Node.js script using fs, glob, and @xenova/transformers to read Markdown files in a /docs folder. Split them by H2 headers. For each chunk, keep the full_text string, and generate a vector embedding using Xenova/all-MiniLM-L6-v2. Output a single array of objects { id, title, url, snippet, full_text, vector } and save it as wiki-index.json."

Phase 2: The Vanilla JS Hybrid Search Module
Prompt for AI: "Create a Vanilla JS ES Module (app.js).

Load pipeline from @xenova/transformers via CDN.

Load MiniSearch via CDN (https://cdn.jsdelivr.net/npm/minisearch@6.3.0/dist/umd/index.min.js).

Fetch wiki-index.json. Initialize MiniSearch indexing the title and full_text fields.

Write a cosineSimilarity(vecA, vecB) function.

Write a hybridSearch(query) function that runs BOTH the MiniSearch query and the Transformers vector query.

Implement a basic Reciprocal Rank Fusion (RRF) algorithm to combine the two arrays of results and return the top 3."

Phase 3: The Tailwind UI Hook
Prompt for AI: "Write Vanilla JS to attach an event listener to an <input id="search">. When the user types, call hybridSearch(query). Map the results into an HTML template literal using basic Tailwind classes (e.g., padding, borders, hover states). Inject this HTML into <div id="results">. Immediately after injection, check if window.mermaid exists and call mermaid.init()."
