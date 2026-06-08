Detailed Briefing: AI Engineering - Building Applications with Foundation Models

This briefing document summarises the key themes and most important ideas presented in excerpts from Chip Huyen's book, "AI Engineering: Building Applications with Foundation Models." The book provides a comprehensive guide to developing generative AI systems in a production environment, bridging the gap between traditional machine learning (ML) and the new landscape of foundation models.

--------------------------------------------------------------------------------

1. The Rise and Definition of AI Engineering

**Core Idea:** AI Engineering is a rapidly growing discipline, distinct from traditional ML Engineering, focused on building applications atop readily available foundation models.

- **Emergence of AI Engineering:** The exponential increase in the capabilities of large language models (LLMs) like ChatGPT, combined with a lowered barrier to entry for developers (e.g., via APIs), has propelled AI Engineering into a "fastest-growing engineering discipline." As Luke Metz, co-creator of ChatGPT, states, Chip Huyen's work "has been instrumental in helping teams bring AI into production." This shift has "transformed AI from a specialised discipline into a powerful development tool everyone can use."

- **AI vs. ML Engineering:** While many core principles from ML engineering remain relevant (e.g., "systematic experimentation, rigorous evaluation, relentless optimization"), AI Engineering introduces new opportunities and challenges. The key distinctions include:

    ◦ **Model Utilisation:** Traditional ML Engineering involves "developing ML models," whereas AI Engineering "leverages existing ones." This means less focus on "modelling and training" and more on "model adaptation."

    ◦ **Scale and Efficiency:** Foundation models are "bigger, consume more compute resources, and incur higher latency." This necessitates greater emphasis on "efficient training and inference optimization."

    ◦ **Open-ended Outputs:** Foundation models "can produce open-ended outputs," which offers flexibility but makes evaluation "a much bigger problem."

- **Definition of Foundation Models:** The book uses "foundation models" to encompass both large language models (LLMs) and large multimodal models (LMMs). These models are "general-purpose models" capable of a "wide range of tasks," marking a shift from previous task-specific models.

--------------------------------------------------------------------------------

2. Understanding Foundation Models: Architecture, Scale, and Alignment

**Core Idea:** Effective AI Engineering requires a high-level understanding of how foundation models are built, their characteristics, and how they are aligned with human preferences, even if developers aren't training them from scratch.

- **Training Data is Paramount:** "An AI model is only as good as the data it was trained on." The composition and quality of training data significantly influence a model's capabilities and limitations.

    ◦ **Self-Supervision:** This technique allows models to "infer labels from the input data," enabling the creation of "massive amount of training data" without manual labelling. This is crucial for scaling language models into LLMs.

    ◦ **Multilingual Models:** The dominance of English in datasets like Common Crawl (45.88%) leads to general-purpose models performing "much better for English than other languages." Under-representation in training data is a key reason for performance disparities in low-resource languages.

    ◦ **Domain-Specific Models:** While general-purpose models excel broadly, domain-specific models, or fine-tuning general models on specific datasets, are often necessary for "drug discovery and cancer screening," where data is scarce and unique.

    ◦ **Data Quality over Quantity:** "A model trained with a smaller amount of high-quality data might outperform a model trained with a large amount of low-quality data."

- **Model Architecture - The Transformer:** The "transformer architecture" is central to modern foundation models, having addressed the sequential processing limitations of earlier recurrent neural networks (RNNs). Key components include:

    ◦ **Attention Mechanism:** This core mechanism allows the model to "compute how much attention to give an input token," enabling it to weigh the importance of different parts of the input sequence. The need to compute and store "key and value vectors" at each step is why extending "context length for transformer models" is challenging.

    ◦ **Prefilling and Decoding:** LLM inference involves two phases: "prefilling" (processing input tokens in parallel) and "decoding" (generating one output token at a time). These different computational profiles drive many optimisation techniques.

- **Model Scale and Cost:** A model's scale is typically indicated by:

    ◦ **Number of Parameters:** A proxy for the model's learning capacity. This has grown from 117 million (GPT-1, 2018) to hundreds of billions.

    ◦ **Number of Training Tokens:** Measures how much data the model has learned from.

    ◦ **FLOPs (Floating Point Operations):** A proxy for training cost. Training a model like GPT-3-175B can cost "over $4 million."

    ◦ **Scaling Laws:** Describe the relationship between model performance and scale (parameters, data, compute). However, for production, "model quality isn’t everything," and smaller, cheaper models (like Llama) might be preferred for usability.

    ◦ **Scaling Bottlenecks:** Real concerns exist about running out of "internet data in the next few years" and the "electricity" required to power ever-larger models. AI-generated content in training data can also lead to "model collapse," degrading performance over time.

- **Post-Training and Alignment (RLHF):** After pre-training, models need alignment to human preferences.

    ◦ **Supervised Fine-tuning (SFT):** Teaches models to follow instructions and generate conversational responses, moving beyond simple text completion. This requires "high-quality human annotation" which is "challenging and expensive."

    ◦ **Preference Fine-tuning (RLHF/DPO):** Aims to align models with "human preference" by training a "reward model" based on human comparisons of generated responses. This is "ambitious, if not impossible," due to the diversity of human preferences.

- **Sampling and Probabilistic Nature:** How a model chooses its output is called sampling.

    ◦ **Probabilistic Outputs:** AI models are inherently probabilistic; they generate responses "based on probabilities, and not guaranteed to be correct." This explains "inconsistency" (same input, different output) and "hallucinations."

    ◦ **Sampling Strategies:** Techniques like "temperature," "top-k," and "top-p" control the diversity and predictability of outputs. "A temperature of 0.7 is often recommended for creative use cases." Setting temperature to 0 makes outputs more consistent but potentially boring.

    ◦ **Structured Outputs:** Getting models to generate outputs in specific formats (e.g., JSON, SQL) is crucial for downstream applications and can be achieved through prompting, post-processing, constrained sampling, and fine-tuning.

--------------------------------------------------------------------------------

3. Evaluation and Model Selection

**Core Idea:** Robust, systematic evaluation is the biggest bottleneck to AI adoption. Designing reliable evaluation pipelines and making informed model selections are critical for production-ready AI applications.

- **Challenges of Evaluation:** Evaluating foundation models is "more challenging than evaluating traditional ML models" due to their intelligence and open-ended nature. There's often "inadequate investment" in evaluation tools and infrastructure, leading many to rely on "word of mouth" or "eyeballing the results."

- **Language Modelling Metrics:** Metrics like "perplexity" and "cross entropy" measure a language model's ability to predict the next token. Lower perplexity generally indicates a more powerful model. However, post-training can sometimes _increase_ perplexity while improving task performance, as "post-training collapses entropy." Perplexity can also detect "data contamination" in benchmarks.

- **Evaluation Methodologies:**

    ◦ **Exact Evaluation:** Produces "judgment without ambiguity." Examples include "functional correctness" (e.g., for code generation, math problems), "exact match," "lexical similarity" (e.g., n-gram overlap), and "semantic similarity" (using embeddings to compare meaning).

    ◦ **AI as a Judge (LLM-as-a-judge):** Uses AI models to evaluate outputs. Benefits include speed, ease of use, and cost-effectiveness compared to humans, especially for "subjective" criteria like "correctness, repetitiveness, toxicity, wholesomeness, hallucinations." Limitations include "inconsistency" (due to AI's probabilistic nature), "biases," and "criteria ambiguity" between different AI judges.

    ◦ **Ranking Models with Comparative Evaluation:** Instead of absolute scores, models are evaluated against each other. This is "easier to do than pointwise evaluation" for subjective qualities and is used by leaderboards like LMSYS's Chatbot Arena. However, it’s crucial to distinguish between tasks where "preference-based voting" is appropriate (e.g., writing style) versus those requiring "correctness" (e.g., math answers).

- **Key Evaluation Criteria for Applications:**

    ◦ **Domain-Specific Capability:** Measured by benchmarks (e.g., code generation, math, science knowledge). Metrics can include accuracy, efficiency (runtime, memory usage), and readability (often subjective).

    ◦ **Generation Capability:** Focuses on the quality of open-ended outputs. * **Factual Consistency:** A critical issue ("hallucinations"). Methods include AI-as-a-judge, "self-verification" (multiple model outputs), and "knowledge-augmented verification" (leveraging search engines). * **Safety:** An umbrella term for "toxicity and biases." Can be detected by general-purpose AI judges or specialized models. Models can exhibit "political biases" depending on training data.

    ◦ **Instruction-Following Capability:** Measures how well a model adheres to specified formats, content constraints, linguistic guidelines, and style rules (e.g., IFEval, INFOBench).

    ◦ **Cost and Latency:** Crucial for production. Latency metrics include "Time to First Token (TTFT)" and "Time Per Output Token (TPOT)." Cost is often measured by API token usage or compute.

- **Model Selection Workflow:**

    1. **Filter by Hard Attributes:** Licenses (commercial use, re-training restrictions), hardware compatibility.

    2. **Public Information:** Use "publicly available information, e.g., benchmark performance and leaderboard ranking," to narrow down options. Be wary of "data contamination" (models trained on benchmark data).

    3. **Custom Evaluation Pipeline:** "You’ll need to run your own evaluation pipeline to find the best one for your application."

- **Model Build vs. Buy:**

    ◦ **Model APIs (Buy):** Offer convenience, scalability, and access to the "best-performing model" (likely closed-source). Downsides include "sending your data to model providers," dependence on their SLAs, and often "less likely to expose logprobs."

    ◦ **Self-Hosting (Build):** Offers control over data privacy, customisation, and access to internal outputs (logprobs). Downsides include higher "talent, time, engineering effort to optimize, host, maintain."

    ◦ **Open Source Models:** Can be self-hosted. The gap between open-source and proprietary models is decreasing, but open-source models may lag in performance and lack user feedback for continuous improvement.

--------------------------------------------------------------------------------

4. Model Adaptation: Prompt Engineering, RAG, Agents, and Fine-tuning

**Core Idea:** Foundation models require adaptation techniques to solve real-world problems effectively, ranging from simple prompting to complex architectural patterns and model modifications.

- **Prompt Engineering:** The "easiest and most common model adaptation technique." It involves "crafting an instruction that gets a model to generate the desired outcome" without changing model weights.

    ◦ **Best Practices:** "Write clear and explicit instructions," "provide sufficient context," "break complex tasks into simpler subtasks" (e.g., Chain-of-Thought), "give the model time to think," and "iterate on your prompts."

    ◦ **Defensive Prompt Engineering:** Addresses attacks like "jailbreaking" and "prompt injection" (injecting malicious instructions). Defences include input/output guardrails and system-level isolation. Reverse prompt engineering allows attackers to try and extract original system prompts.

- **Retrieval-Augmented Generation (RAG):** A common pattern to provide models with external, query-specific information, addressing their "context limitations" and mitigating "hallucinations."

    ◦ **Architecture:** Consists of a "retriever" (indexing and querying external data) and a "generator" (the foundation model).

    ◦ **Retrieval Mechanisms:** "Term-based retrieval" (e.g., BM25, Elasticsearch) for lexical similarity and "embedding-based retrieval" (semantic retrieval, using vector databases) for semantic similarity. Hybrid search combines both.

    ◦ **Optimisation:** Includes "chunking" documents, "reranking" retrieved documents, "query rewriting" (to make ambiguous queries explicit), and "contextual retrieval" (augmenting chunks with surrounding context).

    ◦ **Multimodal RAG:** Extends RAG to images, videos, or other modalities by generating embeddings for them.

- **Agents:** More complex AI systems designed to "accomplish tasks" by reasoning, planning sequences of actions, using "tools," and reflecting on outcomes.

    ◦ **Components:** A "planner" (the AI brain), "tools" (e.g., web search, calculators, SQL executors, code interpreters), and potentially a "memory system."

    ◦ **Action Types:** "Knowledge augmentation" (read-only) and "write actions" (modifying data sources).

    ◦ **Control Flow:** Beyond simple sequential actions, agents can implement "parallel, if statement, and for loop" control flows.

    ◦ **ReAct Framework:** Interleaves "reasoning and action" steps, where the agent explains its thinking before taking an action and then reflects on the observation.

    ◦ **Failure Modes:** Unique failures include "missing tools," "incorrect tool use," "incorrect planning," and "inefficiency."

- **Finetuning:** "Continuing to train a previously trained model" to adapt it to specific tasks, behaviours, or styles.

    ◦ **When to Finetune:** Generally, after exhausting prompt-based methods. Useful for "domain-specific capabilities," "safety," and improving "instruction-following ability," especially for "specific output styles and formats" (e.g., semantic parsing). "Finetuning is for form, and RAG is for facts."

    ◦ **Memory Bottleneck:** Full fine-tuning is resource-intensive due to the need to store "model weights + activations + gradients + optimizer states" (FP32 requires 4 bytes per value).

    ◦ **Parameter-Efficient Fine-tuning (PEFT):** Techniques like LoRA (Low-Rank Adaptation) reduce the number of "trainable parameters" by injecting small, additional modules, making fine-tuning more accessible and requiring less data.

    ◦ **Quantization:** "Reducing precision" (e.g., from FP32 to FP16/FP8) of model weights significantly reduces memory footprint and can accelerate inference.

    ◦ **Model Merging:** Combines multiple models (often fine-tuned ones) into a new model, offering flexibility for multi-task learning or on-device deployment. Approaches include "summing" (e.g., linear combinations), "layer stacking," and "concatenation."

- **Dataset Engineering (for Finetuning):** Crucial for fine-tuning.

    ◦ **Data Curation:** Focuses on "quality, coverage, and quantity." "A small amount of high-quality data can outperform a large amount of noisy data." Data should be "relevant," "aligned with task requirements," "consistent," and "correctly formatted."

    ◦ **Data Synthesis:** Generating artificial data to supplement or replace human-generated data, especially for scarce or expensive annotations. Techniques include "rule-based" generation, "procedural generation" (data augmentation), and "AI-powered data synthesis" (e.g., paraphrasing, translation, self-play).

    ◦ **Model Distillation:** Training a "small model (student) to mimic a larger model (teacher)" using synthetic data generated by the teacher, aiming for a cheaper and/or faster student model.

    ◦ **Data Processing:** Includes "inspecting data," "deduplicating data" (to avoid bias and contamination), "cleaning and filtering data" (e.g., removing PII, low-quality data), and "formatting data" for the model's tokenizer and chat template.

--------------------------------------------------------------------------------

5. Deployment and Operations: Inference Optimisation, Architecture, and User Feedback

**Core Idea:** Optimising the serving of AI models, designing robust system architectures, and effectively leveraging user feedback are essential for successful production deployment and continuous improvement.

- **Inference Optimisation:** Making the model's use "faster and cheaper."

    ◦ **Performance Metrics:** "Latency" (TTFT, TPOT, Total Latency), "Throughput/Goodput" (tokens per second), and "Utilisation" (MFU - Model FLOP/s Utilization, MBU - Model Bandwidth Utilization).

    ◦ **AI Accelerators:** Specialised hardware (e.g., GPUs, TPUs, ASICs) are crucial for efficient AI workloads. Their performance depends on "computational capabilities," "memory size and bandwidth," and "power consumption."

    ◦ **Model-Level Optimisation:** Modifying the model itself. * **Model Compression:** "Quantization" (reducing numerical precision) and "Pruning" (removing unnecessary parameters). * **Autoregressive Decoding Bottleneck:** Techniques like "speculative decoding" (using a small, fast "draft model" to propose tokens) and "inference with reference" (selecting tokens from input context) accelerate token generation. * **Attention Mechanism Optimisation:** Managing "KV cache" (storing key-value pairs from previous tokens) to reduce memory and "writing kernels" (specialised code for hardware) like FlashAttention.

    ◦ **Inference Service Optimisation:** Focuses on resource management without modifying the model. * **Batching:** "Batching the requests that arrive around the same time together can significantly reduce the service’s throughput." "Continuous batching" allows immediate return of completed responses. * **Decoupling Prefill and Decode:** Running these two phases on separate machines optimized for their distinct computational profiles. * **Prompt Caching:** Storing and reusing "overlapping text segments" (e.g., system prompts, long documents) to reduce latency and cost. * **Parallelism:** "Data parallelism" (splitting data across devices) and "model parallelism" (splitting the model across devices, e.g., "tensor parallelism," "pipeline parallelism") to scale performance.

- **AI Engineering Architecture:** Building a comprehensive system beyond just the model.

    ◦ **Progressive Enhancement:** Starts with a simple model API and adds components as needed: "enhance context" (RAG/tools), "put in guardrails" (input/output validation, PII masking, safety), "add model router and gateway," "reduce latency with caches," and "add agent patterns."

    ◦ **Routers and Gateways:** * **Router:** Directs queries to the "optimal solution for each query" (e.g., specialized models, cheaper models) often using "intent classifiers." * **Gateway:** Provides a "unified interface" to different models (self-hosted or third-party APIs), simplifying code maintenance and adding security.

- **Monitoring and Observability:** Essential for quality control and improvement.

    ◦ **Goals:** "Mitigate risks" (failures, attacks, drifts) and "discover opportunities" (improvements, cost savings).

    ◦ **Metrics:** Track "model quality" (format failures, factual consistency, safety, conciseness), "latency" (TTFT, TPOT), and "costs" (token counts, API calls).

    ◦ **Logs and Traces:** Detailed records of events and execution paths, crucial for debugging and understanding system behavior.

    ◦ **Drift Detection:** Monitoring for changes in "data distribution," "user behavior," or "underlying model changes" (especially for third-party APIs).

- **AI Pipeline Orchestration:** Tools that define how different components (models, databases, tools) "work together to create an end-to-end pipeline," ensuring seamless data flow.

- **User Feedback:** "Invaluable for guiding product development" and a "crucial role as a data source for improving models," creating a "data flywheel."

    ◦ **Types:** "Explicit feedback" (e.g., thumbs up/down, star ratings) and "implicit feedback" (inferred from user actions, e.g., edits, query rephrasing, session length). "Conversational interface" makes feedback more natural.

    ◦ **Design Considerations:** Collect feedback "throughout the user journey" (non-intrusively), "when something bad happens," or "when the model has low confidence."

    ◦ **Challenges:** "Feedback biases" (e.g., leniency bias, position bias, preference bias) that need to be understood and mitigated to ensure data quality for model training. The visibility of feedback (private vs. public) also impacts candor.

--------------------------------------------------------------------------------

**Conclusion:**The book highlights that AI Engineering is a dynamic field that requires a multidisciplinary approach, blending traditional software engineering, ML knowledge, and specific strategies for working with foundation models. The emphasis shifts from solely developing models to adapting, integrating, and operating these powerful, probabilistic systems in real-world scenarios, with a strong focus on systematic evaluation and leveraging user feedback for continuous improvement. The challenges of scale, cost, and ensuring reliability and safety are central to the discipline.