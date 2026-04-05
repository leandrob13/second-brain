# Large Language Model (LLM) Architectures

**Date**: 2026-04-05
**Status**: Comprehensive Research Note
**Scope**: Foundational concepts through modern innovations

---

## Table of Contents

1. [Core Concepts](#core-concepts)
2. [Transformer Architecture](#transformer-architecture)
3. [Key Components](#key-components)
4. [Attention Mechanisms](#attention-mechanisms)
5. [Architecture Variants](#architecture-variants)
6. [Scaling Laws](#scaling-laws)
7. [Training Techniques](#training-techniques)
8. [Mixture of Experts (MoE)](#mixture-of-experts)
9. [Context Length Innovations](#context-length-innovations)
10. [Modern Architectures](#modern-architectures)
11. [Multimodal LLMs](#multimodal-llms)
12. [Key References & Timeline](#key-references--timeline)

---

## Core Concepts

### What are LLMs?

Large Language Models (LLMs) are neural networks trained on vast amounts of text data to predict and generate human language. As of 2024-2025, the largest and most capable LLMs are all based on transformer architectures, which are more efficient and parallelizable than earlier statistical and recurrent neural network (RNN) models.

### How LLMs Differ from Traditional NLP

**Traditional NLP Approach:**
- Rule-based systems with hand-crafted features
- Task-specific architectures for different NLP problems
- Limited context understanding beyond immediate surroundings
- Weak long-range dependency modeling
- Feature engineering required

**LLM Approach:**
- Learned representations from massive text corpora
- Unified architectures solving multiple tasks through prompting
- Bidirectional or autoregressive context processing
- Excellent long-range dependency capture via attention
- Automatic feature extraction through self-supervised learning
- Transfer learning capabilities across domains and tasks

---

## Transformer Architecture

### The Foundational Paper: "Attention is All You Need" (2017)

**Authors**: Vaswani et al., Google Brain
**Published**: June 12, 2017
**Venue**: NeurIPS 2017

The transformer architecture revolutionized deep learning by proposing a network based solely on attention mechanisms, dispensing with recurrence and convolutions. The original 100M-parameter encoder–decoder model achieved new state-of-the-art results on machine translation tasks (41.8 BLEU on WMT 2014 English-to-French) while being more parallelizable and requiring significantly less training time.

### Core Innovation: Self-Attention Mechanism

Self-attention allows a model to look at other positions in the input sequence for context that helps encode each token. Each token attends to every other token (and itself), enabling the model to capture relationships between distant positions efficiently.

**How it works:**
- For each token, create three vectors: Query (Q), Key (K), and Value (V)
- Query represents what the token is looking for
- Key represents what the token offers to others
- Value contains the actual information the token holds
- Compute attention scores: soft weights based on query-key similarity

### Multi-Head Attention

Rather than performing a single attention function, transformers use multiple sets of Query/Key/Value weight matrices (typically 8+ attention heads). This allows the model to:
- Focus on different representation subspaces
- Capture syntactic, semantic, and positional relationships simultaneously
- Learn more robust and diverse attention patterns

Each head operates independently, and their outputs are concatenated and projected to the output dimension.

### Positional Encoding

Self-attention has no inherent notion of token order or position—if you shuffle tokens, the attention scores would remain unchanged. Transformers solve this by adding positional information to embeddings:

**Original approach (2017)**: Sinusoidal positional encoding using sine and cosine functions at different frequencies

**Modern alternatives**: Relative position encodings (RoPE, ALiBi) that capture relative distances rather than absolute positions

### Encoder-Decoder Architecture

The original transformer has two components:

**Encoder:**
- Stack of identical layers with self-attention
- No causal masking—each position can attend to all positions
- Processes entire input sequence bidirectionally
- Produces key-value pairs for cross-attention

**Decoder:**
- Stack of identical layers with self-attention (causal), cross-attention, and feedforward
- Causal masking prevents attending to future tokens during generation
- Cross-attention attends to encoder outputs
- Generates output tokens autoregressively (one at a time)

---

## Key Components

### Token Embeddings

Text input is divided into smaller units called **tokens** (words, subwords, or characters). These are converted to numerical vectors called **embeddings** that capture semantic meaning. Modern systems use **subword tokenization** (BPE, WordPiece) to handle rare words efficiently.

### Feedforward Networks

Each transformer layer contains a position-wise feedforward network (FFN) applied independently to each token:
- Typically: Linear → Activation → Linear
- Modern implementations use **SwiGLU** activation instead of GELU or ReLU
- Increases model capacity without adding attention parameters

### Layer Normalization

Stabilizes training and helps the model converge faster. Modern architectures typically use **RMSNorm** instead of traditional LayerNorm for better performance.

**Placement variants:**
- Pre-normalization: Normalize before attention/FFN (common in modern LLMs)
- Post-normalization: Normalize after attention/FFN (original transformer)

### Residual Connections

Skip connections bypass layers and allow gradients to flow directly through the network. This prevents the vanishing gradient problem in deep architectures and enables training of very deep models (100+ layers).

**Structure**: Output = Layer(LayerNorm(Input)) + Input

### Dropout

Prevents overfitting by randomly deactivating neurons during training. Modern LLMs use lower dropout rates (0.0-0.1) due to the regularizing effect of large-scale training data.

---

## Attention Mechanisms

### Scaled Dot-Product Attention (SDPA)

The fundamental attention mechanism used in transformers:

**Formula**: Attention(Q, K, V) = Softmax(QK^T / √d_k) V

Where:
- Q (Queries): n × d_k matrix
- K (Keys): m × d_k matrix
- V (Values): m × d_v matrix
- d_k: dimension of key vectors
- Scaling factor 1/√d_k: keeps dot products in regime with substantial gradients

**Computational complexity**: O(n × m) time and space, where n and m are sequence lengths

### Multi-Head Attention

Projects Q, K, V to multiple "heads" (subspaces), computes attention independently in each head, concatenates results, and projects to output dimension:

**Advantage**: Allows attending to different parts of the representation space simultaneously

**Modern variant (2025)**: Grouped-Query Attention (GQA) and Multi-Query Attention (MQA) reduce parameters and memory by sharing Key/Value heads across Query heads.

### Cross-Attention

One sequence attends to another (unlike self-attention where a sequence attends to itself). Used in encoder-decoder models where the decoder attends to encoder outputs.

### Sparse Attention

Handles extremely long sequences (100,000+ tokens) by restricting attention to specific patterns rather than all-to-all:

**Patterns include:**
- Local attention: attend to nearby tokens only
- Strided attention: attend at regular intervals
- Landmark attention: attend to summary tokens

**Benefits**: Reduces complexity from O(n²) to O(n × √n) or O(n log n) while maintaining reasonable performance

### Flash Attention (2022)

Optimizes memory access patterns during attention computation to minimize slow transfers between GPU main memory and fast on-chip SRAM:

**Key innovation**: Compute attention in tiles without materializing full attention score matrices

**Performance gains:**
- 2-4x speedup on standard sequences
- Enables training on much longer sequences
- Reduced memory usage
- Improved numerical stability

**Flash Attention 2 (2023)**: Further optimizations achieving ~50% speedup over Flash Attention 1

---

## Architecture Variants

### Encoder-Only Models

**Use case**: Understanding and encoding text (classification, NER, semantic search)

**Architecture**: Stack of transformer encoder layers, no causal masking

**Bidirectional**: Each token attends to all positions, providing full context understanding

#### BERT (2018)
- **Authors**: Devlin et al., Google Research
- **Paper**: "BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding"
- **Size**: 110M to 340M parameters
- **Training objective**: Masked Language Modeling (MLM) + Next Sentence Prediction (NSP)
- **Strengths**: Excellent for classification and understanding tasks
- **Limitation**: Cannot generate text autoregressively

#### RoBERTa (2019)
- **Authors**: Liu et al., Facebook AI
- **Improvements over BERT**:
  - Trained on larger corpus (160GB vs 16GB)
  - Dynamic masking strategy (different masks per epoch)
  - Removed NSP task (shown to hurt performance)
  - Larger batch sizes (2k vs 256)
  - Expanded vocabulary (50K tokens)
- **Result**: Better performance on downstream tasks

#### ALBERT (2019)
- **Key innovation**: Parameter reduction through factorization
- **Architecture**: Sharing weights across layers + factorized embedding parameters
- **Benefit**: 90% reduction in parameters while maintaining competitive performance

### Decoder-Only Models

**Use case**: Text generation, translation, question answering, any autoregressive task

**Architecture**: Stack of transformer decoder layers with causal (autoregressive) masking

**Unidirectional**: Each token attends only to previous tokens (left-to-right)

**Training**: Predict next token given previous tokens (causal language modeling)

#### GPT Series (OpenAI)

**GPT-1 (2018)**
- 110M parameters
- Decoder-only transformer
- Pre-training + task-specific fine-tuning approach
- Established that large-scale pre-training transfers well

**GPT-2 (2019)**
- 1.5B parameters
- Demonstrated zero-shot capability
- Showed language models can learn diverse tasks without explicit supervision
- First model showing genuine task generalization

**GPT-3 (2020)**
- 175B parameters
- Few-shot and zero-shot performance improvements
- Sparked the era of prompt engineering
- Revealed scaling laws hold for very large models

**GPT-3.5 & GPT-4 (2022-2023)**
- Optimized versions with instruction tuning
- Improved reasoning and alignment
- GPT-4: multimodal (vision + language)

#### LLaMA Series (Meta)

**LLaMA (2023)**
- **Sizes**: 7B, 13B, 33B, 65B parameters
- **Innovation**: Competitive performance with lower parameter count than GPT-3
- **Key techniques**:
  - Trained on 1-2T tokens (2x more than GPT-3)
  - RoPE positional embeddings
  - SwiGLU activations
  - Pre-normalization
- **Larger variants**: Grouped-Query Attention (GQA) for inference efficiency

**LLaMA 2 (2023)**
- Improved training stability
- Extended context length (4K tokens)
- Better fine-tuning results
- Open weights (commercially usable)

**LLaMA 3 (2024)**
- Further improvements in instruction following
- Better multilingual support
- Scaling improvements

#### Mistral (2023)

**Mistral 7B**
- 7B parameters, competitive with 13B+ models
- **Key techniques**:
  - Grouped-Query Attention (GQA)
  - Sliding Window Attention (4K context)
  - Flash Attention
  - Parallel attention and feedforward
- **Variants**: Mistral Instruct (fine-tuned), Mistral Medium, Mistral Large

**Innovation**: Demonstrated that architectural improvements can compensate for smaller model size

#### Falcon (2023)

**Falcon 7B / 40B / 180B**
- **Unique features**:
  - Multi-Query Attention (MQA) for inference speed
  - Flash Attention integration
  - Parallel attention and feedforward
  - No absolute positional embeddings (ALiBi approach)
- **Training**: 1.5 trillion tokens
- **Performance**: Competitive with larger open models

### Encoder-Decoder Models

**Use case**: Sequence-to-sequence tasks (translation, summarization, question answering)

**Architecture**: Encoder processes input bidirectionally, decoder generates output with cross-attention to encoder

**Training**: Predict target sequence given source sequence

#### T5 (2019)

**Authors**: Raffel et al., Google Brain
**Key innovation**: "Text-to-Text Transfer Transformer"—treats all NLP tasks as text-to-text generation

**Characteristics:**
- Sizes: 60M to 11B parameters
- Unified framework for diverse NLP tasks
- Relative position embeddings (vs. sinusoidal in original)
- Pre-training on large corpus using masked span prediction
- Flexible task specification via text prefixes ("translate English to French: ...")

**Variants:**
- **FLAN-T5** (2023): Instruction-tuned version with better zero-shot generalization
- **mT5**: Multilingual T5 for cross-lingual tasks

#### BART (2019)

**Authors**: Lewis et al., Facebook AI
**Combination**: Merges BERT and GPT strengths

**Architecture:**
- Bidirectional encoder + autoregressive decoder
- Pre-trained by corrupting text (masking, deletion, permutation) and learning to reconstruct
- Combines MLM (BERT) and causal LM (GPT) objectives

**Strengths:**
- Excellent for abstractive summarization
- Strong on dialogue tasks
- Better semantic understanding than pure decoder models

#### mT5 (Multilingual T5)

- Extends T5 to 101 languages
- Zero-shot cross-lingual transfer
- Enables low-resource language applications

---

## Scaling Laws

### Kaplan Scaling Laws (2020)

**Paper**: "Scaling Laws for Neural Language Models"
**Authors**: Kaplan et al., OpenAI

**Key findings:**
- Test loss decreases smoothly and predictably as model size, dataset size, and compute increase
- Power-law relationships: Loss ∝ N^(-α) where α ≈ 0.07
- **Critical insight**: Model size should increase faster than dataset size with fixed compute

**Rule of thumb**: 300B tokens can train a 175B parameter model (≈1.7 tokens per parameter)

**Implication**: Researchers prioritized larger models, sometimes training them on relatively small amounts of data

### Chinchilla Scaling Laws (2022)

**Paper**: "Training Compute-Optimal Large Language Models"
**Authors**: Hoffmann et al., DeepMind

**Revolutionary finding**: Model size and dataset size should be scaled **equally** (not disproportionately)

**Optimal balance**: ~20 tokens per parameter for large-scale transformers

**Example**: 70B parameter model should train on ~1.4 trillion tokens (20 × 70B)

**Practical result**: DeepMind trained Chinchilla (70B params) using same compute as Gopher (280B params) but 4x more data:
- Chinchilla outperformed Gopher, GPT-3, Jurassic-1, and Megatron
- Same FLOP budget, better performance through optimal allocation

**Impact (2023-2025)**: Shifted industry toward more compute-efficient training, spawning competitive smaller models (13B, 7B) that match or exceed larger predecessors

### Modern Scaling Insights (2024)

Recent research challenges some scaling law assumptions:
- **Data quality matters more than quantity**: Better curated datasets enable higher performance with fewer tokens
- **Emergence and phase transitions**: Models show sudden capability jumps at certain scales
- **Diminishing returns**: Scaling benefits flatten for very large models
- **Inference scaling**: Routes like Chain-of-Thought can improve performance without retraining

---

## Training Techniques

### Pre-training

Large-scale unsupervised learning on massive text corpora (typically 1-7 trillion tokens):

**Decoder-only (Causal LM)**:
- Objective: Predict next token given previous tokens
- Loss: Cross-entropy on next token prediction
- Data: Raw internet text (Common Crawl, Books, Wikipedia, etc.)

**Encoder-only (MLM)**:
- Objective: Predict randomly masked tokens
- Loss: Cross-entropy on masked token prediction
- Data: Same as decoder-only

**Encoder-Decoder**:
- Objective: Corrupt input, predict original
- Loss: Reconstruction loss
- Data: Sentence/document pairs

### Supervised Fine-Tuning (SFT)

Also called instruction tuning. Fine-tune pre-trained model on task-specific or instruction-following data:

**Process:**
1. Prepare high-quality instruction-response pairs
2. Fine-tune model to predict responses given instructions
3. Typically 5K-50K examples (much less than pre-training)
4. Lower learning rate than pre-training

**Key research (2024)**: Longer responses and instruction diversity matter more than quantity—selecting 1,000 instructions with longest responses outperforms sophisticated selection methods

### Reinforcement Learning from Human Feedback (RLHF)

Aligns model behavior with human preferences through a two-stage process:

**Stage 1: Reward Model Training**
- Collect human comparisons of model outputs (pairwise preferences)
- Train a reward model to predict which output humans prefer
- Reward model learns to score quality

**Stage 2: Policy Optimization**
- Use reward model to provide learning signal
- Optimize language model with RL algorithm (typically PPO)
- Maximize expected reward while staying close to original model (KL penalty)

**Challenges:**
- Complex, multi-stage process
- Unstable training
- Requires reward model that generalizes well
- Expensive human annotation

**Adoption**: By 2025, RLHF became default alignment strategy for 70% of enterprises (up from 25% in 2023)

### Direct Preference Optimization (DPO) (2023)

**Paper**: "Direct Preference Optimization: Your Language Model is Secretly a Reward Model"
**Authors**: Rafailov et al.

**Key innovation**: Directly optimize language model using preference data, eliminating need for separate reward model

**How it works:**
- Use closed-form solution showing language model can be a reward model
- Define loss comparing preferred vs. dispreferred responses
- Simple classification loss instead of complex RL training
- Stable, efficient training

**Advantages over RLHF:**
- 50-75% faster training
- More stable (no RL instability)
- Simpler to implement
- No need for sampling during training
- Minimal hyperparameter tuning

**Results**: Matches or exceeds PPO-based RLHF on summarization and dialogue while being substantially simpler

**Adoption**: Rapidly becoming default method for LLM alignment (2023-2025)

### Instruction Tuning

Supervised fine-tuning on instruction-response data to improve zero-shot generalization:

**Process:**
1. Create or collect instruction-response pairs
2. Fine-tune model to follow diverse instructions
3. Improves generalization to unseen tasks

**Data sources:**
- Human-annotated examples
- Synthetic data from GPT-4 (Alpaca, etc.)
- Existing datasets formatted as instructions

**Key insight**: Alignment (learning to follow instructions) requires less data than learning new knowledge

**Safety consideration (2024)**: Even benign fine-tuning can degrade safety alignment—fine-tuning "aligned" models introduces new safety risks

---

## Mixture of Experts (MoE)

### Architecture Overview

Mixture of Experts is a neural network architecture containing multiple specialized sub-networks (experts), with a learned routing network directing each input token to a subset of experts:

**Key concept**: Only a fraction of parameters activate for each forward pass (sparse activation)

**Benefits:**
- Massive parameter counts possible with reasonable computation
- Parameter efficiency: 5-10x more parameters with similar compute
- Potential for specialized learning per expert

### Router Network

The router is the most critical component:

**Typical design**:
- Linear layer followed by softmax
- Produces routing scores for each expert
- Top-K selection (e.g., top-2 experts)
- Gating function determines expert weights

**Key challenge**: Expert collapse—when most tokens route to few popular experts while others become dead weight. Requires careful router design and load balancing.

### Mixtral 8x7B (2023)

**Authors**: Mistral AI

**Architecture:**
- 32 transformer blocks
- Each feedforward (MLP) layer replaced with sparse MoE block
- 8 experts per layer, top-2 routing
- Only 2 experts activate per token
- Active parameters: ~13B (5x lower than 8 × 7B = 56B)
- Inference speed equivalent to 12B model (2 × 7B multiplications with shared layers)

**Performance:**
- Outperforms LLaMA 2 70B across most benchmarks
- Better inference throughput than larger dense models
- Maintains competitive quality with fraction of compute

### Sparse vs. Dense MoE

**Dense MoE**: All experts active (no sparse benefit)
**Sparse MoE**: Conditional activation per token (computational advantage)

Sparse MoE enables scaling to 100B+ parameter models while maintaining reasonable inference costs.

---

## Context Length Innovations

### Rotary Position Embeddings (RoPE) (2021)

**Paper**: "RoFormer: Enhanced Transformer with Rotary Position Embedding"
**Authors**: Su et al.

**Core idea**: Represent token embeddings as complex numbers, positions as rotations applied to them

**How it works:**
- Encode absolute position as rotation matrix
- Apply rotation to embedding vectors
- Dot product in self-attention preserves relative positional information
- Discard absolute position (relative distances matter)

**Advantages:**
- Flexible sequence length (no need to specify max length upfront)
- Decaying inter-token dependency with distance
- Enables linear self-attention with relative position encoding
- Explicit relative position dependency in attention

**Modern adoption**: Default in LLaMA, Mistral, Falcon, and most modern LLMs

### Attention with Linear Biases (ALiBi) (2022)

**Paper**: "Train Short, Test Long: Attention with Linear Biases Enables Input Length Extrapolation"
**Authors**: Press et al.

**Core idea**: Inject positional information directly into attention scores rather than embeddings

**How it works:**
- Add bias matrix to attention scores before softmax
- Bias magnitude proportional to distance between query and key
- No learnable parameters (fixed bias pattern)
- Relative position-based (not absolute)

**Advantages over RoPE:**
- Better extrapolation to longer sequences than seen during training
- Faster training than RoPE
- No perplexity degradation when extending context length
- Simpler implementation

**Trade-off**: RoPE has slightly better in-distribution performance; ALiBi better for out-of-distribution length extrapolation

### Context Window Extensions

**Sliding Window Attention** (Mistral):
- Only attend to local window of recent tokens
- Reduces complexity from O(n²) to O(n)
- Combines with distant attention for some long-range connections
- Enables efficient inference on long sequences

**Other techniques:**
- Paged attention (vLLM): Efficient KV cache management
- Token merging: Reduce sequence length dynamically
- Position interpolation: Adjust RoPE frequency for longer contexts

### Practical Context Lengths (2024-2025)

- Standard: 4K-8K tokens (most models)
- Extended: 16K-32K (many open models)
- Long: 64K-128K (Falcon, Yi, specialized models)
- Very long: 200K+ (experimental, specialized architectures)

---

## Modern Architectures

### State Space Models (SSMs) / Mamba (2023)

**Paper**: "Mamba: Linear-Time Sequence Modeling with Selective State Spaces"
**Authors**: Gu & Dao

**Motivation**: Transformers have O(n²) complexity; can we achieve O(n) efficiency while maintaining performance?

**Core innovation**: Selective SSM with input-dependent parameters

**Architecture:**
- Inspired by classical control theory and signal processing
- Selective SSMs allow parameters to vary based on input
- End-to-end architecture without attention or MLM blocks
- Simplified compared to transformers

**Key characteristics:**
- Linear time complexity: O(n) vs O(n²) for transformers
- Linear space complexity: no KV cache needed
- Fast inference: 5-6x higher throughput than transformers
- Excellent long sequence handling: processes million-token sequences
- Competitive language modeling performance

**Performance (2024):**
- Comparable to transformers on standard benchmarks
- Superior on very long sequences
- Emerging applications in vision, audio, genomics

**Mamba-2 (2024)**:
- "Transformers are SSMs: Generalized Models and Efficient Algorithms Through Structured State Space Duality"
- Theoretical connections between SSMs and transformers via semiseparable matrices
- 2-8x faster than Mamba-1
- Maintains transformer-competitive performance

### RWKV (Receptance Weighted Key Value) (2023)

**Paper**: "RWKV: Reinventing RNNs for the Transformer Era"
**Authors**: Peng et al.

**Goal**: Combine transformer training parallelizability with RNN inference efficiency

**Key characteristics:**
- Attention-free architecture (no softmax attention)
- Can be formulated as either transformer (parallel training) or RNN (sequential inference)
- O(n) time complexity, constant memory (no KV cache)
- 100% RNN architecture (not SSM-based)

**Advantages:**
- No attention quadratic bottleneck
- Constant-space inference (no cache growth)
- Infinite effective context length (for RNN mode)
- Fast inference: O(n) for both training and inference

**Limitations:**
- Newer architecture with less evaluation
- Language modeling performance: competitive but not always superior
- Smaller ecosystem compared to transformers

**Variants**:
- RWKV-7 "Goose": Latest version with improved performance
- Suitable for LLM and multimodal applications

### RetNet and Linear Attention (2023)

**Paper**: "Retentive Networks: A Vision for Transformers for Language Models"
**Authors**: Peng et al.

**Alternative**: Retention mechanism instead of attention

**Characteristics:**
- Recurrent structure for memory retention
- Extended long-range dependency modeling
- More efficient than attention during computation
- Reduces computational and memory consumption

**Key mechanism**: Parallel training + recurrent inference (similar to RWKV)

**Comparison**: Retention is more efficient during calculation than attention mechanism

### Emerging Attention Alternatives (2024-2025)

- GLA (Gated Linear Attention)
- HGRN / HGRN2 (Hierarchical Gated Recurrent Networks)
- Various linear attention kernels

**Trend**: Shift from O(n²) attention to subquadratic mechanisms (O(n), O(n log n))

---

## Multimodal LLMs

### Vision-Language Models (VLMs)

VLMs extend LLMs to jointly interpret and generate information from both images and text. Multimodal learning combines multiple input modalities (vision + language, audio + language, etc.).

**Common architecture pattern:**
1. Vision encoder (ViT or similar) → image embeddings
2. Projection/alignment layer → map image embeddings to language model space
3. Language model (LLM) → process image embeddings + text tokens together
4. Generate text based on both modalities

### GPT-4V (2023)

**Capability**: Vision capabilities added to GPT-4

**Features:**
- Upload photographs, diagrams, charts
- Answer questions about image content
- Read text in images
- Analyze visual relationships
- Commercial application

**Architecture**: Proprietary, built on GPT-4 foundation with additional vision training

### LLaVA (Large Language-and-Vision Assistant)

**Paper**: "Visual Instruction Tuning"

**Architecture:**
- CLIP vision encoder → image embeddings
- LLaMA language model → text understanding
- Simple projection/alignment layer → connect modalities
- End-to-end trainable

**Key innovation**: Bridges CLIP and LLaMA with lightweight projection

**Approach:**
- Leverage pre-trained, frozen vision and language models
- Train alignment layer on vision-language instruction data
- Efficient: minimal new parameters
- Open-source alternatives available

**Performance**: Competitive with GPT-4V on many visual understanding tasks, with much smaller model size

### Flamingo (DeepMind, 2022)

**Approach:**
- Separate vision and language models (pre-trained, frozen)
- Interleaves visual tokens with text
- Gated cross-attention: language model learns to attend to image embeddings
- In-context learning: show example image-text pairs, model learns pattern

**Capability**: Visual question answering, image captioning, referring expression understanding

### General Architecture Pattern

Most merged VLMs follow:
1. Separate ViT image encoder
2. Off-the-shelf LLM for text
3. Lightweight projection layer (trained)
4. Joint input format: image tokens + text tokens

**Advantage**: Leverage pre-trained models, minimal training needed

**Example**: LLaVA = CLIP (frozen) + projection layer (trained) + LLaMA (frozen or fine-tuned)

---

## Key References & Timeline

### Foundational Papers

| Year | Paper | Authors | Contribution |
|------|-------|---------|--------------|
| 2014 | Attention Mechanism | Bahdanau et al. | First modern attention for seq2seq |
| 2017 | Attention Is All You Need | Vaswani et al. | Transformer architecture |
| 2018 | BERT | Devlin et al. | Encoder-only, bidirectional MLM |
| 2018 | GPT | Radford et al. | Decoder-only autoregressive |
| 2019 | GPT-2 | Radford et al. | 1.5B parameters, zero-shot capability |
| 2019 | RoBERTa | Liu et al. | Improved BERT training |
| 2019 | ALBERT | Lan et al. | Parameter-efficient BERT variant |
| 2019 | T5 | Raffel et al. | Text-to-text encoder-decoder |
| 2019 | BART | Lewis et al. | BERT + GPT hybrid |
| 2020 | GPT-3 | Brown et al. | 175B parameters, few-shot learning |
| 2020 | Scaling Laws for Neural LM | Kaplan et al. | Power-law scaling relationships |
| 2021 | RoPE | Su et al. | Rotary position embeddings |
| 2022 | Chinchilla | Hoffmann et al. | Compute-optimal scaling laws |
| 2022 | Flash Attention | Dao et al. | Memory-efficient attention |
| 2022 | Flamingo | Alayrac et al. | Vision-language model |
| 2023 | LLaMA | Touvron et al. | Competitive efficiency at scale |
| 2023 | Mistral 7B | Jiang et al. | GQA, sliding window attention |
| 2023 | Falcon | Almazrouei et al. | MQA, Flash Attention |
| 2023 | DPO | Rafailov et al. | Simpler alignment than RLHF |
| 2023 | Mamba | Gu & Dao | Linear-time SSM |
| 2023 | RWKV-5 | Peng et al. | Attention-free RNN-transformer hybrid |
| 2023 | LLaVA | Liu et al. | Vision-language with lightweight projection |
| 2024 | Mamba-2 | Gu et al. | SSM-Transformer duality |
| 2024 | GPT-4 | OpenAI | Multimodal capabilities |
| 2024 | LLaMA 3 | Meta | Improved instruction following |

### Key Researchers & Organizations

**Organizations:**
- OpenAI: GPT series, scaling insights
- DeepMind: Chinchilla, Flamingo, Gopher
- Meta: LLaMA, FAIR research
- Google Brain / Google Research: Transformer, BERT, T5, PaLM
- Mistral AI: Mistral models, MoE insights
- Anthropic: Claude models, Constitutional AI
- EleutherAI: RWKV, open model research

**Key Researchers:**
- Ashish Vaswani (Transformer architecture)
- Jacob Devlin (BERT)
- Alec Radford (GPT series)
- Colin Raffel (T5)
- Jared Kaplan (Scaling laws)
- Jordan Hoffmann (Chinchilla laws)
- Tri Dao (Flash Attention)
- Albert Gu (Mamba)
- Peng et al. (RWKV)

---

## Current Trends & Frontier (2024-2025)

### Model Sizes Shifting

- **Large**: 70B-180B (Llama 2 70B, Falcon 180B, GPT-4)
- **Medium**: 30B-50B (competitive with 70B+ from scaling laws improvements)
- **Small**: 7B-13B (sufficient for many applications, with improvements)
- **Edge**: <1B-7B (distilled, quantized for mobile/embedded)

### Training Data Insights

- Quality over quantity: Better curation > sheer volume
- Scaling plateaus exist (diminishing returns on data)
- Synthetic data becoming important (self-play, model-generated)
- Specialized domain data valuable for specialized models

### Efficiency Focus

- **Quantization**: 4-bit, 8-bit models reducing memory 4-8x
- **Distillation**: Smaller models trained on large model outputs
- **LoRA/QLoRA**: Parameter-efficient fine-tuning
- **Flash Attention 2**: Standard in new implementations

### Context Length Wars

- Extension from 4K to 32K+ tokens becoming standard
- "Lost in the middle" problem: models struggle with information in middle of long context
- Evaluation challenges: benchmarks catch up slowly

### Alignment Methods Evolution

- RLHF → DPO (simpler, faster)
- Emerging: Self-Play Fine-Tuning (SPIN)
- Constitutional AI: Rule-based alignment
- Safety: Discovering misalignment from fine-tuning

### Architecture Exploration

- Beyond attention: SSMs (Mamba), linear attention (RWKV), hybrid approaches
- Sparse models: MoE scaling efficiently
- Hybrid: Combining SSM + attention layers
- Open question: Is attention necessary? What's optimal?

### Multimodal Convergence

- Vision-language models becoming standard
- Audio-language integration advancing
- 3D/mesh + language models emerging
- Foundation models handling multiple modalities

---

## Summary

Large Language Models represent a major paradigm shift in AI, moving from task-specific systems to unified architectures that achieve strong performance across diverse applications. The transformer architecture remains dominant (2024), though alternatives (SSMs, linear attention) show promise for handling longer contexts efficiently.

The field is characterized by rapid iteration on:
1. **Architecture innovations** (attention variants, MoE, SSMs)
2. **Scaling laws** (optimal training allocation)
3. **Efficiency improvements** (quantization, Flash Attention, sparse routing)
4. **Alignment methods** (RLHF → DPO)
5. **Multimodal capabilities** (vision-language, audio-language)

The future likely involves:
- Smaller models with better quality (Chinchilla's impact)
- More efficient architectures for inference
- Better long-context handling
- Improved alignment and safety
- Specialized models for specific domains/modalities
- Hybrid architectures combining proven techniques

---

**Last Updated**: 2026-04-05
**Source**: Web research across academic papers, technical blogs, and implementation guides
