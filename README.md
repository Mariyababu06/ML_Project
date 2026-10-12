# Student Performance Predictor

An end-to-end machine learning project that predicts a student's **math score** from their reading and writing scores and a few background factors. It covers data preprocessing, training and tuning of several regression models, and a Flask web app deployed on AWS.

**Live demo:** [FILL: AWS URL, or remove this line]

---

## Problem

Schools and tutors usually spot struggling students only after exam results arrive, which is often too late to help. Marks in one subject also tend to move with marks in others, and with factors such as test preparation and parental education, but nobody checks that by hand for every student.

This project asks a simple question: **given a student's reading score, writing score and background details, can we estimate their math score before the exam?**

## Solution

- Cleaned and prepared 1,000+ student records with a reusable preprocessing pipeline.
- Trained 7 regression algorithms and tuned each one with grid search and cross-validation.
- Picked the best model by R-squared on a held-out test set (**CatBoost, R-squared 0.88**).
- Wrapped the model in a Flask app so anyone can enter details in a web form and get a prediction.
- Deployed the app on AWS Elastic Beanstalk.

## Benefits

- **Early warning:** a teacher or tutor can flag students likely to score low and offer support sooner.
- **Insight into what matters:** shows how reading and writing scores, test preparation, lunch type and parental education relate to math performance.
- **No coding needed to use it:** the web form lets non-technical users get predictions in seconds.
- **Reproducible:** one preprocessing pipeline is used for both training and prediction, so the app handles new input exactly the way the model was trained.
- **Easy to maintain:** modular code, logging and custom exception handling make failures easier to trace.

## Input and Output

| | Details |
|---|---|
| **Inputs** | gender, race/ethnicity, parental level of education, lunch type, test preparation course, reading score, writing score |
| **Output** | predicted math score |

## Tech Stack

- **Language:** Python
- **Data and ML:** Pandas, NumPy, Scikit-learn, CatBoost, XGBoost
- **Web app:** Flask
- **Deployment:** AWS Elastic Beanstalk, AWS CodePipeline (CI/CD)
- **Tools:** Git, GitHub, Jupyter Notebook

## How It Works

1. **Data ingestion:** reads the dataset and splits it into train and test sets.
2. **Data transformation:** numeric columns get median imputation and scaling. Categorical columns get most-frequent imputation, one-hot encoding and scaling. The fitted preprocessor is saved to `artifacts/`.
3. **Model training:** trains Linear Regression, Decision Tree, Random Forest, Gradient Boosting, AdaBoost, XGBoost and CatBoost. Each is tuned with `GridSearchCV` (3-fold cross-validation) and scored with R-squared on the test set.
4. **Model selection:** the best-scoring model is saved as `artifacts/model.pkl`. Training stops with an error if no model reaches R-squared 0.6.
5. **Prediction pipeline:** the Flask app loads the saved preprocessor and model, transforms the form input and returns the predicted score.

## Results

| Metric | Value |
|---|---|
| Best model | CatBoost Regressor |
| R-squared (test set) | 0.88 |
| Models compared | 7 |

## Project Structure

```
ML_Project/
├── .ebextensions/        # AWS Elastic Beanstalk configuration
├── artifacts/            # saved model and preprocessor
├── src/
│   ├── components/       # data ingestion, transformation, model trainer
│   ├── pipeline/         # prediction pipeline
│   ├── app.py
│   ├── exception.py      # custom exception handling
│   ├── logger.py         # logging setup
│   └── utils.py          # save/load objects, model evaluation
├── templates/            # HTML pages for the web app
├── application.py        # Flask entry point
├── Procfile
├── requirements.txt
└── setup.py
```

## Run Locally

```bash
git clone https://github.com/Mariyababu06/ML_Project.git
cd ML_Project
python -m venv venv
venv\Scripts\activate          # on Mac/Linux: source venv/bin/activate
pip install -r requirements.txt

# train the model (creates files in artifacts/)
python src/components/data_ingestion.py

# start the web app
python application.py
```

Then open `http://127.0.0.1:5000` in your browser.

## Deployment

The Flask app runs on AWS Elastic Beanstalk, configured through `.ebextensions` and `Procfile`. AWS CodePipeline redeploys the app when new code is pushed to GitHub.

## Limitations

- The dataset is small (1,000+ records), so the score may not hold on very different student groups.
- The model predicts from the listed features only and does not explain individual cases.
- Predictions are estimates meant to support teachers, not to replace their judgement.

## Future Improvements

- Use a separate validation set (or nested cross-validation) for model selection, keeping the test set only for the final score.
- Add feature importance charts to show which factors drive a prediction.
- Add automated tests and a Docker setup.

## Author

**Mariya Babu**
[LinkedIn](https://www.linkedin.com/in/mariya-babu-854331257) | [GitHub](https://github.com/Mariyababu06)
