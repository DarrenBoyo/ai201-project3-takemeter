# r/soccer Discourse Classification

## Project Overview

This project builds a text classifier for posts and comments from the Reddit community **r/soccer**. The goal is to automatically classify football discussion into four discourse categories:

* **analysis** – evidence-based football reasoning using tactics, statistics, or detailed explanations.
* **hot_take** – strong opinions with little supporting evidence.
* **reaction** – emotional responses or event-driven commentary.
* **speculation** – predictions, hypotheticals, or discussions of uncertain future outcomes.

The project explores whether a language model can reliably distinguish between different styles of football discussion rather than simply identifying topics.

---

## Dataset

I collected and labeled **200 examples** from public r/soccer content.

Dataset distribution:

| Label       | Examples |
| ----------- | -------: |
| analysis    |       50 |
| hot_take    |       50 |
| reaction    |       50 |
| speculation |       50 |
| **Total**   |  **200** |

Each example contains:

* `text`
* `label`
* `notes`

The notebook automatically split the dataset into:

* Training: 70%
* Validation: 15%
* Test: 15%

---

## Label Definitions

### Analysis

A post that presents a structured football argument supported by evidence such as statistics, tactical observations, historical comparisons, or detailed reasoning.

**Example:**

> Arsenal's defensive improvement comes from Rice covering the left half-space; they've allowed 0.8 xGA per match since January.

### Hot Take

A strong football opinion expressed confidently without substantial supporting evidence or reasoning.

**Example:**

> Bellingham is already the best midfielder in the world.

### Reaction

An immediate emotional response to a match, goal, transfer, referee decision, record, or breaking news event.

**Example:**

> What an unbelievable goal!

### Speculation

A post focused on predicting future outcomes or discussing hypothetical possibilities rather than evaluating current evidence.

**Example:**

> If Mbappé joins Real Madrid, they'll dominate Europe for the next five years.

---

## AI Tool Usage

AI tools were used in three ways:

### Label Stress-Testing

ChatGPT was used to generate ambiguous example posts that sat between categories. This helped refine label boundaries before annotation began.

### Annotation Assistance

ChatGPT was used to suggest labels for some examples. Every suggested label was manually reviewed and corrected when necessary before inclusion in the final dataset.

### Failure Analysis

After evaluation, ChatGPT was used to help identify patterns among incorrect predictions. All observations were verified manually before being included in this report.

---

## Baseline Results

### Overall Performance

| Metric          | Score |
| --------------- | ----: |
| Accuracy        | 0.833 |
| Macro Precision |  0.84 |
| Macro Recall    |  0.83 |
| Macro F1        |  0.83 |

### Per-Class Results

| Label       | Precision | Recall |   F1 |
| ----------- | --------: | -----: | ---: |
| analysis    |      0.86 |   0.75 | 0.80 |
| hot_take    |      0.83 |   0.71 | 0.77 |
| reaction    |      0.80 |   1.00 | 0.89 |
| speculation |      0.86 |   0.86 | 0.86 |

The strongest category was **reaction**, while **hot_take** proved most difficult to classify consistently.

---

## Error Analysis

### Error 1: Hot Take vs Analysis

**Post:**

> Erling Haaland isn't as complete as Harry Kane because he averages fewer touches and progressive passes.

**True:** hot_take
**Predicted:** analysis

The model interpreted the presence of evidence as analytical reasoning. However, the post primarily expresses a strong opinion rather than building a detailed argument.

---

### Error 2: Hot Take vs Speculation

**Post:**

> Neymar will never win the Ballon d'Or because he's too focused on partying.

**True:** hot_take
**Predicted:** speculation

The model likely focused on future-oriented language ("never") and treated the post as a prediction. Under the project taxonomy, it is better understood as an unsupported opinion.

---

### Error 3: Analysis vs Reaction

**Post:**

> Transfer rumours like Julián Álvarez to Atlético Madrid open discussions about tactical fit and team needs.

**True:** analysis
**Predicted:** reaction

The classifier associated transfer-related language with news and reactions instead of recognizing the explanatory football reasoning contained in the post.

---

## Discussion

The classifier performed well overall and exceeded the project's target Macro F1 score of 0.80. Most mistakes occurred near category boundaries rather than between completely unrelated labels.

The largest source of confusion was distinguishing **analysis** from **hot_take** when opinions contained small amounts of supporting evidence. The model also occasionally confused **speculation** with future-oriented opinions and **reaction** with event-related analysis.

These errors suggest that discourse style is often a spectrum rather than a set of perfectly separate categories. Some disagreements may reflect ambiguity in the annotation scheme rather than purely model limitations.

---

## Conclusion

The final classifier achieved:

* **83.3% accuracy**
* **0.83 Macro F1**

These results indicate that the model can reliably distinguish between major discourse styles in r/soccer discussions. While ambiguity remains at category boundaries, the classifier is accurate enough to support community analysis, moderation research, and large-scale studies of football discussion patterns.
