# LLM Fine-Tuning

#llm #fine-tuning #machine-learning #nlp #peft #lora #qlora #rlhf #dpo #transformers

---

## What Is Fine-Tuning?

Fine-tuning is the process of continuing the training of a pre-trained Large Language Model (LLM) on a targeted, domain-specific dataset. Rather than training from scratch, fine-tuning builds upon existing knowledge encoded in billions of parameters, adapting the model's behavior for a specific task, domain, or style — at a fraction of the cost and time of full pre-training.

**Core idea:** A pre-trained model has learned a rich general representation of language. Fine-tuning steers that knowledge toward a specific goal.

---

## When to Fine-Tune (vs. Alternatives)

The diagram below helps choose between the three main LLM customization strategies:

![LLM Customization Decision Tree](diagrams/llm-finetuning-decision-tree.drawio.svg)

| Strategy | Best For | Cost | Speed to Deploy |
|---|---|---|---|
| **Prompt Engineering** | Flexible, diverse outputs; no training data; quick iteration | $ | Hours |
| **RAG** | Real-time / up-to-date info; factual accuracy; low hallucination | $$ | Days |
| **Fine-Tuning** | Deep specialization; custom tone/style; consistent format; encoded expertise | $$$ | Weeks |
| **Fine-Tuning + RAG** | Production systems needing both accuracy and domain specialization | $$$$ | Months |

**Rule of thumb:** Start with prompt engineering → escalate to RAG when you need real-time data → use fine-tuning only when you need deep model-level specialization. These strategies are not mutually exclusive; combining them is common in production systems.

---

## Core Principles

### 1. Transfer Learning Foundation

Fine-tuning exploits the representations already learned during pre-training. The model has seen trillions of tokens and has strong priors about language, reasoning, and world knowledge. Fine-tuning nudges these weights toward a desired distribution.

### 2. Catastrophic Forgetting

When training updates too many parameters too aggressively, a model can forget its general capabilities. Mitigations include lower learning rates, PEFT methods, and regularization techniques.

### 3. Data Quality > Data Quantity

A few hundred high-quality examples consistently outperform thousands of noisy ones. The fine-tuning data must be representative, clean, and formatted consistently with the target use case.

### 4. Task-Objective Alignment

The training objective must match the downstream goal. Instruction-following tasks use supervised fine-tuning (SFT); alignment tasks use RLHF or DPO; domain adaptation may use continued pre-training first.

### 5. Iterative Process

Fine-tuning is not a one-shot operation. Expect multiple rounds: baseline → evaluate → data augmentation → re-train → evaluate → deploy.

---

## Fine-Tuning Methods

![LLM Fine-Tuning Pipeline](diagrams/llm-finetuning-pipeline.drawio.svg)

### Supervised Fine-Tuning (SFT) / Instruction Tuning

The most common approach. The model is trained on (prompt, response) pairs from curated datasets. Teaches the model to follow instructions, answer questions, generate structured output, or adopt a specific persona.

- **Data format:** `{"prompt": "...", "completion": "..."}` or chat templates
- **Datasets:** Alpaca, FLAN, ShareGPT, OpenHermes, custom annotated data
- **Loss:** Standard cross-entropy on completion tokens

### Continued Pre-Training (Domain Adaptation)

Before SFT, continued pre-training on raw domain text (e.g., medical papers, legal documents, code) enriches the model's domain vocabulary and factual knowledge. Useful when the base model lacks domain coverage.

### RLHF (Reinforcement Learning from Human Feedback)

A three-phase pipeline that became the industry standard for aligning LLMs with human values (used in ChatGPT, Claude, Gemini):

1. **SFT Phase:** Train on human-curated demonstrations
2. **Reward Model:** Humans rank pairs of model outputs → train a reward model on these preferences
3. **RL Phase (PPO):** Optimize the SFT model using the reward model as a signal

RLHF produces very safe and helpful outputs but is complex and expensive.

### DPO (Direct Preference Optimization)

Introduced in 2023 (Rafailov et al.), DPO reframes RLHF as a classification problem — directly training the model to prefer one output over another using a binary cross-entropy loss derived from preference pairs. No separate reward model is needed.

- **Advantages:** 40–75% less compute than RLHF; simpler pipeline; more stable training
- **Trade-off:** Slightly weaker out-of-distribution generalization; marginally more unsafe outputs in adversarial scenarios (10% vs. 8% for RLHF)

---

## Parameter-Efficient Fine-Tuning (PEFT)

The breakthrough that made fine-tuning accessible to everyone. PEFT methods freeze the base model weights and train only a small number of added or selected parameters.

![PEFT Methods Comparison](diagrams/llm-peft-methods.drawio.svg)

### LoRA (Low-Rank Adaptation)

Introduced by Hu et al. (2021). Instead of updating a weight matrix W directly, LoRA decomposes the update into two low-rank matrices:

```
ΔW = A × B   where A ∈ ℝ^(d×r), B ∈ ℝ^(r×k), r << d
```

Only A and B are trained. The base model W is frozen. At inference, the adapters can be merged back into W with zero latency overhead.

- **Rank r:** Typically 4–64. Lower rank = fewer params = faster, less expressive
- **Target modules:** Attention Q, K, V, and sometimes FFN layers
- **Memory reduction:** ~10–20x vs full fine-tuning
- **VRAM for 7B model:** ~18GB (vs ~120GB full FT)

### QLoRA (Quantized LoRA)

QLoRA (Dettmers et al., 2023) combines LoRA with 4-bit quantization (NF4 — 4-bit NormalFloat):

1. Base model is loaded in **4-bit precision** (NF4) — frozen
2. LoRA adapters are added and trained in **BF16 precision**
3. Double quantization further compresses quantization constants

- **VRAM for 7B model:** ~6GB (single RTX 4090 feasible at ~$1,500)
- **Quality:** 90–95% of full fine-tuning quality
- **Best for:** Single-GPU setups, resource-constrained environments
- **Limitation:** Does not support multi-GPU training natively

### DoRA (Weight-Decomposed LoRA)

An extension of LoRA that decomposes the weight update into magnitude and direction components, mimicking full fine-tuning behavior more closely. Consistently outperforms LoRA at the same parameter budget.

### Adapters

Small bottleneck layers inserted between transformer sub-layers. Train only the adapter layers. Less popular now than LoRA due to added inference latency.

### Prompt Tuning & Prefix Tuning

Learns soft prompt tokens prepended to input embeddings. The base model is completely frozen. Only the prompt embeddings are trained. Very parameter-efficient but limited expressiveness.

---

## Fine-Tuning Pipeline (Step by Step)

### Phase 1: Data Preparation

**1. Define objectives clearly.** What specific behavior should change? Benchmark the base model first to establish a baseline.

**2. Data collection.** Domain-specific conversations, Q&A pairs, code, documents. Use tools like:
- [Label Studio](https://labelstud.io/) — open-source annotation
- [SuperAnnotate](https://www.superannotate.com/) — enterprise annotation
- [Argilla](https://argilla.io/) — NLP-focused annotation with model-in-the-loop

**3. Data cleaning.** Remove duplicates, PII, low-quality responses. Aim for signal-to-noise ratio > 90%.

**4. Format consistently.** Apply a chat template matching your base model (Llama, Mistral, ChatML, Alpaca). Inconsistent formatting is a top cause of poor fine-tuning results.

**5. Split dataset.** Typical: 80% train / 10% validation / 10% test. Never leak test data.

**6. Tokenize.** Verify that sequences fit within context window. Handle truncation and padding carefully.

### Phase 2: Method Selection

Choose based on your resources and quality requirements:

| Scenario | Recommended Method |
|---|---|
| Limited GPU (single 24GB) | QLoRA |
| Quality-critical, multi-GPU | LoRA or Full FT |
| Alignment / safety | SFT + DPO |
| Domain knowledge injection | Continued Pre-training → SFT |
| Production with strict latency | Full FT (merge weights) |

### Phase 3: Training

Key hyperparameters:

| Parameter | Typical Range | Notes |
|---|---|---|
| Learning rate | 1e-5 to 5e-4 | Use cosine decay with warmup |
| Batch size | 4–32 | Larger = more stable; use gradient accumulation |
| Epochs | 1–5 | More than 3 often causes overfitting on small datasets |
| LoRA rank (r) | 8–64 | Start with 16; increase for complex tasks |
| LoRA alpha | r × 2 | Common heuristic: alpha = 2 × rank |
| Warmup steps | 50–200 | Prevents early training instability |
| Max grad norm | 0.3–1.0 | Gradient clipping prevents explosions |

Use **bf16 (bfloat16)** mixed precision and **gradient checkpointing** to reduce memory usage.

### Phase 4: Evaluation

**Automated metrics:**
- **Perplexity:** Lower is better; measures language model quality
- **ROUGE / BLEU:** Overlap-based metrics for summarization / translation
- **BERTScore / BLEURT:** Semantic similarity metrics
- **Task-specific:** F1, accuracy, EM for classification/QA tasks

**Benchmarks:**
- [MMLU](https://huggingface.co/datasets/cais/mmlu) — general knowledge
- [MT-Bench](https://github.com/lm-sys/FastChat/tree/main/fastchat/llm_judge) — multi-turn conversation quality
- [HumanEval](https://github.com/openai/human-eval) — code generation
- [TruthfulQA](https://github.com/sylinrl/TruthfulQA) — factual accuracy and hallucination

**Human evaluation and red teaming** are essential for production deployments — automated metrics miss nuanced quality issues and safety gaps.

---

## Main Tools & Frameworks

### Core Libraries

| Tool | Description | Best For |
|---|---|---|
| [Hugging Face Transformers](https://github.com/huggingface/transformers) | Model hub + training utilities | All tasks; industry standard |
| [PEFT](https://github.com/huggingface/peft) | Official HF library for LoRA, QLoRA, adapters | Production PEFT workflows |
| [TRL](https://github.com/huggingface/trl) | HF library for SFT, RLHF, DPO, PPO | Alignment training |
| [Transformers Accelerate](https://github.com/huggingface/accelerate) | Multi-GPU / mixed-precision training | Distributed training |

### Higher-Level Frameworks

| Tool | Description | Best For |
|---|---|---|
| [Unsloth](https://github.com/unslothai/unsloth) | 2–5x faster fine-tuning, 80% less memory | Single-GPU, fast iteration |
| [Axolotl](https://github.com/axolotl-ai-cloud/axolotl) | YAML-config wrapper over HF tools; multi-GPU | Beginners + multi-GPU teams |
| [LLaMA-Factory](https://github.com/hiyouga/LLaMA-Factory) | 100+ models, no-code WebUI (LlamaBoard) | Wide model support, teams |
| [Torchtune](https://github.com/pytorch/torchtune) | Native PyTorch fine-tuning library | PyTorch-first engineers |
| [DeepSpeed](https://github.com/microsoft/DeepSpeed) | ZeRO optimization for multi-GPU clusters | Large-scale distributed FT |

### Experiment Tracking

| Tool | Notes |
|---|---|
| [Weights & Biases (W&B)](https://wandb.ai/) | Best-in-class logging; integrates natively with HF Trainer |
| [MLflow](https://mlflow.org/) | Open-source; good for on-prem deployments |
| [Comet ML](https://www.comet.com/) | Alternative to W&B |

### Managed Fine-Tuning APIs

| Service | Notes |
|---|---|
| [OpenAI Fine-Tuning API](https://platform.openai.com/docs/guides/fine-tuning) | GPT-4o mini / GPT-3.5; no infrastructure needed |
| [Together AI](https://www.together.ai/) | Open-source model fine-tuning as a service |
| [Fireworks AI](https://fireworks.ai/) | Fast fine-tuning API for open models |
| [Replicate](https://replicate.com/) | Cloud-hosted fine-tuning and deployment |
| [Modal](https://modal.com/) | Serverless GPU compute for custom fine-tuning scripts |

### Inference After Fine-Tuning

| Tool | Notes |
|---|---|
| [vLLM](https://github.com/vllm-project/vllm) | High-throughput inference server; PagedAttention |
| [Ollama](https://ollama.com/) | Local inference; GGUF format |
| [llama.cpp](https://github.com/ggerganov/llama.cpp) | CPU/GPU inference; GGUF quantization |
| [Text Generation Inference (TGI)](https://github.com/huggingface/text-generation-inference) | HuggingFace's production inference server |

---

## Best Practices & Recommendations

### Data

- **Quality beats quantity.** 500 high-quality examples > 10,000 noisy ones
- **Diversity matters.** Cover edge cases and diverse phrasings
- **Avoid contamination.** Never include test data in training; watch for data leakage
- **Use a consistent template.** Mismatched chat formats are a silent killer of quality
- **Human annotation for alignment.** If training for safety/helpfulness, use real human preferences

### Training

- **Start small.** Validate your pipeline on a 1% data sample before full training
- **Monitor validation loss.** Stop early if validation loss starts rising (overfitting)
- **Use gradient checkpointing + bf16.** Near-mandatory for memory efficiency
- **Low learning rates for LoRA.** 2e-4 to 5e-4 is typical; avoid aggressive LRs that destabilize adapters
- **Log everything.** Use W&B or MLflow to track experiments; you will need to compare runs
- **Use cosine LR schedule with warmup.** More stable than constant LR

### Safety

Fine-tuning can inadvertently remove safety guardrails built into the base model. A research study showed fine-tuned models from Meta and OpenAI could be made to generate harmful content with just a few adversarial examples. Always:

- Red-team your fine-tuned model before deployment
- Evaluate on TruthfulQA and adversarial benchmarks
- Consider adding a constitutional layer (system prompt + RLHF/DPO) even after SFT
- Never fine-tune on synthetic data generated by a misaligned or unfiltered model

### Cost Optimization

- Use QLoRA for exploratory work; switch to full FT only for production
- Share a base model across multiple LoRA adapters (hot-swapping)
- Use gradient accumulation to simulate large batch sizes with limited VRAM
- Consider renting GPU time (Lambda Labs, RunPod, Vast.ai) instead of buying hardware

---

## Common Pitfalls

| Pitfall | Symptom | Fix |
|---|---|---|
| Catastrophic forgetting | Model loses general knowledge | Lower LR; use PEFT; reduce epochs |
| Overfitting | Val loss rises while train loss falls | More data; early stopping; dropout |
| Mode collapse | Model repeats same outputs | More diverse data; adjust temperature |
| Format mismatch | Gibberish or incomplete outputs | Verify chat template matches base model |
| Data leakage | Inflated eval metrics | Strict train/val/test splits |
| Safety degradation | Model generates harmful outputs | DPO alignment; red teaming; guardrails |
| Hallucination increase | More confident but wrong answers | Higher-quality training data; RAG overlay |

---

## 2025–2026 Trends

- **Small Language Model (SLM) fine-tuning** is the dominant trend — models like Llama 3.2 3B, Phi-3, and Gemma 2B can be fine-tuned cheaply and deployed on edge devices
- **Synthetic data for fine-tuning** is growing in use — generating training data from powerful teacher models (GPT-4o, Claude Opus) then fine-tuning smaller models
- **DPO has largely replaced RLHF** in academic research for alignment due to simplicity, though RLHF still holds a slight safety edge
- **Mixture of Experts (MoE) fine-tuning** is emerging — selectively fine-tuning specific expert layers
- **Multi-modal fine-tuning** — adapting LLMs to handle vision-language, audio, and code together
- **LoRA + RAG combination** in production — fine-tuning for behavior/style, RAG for factual grounding

---

## References

### Key Papers

- [LoRA: Low-Rank Adaptation of Large Language Models](https://arxiv.org/abs/2106.09685) — Hu et al., 2021
- [QLoRA: Efficient Finetuning of Quantized LLMs](https://arxiv.org/abs/2305.14314) — Dettmers et al., 2023
- [Direct Preference Optimization](https://arxiv.org/abs/2305.18290) — Rafailov et al., 2023
- [RLHF: Training language models to follow instructions with human feedback](https://arxiv.org/abs/2203.02155) — Ouyang et al., 2022
- [The Ultimate Guide to Fine-Tuning LLMs](https://arxiv.org/abs/2408.13296) — Exhaustive review, 2024

### Articles & Guides

- [Fine-tuning large language models in 2026 — SuperAnnotate](https://www.superannotate.com/blog/llm-fine-tuning)
- [LLM Fine-Tuning: A Guide for Engineering Teams — Heavybit](https://www.heavybit.com/library/article/llm-fine-tuning)
- [The Ultimate Guide to LLM Fine-Tuning — Lakera](https://www.lakera.ai/blog/llm-fine-tuning-guide)
- [RAG vs Fine-tuning vs Prompt Engineering — IBM](https://www.ibm.com/think/topics/rag-vs-fine-tuning-vs-prompt-engineering)
- [How to align open LLMs in 2025 with DPO — Philschmid](https://www.philschmid.de/rl-with-llms-in-2025-dpo)
- [Best frameworks for fine-tuning LLMs in 2025 — Modal](https://modal.com/blog/fine-tuning-llms)
- [Efficient Fine-Tuning with LoRA — Databricks](https://www.databricks.com/blog/efficient-fine-tuning-lora-guide-llms)
- [LoRA vs QLoRA — Red Hat](https://www.redhat.com/en/topics/ai/lora-vs-qlora)
- [Fine-tuning LLMs on Human Feedback (RLHF + DPO) — Shaw Talebi](https://shawhin.medium.com/fine-tuning-llms-on-human-feedback-rlhf-dpo-1c693dbc4cbf)
- [Axolotl vs Unsloth vs TorchTune — Spheron](https://www.spheron.network/blog/axolotl-vs-unsloth-vs-torchtune/)

### Related Notes

- [[Agent_Engineering]] — LLM agents and agentic workflows that leverage fine-tuned models

---

*Last updated: 2026-04-05*
