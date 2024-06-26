# loan_default_prediction_using_PCA

Hi everyone, I'm Anurag from the Indian Statistical Institute. Today, I'll be walking you through a series of code focusing on Principal Component Analysis (PCA) using a notebook from a Kaggle competition hosted by Imperial College London in 2014.

Video Tutorial:  
[<img width="660" alt="Screenshot 2024-05-08 at 3 42 27 PM" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/075d6d83-f523-4533-98c7-99a4c43f404c">](https://www.youtube.com/watch?v=gjHHIFlU2E8 "Play on YouTube")

Link for the Kaggle Competition: www.kaggle.com/competitions/loan-default-prediction
Link for the Kaggle Notebook: www.kaggle.com/code/rajvidoshi/loan-default-prediction-with-pca/notebook

<h3>Aim:</h3>
<div>The competition aimed to predict whether a loan would default and the potential loss if it did. Unlike traditional methods that categorize borrowers as good or bad, this competition sought to anticipate both the default likelihood and the severity of losses. Essentially, it aimed to bridge traditional banking methods with asset management strategies.</div>

<h3>Motivation:</h3>
<div>
  We chose the loan default dataset for two main reasons:
<ul><li>Real-World Impact: The issue of loan default has significant real-world implications for both lenders and borrowers. By analyzing this dataset, we can develop models and strategies to better predict and prevent loan defaults. This can help financial institutions minimize their losses and make more informed lending decisions, ultimately contributing to a more stable financial system.</li>
  
<li>Complexity and Challenge: The loan default prediction task presents a complex and challenging problem for data analysis and machine learning. The dataset likely contains a wide range of variables that influence loan default, such as borrower demographics, financial history, loan terms, economic factors, and more. Tackling this dataset requires sophisticated analytical techniques, including feature engineering, dimensionality reduction (like PCA), and predictive modeling (such as logistic regression or random forests). Successfully addressing these challenges can enhance our skills as data scientists and provide valuable insights into predictive modeling in finance.</li></ul>

In essence, the loan default dataset offers both practical relevance and intellectual challenge, making it a compelling choice for analysis and exploration.

</div>
<h3>Importing Libraries</h3>
<pre>import numpy as np
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt</pre>

<h3>Loading Dataset</h3>
<pre>
  train_data = pd.read_csv("/content/drive/MyDrive/Loan_Default/train_v2.csv")
</pre>
<samp><img width="1293" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/9edafd0a-075a-4b69-a9fd-ec3527a7ba01">
</samp>

<pre>
  test_data = pd.read_csv("/content/drive/MyDrive/Loan_Default/test_v2.csv")
</pre>
<samp>
  <img width="1205" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/e9cfbe9f-cfdf-4808-8338-5e8107e35c08">
</samp>
<h3>View DataFrame</h3>
<h4>Train Data</h4>
<pre>
  train_data.head()
</pre>
<samp>
  <img width="1143" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/3ae1c2d8-75b0-4f3a-8293-c9e8a6b475bf">
</samp>
<h3>Test Data</h3>
<pre>test_data.head()</pre>
<samp><img width="1183" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/205cd7e5-df9c-4e13-8446-bb66849c7775">
</samp>
<h3>Data Information</h3>
<pre>train_data.info()</pre>
<samp>
  <img width="422" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/bf6d9c7b-adc9-46f8-93a7-0e7d38a3bbc0">
</samp>
<pre>test_data.info()</pre>
<samp>
  <img width="507" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/1ff46dbb-341c-47c4-b906-0b769dc2e9c6">
</samp>
<h3>Missing Values/Null Values</h3>
<pre>sns.heatmap(train_data.isnull(), cbar=False)</pre>
<samp>
  <img width="631" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/b48a4f7a-3dac-40ee-91d2-455cae1dd623">
</samp>
<pre>
  missing = train_data.isnull().sum()
missing = pd.DataFrame(missing[missing!=0])
missing.columns = ['No. of missing values']
missing['Percentage'] = 100*missing['No. of missing values']/train_data.id.count()
missing.sort_values(by="Percentage", ascending=False)
</pre>
<samp>
  <img width="744" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/80f7bf3d-45e4-4f52-af60-2fcfb9713772">
</samp>
<h3>Drop columns having incorrect dtype</h3>
<pre>
  train_data.select_dtypes(include=['object']).head()
</pre>
<samp>
  <img width="921" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/43b20124-afb8-4d9c-b5d5-e1d03653c059">
</samp>
<pre>
invalid = train_data.select_dtypes(include=['object']).columns
train_data.drop(invalid, axis=1, inplace=True)
</pre>
<pre>
test_data.drop(invalid, axis=1, inplace=True)
t_id = test_data['id'].copy
test_data.drop('id', axis=1, inplace = True)
</pre>
<h3>Describe Statistics</h3>
<pre>train_data.describe()</pre>
<samp>
  <img width="891" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/b45fb32d-2cd2-464c-a6c7-e3c43f6a2814">
</samp>
<pre>test_data.describe()</pre>
<samp>
  <img width="891" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/015c27d7-68fb-4de4-975b-3b748eee3e11">
</samp>
<h3>Correlations</h3>
<h4>To look at those features having very high correlations among themselves.</h4>
<pre>
  correlations = train_data.iloc[:,1:752].corr()
correlations.head())
</pre>
<samp>
  <img width="1122" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/46838225-1d32-46a1-9894-0c0fbdf5784c">
</samp>
<h3>Splitting Train_dataset into (Train:Test)</h3>
<pre>
x = train_data.iloc[:,1:751].copy()
y = train_data.iloc[:,751].copy()
y.value_counts()
</pre>
<samp>
  <img width="379" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/17e91793-2310-455e-9862-28b4ef76147f">
</samp>
<h3>We first convert y into binary</h3>
<pre>
y[y>0] = 1
y.value_counts()
</pre>
<samp>
  <img width="274" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/6d5ad88a-32c9-4a20-9ef5-914d8e032f72">
</samp>
<pre>
from sklearn.model_selection import train_test_split
X_train, X_test, y_train, y_test = train_test_split(x, y, stratify = y, random_state=0)
</pre>
<pre>
  [X_train.shape, X_test.shape, y_train.shape, y_test.shape]
</pre>
<samp>
  <img width="792" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/e4a287f8-65e5-4e5b-876b-8b7b94ed5b69">
</samp>
<h3>Missing Value Treatment</h3>
<pre>
X_train = X_train.fillna(X_train.mean())
X_test = X_test.fillna(X_train.mean())
</pre>
<pre>
test_data = test_data.fillna(X_train.mean())
[X_train.isnull().sum().sum(), X_test.isnull().sum().sum(), test_data.isnull().sum().sum()]
</pre>
<samp>
  <img width="820" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/2f02633f-2add-4dda-9d02-9c74836dee31">
</samp>
<h3>Standardization of Variables</h3>
<h4>As we know, PCA is effected by scale, so, we need to scale all the features into standard units (mean=0,variabce=1).</h4>
<pre>
from sklearn.preprocessing import StandardScaler
scalar= StandardScaler()
scalar.fit(X_train)
X_train = scalar.transform(X_train)
X_test = scalar.transform(X_test)
</pre>
<pre>
  X_t = scalar.transform(test_data)
</pre>
<h3>PCA</h3>
<h4>Read more about PCA (scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) (sebastianraschka.com/Articles/2015_pca_in_3_steps.html) (www.kdnuggets.com/2023/05/principal-component-analysis-pca-scikitlearn.html)</h4>
<pre>
from sklearn.decomposition import PCA
pca = PCA()
</pre>
<pre>pca.fit(X_train)</pre>
<samp>
  <img width="404" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/78f26d74-8268-427b-a66c-838577276084">
</samp>
<pre>
  pca.components_
</pre>
<samp>
  <img width="587" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/f61d740a-a64b-4520-a4ad-87dc11d39bb4">
</samp>
<pre>
  pca.explained_variance_
</pre>
<samp>
  <img width="673" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/d97b2edf-20af-480d-ba62-228402ef3e70">
</samp>
<pre>
  pca.get_covariance()
</pre>
<samp>
  <img width="673" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/f98e07cd-64b9-4b56-8c65-799a566e3fff">
</samp>
<pre>
plt.plot(np.cumsum(pca.explained_variance_ratio_))
plt.xlabel('number of components')
plt.ylabel('cumulative explained variance');
</pre>
<samp>
  <img width="387" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/63f7bcf9-093b-4ca7-9d01-8b91382296cb">
</samp>
<pre>np.cumsum(pca.explained_variance_ratio_)[200]</pre>
<samp>
  0.9827484142421781
</samp>
<h4>
98.27% of variation is explained by 100 components.
</h4>
<pre>
final_pca = PCA(n_components=200)
final_pca.fit(X_train)
X_train = final_pca.transform(X_train)
X_train = pd.DataFrame(data = X_train)
X_test = final_pca.transform(X_test)
X_test = pd.DataFrame(data = X_test)
X_t = final_pca.transform(X_t)
X_t = pd.DataFrame(data = X_t)
</pre>
<h4>Now we can use these variables to fit the model with 200 independent variables to predict loss.</h4>
<h3>Logistic Regression</h3>
<pre>
from sklearn.linear_model import LogisticRegression
model = LogisticRegression(solver= 'saga', class_weight='balanced',max_iter=500, random_state=1).fit(X_train, y_train)
model.coef_[0]
</pre>
<samp>
  <img width="423" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/a2d54ddb-ca33-4504-9371-e213a205b1fe">
</samp>
<h3>Validation on test data</h3>
<pre>
 y_pred = model.predict(X_test)
 y_pred
</pre>
<samp>
  <img width="273" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/f88b6d44-ba8f-4db9-907e-fef26a22d046">
</samp>
<h3>
 Model Evaluation 
</h3>
<h4>Confusion Matrix</h4>
<pre>
  import sklearn.metrics as sm
c = pd.DataFrame(sm.confusion_matrix(y_test, y_pred), index=['Actual non defaulter','Actual defaulter'])
c.columns = ['Predicted non defaulter','Predicted defaulter']
c['Actual Total'] = c.sum(axis=1)
c.loc['Predicted Total',:] = c.sum(axis = 0)
c
</pre>
<samp>
  <img width="495" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/ece60e65-55f4-4504-b416-46a50f07e297">
</samp>
<h4>The below confusion matrix is generated using (onlineconfusionmatrix.com) </h4>
<samp>
  <img width="691" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/e6ce2cc7-f2e8-4c43-8c84-dc7395cb78ed">

</samp>
<h3>Accuracy</h3>
<pre>
  print(["The accuracy on the validation data is " + str(round(sm.accuracy_score(y_test, y_pred)*100,ndigits = 2)) + "%"])
</pre>
<samp>['The accuracy on the validation data is 64.36%']</samp>
<h3>
  Sensitivity
</h3>
<pre>
print("The sensitivity (true positive rate) is " + str(round(100*c.iloc[1,1]/c.iloc[1,2], ndigits=2)) + "%")
</pre>
<samp>
  The sensitivity (true positive rate) is 68.93%
</samp>
<h3>
  AUC
</h3>
<pre>
ns_fpr, ns_tpr, _ = sm.roc_curve(y_test, np.zeros(len(y_test)))
lr_probs = model.predict_proba(X_test)
# keep probabilities for the positive outcome only
lr_probs = lr_probs[:, 1]
lr_fpr, lr_tpr, _ = sm.roc_curve(y_test, lr_probs)
# plot the roc curve for the model
plt.plot(ns_fpr, ns_tpr, linestyle='--', label='No Skill')
plt.plot(lr_fpr, lr_tpr, marker='.', label='Logistic Regression')
# axis labels
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
# show the legend
plt.legend()
# show the plot
plt.show()
</pre>
<samp>
  <img width="387" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/2fb0c58d-917b-40f7-95db-ea37ffad1144">
</samp>
<pre>
print("The Area under ROC curve is " + str(round(100 * sm.roc_auc_score(y_test, y_pred), ndigits=2)) + "%")
</pre>
<samp>
  The Area under ROC curve is 66.41%
</samp>
<h3>Read more about AUC and ROC Curve (developers.google.com/machine-learning/crash-course/classification/roc-and-auc)
<h3>
  Classification Report
</h3>
<pre>print(sm.classification_report(y_test, y_pred))</pre>
<samp>
  <img width="458" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/3f41fde7-c0ca-4ded-8271-a942050af2c0">
</samp>
<h3>Prediction on given test data</h3>
<pre>
pred = model.predict(X_t)
sns.countplot(pred);
</pre>
<samp>
  <img width="407" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/09d87b96-9c5b-4af1-a82f-9b0b491b48ad">
</samp>
<pre>
submission = pd.read_csv("../input/loan-default-prediction/sampleSubmission.csv")
submission['loss'] = pred
</pre>
<pre>
  submission.head()
</pre>
<samp>
  <img width="135" alt="image" src="https://github.com/anuragprasad95/loan_default_prediction_using_PCA/assets/3609255/e8193a86-e139-452e-9e23-a4021cd9644d">
</samp>
<pre>
  submission.to_csv("submit.csv", index=False)
</pre>
