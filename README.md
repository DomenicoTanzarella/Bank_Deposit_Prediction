# Bank_Deposit_Prediction

## OVERVIEW

The data is related with direct marketing campaigns of a Portuguese banking institution. 

The dataset comes from the UCI Machine Learning repository and provide an overview of factors that may influence the opening (or not) of a bank term deposit.

To frame the task, we will refer back to a standard process in industry for data projects called CRISP-DM. This process provides a framework for working through a data problem. A brief overview of CRISP-DM is here.

The notebook is here: [Bank_account.ipynb](https://github.com/DomenicoTanzarella/Bank_Deposit_Prediction/blob/main/Bank_account.ipynb) 

### Business Understanding

This data represents 17 campaigns with 79354 contacts gathered. The campaigns were all phone campaigns conducted between May 2008 and November 2010. Often, more than one contact to the same client was required, in order to access if the product (bank term deposit) would be ('yes') or not ('no') subscribed.


### Data Understanding

The main featurs are the ones related to the bank client data:

#### bank client data:

1 - age (numeric)

2 - job : type of job (categorical: 'admin.','blue-collar','entrepreneur','housemaid','management','retired','self-employed','services','student','technician','unemployed','unknown')

3 - marital : marital status (categorical: 'divorced','married','single','unknown'; note: 'divorced' means divorced or widowed)

4 - education (categorical: 'basic.4y','basic.6y','basic.9y','high.school','illiterate','professional.course','university.degree','unknown')

5 - default: has credit in default? (categorical: 'no','yes','unknown')

6 - housing: has housing loan? (categorical: 'no','yes','unknown')

7 - loan: has personal loan? (categorical: 'no','yes','unknown')

#### related with the last contact of the current campaign:

8 - contact: contact communication type (categorical: 'cellular','telephone')

9 - month: last contact month of year (categorical: 'jan', 'feb', 'mar', ..., 'nov', 'dec')

10 - day_of_week: last contact day of the week (categorical: 'mon','tue','wed','thu','fri')

11 - duration: last contact duration, in seconds (numeric). Important note: this attribute highly affects the output target (e.g., if duration=0 then y='no'). Yet, the duration is not known before a call is performed. Also, after the end of the call y is obviously known. Thus, this input should only be included for benchmark purposes and should be discarded if the intention is to have a realistic predictive model.

#### other attributes:

12 - campaign: number of contacts performed during this campaign and for this client (numeric, includes last contact)

13 - pdays: number of days that passed by after the client was last contacted from a previous campaign (numeric; 999 means client was not previously contacted)

14 - previous: number of contacts performed before this campaign and for this client (numeric)

15 - poutcome: outcome of the previous marketing campaign (categorical: 'failure','nonexistent','success')

#### social and economic context attributes

16 - emp.var.rate: employment variation rate - quarterly indicator (numeric)

17 - cons.price.idx: consumer price index - monthly indicator (numeric)

18 - cons.conf.idx: consumer confidence index - monthly indicator (numeric)

19 - euribor3m: euribor 3 month rate - daily indicator (numeric)

20 - nr.employed: number of employees - quarterly indicator (numeric)

#### Output variable (desired target):

21 - y - has the client subscribed a term deposit? (binary: 'yes','no')

## Data Preparation

After our initial exploration and fine tuning of the business understanding, it is time to construct our final dataset prior to modeling. Here, we want to make sure to handle any integrity issues and cleaning, the engineering of new features, any transformations that we believe should happen and general preparation for modeling with sklearn.

The Dataset per se is quite clean: few duplicates and no missing data that were removed.

Below is the pie chart showng that the outcome feature 'y' (customer opening an account) is really not balanced:

![alt text](https://github.com/DomenicoTanzarella/Bank_Deposit_Prediction/blob/main/outcome_distribution.png)

We keep this in mind, as will influence how much importance we give to the accuracy score vs F1 Score when we test the model.

Also, we analyzed the numerical features and their correlation to the outcome:

![alt text](https://github.com/DomenicoTanzarella/Bank_Deposit_Prediction/blob/main/correlation_matrix.png)

It does not look like there is a lot of correlation we can leverage, although what we can highlight is correlation between these two features:

emp.var.rate
cons.price.idx


and between these other two:

euribor3m

nr.employed


Also, the only correlation of the outcome y and the features is 'duration' and 'previous': both related to being previously contacted by marketing

### Modeling

The dataset we are analyzing is a typical example of classification problem.

We decide to use the Bank client data as features for our predictions. 

1 - age (numeric)

2 - job : type of job (categorical: 'admin.','blue-collar','entrepreneur','housemaid','management','retired','self-employed','services','student','technician','unemployed','unknown')

3 - marital : marital status (categorical: 'divorced','married','single','unknown'; note: 'divorced' means divorced or widowed)

4 - education (categorical: 'basic.4y','basic.6y','basic.9y','high.school','illiterate','professional.course','university.degree','unknown')

5 - default: has credit in default? (categorical: 'no','yes','unknown')

6 - housing: has housing loan? (categorical: 'no','yes','unknown')

7 - loan: has personal loan? (categorical: 'no','yes','unknown')


We perform the following operations to further massage the data:

- We encode non numerical features with OneHotEncoding
- We use StandardScaler to normalize the data (which is very inportant especially for Support Vecto Machine models)
- We split the data in train (80%) and test (20%)

Now we apply a Dummy classifier to establish a baseline obtaining an accuracy of 88.73%

After this we compare for different classifier to understand the best:

![alt text](https://github.com/DomenicoTanzarella/Bank_Deposit_Prediction/blob/main/classifier_comparison.png)

### Finding

The accuracy for all four tests is similar, with Logistic regression being the best with SVM runner up.

Notable the amount of time taken by SVM due to conmplex matrix based calculations part of the training process

### Improvments

Applying GridSearchCV to the four models used only worked for the fastest models: the fact that we are dealing with 100+ features (due to the oneHotEncoded) and using a normal CPU for the calculations made this process last hours and eventually timed out.

Results for Logistic Regression and Decision Tree after the GridSearch cross validation are in line with what we found using the default parameters.

### Conclusions and Possible Next Steps

The classification model generated has accuracies and f1-score around 88% and up which means it does a good job to predict customer opening an account: domain knowledge shows that marital status, having a mortgage or a personal loan are strong indicators of accepting bank opening account offers and contributed to get the prediction right.

LogisticRegression() along with GridSearchCV helped achieve accuracy on test data of 89% and f1-score of 94%, making it the classifier of choice with the compute resources available.

Being able to access to powerful GPUs would have helped with cross validation for KNN and SVM, possibly scoring even better accuracy with fine tuned models.

Also including the 'number of contacts performed before this campaign and for this client' as feature ('previous') and its outcome (feature 'poutcome') might improve further the accuracy of the prediction and should be tested as next step.

Lastly, It would be interesting to explore accuracy of the model only using numerical features as we only used the bank data that were mostly non numerical: likely not a game changer as we noticed from the correlation matrix posted above, but still interesting to take a look at it.


