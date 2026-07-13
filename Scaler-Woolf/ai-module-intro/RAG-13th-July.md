# RAG - Retrieval-Augmented Generation
- LLMs are great but they are not trained on your personal data.
- 1st Problem is Hallucination 
- 2nd Problem is every question and response on token has a cost. More input and output will have charges.
# Chunking - 
# These agents only understand numbers
# Embeddings 
- Each of the texts are converted into numbers.
- LLM is not doing the cosine comparison
# notebookLM the tools which uses RAG very well
# Grounded LLM vs Un-grounded LLM


# Quizes
- A professional who regularly uses multiple AI platforms is asked by a colleague: 'Which AI tool is the single best one to use for everything?' What framing most accurately reflects how to choose between AI platforms? - **Ans** - The right question is which tool fits this specific job — not which platform is universally best
- **Hallucination in Raw LLMs** - A data analyst asks a raw LLM about his company's internal refund policy updated last month. The model responds with detailed, confident-sounding rules — none of which appear in any actual company document. What behaviour does this demonstrate? - The AI produced a plausible but fabricated answer due to lack of access to the document
- **Explain RAG Simply** - A junior engineer needs to explain the core idea of Retrieval-Augmented Generation to a non-technical colleague in a single sentence. Which of the following most accurately captures the RAG pattern without technical jargon? - Give the AI the right notes before it answers
- **Fundamental RAG Architecture** - In the most fundamental implementation of RAG, how is the retrieved content structurally combined with the user's question before being sent to the language model? - The retrieved content is placed in front of the user's question as a context block within the same prompt

## Why this works

The retrieval system fetches the most relevant passages from a knowledge base (using embeddings or keyword search). These passages are then concatenated with the user's query into a single prompt. The language model reads both the context and the question together and generates an answer grounded in the retrieved information.

Correct answer

The retrieved documents are appended (or prepended) as context in the prompt together with the user's question before being sent to the language model.