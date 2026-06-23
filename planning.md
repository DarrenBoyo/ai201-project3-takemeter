# Planning Document: r/soccer Discourse Classification

## Community Selection

I chose **r/soccer**, one of Reddit's largest football (soccer) discussion communities. The subreddit contains a wide variety of discourse, including tactical analysis, transfer rumors, emotional match reactions, and strong personal opinions about players, managers, and clubs.

This community is a strong fit for a classification task because discussion quality and style vary significantly between posts. Community members regularly distinguish between thoughtful analysis, unsupported opinions, emotional reactions, and speculation about future events. These distinctions are meaningful to users and create clear categories that can be learned by a classifier.

---

## Label Definitions

### 1. Analysis

**Definition:** A post that presents a structured football argument supported by evidence such as statistics, tactical observations, historical comparisons, or detailed reasoning.

**Example Posts:**

1. "Arsenal's defensive improvement comes from Rice covering the left half-space; their expected goals against has dropped significantly since January."
2. "Compared to Klopp's Liverpool, Slot's team presses less aggressively but creates more chances through positional play."

---

### 2. Hot Take

**Definition:** A strong football opinion expressed confidently without substantial supporting evidence or reasoning.

**Example Posts:**

1. "Bellingham is already the best midfielder in the world."
2. "The Premier League is massively overrated compared to La Liga."

---

### 3. Reaction

**Definition:** An immediate emotional response to a match, goal, transfer, referee decision, or breaking news event.

**Example Posts:**

1. "What an unbelievable goal!"
2. "That red card decision is absolutely disgraceful."

---

### 4. Speculation

**Definition:** A post focused on predicting future outcomes or discussing uncertain possibilities rather than evaluating current evidence.

**Example Posts:**

1. "If Mbappé joins Madrid, they'll dominate Europe for the next five years."
2. "I think Xabi Alonso will eventually replace Guardiola at Manchester City."

---

## Hard Edge Cases

Some posts will sit between **Analysis** and **Hot Take**.

Example:

> "Haaland is not as complete as Kane because he averages fewer touches and progressive passes."

This post contains evidence but also presents a strong opinion. To maintain consistency, I will use the following decision rule:

* Label as **Analysis** if the evidence forms the basis of a structured argument and would still be meaningful if the opinionated wording were removed.
* Label as **Hot Take** if the evidence appears cherry-picked, superficial, or mainly decorative rather than supporting genuine reasoning.

Another common ambiguity occurs between **Analysis** and **Speculation**.

Example:

> "Chelsea's young squad should challenge for the title next season because their underlying numbers are improving."

Although evidence is provided, the primary goal is predicting a future outcome. In such cases, the post will be labeled **Speculation** because future forecasting is the dominant purpose.

---

## Data Collection Plan

I will collect data from public posts and comments in **r/soccer**.

### Sources

* Match threads
* Post-match threads
* Transfer discussion threads
* Tactical analysis posts
* General discussion threads
* News and rumor posts

### Dataset Size

I will collect at least **200 examples**.

Target distribution:

| Label       | Target Examples |
| ----------- | --------------- |
| Analysis    | 50              |
| Hot Take    | 50              |
| Reaction    | 50              |
| Speculation | 50              |

### Handling Class Imbalance

If one label is underrepresented after collecting 200 examples:

1. Search specifically for threads where that discourse type is common.
2. Continue collecting additional examples until each category contains at least 40–50 instances.
3. If necessary, slightly oversample the minority class during training while keeping the evaluation set representative of real-world distributions.

---

## Evaluation Metrics

Accuracy alone is insufficient because a classifier could achieve a high score by favoring the most common class.

I will evaluate the model using:

### Accuracy

Measures overall correctness across all predictions.

### Precision

Measures how often a predicted label is actually correct. High precision is important because users should trust the classifier's outputs.

### Recall

Measures how many examples of a label are successfully identified. This is important because missing large numbers of Analysis or Speculation posts would reduce usefulness.

### F1 Score

Balances precision and recall into a single metric. I will compute both:

* Per-class F1 scores
* Macro-averaged F1 score

Macro F1 is particularly important because it treats all labels equally regardless of frequency.

### Confusion Matrix

A confusion matrix will help identify systematic mistakes, such as confusing Analysis with Hot Take or Analysis with Speculation.

---

## Definition of Success

For this classifier to be genuinely useful, it should consistently separate evidence-based discussion from opinions, emotional reactions, and speculation.

### Minimum Acceptable Performance

* Accuracy ≥ 70%
* Macro F1 ≥ 0.65

### Good Performance

* Accuracy ≥ 80%
* Macro F1 ≥ 0.75

### Deployment-Ready Performance

* Accuracy ≥ 85%
* Macro F1 ≥ 0.80
* No individual class F1 below 0.75

At deployment quality, moderators or community researchers could use the classifier to analyze discussion trends, filter content types, or study how discourse quality changes over time in r/soccer.
