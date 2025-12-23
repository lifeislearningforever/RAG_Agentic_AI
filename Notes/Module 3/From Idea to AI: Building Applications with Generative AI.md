# Building AI-Powered Applications: A Developer's Guide

## Introduction

Gartner reported that 80% of enterprises will have used some type of generative AI through models or APIs by 2026. While many developers have used AI through co-pilots in their IDE and popular large language models online, building applications that use AI is becoming an essential skill.

This guide covers where to get started, how to build AI-powered applications, and where to run them, including open source tools and technologies for inner loop development of building, running, and testing applications with AI.

---

## The Three Main Steps of the AI Journey

Going from a simple proof of concept to a production application involves three main phases:

1. **Ideation and Experimentation**
2. **Building**
3. **Development and Operations**

---

## Step 1: Ideation and Experimentation

### Getting Started with Proof of Concepts

The first step is ideating around exploration and proof of concepts. Here are the key considerations:

### Model Selection

**Remember:** Your use case is specialized, so you need a specialized model that can do the job.

- Start by researching and evaluating models from popular repositories like Hugging Face or the open source community
- Consider different factors such as model size and performance
- Understand benchmarking through popular benchmark tools

### Ground Rules to Understand

- **Self-hosting vs. Cloud:** Generally, self-hosting a large language model will be cheaper than a cloud-based service
- **SLMs vs. LLMs:** Small language models (SLMs) versus large language models (LLMs) will generally perform better with lower latency, and they're specialized for a specific task

### Prompting Techniques

Understanding various prompting techniques is crucial when working with models:

#### Zero-Shot Prompting
Asking a model a question without any examples of how to respond.

#### Few-Shot Prompting
Giving a few different examples of how to respond, defining the behavior you want the LLM to have as you're working with the AI.

#### Chain of Thought
Asking the model to explain its thinking and process step by step.

### Key Takeaway

You need to understand the different capabilities and limitations of the models you're working with. Experiment with your data early on so you can understand any potential challenges that might come up as you go through the AI journey.

---

## Step 2: Building Your Application

### Local Model Serving

Just as we can locally run databases and different services on our machine, we can do the same with AI to serve it locally from our machine and make requests to its API from localhost. Plus, you get the added benefit of knowing that your data is secure and private on premise.

### Integrating Your Data with LLMs

There are several methods to use your data with large language models:

#### Retrieval Augmented Generation (RAG)
Take a large language model, a pre-trained foundational model, and supplement it with relevant and accurate data. This can help provide better and more accurate responses.

#### Fine-Tuning
Take the large language model and include the data with it. You're actually baking this information—how you want it to behave, the different styles and intuition you want it to react with—into the model itself. Then you can inference it and have that domain-specific data every time you're working with the AI model.

**Note:** These are just two approaches. There are many more.

### Tools and Frameworks

Having the right tools and frameworks, such as **LangChain**, will simplify your life. They let you focus on building out new features for popular generative AI use cases like:

- Chatbots
- IT process automation
- Data management
- And much more

These frameworks simplify the different calls you're going to make through the model through sequences of prompts and model calls to accomplish more complex tasks. This means you need to break down problems into smaller, more manageable steps and evaluate the flows during these model calls, both in development and in a production environment.

---

## Step 3: Operationalizing AI-Powered Applications

### Machine Learning Operations (MLOps)

Finally, you've got that application powered by AI or a large language model, and you want to deploy it to production to scale things up. This falls under the umbrella of something known as **machine learning operations, or MLOps**.

### Infrastructure Requirements

Your infrastructure needs to be able to handle efficient model deployment and scaling:

- **Containers and Orchestrators:** Technologies such as containers and orchestrators like Kubernetes help with auto-scaling and balancing traffic for your application
- **Production-Ready Runtime:** Use production-ready runtime such as vLLM for model serving

### Hybrid Approach

Organizations are taking a hybrid approach, both with their models and their infrastructure:

- **Multi-Model Strategy:** A "Swiss Army knife" approach to different models for different use cases
- **Infrastructure Mix:** A combination of on-prem and cloud infrastructure to make the most out of resources and budget

### Ongoing Maintenance

With AI-powered applications in production, the job isn't done. You still need to:

- Benchmark
- Monitor
- Handle different exceptions coming from your application

Similar to how we have DevOps, we also have MLOps for ensuring models go into production in a smooth fashion.

---

## Key Takeaways

Recent innovations in the world of AI have made this topic much more accessible for developers. You have plenty of tools out there to help you along the process.

**Remember:** While AI is new, it's actually just another tool that you can add to your tool belt. Use these tools and the different steps of the process to go from ideation, to building, to deployment of these applications to make a real impact with your work by using GenAI.

---

## Summary of the AI Development Journey

1. **Ideation Phase:** Research models, understand prompting techniques, experiment with data
2. **Building Phase:** Serve models locally, implement RAG or fine-tuning, use frameworks like LangChain
3. **Operations Phase:** Deploy with containers and Kubernetes, implement MLOps, monitor and optimize

The key is to start small, experiment early, and gradually scale up your AI-powered applications with the right tools and practices in place.