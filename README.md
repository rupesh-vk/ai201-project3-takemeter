TakeMeter: NBA Discourse Quality Classifier

Project Overview

TakeMeter is a fine-tuned text classifier that evaluates the type of discourse in NBA Reddit comments. The goal is not to decide whether a comment is simply “good” or “bad,” but to classify what kind of take the comment represents.

For this project, I built a classifier for NBA discussion comments using three labels:

* analysis
* hot_take
* reaction

The project focuses on one of the hardest parts of real-world classification: designing labels that are meaningful, mutually exclusive, and usable on messy online text.

⸻

Community Choice

I chose the NBA discussion community, especially public NBA discussion spaces such as r/nba. This community is a strong fit for a discourse-quality classifier because NBA discussion is active, fast-moving, emotional, and opinion-heavy.

In the same thread, users may post detailed basketball reasoning, unsupported player rankings, jokes, memes, or immediate emotional reactions to a game. These distinctions matter in NBA communities because fans often separate “real analysis” from hot takes and from live-game emotional responses.

This makes NBA Reddit discourse a useful setting for TakeMeter because the comments are short, messy, and context-dependent, but the labels still reflect real community norms.

⸻

Label Taxonomy

Analysis

Definition:
An analysis comment makes a basketball argument using specific reasoning, evidence, statistics, tactical observations, historical comparison, or explanation. The key feature is that the comment explains why a claim is true.

Examples:

1. “Jokic creates so many open looks because defenses have to respect both his scoring and passing from the elbow.”
2. “The Celtics switching everything works because they have multiple wings who can guard smaller players without giving up easy mismatches.”

Good tag example:
“Wemby being top 5 already is wild. He has never made the playoffs and never played at a top 5 level for a full season.”

This is Analysis because it supports the ranking argument with playoff experience and season-level performance.

Bad tag example:
“Jalen Brunson is the greatest Knick of all time.”

This should not be Analysis because it makes a strong claim without explaining why.

⸻

Hot Take

Definition:
A hot take comment makes a strong basketball opinion, ranking, prediction, or exaggerated claim with little or no meaningful supporting evidence. These comments mostly assert rather than explain.

Examples:

1. “Anthony Edwards is already better than prime Dwyane Wade.”
2. “The Lakers are cooked. This team is never winning anything again.”

Good tag example:
“CJ McCollum is a bigger threat than Maxey, Embiid, Harden and Mitchell.”

This is a Hot Take because it is a bold player comparison without supporting evidence.

Bad tag example:
“Fox had the ball with an open net. Could have run the clock. Hit the layup. Doesn’t do either. Loses game.”

This should not be Hot Take because it breaks down a specific play and explains the mistake. It should be Analysis.

⸻

Reaction

Definition:
A reaction comment is an immediate emotional response to a game, play, trade, injury, highlight, ranking, or news event. These comments focus on feeling, surprise, excitement, anger, disbelief, humor, or meme-like response rather than argument.

Examples:

1. “WHAT A SHOT. I can’t believe he hit that.”
2. “No way they actually traded him. I’m sick.”

Good tag example:
“WHAT THE FUCK DID I JUST WITNESS.”

This is Reaction because it is an immediate emotional response to a game event.

Bad tag example:
“They could have slowed the game down and used the full clock instead of taking quick threes.”

This should not be Reaction because it explains basketball decision-making. It should be Analysis.

⸻

Data Collection

I collected public comments from r/nba threads. I focused on comments instead of full posts because comments are short, varied, and closer to the type of discourse TakeMeter is meant to classify.

The dataset was collected from multiple thread types to avoid overfitting to a single event or style of discussion. The final dataset included comments from:

* Knicks playoff/championship discussion
* Finals MVP / Jalen Brunson discussion
* NBA Finals comeback discussion
* Top 100 NBA players ranking discussion
* Arena reaction / jumbotron drama discussion

Originally, I collected too many comments from one Knicks championship-related thread. Early model results showed that this created a biased dataset with too many celebration-style comments. I then revised the dataset by collecting from more varied thread types and relabeling comments using stricter taxonomy rules.

⸻

Labeling Process

Each comment was labeled as exactly one of:

* analysis
* hot_take
* reaction

I used the following decision rules:

1. If the comment explains why using evidence, statistics, tactical detail, historical comparison, or specific game context, I labeled it analysis.
2. If the comment mainly asserts a bold opinion, ranking, prediction, or exaggerated claim without meaningful support, I labeled it hot_take.
3. If the comment mainly expresses excitement, anger, disbelief, humor, celebration, or frustration in response to a moment, I labeled it reaction.
4. A statistic alone did not automatically make something Analysis. If the statistic was used as quick ammunition for a bold claim, I labeled it Hot Take.
5. Emotional tone did not automatically make something Reaction. If the comment still identified a basketball action and explained why it mattered, I labeled it Analysis.

I filtered out comments that were too short, too context-dependent, or not meaningful without the surrounding Reddit thread.

Examples removed or avoided:

* “Good.”
* “Cancun.”
* “Galveston.”
* “Is that good?”

These comments may make sense in Reddit context, but they are too ambiguous for a standalone classifier.

⸻

Label Distribution

The final dataset contains 210 labeled examples.

Label	Count
analysis	85
hot_take	68
reaction	57
Total	210

No single label accounts for more than 70% of the dataset.

⸻

Difficult-to-Label Examples

Example 1

Comment:
“LeBron is overrated. He’s only 4-6 in the Finals.”

Possible labels: Analysis or Hot Take
Decision: Hot Take

Reason:
The comment includes a statistic, but the statistic is used as quick ammunition for a bold opinion rather than as part of a deeper argument.

⸻

Example 2

Comment:
“Jokic is the best player in the league and it is not close. Nobody controls an offense like him.”

Possible labels: Analysis or Hot Take
Decision: Hot Take

Reason:
The comment gives a basketball reason, but it does not provide enough specific evidence or explanation to count as Analysis.

⸻

Example 3

Comment:
“That defensive possession was insane. They switched everything and still recovered to the corner.”

Possible labels: Reaction or Analysis
Decision: Analysis

Reason:
Even though the tone is emotional, the comment identifies a specific basketball action and explains what happened tactically.

⸻

Example 4

Comment:
“Brunson had 45 and the rest of the team had 49. Build the statue.”

Possible labels: Analysis, Hot Take, or Reaction
Decision: Analysis

Reason:
The comment includes emotional language, but the main reason the praise works is the specific scoring comparison. I labeled it Analysis because the claim is grounded in concrete game evidence.

⸻

Fine-Tuning Approach

The fine-tuned model used:

* Base model: distilbert-base-uncased
* Task: 3-class sequence classification
* Labels: analysis, hot_take, reaction
* Environment: Google Colab with T4 GPU
* Split: 70% training, 15% validation, 15% test

I chose DistilBERT because it is lightweight enough to fine-tune quickly in Colab while still being a strong baseline transformer model for text classification.

Hyperparameters

The final training configuration used:

Hyperparameter	Value
epochs	10
train batch size	8
eval batch size	32
learning rate	2e-5
weight decay	0.01
warmup steps	0
base model	distilbert-base-uncased

One key hyperparameter decision was setting warmup_steps to 0 and increasing the number of epochs. Earlier runs used a small dataset and the model collapsed into predicting mostly one class. Removing warmup and training longer helped the model learn all three labels instead of predicting only Analysis.

⸻

Baseline Model: Groq Zero-Shot

For the baseline, I used Groq’s llama-3.3-70b-versatile as a zero-shot classifier. The baseline was run on the same held-out test set as the fine-tuned model.

The prompt gave the model the label definitions and instructed it to return only one of the exact labels:

analysis
hot_take
reaction

The prompt included examples and definitions copied from my taxonomy. The model’s outputs were parsed only if they matched one of the valid label strings.

All 32 test examples were parseable.

⸻

Evaluation Results

Overall Accuracy

Model	Accuracy
Groq zero-shot baseline	0.656
Fine-tuned DistilBERT	0.594

The zero-shot Groq baseline outperformed the fine-tuned DistilBERT model by 0.062 accuracy.

This was an important result: fine-tuning did not automatically beat a larger language model with strong prior knowledge. The fine-tuned model improved compared to earlier collapsed runs, but Groq still performed better on the small test set.

⸻

Fine-Tuned Model Metrics

Label	Precision	Recall	F1-score	Support
analysis	0.69	0.69	0.69	13
hot_take	0.42	0.50	0.45	10
reaction	0.71	0.56	0.62	9
accuracy			0.59	32
macro avg	0.61	0.58	0.59	32
weighted avg	0.61	0.59	0.60	32

⸻

Baseline Metrics

Label	Precision	Recall	F1-score	Support
analysis	1.00	0.38	0.56	13
hot_take	0.64	0.70	0.67	10
reaction	0.56	1.00	0.72	9
accuracy			0.66	32
macro avg	0.73	0.69	0.65	32
weighted avg	0.76	0.66	0.64	32

⸻

Confusion Matrix

Fine-tuned DistilBERT confusion matrix on the test set:

True label \ Predicted label	analysis	hot_take	reaction
analysis	9	4	0
hot_take	3	5	2
reaction	1	3	5

The model performed best on Analysis and Reaction, but struggled most with Hot Take. The most common confusion was between Analysis and Hot Take. This makes sense because both often contain basketball-specific terms, but the difference depends on whether the comment actually supports its claim.

⸻

Error Analysis

Error 1: Analysis predicted as Hot Take

Text:
“Largest blown lead in Finals history.”

True label: analysis
Predicted label: hot_take
Confidence: 0.49

Analysis:
This comment contains a factual historical claim, which is why I labeled it Analysis. The model likely predicted Hot Take because the phrase sounds dramatic and resembles exaggerated sports discourse. This shows that short factual comments are hard because they may look like hot takes when they do not include much explanation.

⸻

Error 2: Hot Take predicted as Analysis

Text:
“Like watching the Warriors lose a game and not giving Curry the ball in the clutch, Mitch Johnson is insane.”

True label: hot_take
Predicted label: analysis
Confidence: 0.93

Analysis:
This was a high-confidence mistake. The comment includes a basketball comparison involving Curry and clutch decision-making, which likely made the model treat it as Analysis. However, the actual comment is mostly a strong criticism of Mitch Johnson and does not explain the claim in a structured way. This reveals the model’s tendency to treat basketball terminology and comparisons as analysis, even when the comment is mainly a hot take.

⸻

Error 3: Hot Take predicted as Reaction

Text:
“Where do the Cavs even go from here?”

True label: hot_take
Predicted label: reaction
Confidence: 0.76

Analysis:
This comment is a broad team-direction concern. I labeled it Hot Take because it implies a strong judgment about the Cavs’ future without providing evidence. The model likely predicted Reaction because the comment is short and question-like, similar to emotional live-thread responses. This shows that very short comments are difficult because they often lack enough context.

⸻

Error 4: Reaction predicted as Hot Take

Text:
“During the national anthem is a crazy time to get booed.”

True label: reaction
Predicted label: hot_take
Confidence: 0.55

Analysis:
This comment reacts to a specific arena moment. It is not making a basketball argument or a broad unsupported claim. The model likely predicted Hot Take because the wording is judgmental. This shows that emotional reactions can overlap with opinionated language.

⸻

Error Pattern Analysis

After reviewing the wrong predictions, I found three major patterns:

1. Analysis and Hot Take were often confused.
    The model sometimes treated any basketball comparison or player/team reference as Analysis, even when the comment was mostly an unsupported opinion.
2. Short comments were harder to classify.
    Short comments like “Where do the Cavs even go from here?” can be read as a reaction, hot take, or discussion prompt depending on context.
3. Tone and structure conflicted.
    Some comments had emotional or dramatic wording but still contained factual evidence. Others sounded analytical because they mentioned players or teams but did not actually explain anything.

The largest boundary issue was not topic recognition. The model understood that comments were about NBA discourse. The harder problem was identifying whether the comment supported its claim.

⸻

Sample Classifications

Below are sample classifications from the fine-tuned DistilBERT model.

Text	True Label	Predicted Label	Confidence	Result
“Still happy to hear obvious boos.”	reaction	reaction	0.75	Correct
“Kawhi has the best PR ever.”	hot_take	hot_take	0.82	Correct
“OG is one of the only players in this Finals with a ring and he was by far the most composed player. Massive threes, huge defensive plays, and a dagger tip-in showed the importance of experience.”	analysis	analysis	0.94	Correct
“Cooper Flagg should not be on this list.”	hot_take	hot_take	0.73	Correct
“For rookies, what else is there to go off of besides this season? Flagg is good for a rookie but Knueppel is better right now.”	analysis	analysis	0.90	Correct

The third example is a strong correct prediction. The comment was labeled analysis because it gives a reasoned argument about OG’s composure by pointing to specific plays: threes, defensive plays, and a tip-in. The model predicted analysis with 0.94 confidence, which is reasonable because the comment contains concrete basketball evidence rather than only an emotional reaction or unsupported opinion.

⸻

Reflection: What the Model Learned vs What I Intended

I intended the model to learn the difference between three types of NBA discourse:

* Analysis: supported basketball reasoning
* Hot Take: bold unsupported opinion
* Reaction: emotional or meme-like response

The model partially learned this distinction. It achieved non-zero performance across all three labels and did not collapse into predicting only one class in the final run. However, the confusion matrix shows that it still struggled with the boundary between Analysis and Hot Take.

The model seemed to learn that basketball-specific vocabulary often signals Analysis. This helped when comments included stats, tactics, or specific game decisions. However, it also caused mistakes when a Hot Take included player names, comparisons, or basketball terms without real evidence.

The model also learned some emotional reaction patterns, but it sometimes confused short reactions with Hot Takes. This happened when a reaction included judgmental language or sarcasm.

The biggest gap between my intention and the learned behavior was that my labels depend on argument structure, not just topic. DistilBERT often recognized the topic but did not always recognize whether the comment actually supported its claim.

⸻

Spec Reflection

How the Spec Helped

The project spec helped most by forcing me to define the label taxonomy before training. Writing definitions and edge cases early made it easier to see where the classifier was failing later. When the model confused Analysis and Hot Take, I could connect that failure directly back to the decision rules in my planning document.

The baseline requirement also helped because it gave the fine-tuned model’s performance context. Without the Groq baseline, I might have interpreted 59.4% accuracy in isolation. Comparing it to the 65.6% zero-shot baseline showed that fine-tuning on a small dataset did not automatically beat a larger model with broader language understanding.

How My Implementation Diverged

My implementation diverged from the original plan in the data collection phase. I originally expected to collect a balanced dataset quickly from NBA threads. In practice, my first dataset was too concentrated in one Knicks championship thread and included too many celebration-style comments. Early training runs showed poor behavior, including one run where the model predicted mostly one class.

To fix this, I revised the dataset by collecting from more varied thread types and relabeling comments using stricter taxonomy rules. This improved the final model because it learned all three classes instead of collapsing into one label.

⸻

AI Usage

I used AI tools in several specific ways during this project.

1. Label Taxonomy Stress Testing

I used ChatGPT to discuss the boundary between Analysis, Hot Take, and Reaction. For example, I tested comments that contained both a statistic and an opinion, such as “LeBron is overrated. He’s only 4-6 in the Finals.” The AI helped surface why a statistic alone should not automatically make something Analysis. I revised the decision rule so that evidence must actually support an argument, not just decorate a hot take.

2. Dataset Cleaning and Annotation Support

I used ChatGPT to help clean copied Reddit comments by removing usernames, vote counts, flair text, and reply metadata. I also used it to suggest labels for batches of comments. I manually reviewed the labels and adjusted the dataset after early model results showed the first version was too noisy and too focused on one thread type.

3. Failure Analysis

After evaluation, I used ChatGPT to identify patterns in the model’s wrong predictions. The main pattern it helped identify was the model’s confusion between basketball terminology and actual reasoning. I verified this by rereading misclassified examples myself and checking the confusion matrix.

⸻

Demo Video

Demo video link: TODO

The demo shows:

1. 3–5 comments being classified by the fine-tuned model.
2. One correct prediction and why it makes sense.
3. One incorrect prediction and what went wrong.
4. A short walkthrough of the evaluation results and confusion matrix.


