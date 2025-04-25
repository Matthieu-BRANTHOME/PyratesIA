![image](https://py-rates.fr/assets/welcomePage/logoSmall.png)

# PyratesAI

This repository includes the supplementary material (data and code) of a research paper published at the ACM Conference on Learning at Scale 2025: "Learning from Teachers: AI-Driven Feedback for a High School
Python Serious Game" ([https://doi.org/10.1145/3698205.3729543](https://doi.org/10.1145/3698205.3729543)). Please cite this paper (full citation provided at the end of the readme) if you reuse our data or code.

The research tackles the challenge of building AI-driven adaptive feedback in [Pyrates](https://py-rates.org/), a serious games to support the transition from block-based to text-based programming in high school classes. Specifically, the AI-driven feedback policy is meant to predict, during interaction with Pyrates, what type of formative feedback is suitable to address students’ difficulties. To build this policy, we conducted a user study in regular 10th grade Python classes to collect labels on how experienced teachers (N = 7) support their students (N = 215). We trained machine learning (ML) classifiers to predict these labels using students’ interaction behaviours and code. In this repository, we provide the raw data collected during the user study, our code to train the ML classifiers, as well as the results, pre-trained models and pre-computed features.

- Authors: Matthieu Branthôme (Univ Rennes, IRISA, CNRS) and Sébastien Lallé (Sorbonne University, LIP6, CNRS).
- Date: 2025-04-24
- Licence: BSD 3-Clause License

Should you have any questions regarding the data, the code and the research, please email the authors (matthieu.branthome at univ-rennes.fr, sebastien.lalle at lip6.fr).

## Dependencies

The code was run with Python 3.9 and Jupyterlab (v. 3.6). The machine learning (ML) part of the code is based on scikit-learn (v. 1.1.3). See [requirements.txt](requirements.txt) for the dependencies.

## Data

The raw and pre-processed data are included in the [./data](data/) folder.

### Raw data

The raw experimental data are in [./data/raw_data.csv](./data/raw_data.csv). This is an export of xAPI statements collected by the Pyrates game and stored in xAPI format in a Learning Locker LRS ([https://lrsels.lip6.fr/](https://lrsels.lip6.fr/)). xAPI is an an eLearning specification ([https://xapi.com/statements-101/](https://xapi.com/statements-101/)) which represents interaction data (called statements) in the form of "actor (who did the action), verb (what is the action), object (on what the action was performed)". Each row of the CSV file correspond to one xAPI statement, which in Pyrates can be either: an action performed by a learner in the game interface, e.g., opening the memo, running the code; or a feedback triggered by the teacher in the game (see the paper for more details). The CSV file includes the following columns:

- \_help_origin: whether feedback was asked by the student or proactively provided by the teacher (only for rows about feedback trigger).
- \_stopped_line: at what line in the code the student stopped the program (only for rows about executing the code)
- \_level: the current game level (from 1 to 8). For a description of the 8 levels, see [https://py-rates.org/guide/EN/](https://py-rates.org/guide/EN/).
- \_implemented_concepts: the list of programming concepts extracted from the learner's code upon execution. This corresponds to the concepts taught in the game (see [https://py-rates.org/guide/EN/](https://py-rates.org/guide/EN/)), namely: for-simple-concept, for-counter-0-concept, for-counter-n-concept, while-concept, var-affectation-concept, int-concept, float-concept, boolean-concept, string-concept, comparison-concept, if-branch-concept, elif-branch-concept, else-branch-concept.
- \_lost_level: the reason for an in-game error when the code is correct, e.g., barrel-explosion (only for rows about executing the code).
- \_error: error message provided to the learners when executing the buggy code (only for rows about executing the code).
- \_game_error_reason: an error accompanying the \_error message.
- \_code: the learner's code (only for rows about editing an executing the code).
- \_game_progression: how far the pirate character progressed in the level (in percentage from 0 - did not move, to 100 - level completed).
- \_date: the timestamp of the statement in millisecond (e.g., 2023-04-22T16:08:42.133+02:00).
- \_duration: the duration in second and millisecond (e.g., PT6.802S) for statement that provide time-based information, such as how long it took to run the code and to complete a level.
- \_used_control_functions: the lsit of control functions available in the game (e.g., walk, open, attack, see [https://py-rates.org/guide/EN/](https://py-rates.org/guide/EN/) used in the learner's code (only for rows about executing the code and completing a level).
- \_execution_speed_changed: a float number corresponding to the speed of the pirate character from 1 (slow) to 3 (fast) (only for rows about changing the speed).
- \_extra_lines_number: when there are too many lines in the code, an int number indicating how many extra lines there are in the code (only for rows where \_object_id is set to "too-many-lines")
- \_type: the action (xAPI verb) performed.
- \_id: the unique identifier of the xAPI statement
- \_game_time: the in-game time of the session in seconds.
- \_object_id: the xAPI object (upon which the action is performed in the game).
- \_student: the anonymized session identifier for a given learner.
- \_execution_speed_multiplier: the current speed of the pirate character (only for rows about executing the code).

### Pre-computed features

The [./data](data/) folder also includes one Excel file per game level (e.g., [./data/df_Level1.xlsx](./data/df_Level1.xlsx) for level 1) that includes high-level features derived from the row data. The features are computed with the code provided in the features_engineering Notebook (see below). Specifically, each row in these files corresponds to the feature computed for one given feedback instance provided by a teacher (as described in the paper). The columns include:

- help_type: the type of feedback: 1 (CTLR), 2 (CONC), 3 (IMPL) and 4 (SOLU) (see the paper).
- student_id: the unique session identifier (same as in the row data).
- FE_tot_requested: the amont of help provided to that learner in the same game level so far.
- FE_tot_control_received, FE_tot_notion_received, FE_tot_implementation_received, FE_tot_solution_received: the amount of help received in that game level so far per feedback type (CTRL, CONC, IMPL, SOLU).
- level_time_spent: time spent so far in the current game level.
- All of the other columns are the features used for training the ML models, documented and computed in the _features_engineering.ipynb_ Notebook (see below).

## Code

There are three Jupyter Notebooks in the repository:

- [features_engineering.ipynb](features_engineering.ipynb): Process the row Pyrates data and generates a set of predictive features. It takes as input the raw data in ./data and returns a dataframe with the unique student IDs of the students and the labels as columns, along with all of the predictive features. This is fully dependent on Pyrates. The dataframe is saved as a pickle, and the features as Excel files as described above (see also the Notebook documentation).
- [features_exploration.ipynb](features_exploration.ipynb): This is just meant to explore and visualize the features.
- [model_training.ipynb](model_training.ipynb): Train and evaluate the ML models to predict the feedback type, using as input the dataframe pickled by features_engineering.ipynb. Then this notebook exports: the performances of the models (F1, Precision, Recall); the confusion matrices; and the most important features for the best found model.

Each Notebook includes a documentation with their inputs and outputs. The notebooks are executed and provided with all of the cell outputs for your reference.

### features_engineering.ipynb

- The header provides a markdown documentation of the Notebook.
- Cell 2 includes the list of valid session identifier for the analysis.
- Cell 4 provides the full list of xAPI verbs and objects considered for analysis, and defines relevant constants.
- Cell 5 provides the list of features built by the notebook.
- Cell 6 defines the different sets of features built (FEATURES_SETS dictionary).
- Cell 10 opens the raw data.
- Cell 11 filters out some outlier data based on issues tracked during the experiment.
- Cell 14 computes all of the features per feedback delivery, and print some descriptive statistics.
- Cell 16 exports pickles with the feature vectors built and important constants.

### features_exploration.ipynb

- The header provides a markdown documentation of the Notebook.
- Cell 5 provides the distribution of feedback type per game level.
- Cell 6 provides the distribution of all features.
- Cell 8 provides the correlation matrices for the features.

### model_training.ipynb

- The header provides a markdown documentation of the Notebook.
- Cell 8 defines the constants and ML parameters (the default values are the one used in the paper).
- Cell 10 selects the feature sets to be used in the Notebook to train the classifiers.
- Cells 15 to 20 remove outliers (recommended not to edit them).
- Cell 28 creates the train and test folds.
- Cells 33 to 40 define functions used in the ML pipelines for preprocessing and selecting features.
- Cells 43 to 45 define the baseline classifiers.
- Cells 46 to 51 define the ML classifiers.
- Cell 55 trains and evaluates the "vanilla" classifiers using the built-in _[cross_validate()](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.cross_validate.html)_ function from sklearn.
- Cell 59 trains and evaluates the classifiers in a fully customized way to account for all of the parameters and constants define in Cell 8. This cell supports in particular hyperparameter tuning, SMOTING, and exporting classifiers per folds for further investigation (this is the main cell used for the analysis reported in the paper).
- Cell 61 displays the results of the classifiers trained in Cell 59 (the results of Table 4 and Table 5 in the paper are based in this cell).
- Cell 67 defines the best classifiers trained in Cell 59 to be used for further analysis (in our case, Random Forest).
- Cells 69 and 70 show the correlation matrices for the best classifier. Cell 59 shows the normalized matrices, Cell 7 the absolute ones (which are the ones reported in the paper in Fig. 4).
- Cells 74 to 80 compute and display the most important features used by the best classifiers, using the two approach implemented in sklearn to do so (namely permutation-based, and impurity-based). In the paper we report (in Fig. 5) the impurity-based results from Cell 80.
- Cells 82 to 87 export the trained classifiers as pickles, and the results in CSV format.

## Pretrained models

The best trained classifiers (Random Forests) are exported with sklearn in the [./models](./models) folder, in the [.joblib](https://joblib.readthedocs.io/en/latest/index.html#module-joblib) format. As said above (Feature section), the pre-computed features are available in the [./data](./data) folder.

On a side note, these models have been integrated and tested in Pyrates (cf. publication in [https://hal.science/hal-05024217v1/document](https://hal.science/hal-05024217v1/document).)

## Citation

If you reuse parts of our data and code, please cite the following paper:

- Matthieu Branthôme and Sébastien Lallé. 2025. Learning from Teachers: AI-Driven Feedback for a High School Python Serious Game. In Proceedings of the 12th ACM Conference on Learning @ Scale (L@S '25), July 21–23, 2025, Palermo, Italy. ACM, 12 pages. [https://doi.org/10.1145/3698205.3729543](https://doi.org/10.1145/3698205.3729543)

Bibtex:

> @inproceedings{branthome2025learning,<br>
> title = {{Learning from Teachers: AI-Driven Feedback for a High School Python Serious Game}},<br>
> author = {Branth{\^o}me, Matthieu and Lall{\'e}, S{\'e}bastien},<br>
> booktitle = {{Proceedings of the 12th ACM Learning @ Scale Conference  (L{@}S '25)}},<br>
> address={Palermo, Italy},<br>
> year = {2025},<br>
> doi = {10.1145/3698205.3729543},<br>
> publisher = {ACM}<br>
> }<br>
