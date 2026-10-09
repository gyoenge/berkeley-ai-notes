# CoCa

Contrastive captioners are image-text foundation models ([2022](https://arxiv.org/pdf/2205.01917))

---

### Target

- Unify the capabilities of single-encoder, dual-encoder, and encoder-decoder paradigms within a single image-text foundation model.

### Background 

- Vision and vision-language foundation models have been explored mainly through three paradigms: single-encoder, dual-encoder, and encoder-decoder models.
- Each paradigm has distinct strengths in visual recognition, cross-modal alignment, and multimodal understanding/generation, respectively.
- However, no single framework effectively unified the capabilities of all three paradigms.

### Idea & Method 

- CoCa(Contrastive Captioner) is a minimalist design to pretrain an image-text encoder-decoder foundation model jointly with contrastive loss and captioning (generative) loss. 
    - Structure: 
        - Image encoder (image input) + Unimodal text decoder (text input) + Multimodal text decoder (text output). 
        - The unimodal decoder omits cross-attention to learn text-only representations, while the multimodal decoder cross-attends to image encoder outputs to learn joint image-text representations.
    - Objective: 
        - Contrastive objective between outputs of the image encoder and unimodal text decoder: for learning global representations. 
        - Captioning objective at the output of the multimodal decoder: for fine-grained region-level features. 

### Results 

- This single pretrained model can outperform many specialized modles using zero-shot transfer or minimal task-specific adaptation: ~~~. 
- Generative loss on image annotation text provides a fine-grained training signal similar to the single-encoder cross-entropy loss approach. 
    - (fine-grained?) 

### Limitations 

- 

--- 

### My Questions 

- 

