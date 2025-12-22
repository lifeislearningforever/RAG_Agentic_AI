# LangChain LCEL Chaining Method

## Overview

After reading this guide, you'll be able to:

- Describe how to build flexible, composable chains using LangChain's modern approach to prompt engineering
- Structure prompts effectively using templates
- Connect components using the pipe operator to streamline workflows
- Develop reusable patterns for a variety of AI applications

## What is LCEL?

**LangChain Expression Language (LCEL)** is a pattern for building LangChain applications that utilizes the pipe operator to connect components. This approach ensures a clean, readable flow of data from input to output.

LangChain has evolved significantly, and LCEL represents the newer, recommended pattern rather than the traditional LLM chain approach. This modern method provides:

- Better composability
- Clearer visualization of data flow
- Greater flexibility when constructing complex chains

## Creating an LCEL Pattern

To create a typical LCEL pattern, you need to:

1. Define a template with variables and curly braces
2. Create a prompt template instance
3. Build a chain using the pipe operator to connect components
4. Invoke the chain with input values

## Runnables: The Building Blocks

In LangChain, **runnables** serve as an interface and building blocks that connect different components like LLMs, retrievers, and tools into a pipeline.

### Two Main Runnable Composition Primitives

**RunnableSequence**: Chains components sequentially, passing the output from one component as input to the next.

**RunnableParallel**: Runs multiple components concurrently while using the same input for each.

### LCEL Syntax Shortcuts

LCEL provides elegant syntax shortcuts. Instead of using RunnableSequence, the same sequential chain can be created by simply connecting runnable components with a pipe operator, making the structure more readable and intuitive.

## Automatic Type Coercion

LCEL handles type coercion automatically, converting regular code into runnable components:

- **Dictionary** → becomes a `RunnableParallel`, which runs multiple tasks simultaneously
- **Function** → becomes a `RunnableLambda`, which transforms inputs

This happens behind the scenes, so you don't have to handle the conversion manually.

### Example: Parallel Processing

When you use the pipe operator to combine prompt templates with an LLM, and include a dictionary structure, it creates a RunnableParallel which processes all tasks simultaneously. Each task receives the same input but processes it differently.

For example, a chain might produce results with three keys:
- `summary`
- `translation`
- `sentiment`

Each containing the output from the respective LLM call.

## LCEL in Action

### Simple Chain Example

The pipe operator creates a sequence by connecting runnable components together. Here's how a typical chain works:

1. **RunnableLambda** wraps a function (e.g., `Format_prompt`), transforming it into a runnable component
2. When the chain runs, RunnableLambda takes the input dictionary (containing keys like `adjective` and `content`)
3. This dictionary is passed to the formatting function
4. The function formats the prompt template with these variables
5. The formatted prompt is passed to the next component (the LLM)
6. The pipe operator passes the LLM's response to the output parser (e.g., `StrOutputParser`)

## When to Use LCEL

LCEL is best suited for **simpler orchestration tasks**. For more complex workflows, consider using **LangGraph** while still leveraging LCEL within individual nodes.

## Key Strengths of LCEL

As you develop your applications, take advantage of LCEL's strengths:

- **Parallel execution**: Run multiple tasks concurrently
- **Async support**: Handle asynchronous operations efficiently
- **Simplified streaming**: Process data streams easily
- **Automatic tracing**: Track execution flow automatically

These capabilities enhance both the power and maintainability of your applications.

## Key Takeaways

- LCEL pattern structures workflows using the pipe operator for clear data flow
- Prompts are defined using templates with variables in curly braces
- Components can be linked using RunnableSequence for sequential execution
- RunnableParallel allows multiple components to run concurrently with the same input
- LCEL provides concise syntax by replacing RunnableSequence with the pipe operator
- Type coercion automatically converts functions and dictionaries into compatible components