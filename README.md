# Dream-pattern
IT344; Project exploring dream pattern analysis with prompt refinement and RAG

Dream Pattern Analysis

This is my IT 344 project exploring whether an AI model can analyze dream journal entries and identify patterns, recurring details, and possible connections between dreams. I am testing different prompts, retrieval with RAG, and eventually model refinement to compare how the outputs change.

Data

My dataset is a collection of 15 personal dream journal entries. The raw dataset will stay private and will not be uploaded.

The entries were originally stored as text from my dream journal. I cleaned and prepared them so they could be processed consistently in the notebook. This included separating the dreams into individual entries and removing unnecessary formatting/text.

Current Work

So far, I have:

- Loaded and cleaned the dream journal data
- Created embeddings for the dream entries
- Created a retrieval function that compares a test dream to previous dreams using similarity
- Tested retrieval and displayed similarity scores
- Created a current-dream-only baseline
- Created different prompt versions to test prompt refinement
- Started comparing outputs with and without retrieved dream context

The retrieval system converts the dreams into embeddings and compares a new/test dream with the existing journal entries. It returns the most similar previous dreams, which can then be supplied to the language model as additional context for RAG.

Envisioned Workflow

Dream journal entries
↓
Clean and prepare text
↓
Create embeddings
↓
Enter a test dream
↓
Retrieve similar previous dreams
↓
Add retrieved dreams as context
↓
Run the language model
↓
Generate dream pattern analysis
↓
Compare experimental conditions

Experimental Design

Condition A: Base Model / Baseline Prompt
Condition B: Base Model / Refined Prompt
Condition C: Base Model / Refined Prompt + RAG
—-Planned model-refinement conditions—-
Condition D: QLoRa-Adapted Model / Refined Prompt
Condition E: QLoRa-Adapted Model // Refined Prompt + RAG
## What Each Condition Tests
Condition A is the baseline. The base model receives a basic prompt and analyzes the test dream, without retrieved context or model adaptation. This establishes the starting performance of the system. 
Condition B keeps the same base model and test dream, but replaces the baseline prompt —> refined structure prompt. Comparing A vs. B isolates the effect of behavioral refinement through prompt engineering. 
Condition C keeps the same base model and refined prompt from B, but adds RAG. The retrieval system searches the previous dream entries using semantic similarity and supplies the most relevant entries as additional context. Comparing B vs. C isolates the effect of retrieval.
Conditions D and E are the planned model-refinement stage. Condition D will use a QLoRA-adapted version of the model the refined prompt, but without any RAG. Comparing B vs. D will test whether the model adaptation changes performance independently of retrieval. 
Condition E will combine the QLoRA-adapted model, refined prompt, and RAG. This will represent the complete system. Comparing D vs. E will test the additional contribution of retrieval after model adaptation, while comparing C vs. E will test the contribution of model adaptation when retrieval is already present. 
A vs. B: baseline prompt vs. refined prompt
B vs. C: no RAG vs. RAG
B vs. D: base model vs. QLoRA-adapted model
C vs. E: base model + RAG vs. adapted model + RAG
D vs. E: adapted model w/o RAG vs. adapted model with RAG
For each direct comparison, the other major experimental settings will be kept constant. This includes using the same test dream, generation settings, and model configuration unless the model itself is the variable being tested. 

Goal

The goal is to determine which combination of prompting, retrieval/context, and model refinement is most useful for finding meaningful patterns across dream journal entries while still preserving small details from the original dreams.
