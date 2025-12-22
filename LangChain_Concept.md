# LangChain Core Concepts

Welcome to **LangChain Core Concepts**. After reviewing this material, you will be able to define LangChain and describe its key components.

---

## What is LangChain?

LangChain is an **open-source interface** that simplifies application development using **Large Language Models (LLMs)**. It provides a structured approach to integrating language models into a wide range of use cases, including:

- Natural Language Processing (NLP)
- Data retrieval
- Conversational AI
- Document summarization

---

## Core Components of LangChain

LangChain consists of several components:

- Documents
- Chains
- Agents
- Language Model
- Chat Model
- Chat Message
- Prompt Templates
- Output Parsers

This document focuses on the following components:

- Language Model
- Chat Model
- Chat Message
- Prompt Templates
- Output Parsers

---

## Language Models in LangChain

Language Models are the **foundation** of LangChain. They:

- Accept text input
- Generate text output
- Perform tasks such as summarization and content generation

LangChain supports multiple language model providers, including:

- IBM
- OpenAI
- Google
- Meta

### Example: Using IBM WatsonX.AI

To generate a response for a new sales approach:

1. Use **IBM WatsonX.AI** to create an LLM based on the **Mixtral 8x7B Instruct** model.
2. Import required dependencies such as:
   - `GenParams`
   - `Model Inference`
3. Customize model parameters such as:
   - Tokens
   - Temperature
4. Create the model object and generate a response for the provided prompt.

The model outputs a generated text response based on the input prompt.

---

## Chat Model vs Language Model

### Chat Model

A **Chat Model** is optimized for conversations. It:

- Understands questions or prompts
- Responds in a human-like conversational manner

### Creating a Chat Model

1. Create a language model using WatsonX.AI.
2. Convert it into a chat model using WatsonX’s `LLM` function.
3. The result is a conversational LLM capable of engaging in dialogue.

#### Example Question

> *Who is man’s best friend?*

The chat model generates a conversational response to this input.

---

## Chat Messages

Chat models handle various message types to operate effectively in dynamic conversations:

- **Human Message** – User input
- **AI Message** – Model-generated response
- **System Message** – Instructions for the model
- **Function Message** – Outputs from function calls (with name parameter)
- **Tool Message** – Tool interactions used to achieve results

Each chat message has two key properties:

- **Role** – Who is speaking
- **Content** – What is being said

### Example: System Message

A system message may instruct the model to behave as an AI bot and answer:

> *What to eat in one short sentence.*

---

## Conversational Flow with Chat Models

To generate responses:

1. Configure the model using a **system message** (e.g., set it as a fitness activity bot).
2. Simulate past conversation using **human messages** and **AI messages**.
3. Generate new responses based on prior dialogue.

Alternatively, the chat model can operate directly with **human messages only**, responding without predefined system or AI message queues.

---

## Prompt Templates

Prompt templates translate user questions into **clear, structured instructions** for the language model, ensuring coherent and relevant responses.

### Types of Prompt Templates

- **String Prompt Template** – Single-string formatting
- **Chat Prompt Template** – Message-based prompts
- **Message Prompt Templates**, including:
  - AI Message Prompt Template
  - System Message Prompt Template
  - Human Message Prompt Template
  - Chat Message Prompt Template
- **Messages Placeholder** – Full control over message rendering
- **Few-Shot Prompt Template** – Provides examples (shots) for the LLM

### Using Chat Prompt Templates

- Specify each message’s **role** and **content**
- Use parameter placeholders for reusable, dynamic prompts
- Format prompts to adapt to different input values

---

## Example Selectors in Prompt Templates

Selecting the most relevant examples improves prompt quality.

### Few-Shot Prompt Templates

Few-shot examples provide context that guides the LLM toward the desired output.

### Example Selector Techniques

LangChain supports multiple strategies:

- Semantic Similarity
- Max Marginal Relevance (for diversity)
- Examples of Efficient Prompts
- N-Gram Overlap (for textual similarity)

These selectors optimize few-shot prompts by choosing the most relevant examples from an example library.

---

## Output Parsers

Output Parsers transform raw LLM outputs into **structured formats** suitable for further processing.

### Supported Formats

LangChain provides Output Parsers for:

- JSON
- XML
- CSV
- Pandas DataFrames

### Example: CSV Output

Using a **Comma-Separated List Output Parser**, the LLM’s response can be converted into CSV format. This makes the output easy to analyze in spreadsheet applications.

---

## Summary

In this overview, you learned:

- LangChain is an open-source framework that simplifies LLM-based application development.
- Language Models generate text outputs from text inputs.
- Chat Models enable conversational, human-like interactions.
- Chat Messages include human, AI, system, function, and tool messages.
- Prompt Templates structure instructions for better LLM responses.
- Example Selectors guide the LLM using relevant contextual examples.
- Output Parsers convert LLM outputs into structured, usable formats.

These components together form the foundation of LangChain’s powerful and flexible architecture.
