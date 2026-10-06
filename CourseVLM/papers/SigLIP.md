# SigLIP 

Sigmoid Loss for Language Image Pre-Training ([ICCV 2023](https://arxiv.org/pdf/2303.15343))

---

### Target

- Image-text contrastive loss problem: softmax loss depends on global batch information for normalization, limiting training efficiency and scalability.

### Background 

- Contrastive language-image pre-training: CLIP and ALIGN applied softmax contrastive learning to large-scale image-text datasets. 

- Softmax requires subtracting the maximum logit across the batch to prevent numerical instability, which adds an extra pass over the full batch. 

### Idea & Method 

- Sigmoid loss for language image pre-training is a simpler alternative that does not require computing global normalization factors. 

$$
-\frac{1}{|\mathcal{B}|}
\sum_{i=1}^{|\mathcal{B}|}
\sum_{j=1}^{|\mathcal{B}|}
\log
\underbrace{
\frac{1}
{1 + e^{z_{ij}(-t\mathbf{x}_i \cdot \mathbf{y}_j + b)}}
}_{\mathcal{L}_{ij}}
$$

- 
    - **$z_{ij}$ (label):** Indicates whether image $i$ and text $j$ are paired, with $+1$ for a positive pair and $-1$ for a negative pair.
    - **$b$ (bias):** A learnable bias term initialized to $-10$ to account for the heavy imbalance between positive and negative pairs. It allows training to start close to the positive/negative class prior.
    - **$t$ (temperature):** A learnable temperature parameter that scales the image-text similarity, initialized as $t'=\log 10$ with $t=e^{t'}$.
    - **$\mathbf{x}_i$ (image embedding):** The normalized embedding representation of image $i$.
    - **$\mathbf{y}_j$ (text embedding):** The normalized embedding representation of text $j$.
    - **$\mathbf{x}_i \cdot \mathbf{y}_j$ (similarity):** The dot-product similarity between the normalized image and text embeddings.

- Contrastive training typically utilizes data parallelism, and sigmoid contrastive loss also provides efficient chunked implementation. 

### Results 

- Sigmoid loss significantly outperforms softmax loss at batch sizes below 16k, while the gap narrows at larger batch sizes.  
- A batch size of 32k is generally sufficient for image-text pre-training, with diminishing returns from further scaling.  
- SigLIP is more resource-efficient than CLIP, allowing larger batch sizes with the same computational resources. 
- Sigmoid-based training is more robust to noisy image-text data than softmax-based training. 
- Severe positive-negative imbalance does not significantly harm sigmoid-based training. 

<!--
1. Sigmoid loss is memory-efficient, fast, and numerically stable, enabling efficient distributed training without global all-gather operations.
2. Sigmoid loss significantly outperforms softmax loss at batch sizes below 16k, while the performance gap narrows as the batch size increases.     
3. A batch size of 32k is generally sufficient for image-text pre-training; further scaling provides diminishing returns and can even hurt performance. This trend also holds for multilingual pre-training. 
4. SigLIP is more resource-efficient than CLIP: with the same four TPU-v4 chips, SigLIP fits a batch size of 4096 compared with 2048 for CLIP. 
5. The extreme positive-negative imbalance is not a major issue, while hard negatives contain most of the useful learning signal, suggesting potential benefits from efficient hard-negative mining. 
6. Initializing the learnable bias to \(b=-10\) consistently improves performance by starting training close to the positive/negative class prior and avoiding large early optimization corrections. 
7. Sigmoid-based training is more robust to noisy image-text data than softmax-based training. 
-->

### Limitations 

- The benefit of sigmoid loss diminishes at large batch sizes, as softmax catches up when the batch size increases.

---

### My Questions 

- Why does sigmoid loss outperform softmax loss particularly at smaller batch sizes? Is this advantage mainly due to the loss formulation itself or differences in optimization dynamics? 
