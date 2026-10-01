# REVERSE 

Generate, but Verify: Reducing Hallucination in Vision-Language Models with Retrospective Resampling ([NeurIPS 2025](https://arxiv.org/abs/2504.13169))

---

### Target 

- VLM(Visual-Langauge Model)'s hallucination problem: the tendency to describe objects that aren't actually present in the scene. 

### Background 

- Existing VLM's hallucination mitigation solutions follow two paradigms: generation adjustment or post-hoc verification. 
- However, generation adjustment methods lack mechanisms to correct errors once generated, while post-hoc verification methods often require complex multi-model pipelines and tend to reject or rewrite outputs rather than iteratively refine them. 
- There is a need for a unified franework that can detect and correct hallucinations during generation without relying on external verifier models. 

### Idea & Method 

- To mitigate VLM's hallucination problem, **REVERSE** integrates hallucination-aware training with on-the-fly self-verification. 

- The novel approach consists of two-fold:

    1. **Hallucination-aware training** with specially curated dataset 
        - Especially hallucination-verification dataset, which includes three special tokens, `<SPAN>`, `</CN>`, `</UN>`. (1.3M VLM instruction-tuning dataset)
        - In each response, all positive phrases are enclosed with `<SPAN>` and `</CN>` while the negative phrases are enclosed with `</UN>`. 
        - The prediction loss it set to zero for tokens within `<SPAN> ... </UN>` so that the model does not learn to generate the hallucinated content itself, while still able to learn identifying such pharases with special tokens. 
    
    2. **Retrospective resampling** during model inference time 
        - The model initiates self-correction process whenever the probabilty of `</UN>` exceeds threshold. 
        - In the process, **where to backtrack and how to regenerate content** are determined. 
        - Hierarchical fallback strategy for backtracking: (i) go to the most recent `</CN>` (ii) if the issue persists after K local correction attempts, go to the last sentence boundary (iii) if fails after N total attempts, output is finalized without correction. 
        - For regeneration: (i) **Rejections sampling** refines uncertain phrases by resampling multiple times at an increased temperature. (ii) In addition to rejection sampling, **Query rewriting** can provide stronger signals by modifying prompt (with hint of potential incorrect phrases) to encourage better factual grounding. 

### Results

- REVERSE can generate the correct caption without reducing caption length too much: achieves the best results on LLaVA-series and Qwen2.5-VL, reducing the CHAIRi value by up to 12% on CHAIR-MSCOCO and AMBER-G compared to the best existing methods.

- REVERSE can effectively recognize false-premises or insufficient-context in questions (often producing empty responses when it identifies them as unanswerable): improves accuracy by up to 10% on MMHal-Bench and 34% on HaloQuest compared with the SOTA models. However, its performance on visually challenging questions decreases due to its more cautious approach. 

### Limitations 

- The dataset does not contain sufficient edge cases, such as insufficient-context and false-premise questions, and contains noisy labels. 
- Data augmentation with GPT-4o-mini may introduce biases or limited coverage, which may affect the trained model.
- Source datasets such as MS-COCO may contain well-known biases related to gender, race, and geography.
- REVERSE does not improve performance on discriminative VQA tasks, as backtracking provides limited benefits for further reasoning.
- REVERSE reduces hallucination at the cost of generating more tokens, resulting in additional inference overhead.

---

### My Questions 

- For discriminative VQA tasks, such as multiple-choice questions, how can REVERSE be modified or adapted to improve performance? (Mentioned as a future research direction in the paper)
