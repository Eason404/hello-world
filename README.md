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
