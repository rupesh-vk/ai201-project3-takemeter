TakeMeter Planning Document

1. Community

For this project, I chose the NBA discussion community, especially public NBA discussion spaces such as r/nba on Reddit. This community is a strong fit for a discourse-quality classification task because NBA discussions are active, opinion-heavy, and varied in quality. People post quick emotional reactions, bold unsupported opinions, and longer evidence-based arguments, which makes it possible to define meaningful labels.

The distinction matters because NBA fans often judge whether a post is a serious basketball argument, a hot take meant to provoke debate, or just an immediate reaction to a play, trade, or game result. These categories reflect how people in the community already talk about basketball discourse.

2. Label Taxonomy

Label 1: Analysis

Analysis posts make a basketball argument using specific reasoning, evidence, statistics, tactical observations, or historical comparison. The key feature is that the post explains why a claim is true instead of only stating an opinion.

Example 1:
“Jokic creates so many open looks because defenses have to respect both his scoring and passing from the elbow.”

Example 2:
“The Celtics switching everything works because they have multiple wings who can guard smaller players without giving up easy mismatches.”

Label 2: Hot Take

Hot Take posts make a strong basketball opinion or prediction with little or no meaningful supporting evidence. These posts may be entertaining or debatable, but they mostly assert rather than explain.

Example 1:
“Anthony Edwards is already better than prime Dwyane Wade.”

Example 2:
“The Lakers are cooked. This team is never winning anything again.”

Label 3: Reaction

Reaction posts are immediate emotional responses to a game, play, trade, injury, highlight, or news event. These posts focus more on feeling, surprise, excitement, anger, or disbelief than on argument.

Example 1:
“WHAT A SHOT. I can’t believe he hit that.”

Example 2:
“No way they actually traded him. I’m sick.”

3. Hard Edge Cases

Some NBA posts are difficult to label because they combine opinion, emotion, and evidence in the same short comment.

Hard Case 1:
“LeBron is overrated. He’s only 4-6 in the Finals.”

Possible labels: Analysis or Hot Take
Decision: Hot Take
Reason: The post includes a statistic, but the statistic is used as a quick weapon for a bold opinion rather than as part of a deeper argument.

Hard Case 2:
“Jokic is the best player in the league and it is not close. Nobody controls an offense like him.”

Possible labels: Analysis or Hot Take
Decision: Hot Take
Reason: The post gives a basketball reason, but it does not provide enough specific evidence or explanation to count as analysis.

Hard Case 3:
“That defensive possession was insane. They switched everything and still recovered to the corner.”

Possible labels: Reaction or Analysis
Decision: Analysis
Reason: Even though the tone is emotional, the post identifies a specific basketball action and explains what happened tactically.

Decision rule: If a post uses specific basketball reasoning, evidence, statistics, or tactical detail to support a claim, I will label it Analysis. If it mainly states a bold opinion without meaningful support, I will label it Hot Take. If it mainly expresses emotion about a specific moment or event, I will label it Reaction.

4. Data Collection Plan

I will collect at least 200 public NBA posts or comments from public NBA discussion spaces, mainly r/nba. I will focus on comments because they are short, varied, and easy to classify. I will save the dataset as a CSV file with three columns: text, label, and notes.

My target is around 225 total examples, with about 75 examples per label: Analysis, Hot Take, and Reaction. This gives a more balanced dataset and avoids one label dominating the model. If one label is underrepresented, I will collect more examples from threads where that type of comment is more common. For example, post-game threads may have more Reaction comments, while serious discussion threads may have more Analysis comments.

5. Evaluation Metrics

I will use overall accuracy to measure how often the model predicts the correct label on the test set. However, accuracy alone is not enough because the model could perform well overall while failing on one label.

I will also use per-class precision, recall, and F1 score. F1 is especially useful because it balances precision and recall for each label. I will also use a confusion matrix to see which labels are being confused, such as Analysis being predicted as Hot Take or Reaction being predicted as Hot Take.

6. Definition of Success

A useful classifier should perform better than a zero-shot LLM baseline on the same test set. My target is at least 70% overall accuracy for the fine-tuned model and at least 0.65 F1 score for each class.

For a real community tool, I would consider the model good enough if it can reliably separate serious basketball analysis from unsupported hot takes and emotional reactions. It does not need to be perfect, but it should make understandable mistakes rather than random ones.

7. AI Tool Plan

Label Stress Testing

I will use ChatGPT to stress-test my label definitions by asking it to generate NBA comments that sit between Analysis, Hot Take, and Reaction. If those examples are hard to classify, I will tighten my definitions before labeling the full dataset.

Annotation Assistance

I may use an AI tool to suggest labels for some examples, but I will personally review every label before including it in the final dataset. If I use AI-assisted labeling, I will disclose that in the README.

Failure Analysis

After evaluating the model, I will use an AI tool to help identify patterns in the wrong predictions. I will verify those patterns myself by rereading the misclassified examples before writing the final error analysis.
