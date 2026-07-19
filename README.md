NUTSGRAPH: Uncertainty-Aware Graph-Based Intrusion Detection for IoT

NUTSGRAPH is a graph-based Bayesian intrusion detection framework for IoT traffic analysis. It combines GraphSAGE-based relational representation learning with a Bayesian multilayer perceptron (MLP) trained using No-U-Turn Sampling (NUTS) to improve not only predictive performance, but also calibration quality and out-of-distribution (OoD) awareness. The notebook in this repository implements the full experimental pipeline used for the NUTSGRAPH study, including graph construction from NetFlow traffic, deterministic and Bayesian model comparisons, uncertainty analysis, and visualization.

Overview

Traditional neural intrusion detection systems often achieve strong classification performance but remain overconfident on ambiguous, misclassified, or previously unseen traffic. NUTSGRAPH addresses this limitation by first learning graph-aware traffic embeddings through a GraphSAGE encoder and then applying Bayesian posterior inference over frozen edge embeddings using NUTS. This design enables the framework to retain the structural advantages of graph neural networks while producing more reliable uncertainty estimates for trustworthy IoT intrusion detection.

Main Components

The notebook implements the following pipeline:

Dataset loading and preprocessing
Loads the IoT network flow dataset from CSV, performs basic cleaning, label encoding, and feature scaling.
Graph construction
Converts traffic records into a directed communication graph where IP addresses are nodes and network flows are edges.
Node feature generation
Builds node features by aggregating incident edge attributes.
Graph neural representation learning
Trains a GraphSAGE-based edge classification model on the constructed graph.
Deterministic and uncertainty-aware baselines
Evaluates:
Base GNN
Temperature-scaled GNN
GNN + MC Dropout
Bayesian posterior inference with NUTS
Extracts frozen edge embeddings from the trained GNN and fits a Bayesian MLP using Pyro’s No-U-Turn Sampler (NUTS).
Evaluation and visualization
Produces:
Accuracy, Precision, Recall, F1-score
ECE and MCE calibration metrics
Confusion matrices
Reliability diagrams
Entropy and confidence plots
Misclassification AUROC / AUPR / FPR@95%TPR
UMAP visualization of learned node embeddings
Runtime and memory comparisons
Repository Contents
NUTSGRAPH_implementation.ipynb — Full implementation notebook
README.md — Project documentation
Dataset

This implementation is designed for a NetFlow-based IoT intrusion detection dataset in CSV format. The notebook expects key source, destination, and label columns to be present.

By default, the configuration uses:

IPV4_SRC_ADDR as source node column
IPV4_DST_ADDR as destination node column

You must update the dataset path in the configuration section of the notebook:

"CSV_PATH": "path/to/your/dataset.csv"

This implementation is intended for the NF-ToN-IoT-v3 dataset, though it can be adapted to other flow-based intrusion detection datasets with similar structure.

Environment and Dependencies

The notebook is designed for Google Colab and uses GPU acceleration where available.

Main libraries:

Python 3
PyTorch
PyTorch Geometric
Pyro
NumPy
pandas
scikit-learn
matplotlib
seaborn
umap-learn

The setup cell in the notebook installs the required packages automatically.

How to Run

Open the notebook in Google Colab and execute the cells sequentially.

Typical workflow:

Upload or mount the dataset
Edit the configuration block
Run preprocessing and graph construction
Train the base GraphSAGE model
Evaluate deterministic and MC Dropout baselines
Extract frozen edge embeddings
Run Bayesian posterior inference with NUTS
Generate comparison metrics and figures
Save the resulting artifacts
Important Configuration Notes

Before running the notebook, check the configuration block carefully.

You should especially confirm:

dataset path
source and destination column names
batch size
neighborhood sampling sizes
number of epochs
NUTS subset size
number of warmup steps and posterior samples

The notebook uses a subset of frozen edge embeddings for NUTS in order to keep Bayesian inference practical in Colab. This is necessary because full-batch MCMC over very large graph embeddings is computationally expensive.

Outputs

The notebook saves key outputs such as:

comparison metric tables
trained deterministic model weights
posterior samples from the Bayesian classifier
configuration file
generated visualizations

Results are saved to a Colab directory such as:

/content/nuts_gibbon_results

or

/content/nuts_gibbon_results_new

You may rename this output directory if desired.

Methodological Notes

NUTSGRAPH uses a hybrid design:

the GraphSAGE encoder captures communication topology and relational traffic structure,
the Bayesian MLP models epistemic uncertainty over the learned edge embeddings.

To make Bayesian inference tractable, the GNN encoder is trained first and then frozen before NUTS is applied. This keeps the posterior sampling stage computationally manageable while still allowing uncertainty-aware downstream classification.

The UMAP plot in the notebook provides a qualitative view of the learned node embedding space. Node colors are based on an approximate majority label inferred from incident training edges, rather than true node labels.

--------------------------------------------
CITATION
---------------------------------------------------

-------------------------------------
CITATION
-----------------------------------
If you use this implementation in academic work, please cite the associated research paper :
Osei-Wusu,F., Appiah, O., Mensah,P. K., Nimbe, P.,  and Donkoh,E. K. “ A Graph-Based Bayesian Intrusion Detection Framework With No-U-Turn Sampling for Uncertainty-Aware IoT Security,” Concurrency and Computation: Practice and Experience 38, no. 14 (2026): e70817, https://doi.org/10.1002/cpe.70817.
 
License
This repository is intended for research and academic purposes.
Please cite the associated work if you use this implementation.
 
Contact
For questions or collaboration related to this research, please contact the repository author through GitHub.


-------------------------------
LIMITATIONS
---------------------------------

This implementation applies Bayesian inference only to the classifier stage and not to the full graph encoder. As a result, uncertainty in the representation-learning component is not fully propagated through the entire architecture. In addition, NUTS can be computationally intensive, so practical execution may require careful control of subset size and sampler settings.
