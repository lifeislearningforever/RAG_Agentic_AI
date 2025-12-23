# Generative AI Course Summary

## Core Concepts

### Generative AI Models
AI systems that predict and generate new content including text, images, code, and audio based on input data.

### Foundational Models
A subset of generative AI models that includes Large Language Models (LLMs).

### Prompts
Instructions or inputs given to an LLM designed to guide it toward performing a specific task or generating a desired output.

## Prompt Components

1. **Instructions** - Clear, specific commands that tell the AI system what to do
2. **Context** - Necessary information or background that helps the LLM make sense of the instruction

**Note:** Prompts can include task instructions, input data, contexts, and examples. Model parameters influence output but are not part of the prompt itself.

### In-Context Learning
A specific method of prompt engineering where demonstrations of the task are provided as part of the prompt in natural language. It does not require any training, and a new task is learned from a small set of examples presented within the context at inference time.

## Key Prompting Techniques

### Zero-Shot Prompt
Instructs an LLM to perform a task with no examples. Relies entirely on its pre-trained knowledge.

### One-Shot Prompt
Provides the LLM with a single example along with the instruction to help it perform a task.

### Few-Shot Prompt
Uses a small set of examples to help the LLM generalize patterns and apply its learning to perform a task.

### Chain of Thought (CoT) Prompt
Guides LLMs through complex reasoning in a step-by-step manner.

### Self-Consistency
Enhances the reliability of outputs by generating multiple reasoning paths or answers, then selecting the most consistent or most frequent one.

## LangChain Framework

An open-source framework that simplifies application development using LLMs.

### Core Components
- Language model
- Chat model
- Chat messages
- Response templates
- Output parsers

### Chains
A sequence of calls where a sequential chain consists of a series of steps, each taking one input to generate one output. The output of step 1 becomes the input for step 2, and so on.

### Agents
Dynamic systems where language models determine the sequence of actions. The model generates text-based outputs to guide these actions. Agents can use integrated tools such as databases and search engines to gather information or perform tasks that fulfill user requests.

### LangChain Expression Language (LCEL)
A pattern for building LangChain applications that utilizes the pipe operator to connect components. This approach ensures a clean, readable flow of data from input to output.

**Benefits:**
- Provides elegant syntax shortcuts
- Creates readable and intuitive structure
- Connects components with pipes instead of using runnable sequences

## AI Models Covered

### Llama3
- Enhanced context understanding
- Better handling of nuanced problems
- Known for enhanced reasoning capabilities

### Granite (IBM)
- Advanced language model, part of the watsonx.ai platform
- Optimized for enterprise use cases
- Strong performance in business and technical domains

### Mixtral
- Uses a mixture of expert systems
- Each layer has 8 expert submodels
- Only two most suitable experts are activated per inference/task
- High adaptability due to specialized experts
- Allows fine-tuning for specific needs
- Resource-efficient for diverse tasks (activates only necessary experts)

---

*Summary of key concepts from the Generative AI Applications course*