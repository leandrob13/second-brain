# LLM Architectures

#llm #transformers #deep-learning #nlp #architecture #ai #machine-learning #attention-mechanism #gpt #bert #fine-tuning #rlhf

---

## What is a Large Language Model?

A **Large Language Model (LLM)** is a deep neural network trained on massive corpora of text data, capable of generating, understanding, and transforming human language. LLMs differ from traditional NLP systems in three fundamental ways:

- **Scale**: LLMs contain billions to hundreds of billions of parameters, trained on trillions of tokens.
- **General-purpose learning**: Rather than task-specific architectures, LLMs learn general language representations that transfer across virtually all language tasks.
- **Emergent capabilities**: As scale increases, LLMs exhibit unexpected abilities (reasoning, code generation, in-context learning) not present at smaller scales.

The dominant architecture underlying virtually all modern LLMs is the **Transformer**, introduced in 2017 by Vaswani et al. in the landmark paper *"Attention Is All You Need"*.

---

## The Transformer Architecture

The Transformer replaced recurrent networks (RNNs, LSTMs) entirely with attention mechanisms, enabling parallel training and capturing long-range dependencies efficiently.

![Transformer Architecture](diagrams/transformer-architecture.drawio.svg)

### Core Components

**Tokenization** converts raw text into a sequence of integer IDs. Common strategies include:
- **Byte Pair Encoding (BPE)** — used by GPT series, LLaMA
- **WordPiece** — used by BERT
- **SentencePiece / Unigram LM** — used by T5, LLaMA-2/3

**Embeddings** map token IDs into continuous, high-dimensional vectors (typically 512–4096 dimensions). Token embeddings are learned during training.

**Positional Encoding** injects position information since attention is inherently permutation-invariant. Two approaches exist:
- *Sinusoidal (fixed)*: Original Transformer. Deterministic sine/cosine functions.
- *Learned absolute*: Parameters added per-position (GPT-2, BERT).
- *Rotary (RoPE)*: Modern standard (LLaMA, Mistral). Relative positions encoded by rotating the query/key vectors.
- *ALiBi (Attention with Linear Biases)*: Adds a linear penalty to attention scores based on distance, improving length generalization.

**Residual Connections** (He et al., 2016) add the input of each sub-layer directly to its output — `output = sublayer(x) + x` — enabling stable training of very deep networks.

**Layer Normalization** normalizes activations. Pre-LN (normalize *before* the sub-layer) is standard in modern LLMs, as it provides more stable training gradients than Post-LN.

**Feed-Forward Network (FFN)** applies two linear transformations with a non-linearity: `FFN(x) = max(0, xW1 + b1)W2 + b2`. Modern LLMs often use SwiGLU or GeLU activations and expand the hidden dimension by 4x.

---

## Attention Mechanism

Attention is the core innovation of the Transformer. It allows each token to directly "attend" to all other tokens in the sequence.

![Attention Mechanism](diagrams/attention-mechanism.drawio.svg)

### Scaled Dot-Product Attention

Given three matrices derived from the input — **Queries (Q)**, **Keys (K)**, and **Values (V)** — attention is computed as:

```
Attention(Q, K, V) = softmax(Q·Kᵀ / √dₖ) · V
```

The `√dₖ` scaling factor prevents the dot products from growing too large in high dimensions, stabilizing the softmax gradient.

### Multi-Head Attention (MHA)

Rather than computing a single attention function, MHA runs `h` attention heads in parallel, each with its own learned projection matrices (WiQ, WiK, WiV). The outputs are concatenated and projected:

```
MHA(Q, K, V) = Concat(head₁, ..., headₕ) · W^O
where headᵢ = Attention(Q·WᵢQ, K·WᵢK, V·WᵢV)
```

Each head can attend to different aspects of the representation — syntax, semantics, coreference, etc.

### Variants and Efficiency Improvements

| Variant | Key Idea | Used In |
|---|---|---|
| **Multi-Head Attention (MHA)** | Standard h-heads, separate K/V per head | GPT-2, BERT, original Transformer |
| **Multi-Query Attention (MQA)** | All heads share one K/V pair; faster inference | PaLM, Falcon |
| **Grouped-Query Attention (GQA)** | Groups share K/V; balance of MHA and MQA | LLaMA-2/3, Mistral, Gemma |
| **Sparse / Local Attention** | Attends only to nearby or selected tokens | Longformer, BigBird |
| **FlashAttention** | Tiling algorithm; IO-aware exact attention, no approximation | LLaMA-3, GPT-4 training |
| **Sliding Window Attention** | Attends to a fixed-size window + global tokens | Mistral, Mixtral |

**Cross-Attention** is used in Encoder-Decoder models: the Decoder attends to the Encoder's output, using the Decoder's representation as Queries and the Encoder's output as Keys and Values.

---

## LLM Architecture Types

Modern LLMs fall into three architectural families, each optimized for different task types.

![LLM Architecture Types](diagrams/llm-architecture-types.drawio.svg)

### Encoder-Only

Only the Transformer encoder is used. All tokens attend to all other tokens bidirectionally (full self-attention). Produces contextual representations, not autoregressive output.

**Training objective**: Masked Language Modeling (MLM) — randomly mask ~15% of tokens and predict them.

**Best for**: Classification, Named Entity Recognition (NER), question answering (extractive), semantic similarity.

| Model | Org | Year | Params | Notable Feature |
|---|---|---|---|---|
| **BERT** | Google | 2018 | 110M / 340M | First large-scale MLM pre-training |
| **RoBERTa** | Meta | 2019 | 355M | Improved BERT training: more data, dynamic masking |
| **ALBERT** | Google | 2019 | 12M–235M | Parameter sharing across layers |
| **DistilBERT** | HuggingFace | 2019 | 66M | Knowledge distillation of BERT |
| **DeBERTa** | Microsoft | 2021 | 1.5B | Disentangled attention (position + content separate) |

### Decoder-Only

Only the Transformer decoder stack is used, but **without** cross-attention (no encoder). Each token can only attend to previous tokens via **causal (masked) self-attention**. Generates text autoregressively.

**Training objective**: Causal Language Modeling (CLM) / Next Token Prediction.

**Best for**: Open-ended text generation, chatbots, code generation, reasoning, in-context learning.

| Model | Org | Year | Params | Notable Feature |
|---|---|---|---|---|
| **GPT-1** | OpenAI | 2018 | 117M | First large-scale decoder LLM |
| **GPT-2** | OpenAI | 2019 | 1.5B | Zero-shot task transfer, initially withheld |
| **GPT-3** | OpenAI | 2020 | 175B | Few-shot in-context learning, commercial API |
| **GPT-3.5 / ChatGPT** | OpenAI | 2022 | ~175B | RLHF alignment, conversational assistant |
| **GPT-4** | OpenAI | 2023 | ~1.8T (MoE, unconfirmed) | Multimodal, best benchmark performance |
| **LLaMA** | Meta | 2023 | 7B–65B | Open weights, efficient architecture |
| **LLaMA-2** | Meta | 2023 | 7B–70B | Commercial license, RLHF-trained chat variants |
| **LLaMA-3** | Meta | 2024 | 8B–405B | GQA, 128K context, strong open-source baseline |
| **Mistral 7B** | Mistral AI | 2023 | 7B | Sliding window attention, GQA, outperforms LLaMA-2 13B |
| **Falcon** | TII | 2023 | 7B–180B | Multi-Query Attention, trained on RefinedWeb |
| **Claude series** | Anthropic | 2023-25 | Undisclosed | Constitutional AI, harmlessness-focused RLHF |
| **Gemini** | Google | 2023-24 | Undisclosed | Multimodal from ground up |

### Encoder-Decoder (Seq2Seq)

Both an encoder and a decoder stack are used. The encoder processes the full input and creates contextual representations; the decoder generates output autoregressively while attending to those representations via cross-attention.

**Training objective**: Span Corruption (T5), Denoising (BART), or standard Seq2Seq objectives.

**Best for**: Machine translation, summarization, question generation, structured prediction.

| Model | Org | Year | Params | Notable Feature |
|---|---|---|---|---|
| **T5** | Google | 2019 | 60M–11B | Unified text-to-text framework for all NLP tasks |
| **BART** | Meta | 2019 | 400M | Denoising autoencoder, strong summarization |
| **mT5** | Google | 2020 | 300M–13B | Multilingual T5 across 101 languages |
| **mBART** | Meta | 2020 | 610M | Multilingual seq2seq, machine translation |
| **Flan-T5** | Google | 2022 | 80M–11B | Instruction fine-tuned T5, strong zero-shot |

---

## Scaling Laws

Two landmark empirical studies define how performance scales with compute.

### Kaplan et al. Scaling Laws (2020 — OpenAI)

*"Scaling Laws for Neural Language Models"* showed that LLM performance (cross-entropy loss) follows smooth power laws with respect to three variables: model size (N), dataset size (D), and compute (C). Key finding: **model size matters most** when holding compute fixed. This drove the GPT-3 era of large models trained on relatively small datasets.

### Chinchilla Scaling Laws (2022 — DeepMind)

*"Training Compute-Optimal Large Language Models"* (Hoffmann et al.) corrected Kaplan by showing that model size and training tokens should scale **proportionally**. The Chinchilla rule of thumb: **~20 tokens per parameter**. A 70B model needs ~1.4T tokens. This produced Chinchilla (70B, 1.4T tokens), which outperformed Gopher (280B) at 4x its compute. LLaMA-1 violated this rule intentionally to optimize for inference cost (train smaller models on more data).

---

## Training Pipeline

Modern LLMs are trained in three distinct phases.

![LLM Training Pipeline](diagrams/llm-training-pipeline.drawio.svg)

### Phase 1 — Pre-Training

The model learns a general-purpose representation of language by predicting text on a massive, diverse corpus: Common Crawl, Books, Wikipedia, GitHub, academic papers. This is the most compute-intensive phase (thousands of GPU-hours, billions of dollars at frontier scale).

### Phase 2 — Supervised Fine-Tuning (SFT)

The pre-trained base model is fine-tuned on **curated instruction-response pairs** (e.g., InstructGPT dataset, Alpaca, OpenAssistant). The model learns to follow instructions and behave like an assistant, using standard cross-entropy loss on the demonstration outputs.

### Phase 3 — Alignment via RLHF or DPO

**RLHF (Reinforcement Learning from Human Feedback)** (Ouyang et al., 2022 — InstructGPT):
1. Human annotators rank multiple model outputs from best to worst.
2. A **Reward Model** is trained to predict these human preferences.
3. The SFT model is further optimized via **PPO (Proximal Policy Optimization)** to maximize the reward signal, with a KL-divergence penalty preventing it from diverging too far from the SFT model.

**DPO (Direct Preference Optimization)** (Rafailov et al., 2023): Eliminates the separate reward model. It directly optimizes the policy on preference pairs using a classification loss derived analytically from the RLHF objective. Simpler, more stable, now widely adopted.

**RLAIF (RL from AI Feedback)**: Human feedback is replaced by feedback from a more capable AI model (Constitutional AI / Claude). Scales preference data collection without human raters.

---

## Mixture of Experts (MoE)

MoE replaces the dense Feed-Forward Network in each Transformer layer with a set of parallel "expert" FFN sub-networks, routing each token to only a sparse subset (Top-K) of experts.

![Mixture of Experts Architecture](diagrams/moe-architecture.drawio.svg)

### How It Works

A **Gating / Router Network** computes a score for each expert given the input token. The top-K experts (typically K=1 or K=2) are selected, their outputs are computed and then combined via weighted sum. All other experts are bypassed.

This enables a model to have a very large **total parameter count** while keeping the **active parameters per forward pass** (and thus compute) much smaller.

**Mixtral 8x7B** (Mistral AI, 2023): 8 experts, 2 active per token. ~47B total parameters, ~13B active per forward pass. Outperforms LLaMA-2 70B on most benchmarks while requiring ~3x less compute.

**GPT-4** is widely believed to be a sparse MoE model, though OpenAI has not officially confirmed the architecture details.

Key challenge: **Load balancing** — preventing all tokens routing to the same expert. Addressed via auxiliary load-balancing loss terms.

---

## Context Length Innovations

Standard attention is O(n²) in memory and compute with respect to sequence length. Several innovations have extended practical context windows dramatically.

### Rotary Position Embeddings (RoPE)

Introduced by Su et al. (2021). Instead of absolute positional encodings, RoPE encodes position by **rotating** the query and key vectors before the attention dot product. Properties: (1) The attention score between two tokens depends only on their *relative* position, (2) There is no explicit upper limit — length extrapolation is possible with tricks like YaRN or NTK-aware scaling. **Now the dominant positional encoding**: LLaMA-1/2/3, Mistral, Falcon, GPT-NeoX.

### ALiBi (Attention with Linear Biases)

Train Press et al. (2022). Instead of positional embeddings, adds a linear penalty (`-m * |i - j|`) to attention scores before softmax, where m is a head-specific slope. Trains at short context but generalizes well to longer sequences without modifications.

### FlashAttention (Dao et al., 2022)

An **IO-aware exact attention algorithm** that computes standard scaled dot-product attention without materializing the full N×N attention matrix. Uses tiling to keep data in SRAM (fast GPU memory), reducing memory bandwidth usage by 5-20x and enabling 2-4x speedup. **FlashAttention-2** (2023) and **FlashAttention-3** (2024) improved further. Enables training at 128K+ context lengths.

### Sliding Window Attention

Each token attends only to a fixed window of W previous tokens (e.g., W=4096) rather than the entire history. Combined with a few global attention tokens, this reduces complexity to O(n·W). Used in Mistral 7B, Mixtral.

### Modern Context Lengths (2024-2025)

| Model | Context Window |
|---|---|
| GPT-3.5 | 16K |
| GPT-4 | 128K |
| Claude 3 Opus | 200K |
| Gemini 1.5 Pro | 1M |
| LLaMA-3.1 | 128K |

---

## Alternative Architectures (Beyond Transformers)

While Transformers dominate, research has produced promising alternatives that achieve linear (O(n)) complexity.

### State Space Models (SSMs) — Mamba

**Mamba** (Gu & Dao, 2023) is a Selective State Space Model with a hardware-aware parallel scan algorithm. Key innovation: input-dependent (selective) state spaces — the SSM parameters vary per token, allowing the model to selectively propagate or forget information. Achieves Transformer-quality performance on language modeling with O(n) inference complexity. **Mamba-2** (2024) unified SSMs and attention mathematically (Structured State Space Duality).

### RWKV

**RWKV** (Peng et al., 2023) combines the parallelizable training of Transformers with the efficient O(n) inference of RNNs. Uses a time-mixing mechanism (approximating attention) and channel-mixing (FFN substitute), formulated to support both parallel training and recurrent inference. Models trained up to 14B parameters.

### RetNet

**RetNet** (Sun et al., 2023 — Microsoft) proposes a "retention" mechanism as an alternative to attention. Supports three computation paradigms: parallel (training), recurrent (efficient inference), and chunkwise recurrent (hybrid). Claims the "impossible triangle" of performance, efficiency, and training stability simultaneously.

---

## Multimodal LLMs

Vision-Language Models (VLMs) extend LLMs to process images, audio, and video alongside text.

### Architecture Patterns

The dominant approach uses a **visual encoder** (typically a CLIP-style ViT) to project image patches into visual tokens, which are fed into the LLM alongside text tokens.

**LLaVA** (Liu et al., 2023): A simple, open-source VLM. A CLIP ViT encodes the image; a linear projection maps visual features to the LLM's embedding space. The LLM (LLaMA) processes the combined visual+text tokens. LLaVA-1.5 and LLaVA-Next further improved with MLP connectors and higher resolution.

**Flamingo** (DeepMind, 2022): Cross-attention layers interleave with frozen LLM layers. Visual tokens act as Keys/Values in cross-attention blocks injected between LLM transformer layers. Strong few-shot visual question answering.

**GPT-4V / GPT-4o** (OpenAI, 2023/2024): Full multimodal integration. GPT-4o processes text, image, and audio natively, without separate encoders for each modality.

**Gemini** (Google, 2023): Multimodal from pretraining — not retrofitted. Processes interleaved text, images, audio, and video tokens natively in a single model.

---

## LLM Evolution Timeline

![LLM Evolution Timeline](diagrams/llm-evolution-timeline.drawio.svg)

---

## Key Principles & Design Decisions

**Vocabulary size**: Typically 32K–128K tokens. Larger vocabularies improve tokenization of non-English languages and code. LLaMA-3 expanded from 32K to 128K.

**Layer depth vs. width**: More layers allow deeper abstraction; wider layers (larger d_model) store more knowledge per layer. Both scale together.

**Activation function**: ReLU → GeLU (Gaussian Error Linear Unit, used in BERT/GPT-2) → SwiGLU (Swish-Gated Linear Unit, used in LLaMA, PaLM) — shown to improve model quality.

**Weight tying**: Tying the input embedding matrix to the output projection (lm_head) reduces parameters significantly. Used in GPT-2, T5.

**KV Cache**: During inference, previously computed Key and Value tensors are cached to avoid recomputation. Critical optimization for autoregressive generation. Memory increases linearly with context length and batch size.

**Parameter Efficient Fine-Tuning (PEFT)**: Fine-tuning all parameters of a large LLM is expensive. Techniques like **LoRA** (Low-Rank Adaptation) freeze the base model and inject small trainable rank-decomposition matrices into attention layers. Reduces trainable parameters by 99%+ while achieving comparable performance.

---

## References

| Paper | Authors | Year | Contribution |
|---|---|---|---|
| [Attention Is All You Need](https://arxiv.org/abs/1706.03762) | Vaswani et al. | 2017 | Transformer architecture |
| [BERT](https://arxiv.org/abs/1810.04805) | Devlin et al. (Google) | 2018 | Encoder-only, MLM pre-training |
| [GPT-2](https://openai.com/research/language-models-are-unsupervised-multitask-learners) | Radford et al. (OpenAI) | 2019 | Large-scale decoder LM, zero-shot |
| [Exploring the Limits of Transfer Learning with T5](https://arxiv.org/abs/1910.10683) | Raffel et al. (Google) | 2019 | Text-to-text Enc-Dec framework |
| [Language Models are Few-Shot Learners (GPT-3)](https://arxiv.org/abs/2005.14165) | Brown et al. (OpenAI) | 2020 | 175B, in-context learning |
| [Scaling Laws for NLMs](https://arxiv.org/abs/2001.08361) | Kaplan et al. (OpenAI) | 2020 | Compute/data/model scaling laws |
| [Training Compute-Optimal LLMs (Chinchilla)](https://arxiv.org/abs/2203.15556) | Hoffmann et al. (DeepMind) | 2022 | Optimal token/param ratio |
| [InstructGPT / RLHF](https://arxiv.org/abs/2203.02155) | Ouyang et al. (OpenAI) | 2022 | RLHF alignment for LLMs |
| [RoFormer / RoPE](https://arxiv.org/abs/2104.09864) | Su et al. | 2021 | Rotary positional embeddings |
| [FlashAttention](https://arxiv.org/abs/2205.14135) | Dao et al. | 2022 | IO-aware exact attention algorithm |
| [LLaMA](https://arxiv.org/abs/2302.13971) | Touvron et al. (Meta) | 2023 | Open-weights efficient LLM |
| [Mistral 7B](https://arxiv.org/abs/2310.06825) | Jiang et al. | 2023 | Sliding window attention, GQA |
| [Mixtral of Experts](https://arxiv.org/abs/2401.04088) | Jiang et al. (Mistral) | 2023 | Sparse MoE decoder LLM |
| [DPO](https://arxiv.org/abs/2305.18290) | Rafailov et al. | 2023 | Direct preference optimization |
| [Mamba / SSM](https://arxiv.org/abs/2312.00752) | Gu & Dao | 2023 | Linear-time selective SSM |
| [RWKV](https://arxiv.org/abs/2305.13048) | Peng et al. | 2023 | RNN-Transformer hybrid |
| [LLaVA](https://arxiv.org/abs/2304.08485) | Liu et al. | 2023 | Open-source vision-language model |
| [LoRA](https://arxiv.org/abs/2106.09685) | Hu et al. (Microsoft) | 2021 | Low-rank adaptation fine-tuning |
| [FlashAttention-2](https://arxiv.org/abs/2307.08691) | Dao | 2023 | Improved IO-aware attention |
| [LLaMA-3](https://ai.meta.com/blog/meta-llama-3/) | Meta | 2024 | 128K context, 128K vocab, GQA |

---

## Related Notes

- [[Attention-Mechanism]]
- [[Transformer-Architecture]]
- [[RLHF-and-Alignment]]
- [[Prompt-Engineering]]
- [[Fine-Tuning-Techniques]]

---

*Last updated: 2026-04-05 | Sources: Anthropic, OpenAI, DeepMind, Meta AI, arXiv*
