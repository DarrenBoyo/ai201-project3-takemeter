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

## Evaluation Report

### Model Comparison

| Metric          | Baseline Model | Fine-Tuned Model |
| --------------- | -------------: | ---------------: |
| Accuracy        |          0.833 |            0.700 |
| Macro Precision |           0.84 |             0.71 |
| Macro Recall    |           0.83 |             0.70 |
| Macro F1        |           0.83 |             0.68 |

The baseline model outperformed the fine-tuned model on every evaluation metric. While fine-tuning is often expected to improve performance, it can reduce generalization when training data is limited or contains ambiguous examples. In this project, the fine-tuned model appears to have learned narrower decision boundaries that performed worse on the held-out test set. The final fine-tuned accuracy was 70.0%, compared to 83.3% for the baseline model.

---

### Fine-Tuned Model Per-Class Metrics

| Label       | Precision | Recall |   F1 |
| ----------- | --------: | -----: | ---: |
| analysis    |      0.70 |   0.88 | 0.78 |
| hot_take    |      0.75 |   0.43 | 0.55 |
| reaction    |      0.71 |   0.62 | 0.67 |
| speculation |      0.67 |   0.86 | 0.75 |

The strongest category for the fine-tuned model was **analysis**, while **hot_take** was the weakest. The model frequently struggled to identify hot takes correctly and often reassigned them to analysis or speculation.

---

### Fine-Tuned Confusion Matrix

| True \ Predicted | analysis | hot_take | reaction | speculation |
| ---------------- | -------: | -------: | -------: | ----------: |
| analysis         |        7 |        0 |        1 |           0 |
| hot_take         |        2 |        3 |        0 |           2 |
| reaction         |        1 |        1 |        5 |           1 |
| speculation      |        0 |        0 |        1 |           6 |

The confusion matrix reveals a clear directional pattern. The largest source of error comes from **hot_take** posts being misclassified as either **analysis** or **speculation**. This suggests the model did not learn a reliable boundary around the hot_take category.

---

## Error Analysis

### Error 1: Hot Take → Analysis

**Post:**

> Erling Haaland isn't as complete as Harry Kane because he averages fewer touches and progressive passes.

**True Label:** hot_take
**Predicted Label:** analysis

This mistake highlights the most common confusion in the dataset. The post includes statistics, which resemble evidence-based reasoning. However, according to the labeling rules, the statistics are being used to support a strong opinion rather than build a structured football argument. The model appears to have learned that the presence of statistics is a strong signal for analysis, even when the overall discourse style is opinionated.

This suggests a training-data problem rather than a purely modeling problem. More examples showing the difference between evidence-supported opinions and genuine analysis would likely improve performance.

---

### Error 2: Hot Take → Speculation

**Post:**

> Neymar will never win the Ballon d'Or because he's too focused on partying.

**True Label:** hot_take
**Predicted Label:** speculation

The model likely focused on the phrase "will never," which references a future outcome. However, the primary purpose of the post is not prediction. Instead, it expresses an opinion about Neymar's attitude and career.

This reveals that the model sometimes treats future-oriented language as speculation regardless of whether the post is actually making a reasoned prediction. Additional training examples distinguishing opinions from forecasts would help reduce this confusion.

---

### Error 3: Analysis → Reaction

**Post:**

> Transfer rumours like Julián Álvarez to Atlético Madrid open discussions about tactical fit and team needs.

**True Label:** analysis
**Predicted Label:** reaction

The model appears to associate transfer-related language with news and event reporting. Although the post discusses tactical implications, the presence of a transfer rumor likely triggered a reaction-style prediction.

This error suggests that the model relies heavily on topic keywords rather than discourse structure. More analysis examples connected to current events and transfer discussions would likely help the model learn this distinction.

---

## Sample Classifications

| Example Post                                                                                                  | Predicted Label | Confidence |
| ------------------------------------------------------------------------------------------------------------- | --------------- | ---------: |
| "Jude Bellingham will win multiple Ballon d'Or awards."                                                       | reaction        |       0.29 |
| "Messi's goal shouldn't have stood because of a foul in the build-up."                                        | speculation     |       0.26 |
| "Video assistant referees have made the game worse; we should return to human error."                         | analysis        |       0.27 |
| "Transfer rumours like Julián Álvarez to Atlético Madrid open discussions about tactical fit and team needs." | reaction        |       0.28 |
| "Erling Haaland isn't as complete as Harry Kane because he averages fewer touches and progressive passes."    | analysis        |       0.27 |

The confidence scores are relatively low across many mistakes, suggesting the model was uncertain when making these predictions. This is consistent with the overlapping nature of several label boundaries.

---

## Reflection: What the Model Learned vs. What I Intended

My goal was to classify posts based on **discourse style** rather than football topic. The intended distinction was whether a post represented analysis, a hot take, a reaction, or speculation.

The model partially learned these distinctions, but it also learned shortcuts. For example, it frequently associated statistics with analysis, future-tense language with speculation, and transfer-related language with reaction. These topic-level cues sometimes overrode the actual discourse structure.

The biggest mismatch between my intended labels and the model's behavior involved the hot_take category. I intended hot takes to be defined by unsupported opinion, but the model often interpreted any opinion containing evidence as analysis. This suggests that the boundary between analysis and hot_take remains difficult both for humans and for the classifier.

---

## Spec Reflection

The project specification helped guide implementation by requiring explicit label definitions, edge-case handling rules, and an evaluation plan before collecting data. Defining difficult cases early made the annotation process more consistent and reduced uncertainty during labeling.

One way my implementation diverged from the original plan was in the performance outcome. I initially expected the fine-tuned model to outperform the baseline model. Instead, the baseline achieved 83.3% accuracy while the fine-tuned model achieved 70.0%. This suggests that the dataset may have been too small or too ambiguous for effective fine-tuning, and that a stronger pretrained model generalized better than a specialized model trained on limited examples.
