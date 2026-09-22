# Dream-pattern
IT344; Project exploring dream pattern analysis with prompt refinement and RAG

Dream Pattern Analysis

This is my IT 344 project exploring whether an AI model can analyze dream journal entries and identify patterns, recurring details, and possible connections between dreams. I am testing different prompts, retrieval with RAG, and model refinement to compare how the outputs change.

Data

My dataset is a collection of 15 personal dream journal entries. The raw dataset will stay private and will not be uploaded.

The entries were originally stored as text from my dream journal. I cleaned and prepared them so they could be processed consistently in the notebook. This included separating the dreams into individual entries and removing unnecessary formatting/text.

Current Work

- Loaded and cleaned the dream journal data
- Created embeddings for the dream entries
- Created a retrieval function that compares a test dream to previous dreams using similarity
- Tested retrieval and displayed similarity scores
- Created a current-dream-only baseline
- Created different prompt versions to test prompt refinement
- Started comparing outputs with and without retrieved dream context
- Defined controlled experimental conditions and evaluation framework for testing

The retrieval system converts the dreams into embeddings and compares a new/test dream with the existing journal entries. It returns the most similar previous dreams, which can then be supplied to the language model as additional context for RAG.

Workflow

Dream journal entries
↓
Clean and prepare text
↓
Create embeddings for retrieval
↓
Select test dream
↓
Run test dream through experimental conditions:

Condition A → Base Model + Baseline Prompt
Condition B → Base Model + Refined Prompt
Condition C → Base Model + Refined Prompt + Retrieved Context (RAG)
Condition D → QLoRA-Adapted Model + Refined Prompt
Condition E → QLoRA-Adapted Model + Refined Prompt + Retrieved Context (RAG)

↓
Generate analysis for each condition
↓
Save outputs
↓
Evaluate outputs using the same criteria
↓
Compare controlled condition pairs
↓
Determine the contribution of prompt refinement, retrieval, and model adaptation

Experimental Design

Condition A: Base Model / Baseline Prompt
Condition B: Base Model / Refined Prompt
Condition C: Base Model / Refined Prompt + RAG
QLoRA model-refinement conditions:
Condition D: QLoRa-Adapted Model / Refined Prompt
Condition E: QLoRa-Adapted Model / Refined Prompt + RAG

What Each Condition Tests
Condition A is the baseline. The base model receives a basic prompt and analyzes the test dream, without retrieved context or model adaptation. This establishes the starting performance of the system. 
Condition B keeps the same base model and test dream, but replaces the baseline prompt —> refined structure prompt. Comparing A vs. B isolates the effect of behavioral refinement through prompt engineering. 
Condition C keeps the same base model and refined prompt from B, but adds RAG. The retrieval system searches the previous dream entries using semantic similarity and supplies the most relevant entries as additional context. Comparing B vs. C isolates the effect of retrieval.
Conditions D and E are the planned model-refinement stage. Condition D will use a QLoRA-adapted version of the model with the refined prompt, but without any RAG. Comparing B vs. D will test whether the model adaptation changes performance independently of retrieval. 
Condition E will combine the QLoRA-adapted model, refined prompt, and RAG. This will represent the complete system. Comparing D vs. E will test the additional contribution of retrieval after model adaptation, while comparing C vs. E will test the contribution of model adaptation when retrieval is already present. 

Direct Comparisons
A vs. B: baseline prompt vs. refined prompt; effect of prompt refinement
B vs. C: no RAG vs. RAG: effect of RAG/retrieval
B vs. D: base model vs. QLoRA-adapted model: effect of QLoRA model adaptation without RAG
C vs. E: base model + RAG vs. adapted model + RAG: effect of QLoRA model adaption with RAG
D vs. E: adapted model w/o RAG vs. adapted model with RAG: effect of RAG after QLoRA adaptation
For each direct comparison, the other major experimental settings will be kept constant. This includes using the same test dream, generation settings, and model configuration unless the model itself is the variable being tested. 

Evaluation
The experimental conditions will be evaluated using the same criteria so that changes in output can be consistent during comparison. I will evaluate whether each output:
~ preserves specific details from the test dream
~ identifies recurring locations, emotions, people, objects, or events
~ identifies connections between the test dream and previous journal entries
~ uses retrieved dream context when RAG is enabled
~ distinguishes information supported by the dream entries from possible interpretations
~ avoids introducing unsupported details
~ follows the requested analysis structure
Each output will be evaluated using a consistent scoring rubric across these criteria. The same rubric will be applied to every experimental condition and test dream so that the results can be compared across conditions. I will record the individual criterion scores and overall results for each condition rather than selecting examples based only on qualitative impressions.

Scoring Method

Each evaluation criterion will be scored on a 0-2 scale:
0 = Not demonstrated / the output does not meet the criterion.
1 = Partially demonstrated / the output meets the criterion to some extent, but some relevant information is missing, incomplete or inconsistent.
2 = Fully demonstrated / the output consistently and clearly meets the criterion without relevant information being missing or unsupported.
Criteria not applicable to a condition will be marked as N/A. Overall scores will be calculated using only the applicable criteria, and reported as a percentage of the total points possible for the criteria that apply to that condition. 
I will compare across the conditions to determine what system component contributes to the final analysis.

Test Procedure

The same test dreams will be run through the applicable experimental conditions. For each direct comparison, I will keep the test input and generation settings constant and change only the component being tested.

Multiple test dreams will be used so that the results are not based on a single example. The outputs from each condition will be saved and compared using the same evaluation criteria. 

For RAG conditions, the retrieval system will use semantic similarity to select relevant previous entries as context. For non-RAG conditions, the model will receive only the current test entry. This allows retrieval to be tested separately from prompt refinement and model adaptation. 

Goal

The goal is to determine how prompt refinement, retrieval/context, and model refinement affect the model’s ability to identify meaningful patterns across dream journal entries while preserving specific details from the original dreams. The controlled comparisons and scoring rubric will allow me to measure the contribution of prompt engineering, retrieval, model adaptation, and their combination. 
