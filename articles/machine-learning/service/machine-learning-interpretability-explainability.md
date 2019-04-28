---
title: Model interpretability
titleSuffix: Azure Machine Learning service
description: Learn how to explain why your model makes predictions using the Azure Machine Learning SDK. It can be used during training and inferencing to understand how your model makes predictions.
services: machine-learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/29/2019
---

# Model interpretability with Azure Machine Learning service

In this article, you will learn how to explain why your model made the predictions it did with the interpretability package ([`explain`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)) of the Azure Machine Learning Python SDK.

During the training phase of the development cycle, model designers and evaluators can use to explain the output of a model to stakeholders to build trust.  They also use the insights into the model for debugging, validating model behavior matches their objectives, and to check for bias.

During the inferencing phase, data scientists can use interpretability to explain predictions to the people who use your model. For example, why did the model deny a mortgage loan, or predict that an investment portfolio carries a higher risk?

The interpretability classes are made available through two Python packages. Learn how to [install SDK packages for Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [azureml.explain.model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), the main package, contaiing functionalities supported by Microsoft. 

* `azureml.contrib.explain.model`, preview and experimental functionalities that you can try.

> [!IMPORTANT]
> Things in contrib are not fully supported. As the experimental functionalities become mature, they will gradually be moved to the main package.

## How to interpret your model

You can apply the interpretability classes and methods to understand the model’s global behavior or specific predictions. The former is called global explanation and the latter is called local explanation.

The methods can be also categorized based on whether the method is model agnostic or model specific. Some methods target certain type of models. For example, SHAP’s tree explainer only applies to tree-based models. Some methods treat the model as a black box, such as mimic explainer or SHAP’s kernel explainer. The `explain` package leverages these different approaches based on data sets, model types, and use cases. 

The output is a set of information on how a given model makes its prediction, such as:
* Global/local relative feature importance

* Global/local feature and prediction relationship

### Explainers

There are two sets of explainers: Direct Explainers and Meta Explainers in the SDK.

__Direct explainers__ come from integrated libraries. The SDK wraps all the explainers so that they expose a common API and output format. If you are more comfortable directly using these explainers, you can directly invoke them instead of using the common API and output format. The following are a list of the direct explainers available in the SDK:

* **Tree Explainer**: SHAP’s tree explainer, which focuses on polynomial time fast SHAP value estimation algorithm specific to trees and ensembles of trees.
* **Deep Explainer**: Based on the explanation from SHAP, Deep Explainer "is a high-speed approximation algorithm for SHAP values in deep learning models that builds on a connection with DeepLIFT described in the SHAP NIPS paper. TensorFlow models and Keras models using the TensorFlow backend are supported (there is also preliminary support for PyTorch)".
* **Kernel Explainer**: SHAP's Kernel explainer uses a specially weighted local linear regression to estimate SHAP values for any model.
* **Mimic Explainer**: Mimic explainer is based on the idea of global surrogate models. A global surrogate model is an intrinsically interpretable model that is trained to approximate the predictions of a black box model as accurately as possible. Data scientist can interpret the surrogate model to draw conclusions about the black box model.
* **LIME Explainer** (`contrib`): Based on LIME, LIME Explainer uses the state-of-the-art Local interpretable model-agnostic explanations (LIME) algorithm to create local surrogate models. Unlike the global surrogate models, LIME focuses on training local surrogate models to explain individual predictions.
* **HAN Text Explainer** (`contrib`): HAN Text Explainer uses a Hierarchical Attention Network for getting model explanations from text data for a given black box text model. We train the HAN surrogate model on a given teacher model's predicted outputs. After training globally across the text corpus, we have added a fine-tune step for a specific document in order to improve the accuracy of the explanations. HAN uses a bidirectional RNN with two attention layers, for sentence and word attention. Once the DNN is trained on the teacher model and fine-tuned on a specific document, we can extract the word importances from the attention layers. We have found HAN to be more accurate than LIME or SHAP for text data but more costly in terms of training time as well. However, we have made improvements to the training time by giving the user the option to initialize the network with GloVe word embeddings, although it is still slow. The training time can be improved significantly by running HAN on a remote Azure GPU VM. The implementation of HAN is described in 'Hierarchical Attention Networks for Document Classification (Yang et al., 2016)' ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ automatically select a suitable direct explainer and generate the best explanation info based on the given model and data sets. The meta explainers leverage all the libraries (SHAP, LIME, Mimic, etc.) that we have integrated or developed. The following are the meta explainers available in the SDK:

* **Tabular Explainer**: Used with tabular datasets.
* **Text Explainer**: Used with text datasets.

In addition to Meta-selecting of the direct explainers, meta explainers develop additional features on top of the underlying libraries and improve the speed and scalability over the direct explainers.

Currently `TabularExplainer` employs the following logic to invoke the Direct Explainers:

1. If it's a tree-based model, apply `TreeExplainer`, else
2. If it's a DNN model, apply `DeepExplainer`, else
3. Treat it as a black-box model and apply `KernelExplainer`

The intelligence built into `TabularExplainer` will become more sophisticated as more libraries are integrated into the SDK and we learn about pros and cons of each explainer.

`TabularExplainer` has also made significant feature and performance enhancements over the Direct Explainers:

* **Summarization of the initialization dataset**. In cases where speed of explanation is most important, we summarize the initialization dataset and generate a small set of representative samples, which speeds up both global and local explanation.
* **Sampling the evaluation data set**. If the user passes in a large set of evaluation samples but doesn't actually need all of them to be evaluated, the sampling parameter can be set to true to speed up the global explanation.

The following diagram shows the relationship between the two sets of direct and meta explainers.

[![Machine Learning Interpretability Architecture](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### Models supported

Any models that are trained on datasets in Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, or `scipy.sparse.csr_matrix` format are supported by the interpretability `explain` package of the SDK.

The explanation functions accept both models and pipelines as input. If a model is provided, the model must implement the prediction function `predict` or `predict_proba` that conforms to the Scikit convention. If a pipeline (name of the pipeline script) is provided, the explanation function assumes that the running pipeline script returns a prediction.

### Local and remote compute target

The `explain` package is designed to work with both local and remote compute targets. If run locally, The SDK functions will not contact any Azure services. You can run explanation remotely on Azure Machine Learning Compute and log the explanation info into Azure Machine Learning Run History Services. Once this information is logged, reports and visualizations from the explanation are readily available on Azure Machine Learning Workspace portal for user analysis.

## Training time interpretability

### Train and explain locally

1. Train your model in a local Jupyter notebook. 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Call the explainer: To initialize an explainer object, you need to pass your model and some training data to the explainer's constructor. You can also optionally pass in feature names and output class names (if doing classification) which will be used to make your explanations and visualizations more informative. Here is how to instantiate an explainer object using [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py) and [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) locally. `TabularExplainer` is calling one of the three explainers underneath (`TreeExplainer`, `DeepExplainer`, or `KernelExplainer`), and is automatically selecting the most appropriate one for your use case. You can however, call each of its three underlying explainers directly.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    or
    
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Get the global feature importance values.

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Local feature importance values: use the following function calls to explain an individual instance or a group of instances.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    or
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### Train and explain remotely

While you can train on the various compute targets supported by Azure Machine Learning service, the example in this section shows how to do this using an Azure Machine Learning Compute target.

1. Create a training script in a local Jupyter notebook (for example, run_explainer.py).

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine   
    # "features" and "classes" fields are optional 
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Follow the instructions on [Set up compute targets for model training](how-to-set-up-training-targets.md#amlcompute) to learn about how to set up an Azure Machine Learning Compute as your compute target and submit your training run.

3. Download the explanation in your local Jupyter notebook. 


    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```
    
## Next Steps

To see a demonstration of interpretability with the Azure Machine Learning SDK, look at the [Interpretability sample notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) on GitHub.
