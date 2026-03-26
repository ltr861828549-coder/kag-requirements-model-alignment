# kag-requirements-model-alignment

## Project Overview

This project designs and implements a requirement change impact analysis and automatic model alignment system based on the KAG (Knowledge-Augmented Generation) framework. The system combines the semantic understanding capability of large language models with the structured reasoning ability of knowledge graphs to analyze the impact of natural language requirement changes on an existing SysML multi-view requirement model and automatically generate model modification plans.

The main goal of this project is to solve the problem that requirement changes in complex software and systems engineering projects are difficult to trace, expensive to maintain manually, and likely to cause inconsistency between textual requirements and formal models. By introducing knowledge graph reasoning and multi-hop propagation analysis, the system can identify affected model elements, explain the propagation chain of a change, and provide engineers with interpretable decision support.

This project is especially focused on the application of multi-hop reasoning in requirement change propagation analysis. It aims to improve the consistency maintenance process between requirement descriptions and SysML models, reduce manual model maintenance cost, and support intelligent requirements engineering in model-based system development.

## Research Motivation

In large-scale and cross-domain software systems, requirement changes are frequent and unavoidable. Traditional requirement change management often relies on manual analysis and manual maintenance of modeling tools such as SysML. This process is time-consuming, error-prone, and difficult to scale when the dependency chains among model elements become increasingly complex.

Although SysML supports multi-view modeling, including requirement, structure, behavior, and interface views, maintaining consistency across these views remains challenging. Once a requirement changes, engineers often need to inspect multiple diagrams and dependent elements manually. This project addresses this challenge by building a closed-loop pipeline from natural language change input to semantic parsing, graph-based reasoning, impact analysis, and model alignment suggestion generation.

## Key Features

The system provides the following capabilities:

Natural language requirement change understanding.  
Users can describe requirement changes in natural language, and the system extracts the changed module, intent, and affected semantics.

Knowledge graph based model representation.  
SysML model elements and their cross-view relations are represented as graph nodes and edges, allowing structured storage and query.

Multi-hop impact propagation analysis.  
Starting from a changed module, the system performs multi-hop graph traversal and reasoning to identify direct and indirect impacted modules.

Explainable reasoning evidence.  
The system does not only return impacted modules, but also provides the propagation path and relation chain as evidence.

Automatic model alignment suggestions.  
Based on the affected module set and propagation paths, the system generates structured modification suggestions for model alignment.

Visualization and JSON output.  
The system supports a Streamlit-based interactive frontend and can export impact analysis results as JSON for further inspection or reuse.

## System Architecture

The overall workflow of the system is as follows:

1. Requirement engineers input a natural language requirement change.
2. A large language model parses the change intent and identifies the target module.
3. The target module is matched to the corresponding node in the SysML knowledge graph.
4. Multi-hop reasoning is performed on the graph through dependency, composition, flow, and association relations.
5. The impacted modules and propagation paths are collected.
6. The system generates an explainable impact analysis report and structured JSON result.
7. Based on the reasoning evidence, the system outputs model alignment and modification suggestions.

This architecture integrates semantic understanding and symbolic reasoning into one pipeline, forming a complete requirement-change-to-model-alignment solution.

## Data Types

The project currently involves several types of data:

### 1. SysML model data
The source model can be expressed in PlantUML / SysML-like textual form. The parser extracts model elements as nodes and their relations as edges, then imports them into Neo4j as a graph structure.

Typical node attributes include:
- `id`
- `name`
- `type`

Typical relation attributes include:
- `source`
- `target`
- `type`
- `arrow`
- `desc`

### 2. Knowledge graph data
After parsing, the model is stored in Neo4j as a knowledge graph. Each SysML element is represented as a `SysML_Element` node, and relations such as `COMPOSITION`, `DEPENDENCY`, `FLOW`, and `ASSOCIATION` are used to represent structural and behavioral dependencies.

### 3. Requirement change query data
Users input natural language questions such as:
- “If the function button panel adds a volume control feature, what will be affected?”
- “Why will this module affect the main control module?”
- “How should related modules be modified after this change?”

These queries are treated as unstructured text input and are processed by the LLM plus graph reasoning pipeline.

### 4. Analysis result data
The system generates structured JSON outputs for each analysis session. These JSON files usually contain:
- metadata of the query
- start module information
- impacted module list
- reasoning paths
- node and edge sets
- LLM-generated explanation and modification suggestions

### 5. Conversation and state data
To support continuous interaction, the system stores chat history and conversation state locally. This includes previously queried modules, impacted modules, traversal frontier, visited modules, and saved paths.

## Data Scale

At the current project stage, the dataset scale is moderate and mainly designed for prototype validation and system demonstration.

The data scale includes three levels:

### Prototype-scale graph data
The project currently supports importing a SysML-like model from PlantUML text into Neo4j. A typical experimental dataset contains tens to hundreds of nodes and relationships, representing requirement elements, functional components, structural modules, interfaces, and dependencies.

### Session-scale interaction data
Each user query produces one analysis result JSON file. Over multiple sessions, the system accumulates conversational history, state snapshots, and graph analysis outputs. This is suitable for demonstrating iterative requirement change propagation.

### Expandable future scale
The architecture is designed to scale to larger industrial requirement models. In future work, the graph can be extended to include:
- more SysML views
- larger requirement-document collections
- trace links between requirements, design models, and implementation artifacts
- versioned change histories

Therefore, while the current implementation is a research prototype, the data model is extensible enough for larger requirement engineering scenarios.

## Technology Stack

This project uses the following technologies:

### Backend
Python is used as the main development language for system integration, graph processing, reasoning orchestration, and file management.

### Knowledge Graph
Neo4j is used as the graph database for storing SysML elements and their relations. It supports efficient graph queries, path traversal, and multi-hop dependency analysis.

### Large Language Model
The project uses Tongyi / Qwen through LangChain-based orchestration for semantic parsing, reasoning explanation, and suggestion generation.

### Prompt and Chain Orchestration
LangChain is used to build multiple task-oriented chains, including:
- impact analysis chain
- reasoning explanation chain
- modification suggestion chain
- continued propagation chain

### Frontend
Streamlit is used to build the interactive visualization interface. It supports:
- user chat interaction
- impacted module visualization
- graph structure display
- JSON result browsing and download

### Data Format
JSON is used as the main structured output format for analysis results. Local files are also used to persist conversation history and conversation state.

### Modeling Input
PlantUML is used to describe the initial SysML-like model in a lightweight textual form, which is then parsed and imported into Neo4j.

## Core Reasoning Mechanism

A major technical highlight of this project is multi-hop reasoning over the requirement model knowledge graph.

Instead of relying only on semantic similarity or one-step dependency lookup, the system explores multiple hops in the graph to identify hidden propagation chains. For example, a requirement change may first affect a functional module, then propagate to a controller, then to an interface, and finally to another subsystem. This kind of chain is difficult to detect with traditional keyword matching or shallow traceability analysis.

By combining graph traversal with LLM-based explanation, the system can both compute the impact range and explain why a certain module is affected. This makes the analysis more accurate, transparent, and useful for requirement engineers.

## Example Output

For each requirement change query, the system can generate:

- a human-readable explanation of impacted modules
- direct vs indirect impact classification
- path-based reasoning evidence
- modification suggestions for related modules
- a graph-oriented JSON result for visualization and reuse

This allows the system to serve both as an intelligent assistant and as a structured analysis tool.

## Project Structure

A typical project structure is as follows:

```bash
.
├── streamlit_app.py          # Streamlit frontend
├── change_agent.py           # Main agent logic
├── graph_tools.py            # Neo4j graph query and reasoning utilities
├── llm_chains.py             # LLM prompt chains
├── conversation_state.py     # Session state persistence
├── file_chat_history.py      # Chat history storage
├── config.py                 # Configuration file
├── coffee_neo4j.py           # PlantUML to Neo4j import script
├── analysis_outputs/         # Generated JSON analysis results
├── chat_history/             # Stored chat history
└── state_store/              # Stored session states

