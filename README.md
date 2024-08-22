# PhylogenyCreator

This repository contains code to detect breaks and fusions in a HiC file through machine learning, and use this feature data to reconstruct phylogenetic trees. There were two different kinds of models developed for this task:

**Distance Classifier:** This model predicted the range of the distance between the regions in the center of its input data (e.g. Between 2 and 5 MB)
**Binary Classifier:** This model predicted a probability there was a break between the regions in the center of its input data.

The Distance Classifier is the most successful model as of August 2024.

This guide provides a breakdown of what each notebook provides. 

**Distance Notebooks:**

DataCreation.ipynb:
Constructs interchromosomal break matrices
Constructs intrachromosomal break matrices with the break distance in a specific range
Constructs no break matrices
Saves all n by n matrices from a HiC file’s diagonal (n can be any number)

Training.ipynb:
Loading and preprocessing training, validation, and testing data
Visualizing training examples
Multiple models (including autoencoders) with custom loss functions and callbacks
Evaluating the model with confusion matrices, accuracy, loss, etc.
Visualizing correct and incorrect examples
Saliency map on the model’s predictions

Deployment.ipynb:
Removing low-read bins from input matrices into the model
Need to name folder and folder results
Model deployment for breaks
Filtering output bed files and combined handling of bed files to compare features across files
Heatmaps of the presence of breaks and fusions in different files
Building and visualizing fusions and predicting on them
Determining most likely fusions
Reconstructing phylogeny based on breaks and fusions

Visualizer.ipynb:
Visualizing and predicting on HiC regions
Visualizing and predicting on fusions after removing low-read bins

DistanceMetrics.ipynb:
Constructing interchromosomal break matrices
Constructing intrachromosomal break matrices with a specific break distance
Creating Distance vs. Probability graphs for the model
Creating Distance vs. Saliency graphs for the model

**Binary Classifier Notebooks:**

DataCreation.ipynb: 
Constructs interchromosomal break matrices with specific modifications possible
Constructs intrachromosomal break matrices with the break distance in a specific range with specific modifications possible
Constructs no break matrices

Training.ipynb: 
Loading and preprocessing training, validation, and testing data
Visualizing training examples
Train models with custom loss functions and callbacks
Evaluating the model with confusion matrices, accuracy, loss, etc.
ROC curves
Visualizing correct and incorrect examples

Deployment.ipynb:
Model deployment for break
Need to name folder and folder results
Filtering output bed files and combined handling of bed files to compare features across files
Heatmaps of the presence of breaks and fusions in different files
Heatmap of the probability of fusions in different files
Building and visualizing fusions and predicting on them
Determining most likely fusions
Reconstructing phylogeny based on breaks and fusions

DistanceVsProbability.ipynb:
Constructing interchromosomal break matrices
Constructing intrachromosomal break matrices with a specific break distance
Creating Distance vs. Probability graphs for the model

**Pipeline: Distance Classifier**:
Step 1: Use the DataCreation.ipynb notebook to create training examples. To run, fill the _max_distance_, _WIDTH_, _res_, and _ds_scale_ variables. Then, fill the _folder_ variable to indicate where the examples should be saved. Finally, fill the _folder_root_ variable to indicate where the data that the model will be deployed on will be saved.

Step 2: Use the Training.ipynb notebook to train the model. To run, fill the _WIDTH_ and _res_ variables. Then, fill the _folder_ and _bins_ variables to indicate where the training examples should be drawn from and the ranges the model will predict from. Finally, indicate the name the model will be saved by in the _od.save()_ function. 

Step 3: Use the Deployment.ipynb notebook to deploy the model on breaks and fusions and construct the final phylogeny. To run, fill the  _WIDTH_, _res_, and _model_ fields. Then, fill the _folder_ variable to indicate the data the model will be deployed on. Finally, fill the _folder_results_ variable to indicate where the model's results will be saved.

Step 4 (Optional): 

**Pipeline: Binary Classifier**
Step 1: Use the DataCreation.ipynb notebooks to create training examples. To run, fill the _max_distance_, _WIDTH_, _res_, and _ds_scale_ variables. Then, fill the _folder_ variable to indicate where the results should be saved.

Step 2: Use the Training.ipynb notebooks to train the model. To run, fill the _WIDTH_, _res_, and _break_distance_ variables. Then, fill the _folder_ and _bins_ variables to indicate where the training examples should be drawn from and each kind of training data. Finally, indicate the name the model will be saved by in the _od.save()_ function. 

Step 3: Use the Deployment.ipynb notebook to deploy the model on breaks and fusions and construct the final phylogeny. To run, fill the  _WIDTH_, _res_, and _model_ fields. Then, fill the _folder_ variable to indicate the data the model will be deployed on. Finally, fill the _folder_results_ variable to indicate where the model's results will be saved.

**NOTE:** These notebooks are primarily designed to handle observed data, but can be easily modified to work with other types of data as well. Additionally, all notebooks contain functions to aid in the use of observed/expected (oe) data. 
