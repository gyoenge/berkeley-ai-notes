# ECHO 

Constantly improving image models need constantly improving benchmarks ([ICLR 2025](https://arxiv.org/abs/2510.15021))

---

### Target 

- Image Generation Model Benchmark Problem: traditional benchmarks are often too simple, outdated, and not collected in the wild. 

### Background 

- Existing image generation benchmarks often lag behind and fail to capture emerging use cases, leaving a gap between community perceptions of progress and formal evaluation. (e.g., the emergence of novel tasks such as Ghiblification)

- There is a need for framework for constructing benchmarks directly from real-world evidence of model use. 

- Unanticipated capabilities are usually discussed on social media, where users document their interactions with new models and qualitatively discuss their performance. 

### Idea & Method 

- **ECHO**(Extracting Community Hatched Observations) uses social media posts that showcase novel prompts and qualitative user judgements. 

    1. Collect large volume of relevent posts. 

        - Starts with broad queries followed by relevance filtering, since basic querying presents a volume-relevance tradeoff. 

        - A two-stage pipeline collects a large pool of posts using time-specific keywords and then uses an LLM to filter them based on a 5-point relevance scale. 

    2. Reconstruct context with post trees. 

        - Utilize the full post tree, as context can be spread across posts. 

        - Reconstructs full reply trees and uses an LLM to extract self-contained samples containing prompts, community feedback, and quality labels.


    3. Process multimodal data in non-standard formats, using VLM.  

        - Classify Input–Output Images: Identify which images are inputs and which are generated outputs. 

        - Fill in the Blank: Reconstruct missing prompt content using the provided images. 

        - Parse Conversation Screenshots: Extract prompts, input images, and output images from screenshots of model interactions. 

    4. Finalize samples. 

        - format: `<input text, input image(s)*, output image, community feedback*>`
        - ~30K samples are retained for large-scale analysis.
        - The highest-quality samples are manually inspected and selected for benchmarking:
            - 710 image-to-image samples
            - 848 text-to-image samples 

- This re-usable framework can be used to automatically build next-generation benchmarks. 

- Overall evaluation metric for the benchmark is head-to-head “win rate”, a relative rather than absolute metric.

    - Automatic Evaluation: Three VLM judges (GPT-4o, Gemini 2.0, and Qwen2.5-VL) score model outputs, which are converted into pseudo-pairwise comparisons and aggregated by majority vote. 

    - Human Correlation: VLM-as-a-Judge evaluations show a positive but weak correlation with human ratings, indicating the need for better judge models.

### Results

- **Novel Tasks:** ECHO discovers creative and complex tasks absent from existing benchmarks, such as re-rendering product labels across languages or generating receipts with specified totals.

- **Better Model Differentiation:** ECHO more clearly distinguishes state-of-the-art models from alternatives.

- **Feedback-driven Metrics:** ECHO surfaces community feedback and uses it to inform the design of model-quality metrics, such as:
  - Color shift magnitude
  - Face identity similarity
  - Structure distance
  - Text rendering accuracy

### Limitations 

- Social media data can be noisy, incomplete, or biased toward users who actively share interesting model behaviors.

- The benchmark may be influenced by the capabilities and popularity of the target model used for data collection.

- Automatic evaluation using VLM-as-a-Judge shows only weak correlation with human judgments, suggesting that better evaluation methods are still needed.

---

### My Questions 

- How can we reduce biases introduced by social media data and VLM-based filtering?

- Can community feedback be automatically converted into new quantitative evaluation metrics?

- Can the ECHO framework be extended beyond image generation to evaluate VLM reasoning or hallucination?

