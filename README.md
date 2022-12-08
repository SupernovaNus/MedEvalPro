# MedEvalPro: A forecasting tool for medical decisions

## Project Overview (Everyone)
[CoverMyMeds](https://www.covermymeds.com/main/) is a healthcare technology company that aims to improve patients' access to medications by simplifying the process of connecting with the healthcare network. It can speed up the time for patients to receive therapy and reduce the number of prescriptions that will not be approved by the health insurance payers. Furthermore, it provides the electronic prior authorization (ePA) service for the medical claims rejected by payers, which could tremendously boost the process of PA filing with a convenient portal-based experience. 

Taking advantage of the historical medical data associated with patients' claims and PAs, it is possible to extract valuable information and make practical applications to benefit customers. For example, we can infer payers' formulary of drugs and how payers make decisions based on it and medical prescriptions.

**Medical Evaluation Prophet (MedEvalPro)** utilitzes the mock data of medical claims provided by CoverMyMeds.

* d
* 

we can try to discover the formulary status of certain drugs for certain payers, which is very valuable data in this market. We can also try to predict whether a PA will be needed at the point of prescribing to prevent a patient from going to the pharmacy, only to return home empty handed. We can predict whether a PA will be successful based upon claims data, or data included on the ePA, to prevent patients from waiting on a decision, only to end up having to switch to another therapy if the PA is rejected. We can also forecast future monthly PA volume as a function of historical volume to generate more accurate budgets.




## Dataset (Po-Wen)

### Overview
The dataset we use in this project contains information of around 1.3 million claims. For each claim, the following features are provided in file `dim_claims.csv`:

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
* Key question: What is the formulary of each payer?
* Results

## Classification (Ozkan, Ismail, Po-Wen)
* Key question: How to predict if a PA will be approved or not?
* Metric used: In our contest, it is crucial for any model to minimize the false positives since it safer 
* Models:

* `Support Vector Machine Classifier`:
* `Feedforward neural network`: We used a feedforward neural network with two hidden layers. The input is an 8 dimensional array containing the categorical entries corresponding to the following features:
`rejected_code`, `drug_type`, `correct_diagnosis`, `tried_and_failed`, `contraindication`, `is_holiday`, `is_weekday`, `is_workday`

This model consists of two layers of 50 and 20 layers respectively and uses the ‘relu’ function as the activation function. This model acheives an F1 score (...) of : when trained ... The performance of this simple neural network is comparable to almost every model we have tried.

* `Random forest with GridSearch`:

In addition to these machine learning models, we computed PA approval rates using contingency tables and build a model by using different thresholds with the approval rates. We observed that using all six categorical features we used in the above models results in a contingency table with cells with 0 counts, and those cells cannot be used to compute approval rates. So, we used the feature importance scores we get from the best model to get an importance order among the subsets of features, and used it to get a contingency table with a nonempty count for any given feature string. The following is the top 4 feature subsets in this order:
**All features > All features except "correct_diagnosis" > All features except "bin_no" > All features except "correct_diagnosis" & "bin_no"**

We observe that the contingency table built from all features except "correct_diagnosis" & "bin_no" is full, so we only needed to go this deep in the order to get all the approval rates. Then we get the metrics for this model with different thresholds and saw that... (Results)

## Time series analysis (Axel)
* Key question: How to predict the claim/PA volumes in the future?
* Models:



* Best models/results

## Future directions (Everyone)
* What would we do if?

* `Convoluntional neural network`: The feedforward neural network we built 



---
Useful Markdown syntax
---

*asterisks*

**asterisks**

**_asterisks_**

~~scratch~~

Inline `code`


```
Test.
```

1. First ordered list item
2. Another item
