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

I plan to use A/B comparisons so I can change one major part of the system and compare the results.

Base model comparison: Compare two base model configurations on the same dream analysis task.

Behavioral refinement: Compare the model’s behavior using different prompt designs. My current notebook includes a baseline and more structured prompt versions. I also plan to compare analysis without RAG against analysis using retrieved dream context.

Test cases: Use different test dreams to see whether the same differences between the experimental conditions appear across more than one example.

Keeping the test dream and other generation settings the same when comparing prompt versions will help show whether changes in the output are actually coming from the prompt refinement.

Goal

The goal is to determine which combination of prompting, retrieval/context, and model refinement is most useful for finding meaningful patterns across dream journal entries while still preserving small details from the original dreams.
