# Output Format and Evaluation for Family History Extraction

## Subtask 1: Entity Identification

For the first subtask, we would like you to extract two types of entities:
1) family members mentioned in the text
2) observations (diseases) in the family history

The participants should also
 provide side of family (i.e. Paternal, Maternal, NA) for each family member.

The possible family members in this task are:
```
Father, Mother, Parent, Sister, Brother, Daughter, Son, Child,
Grandmother, Grandfather, Grandparent, Cousin, Sibling, Aunt, Uncle.
```

Please do not include other relatives in your results (e.g. nephew).

### Note
* For first degree relatives (e.g. parents, children, siblings),
the side of family should always be `"NA"`.

* To reduce the ambiguity in  observation extraction,
 we accept partial matching of the observations. For example, an extraction
 of `"diabetes"` in the phrase of `"type 2 diabetes"` will be considered as a
 true positive when calculating F1 score. However, each observation should
 contain no more than 4 tokens.

* To reduce the complexity of the task, the negation information is removed
from evaluation for both subtasks.

### Output Format

Within a single file, the fields are delimited by tabs.
```
doc_id  FamilyMember family_member   SideOfFamily
doc_id  Observation  text of observation
```

Examples:
```
doc_1	FamilyMember	Brother	NA
doc_1	FamilyMember	Grandfather	Paternal
doc_1	FamilyMember	Father	NA
doc_1	Observation	conduction disorders
```


## Subtask 2: Family History Extraction

In the Subtask 2, the participants need to extract the relations between family members, observations and living status. The purpose of this subtask is to evaluate participating systems as end-to-end family history summarization systems based on clinical texts.

The output file should be in TSV format which the columns are:

   * Family member
   * Side of family
   * Living status
   * Observation

In cases where there are more than one observation for one family member category,
 the systems should provide those observations in separate rows.

### Output Format

Within a single file. Fields are delimited by tabs.
```
doc_id  family_member    side_of_family    LivingStatus    living_status_score
doc_id  family_member    side_of_family    Observation    text_of_observation1
doc_id  family_member    side_of_family    Observation    text_of_observation2
```

Examples:
```
doc_1	Brother	NA	LivingStatus	4
doc_1	Father	NA	LivingStatus	4
doc_1	Grandfather	Paternal	LivingStatus	0
doc_1	Cousin	Paternal	Observation	 phaeochromocytoma
doc_1	Cousin	Paternal	Observation	choreic dysphonia
```


### Living Status Score

We use only one score to represent living status for each individual.

For both "Alive" and "Healthy" properties,  the results
are encoded as:
* Yes: 2
* NA: 1
* No: 0

And the overall score for each individual are the alive score times healthy score.

For example:

For a relative with `"Alive: Yes"` and `"Healthy: Yes"`,
the living status score should be ``2 * 2 = 4``.

For a relative with `"Alive: No"` and `"Healthy: NA"`,
the living status score should be `0 * 1 = 0`.

### Note
* If multiple relatives are under the family member category,
(e.g. multiple maternal aunts)
with different living status scores, use the minimum of the
scores as the final score for that category.
* To be considered as a correct prediction (True Positive) for family members,
all of the fields  have to be matched, including living status.
* The observation matching criterion is the same as subtask 1, where
partial matching is allowed.
* For Subtask 2, conditions applied to all relatives should  not be included. For
example, in the sentences of "There were no reports of mental retardation. ", 
the observation of "mental retardation" should not appear in any family members. 
* Negation information is not included in the evaluation. The participants should output all the observations without removing negated ones.


# Evaluation

We use standard F1-score as the evaluation (ranking) metrics.

Specifically,

```
Precision = TP / (TP + FP)
Recall = TP / (TP + FN)
F1 = 2 * Precision * Recall / (Precision + Recall)
```

Please run the evaluation script between your output file and the
gold standard TSV file (sent separately) to check your system performance.

```
python eval.py subtask path/to/gold_standard path/to/your/prediction
```


The `subtask` field is either `1` or `2`. For instance, for subtask 1:

```
python eval.py 1 gs_subtask1.tsv path/to/your/subtask1/prediction
```

You will find your system performance in your command line console.

```
TP: 18
FP: 1
FN: 3
Precision: 0.9696969696969697
Recall: 0.9142857142857143
F1: 0.9411764705882354
```

# Submission

Each team may submit *up to 3 runs* for each subtask (i.e. subtask 1 and 2).
 We will use the highest F1 score for each subtask as your final
 performance for ranking.

You may submit results of either of the subtasks, or both. The two tasks will be ranked separately. 

Once the official evaluation is done, you may also submit a 4-page, double-column system description
 to be included in the official ranking.
 The template and the instructions can be find here: http://www.biocreative.org/media/store/files/2017/2017_Biocreative_template_format.doc. 

Due to the tight timeline, we strongly encourage you to work on your system description
 while developing your system.

# Awards

*BioCreative* will provide a total of $500 for the
shared task winners ($250 for each subtask). Besides, a limited number
of *student travel awards* ($500) are also available to
support student presenters for their travel expenses.


# Contact

Please find more information on our [workshop site](https://sites.google.com/view/ohnlp2018/home).

# Acknowledgment
Please kindly cite our task overview paper as:

Sijia Liu, Majid Rastegar-Mojarad, Yanshan Wang, Liwei Wang, Feichen Shen, Sunyang Fu, Hongfang Liu. Overview of the BioCreative/OHNLP 2018 Family History Extraction Task. *BioCreative/OHNLP 2018 Workshop Proceedings*  [[PDF](https://github.com/ohnlp/BioCreativeOHNLPProceedings/raw/master/FHE_overview.pdf)]
