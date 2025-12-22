# LangChain Chains and Agents for Building Applications

Welcome to **LangChain Chains and Agents for Building Applications**. After reviewing this material, you will be able to:

- Describe **chains** in LangChain for generating responses  
- Understand how **LangChain stores memory**  
- Define and explain **agents** used in LangChain  

---

## What is LangChain?

LangChain is a **platform embedded with APIs** designed to help developers build applications with advanced language processing capabilities. It is widely used for creating intelligent applications powered by Large Language Models (LLMs).

LangChain provides several tools for building applications, including:

- Documents  
- Chains  
- Agents  

This document focuses on **chains**, **memory**, and **agents**.

---

## Chains in LangChain

In LangChain, a **chain** is a sequence of calls where:

- Each step takes an input
- Produces an output
- Passes that output to the next step

This creates a seamless flow of information.

### Sequential Chains

A **sequential chain** consists of multiple steps executed in order:

- Output of Step 1 → Input of Step 2  
- Output of Step 2 → Input of Step 3  

---

## Example: Sequential Chain with Three Steps

### Objective

Identify:
1. A famous dish from a user-specified location  
2. The recipe for that dish  
3. The estimated cooking time  

### Chain Overview

- **Chain 1**: Selects a famous dish based on location  
- **Chain 2**: Generates the recipe for the dish  
- **Chain 3**: Estimates the cooking time  

---

### Chain 1: Location to Dish

**Input:** User-specified location (e.g., China)  
**Output:** Famous dish from that location (e.g., *Peking Duck*)

**Steps:**

1. Define a template string asking for a famous dish from a location  
2. Create a prompt template with `location` as the input variable  
3. Create an LLM chain (e.g., using a Mixtral LLM)  
4. Store the output under the key `meal`  

---

### Chain 2: Dish to Recipe

**Input:** Dish name (output from Chain 1)  
**Output:** Recipe for the dish  

**Steps:**

1. Define a template asking for a simple recipe for a given meal  
2. Create a prompt template with `meal` as the input variable  
3. Create an LLM chain named `dish_chain`  
4. Store the output under the key `recipe`  

---

### Chain 3: Recipe to Cooking Time

**Input:** Recipe (output from Chain 2)  
**Output:** Estimated cooking time  

**Steps:**

1. Define a template asking for cooking time based on a recipe  
2. Create a prompt template with `recipe` as the input variable  
3. Create an LLM chain named `recipe_chain`  
4. Store the output under the key `time`  

---

## Combining Chains into a Sequential Chain

All three chains are wrapped into a **single sequential chain**, forming a unified workflow.

- Invoking the combined chain allows tracing the flow from start to finish  
- Setting `verbose = true` shows detailed intermediate outputs  
- This provides transparency into how each step transforms the input  

---

## Memory in LangChain

Memory plays a crucial role in maintaining **context and continuity** across interactions.

### How Memory Works

- Chains read from memory to enhance user inputs  
- After execution, chains write inputs and outputs back to memory  
- This ensures historical context is preserved  

---

### Chat Message History

LangChain provides the `ChatMessageHistory` class to manage conversation history, including:

- Human messages  
- AI messages  

**Example Flow:**

1. Create a `ChatMessageHistory` instance  
2. Add an AI message (e.g., `"Hi"`)  
3. Add a human message (e.g., `"What is the capital of France?"`)  
4. The model generates responses using stored memory  

---

## Agents in LangChain

Agents are **dynamic systems** where a language model:

- Determines which actions to take  
- Sequences actions such as predefined chains  
- Uses reasoning to decide next steps  

### Key Characteristics of Agents

- The language model generates text to guide actions  
- Agents do not execute actions directly  
- They integrate with external tools such as:
  - Search engines  
  - Databases  
  - Websites  

---

### Example: Agent Query

If a user asks:

> *What is the population of Italy?*

The agent:
1. Uses LLM reasoning to determine the approach  
2. Queries a database or external source  
3. Returns a curated response  

This demonstrates autonomous reasoning combined with tool usage.

---

## Example: Pandas DataFrame Agent

LangChain supports agents for structured data interaction.

### Pandas DataFrame Agent

This agent allows users to:

- Query data using natural language  
- Visualize and analyze data programmatically  

**Setup Steps:**

1. Instantiate `create_pandas_dataframe_agent`  
2. Pass:
   - LLM chat model  
   - Pandas DataFrame  
3. Set `verbose = true` to observe LLM reasoning  
4. Invoke a query such as:
   - *How many rows are in the DataFrame?*  

The LLM converts the query into Python code, executes it, and returns the result.

**Example Output:**  
The DataFrame contains **139 rows**.

---

## Summary

In this lesson, you learned that:

- LangChain is a platform embedding APIs for application development  
- **Chains** are sequences of calls where outputs flow into subsequent inputs  
- Building a chain involves:
  - Defining a prompt template  
  - Creating a prompt template object  
  - Creating an LLM chain  
- **Memory** enables reading and writing historical data for context retention  
- **Agents** are dynamic systems that:
  - Use LLM reasoning  
  - Sequence actions  
  - Integrate with external tools to fulfill user requests  

These concepts form the foundation for building powerful, intelligent applications with LangChain.
