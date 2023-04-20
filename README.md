# PyratesAI

Set of Notebook for the PyratesAI experiment: *Predict Feedback Type from Student Interaction in the Pyrates Programming Serious Game*

## Notebooks

There are three Notebooks that are meant to be used as follows.
- features_engineering.ipynb: Process the row Pyrates data and generates a set of predictive features. It takes as input the raw data in /data and returns a dataframe with the unique student IDs of the students and the labels as columns, along with all of the predictive features. This is fully dependent on Pyrates. The dataframe is saved as a pickle (see the Notebook documentation).
- features_exploration.ipynb: This is just meant to explore and visualize the set of features.
- model_training.ipynb: Train and evaluate the models to predict the fedback type, using as input the dataframe pickled by features_engineering.ipynb. Then this notebook exports: the performances of the models (F1, Prcision, recall); the confusion matrices; and the most important features for the best found model.

Each Notebook includes a documentation with the Inputs and Outputs.