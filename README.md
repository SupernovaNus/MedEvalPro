![alt text](https://github.com/SupernovaNus/CoverMyMeds_project/blob/main/Images/MedEvalPro_graphic.png)
## The Erdös Institute Data Science Bootcamp, Fall 2022
Ismail Abouamal, Po-Wen Chang, Özkan Demir, Axel La Salle, Victoria Uribe


## Project Overview (Everyone)
[CoverMyMeds](https://www.covermymeds.com/main/) is a healthcare technology company that aims to improve patients' access to medications by simplifying the process of connecting with the healthcare network. It can speed up the time for patients to receive therapy and reduce the number of prescriptions that will not be approved by the health insurance payers. Furthermore, it provides the electronic prior authorization (ePA) service for the medical claims rejected by payers, which could tremendously boost the process of PA filing with a convenient portal-based experience. 

![alt text](https://github.com/SupernovaNus/CoverMyMeds_project/blob/main/Images/claim_approval_process.png)

Taking advantage of the historical medical data associated with patients' claims and PAs, it is possible to extract valuable information and make practical applications to benefit customers. For example, we can infer payers' formulary of drugs and how payers make decisions based on it and medical prescriptions.

Through this project, we provide **Medical Evaluation Prophet (MedEvalPro)**, a forecasting tool that enables healthcare providers to make efficient decisions on whether or not a PA is needed, significantly enhance the chance of PA approval, and predict the trend of future demands on PAs. We use the data provided by CoverMyMeds to:

![alt text](https://github.com/SupernovaNus/CoverMyMeds_project/blob/main/Images/project_outline.png)


## Dataset (Po-Wen)

The synthetic dataset we use in this project contains information of around 1.3 million claims. For each claim, the following features are provided in file `dim_claims.csv`:

* `dim_claim_id`: The unique identification number of the claim, which ranges from `1` to `1335576`.
* `bin`: The BIN identification number of the payer for the claim. There are 4 different BINs: `417380`, `417740`, `417614`, and `999001`.
* `drug`: The drug that was prescribed to the patient and associated with the claim. There are 3 types of drugs: `A`, `B`, and `C`.
* `reject_code`: Some claims in our dataset were directly approved by patients’ insurance companies or payers, while others were rejected due to various reasons. If a claim was rejected, it would be associated with a rejection code. There are 3 rejection codes in the dataset: `70`, `75`, and `76`. If a claim was approved, the rejection code is labeld as `NaN`.
* `pharmacy_claim_approved`: If the claim was approved by the payer, the value of this feature is assigned as `1`; otherwise, it is `0`. 


For all claims rejected by patients’ payers, healthcare providers will have to submit **_prior authorizations (PAs)_** for their patients. The rejected claims with PAs are then reviewed by payers with more detailed information about patients and prescriptions. Such additional features associated with **_rejected claims_** and whether or not PAs are approved are provided in file `dim_pa.csv`:

* `dim_pa_id`: The unique identification number of the PA claims, which ranges from `1` to `555951`.
* `correct_diagnosis`: If the provider contends that the patient has the correct diagnosis for the associated drug, the value is `1`; otherwise `0`.
* `tried_and_failed`: If the patient has tried and failed alternative treatments, the value is `1`; otherwise `0`. 
* `contraindication`: If the patient has an associated contraindication for the medication requested, the value is `1`; otherwise `0`. 
* `pa_approved`: If the payer approved the PA of the rejected claim, the value is `1`; otherwise `0`.


File `dim_date.csv` contains the calendar records of all claims:

* `dim_date_id`: Index for the record. 
* `date_val` and `calendar_year`/`calendar_month`/`calendar_day`: Year-Month-Day for the record. 
* `day_of_week`: The *n*th day of the week (integers). 
* `is_weekday`: Monday through Friday $=$ `1`; otherwise $=$ `0`.
* `is_workday`: Monday through Friday and not holiday $=$ `1`; otherwise $=$ `0`.
* `is_holiday`: Holiday $=$ `1`; otherwise $=$ `0`. 


File `bridge.csv` provides a table that bridges all information associated with `dim_claim_id`, `dim_pa_id`, and `dim_date_id`.



## Formulary (Victoria)
`reject_code`: 

* `70` = drug is not covered by plan and is not on the payer’s formulary
* `75` = drug is on the payer’s formulary but is not preferred - PA required
* `76` = drug is covered but that the plan’s limit on the number of fills for that drug have been met
* `NaN` = approved


**Results:**


![alt text](https://github.com/SupernovaNus/CoverMyMeds_project/blob/main/Images/formulary_1.png)
![alt text](https://github.com/SupernovaNus/CoverMyMeds_project/blob/main/Images/formulary_2.png)
![alt text](https://github.com/SupernovaNus/CoverMyMeds_project/blob/main/Images/formulary_3.png)
![alt text](https://github.com/SupernovaNus/CoverMyMeds_project/blob/main/Images/formulary_4.png)


**45% of rejected claims were rejected with code 70**


## Classification (Ozkan, Ismail, Po-Wen)
* Key question: How to predict if a PA will be approved or not?

**Models:**

* 'Decision Tree Classifier': Default
* 'AdaBoost Classifier': Default
* 'Random Forest Classifier': See classification/Cover_My_med_Random_Forest with GridSearch.ipynb
* 'Extra Trees Classifier': Default
* 'Gradient Boosting Classifier': Default
* 'K Neighbors Classifier': Default
* 'Logistic Regression': Default
* 'Linear Discriminant Analysis': Default
* 'CatBoost Classifier': See classification/CatBoostGridSearchCV.ipynb
* 'XGB Classifier': See classification/XGBoostGridSearchCV.ipynb
* 'Feedforward neural network': We used a feedforward neural network with two hidden layers. The input is an 8 dimensional array containing the categorical entries corresponding to the following features:
`rejected_code`, `drug_type`, `correct_diagnosis`, `tried_and_failed`, `contraindication`,`bin_number`

The feedforward neural networkThis consists of two layers of 50 and 20 layers respectively and uses the rectifier function as the activation function. This is our best machine learning model: It acheives an F1 score 0.86 when trained on 80% of the dataset.

**Embedding of last layer of the neural network:**


![TSNE_feedforward_network](https://user-images.githubusercontent.com/81845143/206777035-40d192db-76a2-4be9-b889-12a55669a7ee.png)

Is our best model capable of separating the claims based on a given feature?
We used the TSNE (t-distributed stochastic neighbor embedding) algorithm to reduce the dimension of the last layer of the network, which is a 20 dimensional array. This allows us to visualize how the model performs the classificstion task. For example, based on the figure above our feedforward network seems to isolate the claims containing drugs A and B relatively well.



**The Metric used for the Comparison of all Models:**

We have the results comparing above models on a single train-test split on the whole dataset in classification/AllModelsComparison.ipynb
In summary, for our metric to choose the final model, we did not use accuracy because the dataset was imbalanced, having 73% PA approval rate overall. For this classification problem, false positive is worse than a false negative, as classifying a to-be-rejected PA claim as otherwise and filing the PA claim would put an unnecessary cost on the company. So, precision is our most important metric to maximize. However, the naive model that rejects every PA claim has 100% precision, so we need another metric. We decided to use a *weighted harmonic mean of precision and recall, weighed more on the precision side (2-to-1)*, which can be thought of as a *weighted f1 score*. With this metric, the *Feedforward neural network model* turned out to have the best performance.

**An Intuitive Model using PA Approval Rates Calculated from Contingency Tables:**

In addition to these machine learning models, we computed PA approval rates using contingency tables and build a model by using different thresholds with the approval rates in classification\PAApprovalRateCalculationsFromContingencyTables.ipynb
We observed that using all six categorical features we used in the above models results in a contingency table with cells with 0 counts, and those cells cannot be used to compute approval rates. So, we used the feature importance scores we get from the best model to get an importance order among the subsets of features, and used it to get a contingency table with a nonempty count for any given feature string. The following is the top 4 feature subsets in this order:
*All features > All features except "correct_diagnosis" > All features except "bin_no" > All features except "correct_diagnosis" & "bin_no"*

We observe that the contingency table built from all features except "correct_diagnosis" & "bin_no" is full, so we only needed to go this deep in the order to get all the approval rates. Then we get the weighted f1 scores for models with different thresholds, and shown that the model with 60% threshold outperforms all other machine learning mothers we have investigated so far. 

## Time series analysis (Axel)
* Key question: How to predict the claim/PA volumes in the future?
* Models:



* Best models/results

## Future Work (Everyone)
**Convolutional Neural Network (CNN):**
* Transform the input array corresponding to any given claim to an image-like input
* Use different sequential treatments throughout the different layers of the network to make the model more sensitive to certain key features
* _We believe this model would achieve a better F1 score_


**Tune our Models**
* Highly non-trivial task for structured data (i.e. our time series data)
* Explore how to create better end-to-end pipelines for easier forecasting and visualization for budgeting needs



