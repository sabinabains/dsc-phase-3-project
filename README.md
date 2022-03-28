# Overview:

![volunteer_pic](https://github.com/sabinabains/dsc-phase-3-project/blob/main/images/volunteer_pic.jpg)

A volunteer organization in New York City called *Jump Start* wants to assist high school students in finding success in the classroom.

# Business Understanding

*Jump Start* plans to work with struggling school districts in the area to provide free tutoring for high school children they believe need additional support to pass their classes, however tutors are limited and the organization is unsure how to predict which students will need support the most.

Our suggestion is to use a supervised learning approach to predict students that are at risk to fail their courses, specifically with classification models. 

# Data Understanding

Portugal has obtained [data](https://www.kaggle.com/impapan/student-performance-data-set) on demographic and social characteristics of students in multiple high schools in the area, along with their final course grade. By training our model on this data, we can predict which students are likely to fail their courses based on the attributes given. Lastly, school districts can distribute surveys to collect this data at the beginning of the school year, and we can predict whether each student should be assigned a tutor based on their pass/fail classification. A pass grade is defined as a 60% (D) or higher for these districts.

As more data is gathered, our model can improve further and potentially be used at a national level to provide schools with the ability to help students in need of support. 

## Dataset Attributes and Definitions

* school - student's school (binary: 'GP' - Gabriel Pereira or 'MS' - Mousinho da Silveira)
* sex - student's sex (binary: 'F' - female or 'M' - male)
* age - student's age (numeric: from 15 to 22)
* address - student's home address type (binary: 'U' - urban or 'R' - rural)
* famsize - family size (binary: 'LE3' - less or equal to 3 or 'GT3' - greater than 3)
* Pstatus - parent's cohabitation status (binary: 'T' - living together or 'A' - apart)
* Medu - mother's education (numeric: 0 - none, 1 - primary education (4th grade), 2 â€“ 5th to 9th grade, 3 â€“ secondary education or 4 â€“ higher education)
* Fedu - father's education (numeric: 0 - none, 1 - primary education (4th grade), 2 â€“ 5th to 9th grade, 3 â€“ secondary education or 4 â€“ higher education)
* Mjob - mother's job (nominal: 'teacher', 'health' care related, civil 'services' (e.g. administrative or police), 'at_home' or 'other')
* Fjob - father's job (nominal: 'teacher', 'health' care related, civil 'services' (e.g. administrative or police), 'at_home' or 'other')
* reason - reason to choose this school (nominal: close to 'home', school 'reputation', 'course' preference or 'other')
* guardian - student's guardian (nominal: 'mother', 'father' or 'other')
* traveltime - home to school travel time (numeric: 1 - <15 min., 2 - 15 to 30 min., 3 - 30 min. to 1 hour, or 4 - >1 hour)
* studytime - weekly study time (numeric: 1 - <2 hours, 2 - 2 to 5 hours, 3 - 5 to 10 hours, or 4 - >10 hours)
* failures - number of past class failures (numeric: n if 1<=n<3, else 4)
* schoolsup - extra educational support (binary: yes or no)
* famsup - family educational support (binary: yes or no)
* paid - extra paid classes within the course subject (Math or Portuguese) (binary: yes or no)
* activities - extra-curricular activities (binary: yes or no)
* nursery - attended nursery school (binary: yes or no)
* higher - wants to take higher education (binary: yes or no)
* internet - Internet access at home (binary: yes or no)
* romantic - with a romantic relationship (binary: yes or no)
* famrel - quality of family relationships (numeric: from 1 - very bad to 5 - excellent)
* freetime - free time after school (numeric: from 1 - very low to 5 - very high)
* goout - going out with friends (numeric: from 1 - very low to 5 - very high)
* Dalc - workday alcohol consumption (numeric: from 1 - very low to 5 - very high)
* Walc - weekend alcohol consumption (numeric: from 1 - very low to 5 - very high)
* health - current health status (numeric: from 1 - very bad to 5 - very good)
* absences - number of school absences (numeric: from 0 to 93)
* final_grade - Pass / Fail status (1 - Fail, 0 - Pass)

# Modeling

### Preprocessing
Prior to modeling, we preprocessed the data. Steps included:
  * normalizing the data (necessary for distance based classification models such as K-Nearest Neighbors and Log Regression)
  * recoding the outcome variable to be binary (0 for pass, 1 for fail)
  * splitting our data into training and testing sets to evaluate the model on "new data" and ensure the model does not overfit to noise.

### Logistic Regression
the first classification model trained is a simple logisitic model with no hyperparameters in place. This generated a testing accuracy of 66.4%, and training accuracy of 73.5%

While this model does not overfit drastically, we tested other model types to see if they can improve testing accuracy and reduce the gap between the two scores.

### Decision Trees and Random Forest Models

The next model run was a basic decision tree. This resulted in an almost perfect training accuracy of 98.8%, but a very low testing accuracy of 62%. The model is severly overfitting to random fluctuations in the training data, and therefore is not going to succeed when running unseen data through the model, which is our eventual goal.

To attempt to fix this problem, we need to prune our tree. This includes parameters to our model to restrict it from splitting nodes until all leaf nodes are "pure". our second decision tree restricted the tree's maximum depth to 6, and the maximum sample requried to split a note to be 10% of it's sample. This resulted in a tree with 62% test accuracy as well, but a much lower training accuracy of 70%. 

Next, we ran a random forest, which essentially uses multiple decision trees to make their choice based on majority rule. We used a grid search on this random forest to find the optimal C value, max depth, min sample before splitting, and min sample per leaf. Grid Search runs each combination of these parameters before returning the model with the highest scoring value (in our case, accuracy). This resulted in a testing accuracy of 64%. 

# Evaluation

In the end, we returned to our Logistic Regression models and tuned our hyperparameters using a grid search. Our final model had a low C value (meaning we did not give higher weight to the training data, and therefore reduces our chances of overfitting. This model had the following scores and confusion matrix values:

* Training Data Accuracy Score: 73.5%
* Testing Data Accuracy Score: 66.5%

![final_model_cm](https://github.com/sabinabains/dsc-phase-3-project/blob/main/images/final_model_cm.png)

This result had the highest accuracy score, although only slightly. Most importantly, the gap between testing and training accuracies indicates the model is not overfitting. This means the model will not be sensitive to noise, and therefore more accurate on unseen data when predicting grades for students in the upcoming school year.

   Although Decision Trees had a similar score to Logistic Regression, they do not derive the significance of features in their models, whereas Logistic 
Regression does. We can directly see which features increase the odds of failure among students, and to what extent. This is shown in the loop below, as we can see increased absences and time going out increase the odds of failure. This will help us indicate which survey questions are most crucial to ask our students, to better predict their final grade and allocate our resources to those who need them most. Lastly, although Decision trees are unafected by multicollinearity, we can see that this was not an issue within our dataset, and therefore is not a reason to dismiss regression.

![corr_matrix](https://github.com/sabinabains/dsc-phase-3-project/blob/main/images/corr_matrix.png)


# Conclusion

   Overall, this result is not particularly strong. a testing recall of 67% means that our model will predict that 32% of all students who will fail at the end of the school year will in fact pass, which is not the case. We will need more robust data or different predictors to better assist our stakeholder in determining which  students to provide tutors for.
   
   Our stakeholders should ditribute this test to their students on the first day of school, and continue to gather more data to improve this predictive model. They should also tune the questions to those which provide the most information gain. In this case, number of absences, time spent on activities other than school, and interest in higher educations were some of the strongest features associated with an increased odds of failure. 

# Repository Structure

```
├── data : data used for modeling
├── images : images used in PPT and readme
├── README.md : project information and repository structure
├── Phase 3 Project - Sabina Bains : (Presentation for Stakeholders)
└── dsc-phase-3-project-3.28.22.ipynb (jupyter notebook used for modeling)
```
