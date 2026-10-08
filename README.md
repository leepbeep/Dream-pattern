Context-Aware Dream Interpretation System

IT 344

Project Overview

This project develops a dream journal analysis system using Phi-3 Mini, semantic retrieval, prompt engineering, and QLoRA fine-tuning.

The system processes dream narratives to identify people, locations, environments, events, emotions, and distinctive details. It also supports semantic searches across journal entries to identify related dreams and examine recurring elements.

The project combines structured information extraction with broader dream analysis, including the examination of patterns, connections, and different interpretive frameworks.

The current notebook implements dream embedding and retrieval, three dream-analysis prompt configurations, structured extraction, and a QLoRA experiment comparing an adapted model against the original Phi-3 Mini.

Research Questions

1. Does QLoRA fine-tuning improve Phi-3 Mini’s accuracy and consistency when extracting structured information from dream narratives?
2. How does prompt refinement affect the detail, organization, and analytical quality of model-generated dream interpretations?
3. How can retrieval-augmented generation (RAG) incorporate related dream entries when analyzing recurring people, places, events, and themes?

Technical Stack

Component	Technology
Programming language	Python
Base model	microsoft/Phi-3-mini-4k-instruct
Model framework	Hugging Face Transformers
Quantization	BitsAndBytes, 4-bit NF4
Fine-tuning	QLoRA, PEFT
Training	TRL SFTTrainer
Dataset processing	Hugging Face Datasets
Sentence embeddings	all-MiniLM-L6-v2
Vector search	FAISS
Computation	PyTorch, NumPy
Evaluation	Pandas, human scoring
Environment	Google Colab, NVIDIA T4 GPU

Dataset and Preprocessing

The dataset contains 24 dream journal entries.

Dream narratives are loaded from UTF-8 text files, sorted numerically, and stored as Python dictionaries containing a dream identifier and its associated text.

A fixed random seed of 42 is applied to Python, NumPy, PyTorch, and Transformers.

The notebook uses the dream entries for semantic retrieval, prompt experiments, and supervised fine-tuning.

Semantic Retrieval

Dream narratives are encoded using the all-MiniLM-L6-v2 sentence-transformer model, producing 384-dimensional embeddings.

The embeddings are L2-normalized and converted to float32 before being added to a FAISS IndexFlatIP index.

With normalized embeddings, inner-product similarity corresponds to cosine similarity.

The retrieve_similar_dreams() function performs the following operations:

1. Encodes and normalizes the input query.
2. Searches the FAISS index.
3. Retrieves the top-k matching entries.
4. Returns each dream’s identifier, similarity score, and narrative.

The default retrieval parameter is top_k=3.

This supports semantic matching between dream narratives and allows related entries to be identified without relying exclusively on exact keyword matches.

Prompt Engineering

Three analysis prompts are implemented using the unchanged Phi-3 Mini model.

Condition	Description
Baseline	Dream summary, detailed element identification, and analysis through multiple interpretive frameworks
Prompt V1	More explicit instructions for examining individual frameworks, matching them to dream details, and organizing the analysis
Prompt V2	Structured analysis covering dream reconstruction, prominent elements, smaller details, interpretive frameworks, alternative explanations, and synthesis

The prompts differ in instruction specificity, response organization, and the level of detail requested.

All three use the same model and deterministic generation settings:

max_new_tokens=700
do_sample=False
repetition_penalty=1.1

The preliminary prompt comparison examines detail preservation, response structure, and how the model connects its analysis to information in the input narrative.

These prompt conditions operate on the current dream without adding retrieved journal entries to the generation context.

Structured Information Extraction

The notebook includes a separate extraction function, extract_dream_record(), which processes dream narratives using eight headings:

* PEOPLE
* NAMED PLACES
* SETTING TYPES
* OBJECTS AND DETAILS
* EVENTS
* EMOTIONS AND SENSATIONS
* SEQUENCE
* UNCERTAINTIES

This function distinguishes explicitly identified places from general settings, preserves event sequences, and records uncertain identifications separately.

It uses deterministic generation with a 400-token output limit, a repetition penalty of 1.15, and a three-token n-gram repetition constraint.

The QLoRA experiment uses a separate, standardized six-field extraction schema.

Experimental Arm 1: QLoRA for Structured Dream Information Extraction

Objective

Evaluate whether fine-tuning Phi-3 Mini on manually labeled dream records improves structured extraction accuracy, completeness, and category consistency compared with the unchanged base model.

Hypothesis

QLoRA fine-tuning will improve extraction quality by preserving more relevant details, reducing unsupported additions, and assigning information more consistently to the correct categories.

Experimental Design

Two conditions are compared:

Condition	Configuration
A — Base	Phi-3 Mini with the LoRA adapter disabled
B — QLoRA	Phi-3 Mini with the trained LoRA adapter enabled

Both conditions are evaluated using identical held-out dream entries, extraction instructions, output fields, and decoding settings.

Training Dataset

The 24 dream entries are divided into:

Split	Entries
Training	18
Held-out testing	6
Total	24

Training examples pair original dream narratives with manually prepared reference labels.

Dream identifiers are normalized before dataset construction. Assertions verify the expected split sizes and confirm that the training and held-out sets do not overlap.

Extraction Schema

Arm 1 uses six output fields:

Field	Description
PEOPLE	Individuals appearing in the dream
PLACES	Named or identifiable locations
SETTINGS	Environments and surrounding conditions
EVENTS	Actions, interactions, and occurrences
EMOTIONS	Emotions described in the narrative
OBJECTS_DETAILS	Objects, physical features, and smaller descriptive details

Reference labels are formatted as field-based text records, with none used when a category contains no supported information.

Training Data Formatting

Each training example is constructed using Phi-3’s chat template.

The formatted sequence includes a system instruction defining the extraction rules, a user message containing the dream narrative, and an assistant response containing the manually labeled extraction record.

The examples are converted into a Hugging Face Dataset and processed for supervised fine-tuning.

Quantization and LoRA Configuration

Phi-3 Mini is loaded with 4-bit NF4 quantization, double quantization, and FP16 computation.

The model is prepared using prepare_model_for_kbit_training(), and a LoRA adapter is attached using PEFT.

Parameter	Value
Quantization	4-bit NF4
Double quantization	Enabled
Compute dtype	FP16
LoRA rank	8
LoRA alpha	16
LoRA dropout	0.05
Bias	none
Task type	CAUSAL_LM
Target modules	qkv_proj, o_proj, gate_up_proj, down_proj

The adapter targets attention and feed-forward projection modules. The base model remains quantized while trainable LoRA parameters are updated.

Training Configuration

Supervised fine-tuning is performed using TRL’s SFTTrainer.

Hyperparameter	Value
Epochs	3
Per-device batch size	1
Gradient accumulation steps	4
Learning rate	2e-4
Optimizer	paged_adamw_8bit
Maximum sequence length	1024
Gradient checkpointing	Enabled
FP16 training	Enabled
Random seed	42
Checkpoint saving	Each epoch

Trainable LoRA parameters are cast to FP32 before training. The trained adapter and tokenizer are saved using save_pretrained().

Arm 1 Evaluation

Held-Out Inference

The evaluation set contains six dreams:

dream2, dream8, dream19, dream20, dream22, and dream23.

Both model conditions receive the same system prompt and dream narrative.

Inference uses:

max_new_tokens=350
do_sample=False
pad_token_id=tokenizer.eos_token_id

The base condition is generated within disable_adapter(), allowing comparison against the QLoRA-adapted condition using the same underlying model.

Generated outputs are stored by dream identifier and assembled into a Pandas DataFrame for comparison.

Human Evaluation

Model responses are evaluated against the original dream narratives using four criteria.

Criterion	Description
Accuracy	Correctness of extracted information
Completeness	Preservation of relevant dream details
Unsupported detail avoidance	Absence of additions not supported by the narrative
Structure	Correct assignment of information to extraction categories

Each criterion is scored from 0 to 3:

* 0: Poor
* 1: Weak
* 2: Mostly correct
* 3: Strong

Each dream has a maximum score of 12 points, giving a maximum of 72 points per model.

Criterion-level scores are stored in a Pandas DataFrame. The notebook calculates per-dream totals, differences between conditions, model-level totals, mean scores, and percentages.

Results

Per-Dream Scores

Dream ID	Base	QLoRA
dream2	9	9
dream8	9	10
dream19	10	8
dream20	9	8
dream22	9	5
dream23	10	6
Total	56	46

Overall Performance

Condition	Total score	Percentage
Base Phi-3 Mini	56/72	77.8%
QLoRA Phi-3 Mini	46/72	63.9%

The unchanged base model achieved a higher total score than the QLoRA-adapted model.

The base model outperformed QLoRA by 10 points, equivalent to approximately 13.9 percentage points.

QLoRA produced additional details in some responses, but several outputs also contained unsupported information or incorrect category assignments.

The results did not support the hypothesis that fine-tuning would improve extraction performance under this experimental configuration.

The comparison demonstrates that increased output detail does not necessarily correspond to better structured extraction, particularly when fine-tuning on a small dataset.

Experimental Considerations

Arm 1 uses 18 training examples and six held-out test examples. The limited dataset size affects how broadly the results can be generalized.

Evaluation is based on manual judgments using a consistent four-criterion rubric. The reported scores describe performance on the selected held-out dreams under the specified model and generation settings.
