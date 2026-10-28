# ML Practical & Viva — 1-Day Survival Guide (Shortest Code Edition) 🎯

**Mumbai University | Sem 7 Computer Engineering | TSEC**

> **Strategy:** 31 questions but they all use the SAME patterns. Learn 8 code templates below and you can solve ANY question from the paper. Every template is under 25 lines.

---

# PART 1: The 8 Code Templates That Cover Everything

---

## Template 1: Linear Regression (Covers Q1-5, Q7)

**Covers:** Advertising dataset regression, comparing features, polynomial, training size effect, Used Cars.

```python
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import PolynomialFeatures
from sklearn.metrics import mean_squared_error, r2_score
import matplotlib.pyplot as plt

# --- LOAD DATA ---
df = pd.read_csv('Advertising.csv')  # columns: TV, Radio, Newspaper, Sales
X = df[['TV', 'Radio', 'Newspaper']]  # change columns as needed
y = df['Sales']

# --- SPLIT ---
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# --- TRAIN ---
model = LinearRegression()
model.fit(X_train, y_train)

# --- PREDICT & EVALUATE ---
y_pred = model.predict(X_test)
print("RMSE:", np.sqrt(mean_squared_error(y_test, y_pred)))
print("R² Score:", r2_score(y_test, y_pred))
print("Coefficients:", model.coef_)
print("Intercept:", model.intercept_)

# --- PLOT ACTUAL vs PREDICTED ---
plt.scatter(y_test, y_pred)
plt.xlabel("Actual"); plt.ylabel("Predicted")
plt.title("Actual vs Predicted"); plt.show()

# ====== POLYNOMIAL REGRESSION (Q2) ======
poly = PolynomialFeatures(degree=2)
X_poly_train = poly.fit_transform(X_train[['TV']])
X_poly_test = poly.transform(X_test[['TV']])
model_poly = LinearRegression().fit(X_poly_train, y_train)
print("Poly R²:", r2_score(y_test, model_poly.predict(X_poly_test)))

# ====== COMPARE INDIVIDUAL vs PAIRS vs ALL FEATURES (Q1) ======
for cols in [['TV'], ['Radio'], ['TV','Radio'], ['TV','Radio','Newspaper']]:
    m = LinearRegression().fit(X_train[cols], y_train)
    print(f"{cols}: R²={r2_score(y_test, m.predict(X_test[cols])):.4f}")

# ====== RESIDUALS (Q4) ======
residuals = y_test - y_pred
print("Top 5 largest residuals:\n", residuals.abs().nlargest(5))
```

---

## Template 2: Decision Tree / Random Forest Regressor (Covers Q6, Q8, Q11)

**Covers:** Used Cars regression, hyperparameter tuning, feature importance.

```python
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.tree import DecisionTreeRegressor
from sklearn.ensemble import RandomForestRegressor
from sklearn.metrics import mean_squared_error, r2_score
from sklearn.preprocessing import LabelEncoder

# --- LOAD & ENCODE CATEGORICALS ---
df = pd.read_csv('used_cars.csv')
le = LabelEncoder()
for col in df.select_dtypes('object').columns:
    df[col] = le.fit_transform(df[col])

X = df.drop('selling_price', axis=1)
y = df['selling_price']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

# --- DECISION TREE ---
dt = DecisionTreeRegressor(random_state=42).fit(X_train, y_train)
print("DT RMSE:", np.sqrt(mean_squared_error(y_test, dt.predict(X_test))))
print("DT R²:", r2_score(y_test, dt.predict(X_test)))

# --- RANDOM FOREST ---
rf = RandomForestRegressor(n_estimators=100, random_state=42).fit(X_train, y_train)
print("RF RMSE:", np.sqrt(mean_squared_error(y_test, rf.predict(X_test))))
print("RF R²:", r2_score(y_test, rf.predict(X_test)))

# --- FEATURE IMPORTANCE (Q11) ---
import matplotlib.pyplot as plt
imp = pd.Series(rf.feature_importances_, index=X.columns).sort_values(ascending=False)
print("Feature Importance:\n", imp)
imp.plot(kind='barh', title='Feature Importance'); plt.show()

# --- HYPERPARAMETER TUNING (Q8) ---
params = {'max_depth': [3, 5, 10, None], 'min_samples_split': [2, 5, 10]}
grid = GridSearchCV(DecisionTreeRegressor(random_state=42), params, cv=5, scoring='r2')
grid.fit(X_train, y_train)
print("Best params:", grid.best_params_)
print("Best R²:", grid.best_score_)
```

---

## Template 3: SVM Classification (Covers Q12-18, Q25)

**Covers:** Linear vs RBF SVM, scaling effect, hyperparameter tuning, SVM vs Logistic Regression, misclassification, decision boundary.

```python
import pandas as pd
import numpy as np
from sklearn.model_selection import train_test_split, GridSearchCV
from sklearn.preprocessing import StandardScaler
from sklearn.svm import SVC
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import (confusion_matrix, classification_report,
                             accuracy_score, f1_score)
import matplotlib.pyplot as plt

# --- LOAD ---
df = pd.read_csv('Social_Network_Ads.csv')
X = df[['Age', 'EstimatedSalary']]
y = df['Purchased']

# --- SCALE (CRITICAL FOR SVM) ---
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)

X_train, X_test, y_train, y_test = train_test_split(
    X_scaled, y, test_size=0.2, random_state=42, stratify=y)

# --- LINEAR SVM ---
svm_lin = SVC(kernel='linear', random_state=42).fit(X_train, y_train)
print("Linear SVM Accuracy:", accuracy_score(y_test, svm_lin.predict(X_test)))
print(classification_report(y_test, svm_lin.predict(X_test)))

# --- RBF SVM ---
svm_rbf = SVC(kernel='rbf', random_state=42).fit(X_train, y_train)
print("RBF SVM Accuracy:", accuracy_score(y_test, svm_rbf.predict(X_test)))
print(confusion_matrix(y_test, svm_rbf.predict(X_test)))

# --- LOGISTIC REGRESSION (Q18 comparison) ---
lr = LogisticRegression(random_state=42).fit(X_train, y_train)
print("LogReg Accuracy:", accuracy_score(y_test, lr.predict(X_test)))

# --- SCALING EFFECT (Q13): train without scaling ---
X_tr_raw, X_te_raw, _, _ = train_test_split(X, y, test_size=0.2, random_state=42, stratify=y)
svm_noscale = SVC(kernel='rbf').fit(X_tr_raw, y_train)
print("Without scaling:", accuracy_score(y_test, svm_noscale.predict(X_te_raw)))

# --- HYPERPARAMETER TUNING (Q14) ---
params = {'C': [0.1, 1, 10, 100], 'gamma': ['scale', 0.01, 0.1, 1]}
grid = GridSearchCV(SVC(kernel='rbf'), params, cv=5, scoring='accuracy')
grid.fit(X_train, y_train)
print("Best params:", grid.best_params_, "Best acc:", grid.best_score_)

# --- DECISION BOUNDARY PLOT (Q16) ---
def plot_boundary(model, X, y, title):
    h = 0.02
    x_min, x_max = X[:,0].min()-1, X[:,0].max()+1
    y_min, y_max = X[:,1].min()-1, X[:,1].max()+1
    xx, yy = np.meshgrid(np.arange(x_min, x_max, h), np.arange(y_min, y_max, h))
    Z = model.predict(np.c_[xx.ravel(), yy.ravel()]).reshape(xx.shape)
    plt.contourf(xx, yy, Z, alpha=0.3)
    plt.scatter(X[:,0], X[:,1], c=y, edgecolors='k')
    plt.title(title); plt.show()

plot_boundary(svm_rbf, X_test, y_test, "RBF SVM Decision Boundary")
```

---

## Template 4: Decision Tree Classifier — Depth & Impurity (Covers Q19-21, Q24)

**Covers:** Iris classification, depth vs overfitting, Gini vs Entropy, ensemble classifiers, feature selection.

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import (RandomForestClassifier, BaggingClassifier,
                              AdaBoostClassifier)
from sklearn.metrics import accuracy_score, f1_score
import matplotlib.pyplot as plt

X, y = load_iris(return_X_y=True)
X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.3, random_state=42, stratify=y)

# --- Q19: DEPTH vs OVERFITTING ---
train_acc, test_acc = [], []
for d in range(1, 11):
    dt = DecisionTreeClassifier(max_depth=d, random_state=42).fit(X_train, y_train)
    train_acc.append(accuracy_score(y_train, dt.predict(X_train)))
    test_acc.append(accuracy_score(y_test, dt.predict(X_test)))

plt.plot(range(1,11), train_acc, label='Train')
plt.plot(range(1,11), test_acc, label='Test')
plt.xlabel('Max Depth'); plt.ylabel('Accuracy')
plt.legend(); plt.title('Depth vs Overfitting'); plt.show()

# --- Q20: GINI vs ENTROPY ---
for crit in ['gini', 'entropy']:
    dt = DecisionTreeClassifier(criterion=crit, max_depth=3, random_state=42)
    dt.fit(X_train, y_train)
    print(f"{crit}: Acc={accuracy_score(y_test, dt.predict(X_test)):.4f}")

# --- Q21: INDIVIDUAL vs ENSEMBLE ---
models = {
    'Decision Tree': DecisionTreeClassifier(random_state=42),
    'Bagging': BaggingClassifier(random_state=42),
    'Random Forest': RandomForestClassifier(random_state=42),
    'AdaBoost': AdaBoostClassifier(random_state=42),
}
for name, m in models.items():
    m.fit(X_train, y_train)
    print(f"{name}: Acc={accuracy_score(y_test, m.predict(X_test)):.4f}  "
          f"F1={f1_score(y_test, m.predict(X_test), average='macro'):.4f}")

# --- Q24: FEATURE IMPORTANCE & SELECTION ---
dt = DecisionTreeClassifier(random_state=42).fit(X_train, y_train)
feat_names = load_iris().feature_names
imp = sorted(zip(feat_names, dt.feature_importances_), key=lambda x: -x[1])
print("Feature Importance:", imp)
top2_idx = [i for i,v in enumerate(dt.feature_importances_)
            if v >= sorted(dt.feature_importances_)[-2]]
dt2 = DecisionTreeClassifier(random_state=42).fit(X_train[:,top2_idx], y_train)
print("Top-2 Acc:", accuracy_score(y_test, dt2.predict(X_test[:,top2_idx])))
```

---

## Template 5: PCA + Classification (Covers Q22-23)

**Covers:** PCA with different components, before vs after PCA comparison.

```python
from sklearn.datasets import load_iris
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
from sklearn.decomposition import PCA
from sklearn.svm import SVC
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score, f1_score

X, y = load_iris(return_X_y=True)
scaler = StandardScaler()
X_scaled = scaler.fit_transform(X)
X_train, X_test, y_train, y_test = train_test_split(
    X_scaled, y, test_size=0.3, random_state=42, stratify=y)

# --- ORIGINAL (4 features) ---
svm = SVC(kernel='rbf').fit(X_train, y_train)
print(f"Original (4 feat): Acc={accuracy_score(y_test, svm.predict(X_test)):.4f}")

# --- PCA: 2 and 3 components ---
for n in [2, 3]:
    pca = PCA(n_components=n)
    X_tr_pca = pca.fit_transform(X_train)
    X_te_pca = pca.transform(X_test)
    svm_pca = SVC(kernel='rbf').fit(X_tr_pca, y_train)
    print(f"PCA ({n} comp): Acc={accuracy_score(y_test, svm_pca.predict(X_te_pca)):.4f}  "
          f"Var explained={sum(pca.explained_variance_ratio_):.4f}")

# --- Q23: COMPARE CLASSIFIERS BEFORE vs AFTER PCA ---
pca95 = PCA(n_components=0.95)   # retain 95% variance
X_tr_pca = pca95.fit_transform(X_train)
X_te_pca = pca95.transform(X_test)
print(f"Components for 95% var: {pca95.n_components_}")

for name, clf in [('SVM', SVC()), ('LogReg', LogisticRegression(max_iter=200))]:
    clf.fit(X_train, y_train)
    acc_orig = accuracy_score(y_test, clf.predict(X_test))
    clf.fit(X_tr_pca, y_train)
    acc_pca = accuracy_score(y_test, clf.predict(X_te_pca))
    print(f"{name}: Original={acc_orig:.4f}  After PCA={acc_pca:.4f}")
```

---

## Template 6: Outlier Detection (Covers Q10)

**Covers:** IQR method for outlier removal in Used Cars.

```python
import pandas as pd
from sklearn.linear_model import LinearRegression
from sklearn.model_selection import train_test_split
from sklearn.metrics import mean_squared_error, r2_score
import numpy as np

df = pd.read_csv('used_cars.csv')

# --- IQR OUTLIER DETECTION ---
Q1 = df['selling_price'].quantile(0.25)
Q3 = df['selling_price'].quantile(0.75)
IQR = Q3 - Q1
lower, upper = Q1 - 1.5 * IQR, Q3 + 1.5 * IQR

outliers = df[(df['selling_price'] < lower) | (df['selling_price'] > upper)]
print(f"Outliers found: {len(outliers)}")

df_clean = df[(df['selling_price'] >= lower) & (df['selling_price'] <= upper)]
print(f"Before: {len(df)} rows | After: {len(df_clean)} rows")
# Then train on both and compare RMSE/R² using Template 2
```

---

## Template 7: Linear Regression FROM SCRATCH (Covers Q26-28)

**Covers:** Normal equation, multiple regression, gradient descent — all WITHOUT sklearn.

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

df = pd.read_csv('Advertising.csv')
X = df['TV'].values.reshape(-1, 1)
y = df['Sales'].values

# Add intercept column (bias term)
X_b = np.c_[np.ones(len(X)), X]  # shape: (n, 2)

# ====== METHOD 1: NORMAL EQUATION (Q26, Q27) ======
# theta = (X^T X)^{-1} X^T y
theta = np.linalg.inv(X_b.T @ X_b) @ (X_b.T @ y)
print(f"Intercept: {theta[0]:.4f}, Slope: {theta[1]:.4f}")

y_pred = X_b @ theta
rmse = np.sqrt(np.mean((y - y_pred)**2))
ss_res = np.sum((y - y_pred)**2)
ss_tot = np.sum((y - np.mean(y))**2)
r2 = 1 - ss_res / ss_tot
print(f"RMSE: {rmse:.4f}, R²: {r2:.4f}")

plt.scatter(X, y, alpha=0.5)
plt.plot(X, y_pred, color='red')
plt.xlabel('TV'); plt.ylabel('Sales'); plt.title('Linear Regression'); plt.show()

# ====== METHOD 2: GRADIENT DESCENT (Q28) ======
m, b = 0.0, 0.0       # initial slope and intercept
lr = 0.0001            # learning rate
epochs = 1000
n = len(X.flatten())
costs = []

for i in range(epochs):
    y_pred = m * X.flatten() + b
    cost = np.mean((y - y_pred)**2)
    costs.append(cost)
    dm = (-2/n) * np.sum(X.flatten() * (y - y_pred))
    db = (-2/n) * np.sum(y - y_pred)
    m -= lr * dm
    b -= lr * db

print(f"GD: Slope={m:.4f}, Intercept={b:.4f}")
plt.plot(costs); plt.xlabel('Iteration'); plt.ylabel('MSE')
plt.title('Cost vs Iterations'); plt.show()
```

---

## Template 8: Logistic Regression FROM SCRATCH (Covers Q29-31)

**Covers:** Binary logistic regression, learning rate effect, multiclass (Iris).

```python
import numpy as np
import pandas as pd
from sklearn.metrics import accuracy_score, confusion_matrix, classification_report
from sklearn.model_selection import train_test_split
from sklearn.preprocessing import StandardScaler
import matplotlib.pyplot as plt

# ====== BINARY LOGISTIC REGRESSION (Q29) ======
df = pd.read_csv('Social_Network_Ads.csv')
X = df[['Age', 'EstimatedSalary']].values
y = df['Purchased'].values

scaler = StandardScaler()
X = scaler.fit_transform(X)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=42)

def sigmoid(z):
    return 1 / (1 + np.exp(-np.clip(z, -500, 500)))

# Add bias
X_tr = np.c_[np.ones(len(X_train)), X_train]
X_te = np.c_[np.ones(len(X_test)), X_test]
weights = np.zeros(X_tr.shape[1])

lr = 0.01
epochs = 1000
losses = []

for i in range(epochs):
    z = X_tr @ weights
    h = sigmoid(z)
    loss = -np.mean(y_train * np.log(h + 1e-8) + (1 - y_train) * np.log(1 - h + 1e-8))
    losses.append(loss)
    grad = (1 / len(y_train)) * (X_tr.T @ (h - y_train))
    weights -= lr * grad

y_pred = (sigmoid(X_te @ weights) >= 0.5).astype(int)
print("Accuracy:", accuracy_score(y_test, y_pred))
print(confusion_matrix(y_test, y_pred))

# ====== LEARNING RATE EFFECT (Q30) ======
for rate in [0.001, 0.01, 0.1]:
    w = np.zeros(X_tr.shape[1])
    loss_hist = []
    for _ in range(500):
        h = sigmoid(X_tr @ w)
        loss = -np.mean(y_train*np.log(h+1e-8) + (1-y_train)*np.log(1-h+1e-8))
        loss_hist.append(loss)
        w -= rate * (X_tr.T @ (h - y_train)) / len(y_train)
    plt.plot(loss_hist, label=f'lr={rate}')
plt.legend(); plt.xlabel('Epoch'); plt.ylabel('Loss')
plt.title('Learning Rate Effect'); plt.show()
```

---

# PART 2: Per-Experiment Viva Attack

---

## Regression (Advertising / Used Cars) Viva

| Q | A |
|---|---|
| What is R² Score? | Proportion of variance in the target explained by the model. R²=1 is perfect; R²=0 means the model is no better than predicting the mean. |
| What is RMSE? | Root Mean Squared Error — average magnitude of prediction error in the same units as the target. Lower is better. |
| Why is R² sometimes negative? | When the model is worse than just predicting the mean — typically underfitting or wrong features. |
| What is the Normal Equation? | Closed-form solution: theta = (X^T X)^{-1} X^T y. Works for small datasets but O(n³) inversion is expensive for large ones. |
| Linear vs Polynomial regression? | Linear fits a straight line. Polynomial adds powers of features (x², x³...) to capture curves. Higher degree → risk of overfitting. |
| What is multicollinearity? | When two features are highly correlated. Makes individual coefficients unreliable but predictions may still be fine. |
| What does adjusted R² fix? | Regular R² always increases with more features. Adjusted R² penalises unnecessary features that don't improve the model. |

## SVM Classification Viva

| Q | A |
|---|---|
| Why must you scale features for SVM? | SVM uses distance-based calculations. If Age is 20-60 and Salary is 20000-80000, salary dominates. Scaling puts both on the same range. |
| Linear vs RBF kernel? | Linear draws a straight boundary. RBF (Gaussian) maps to infinite dimensions and can draw curved/circular boundaries. |
| What is C in SVM? | Regularisation parameter. High C = less tolerance for misclassification (risk overfitting). Low C = wider margin, allows some errors. |
| What is gamma in RBF? | Controls the reach of each training point. High gamma = narrow influence (complex boundary). Low gamma = smooth boundary. |
| What is the kernel trick? | Computing dot products in higher-dimensional space without actually transforming the data — saves computation. |
| What is F1 Score? | Harmonic mean of Precision and Recall: F1 = 2×P×R/(P+R). Better than accuracy for imbalanced datasets. |
| Precision vs Recall? | Precision = of all predicted positive, how many are actually positive. Recall = of all actual positives, how many did we catch. |

## Decision Tree / Ensemble Viva

| Q | A |
|---|---|
| What is overfitting in Decision Trees? | Deep trees memorise training data but fail on unseen data. Limiting max_depth or min_samples_split prevents this. |
| Gini vs Entropy? | Both measure impurity. Gini = 1 - Σpi². Entropy = -Σpi log₂(pi). Gini is faster; usually similar results. |
| What is Bagging? | Train multiple trees on random subsets (with replacement), then average. Reduces variance. |
| What is Random Forest? | Bagging + random feature selection at each split. More diversity → better generalisation. |
| What is AdaBoost? | Sequentially trains weak learners; each focuses on mistakes of the previous. Reduces bias. |
| Bagging vs Boosting? | Bagging = parallel, reduces variance. Boosting = sequential, reduces bias. |
| What is feature importance? | Measures how much each feature reduces impurity across all splits. Higher = more useful feature. |

## PCA Viva

| Q | A |
|---|---|
| What is PCA? | Unsupervised dimensionality reduction. Finds new axes (principal components) that capture the most variance. |
| Why standardise before PCA? | Features with larger scales dominate variance. Standardising ensures equal contribution. |
| What is explained variance ratio? | Fraction of total variance captured by each component. E.g., PC1=0.72 means 72% of information. |
| What does PCA(n_components=0.95) do? | Selects minimum components to retain 95% of total variance. |
| Does PCA improve accuracy? | Not always. Reduces noise (can help), but also removes info (can hurt). |

## From-Scratch Implementation Viva

| Q | A |
|---|---|
| What is the cost function for linear regression? | MSE: J = (1/n)Σ(yi - ŷi)². We minimise this. |
| What is gradient descent? | Iterative optimisation: update weights by subtracting learning_rate × gradient. Gradient points uphill, so we go opposite. |
| What happens if learning rate is too high? | Cost oscillates or diverges — overshoots the minimum. |
| What happens if learning rate is too low? | Convergence is extremely slow — too many iterations. |
| What is the sigmoid function? | σ(z) = 1/(1+e^−z). Maps any number to (0,1). Used in logistic regression for probabilities. |
| What is binary cross-entropy? | L = -(1/n)Σ[y·log(ŷ) + (1-y)·log(1-ŷ)]. Penalises confident wrong predictions heavily. |
| Why np.clip in sigmoid? | Prevents overflow. e^-z explodes for very large negative z, causing NaN. |
| Normal equation vs GD? | Normal: exact, O(n³), no hyperparams. GD: iterative, works for large data, needs learning rate. |

---

# PART 3: Rapid-Fire Viva Q&A (60+ Questions)

---

## Supervised Learning Fundamentals

| # | Q | A |
|---|---|---|
| 1 | Supervised vs Unsupervised? | Supervised = labelled data (classification/regression). Unsupervised = no labels (clustering, PCA). |
| 2 | Classification vs Regression? | Classification = predict category (spam/not). Regression = predict number (price). |
| 3 | What is train/test split? | Split data: training (learn) and test (evaluate). Prevents evaluating on seen data. |
| 4 | What is overfitting? | Model learns noise; high train acc, low test acc. |
| 5 | What is underfitting? | Model too simple; low accuracy on both train and test. |
| 6 | Bias-Variance tradeoff? | High bias = underfitting. High variance = overfitting. Goal: balance both. |
| 7 | What is cross-validation? | Split into k folds, train on k-1, test on 1, rotate k times. More reliable. |
| 8 | What is stratified split? | Each split has same class proportion as original. Important for imbalanced data. |
| 9 | What is random_state? | Seed for reproducibility. Same seed = same split every run. |

## Linear Regression

| # | Q | A |
|---|---|---|
| 10 | Assumptions of linear regression? | Linearity, independence, homoscedasticity, normality of residuals. |
| 11 | What are residuals? | Difference between actual and predicted: e = y - ŷ. |
| 12 | What is the intercept? | Predicted value when all features are zero. The baseline. |
| 13 | What are coefficients? | Weight/slope for each feature. Coefficient of TV=0.05 means +\$1 TV spend → +0.05 sales. |
| 14 | RMSE vs MAE? | RMSE penalises large errors more (squares them). MAE treats all equally. RMSE ≥ MAE always. |
| 15 | When to use polynomial? | When the relationship is curved, not straight. |
| 16 | What is regularisation? | Penalty for large coefficients. Ridge (L2) shrinks; Lasso (L1) can zero them out. |

## SVM

| # | Q | A |
|---|---|---|
| 17 | What is a support vector? | Training points closest to the decision boundary. They define the hyperplane. |
| 18 | What is the margin? | Distance between boundary and nearest support vector. SVM maximises this. |
| 19 | Hard vs soft margin? | Hard = no errors allowed. Soft = allows some via slack variables (C controls). |
| 20 | Can SVM do regression? | Yes, SVR fits data within an ε-tube around the prediction. |
| 21 | Can SVM do multiclass? | Yes, via one-vs-one or one-vs-rest. Sklearn does it automatically. |

## Decision Trees

| # | Q | A |
|---|---|---|
| 22 | How does a tree split? | Picks feature and threshold that maximises information gain / minimises Gini. |
| 23 | What is information gain? | Reduction in entropy after a split. Higher = better split. |
| 24 | What is pruning? | Removing branches that add little value. Pre-pruning (limit depth) or post-pruning. |
| 25 | Advantage of decision trees? | Interpretable, no scaling needed, handles both numerical and categorical. |
| 26 | Disadvantage? | Overfitting, unstable, biased toward features with many levels. |

## Ensemble Methods

| # | Q | A |
|---|---|---|
| 27 | What is ensemble learning? | Combining multiple models for better predictions than any single one. |
| 28 | What is bootstrap sampling? | Sampling with replacement. Some points appear multiple times, ~37% left out. |
| 29 | What are out-of-bag samples? | Points not selected in bootstrap. Used for internal validation in RF. |
| 30 | Random Forest: how many trees? | 100-500 typical. More = stabler but slower, diminishing returns. |

## Metrics

| # | Q | A |
|---|---|---|
| 31 | What is a confusion matrix? | 2×2 table: TP, TN, FP, FN. |
| 32 | Accuracy formula? | (TP+TN)/(TP+TN+FP+FN). Misleading for imbalanced data. |
| 33 | When is accuracy misleading? | Imbalanced data: 95% class-0 → always predict 0 = 95% acc but useless. |
| 34 | Precision formula? | TP/(TP+FP). "Of predicted positive, how many correct?" |
| 35 | Recall formula? | TP/(TP+FN). "Of actual positives, how many caught?" |
| 36 | When prioritise Precision? | FP costly: spam filter (don't mark real email as spam). |
| 37 | When prioritise Recall? | FN costly: cancer detection (don't miss a case). |
| 38 | Macro vs Micro F1? | Macro = avg F1 per class. Micro = global TP/FP/FN counts. |

## PCA & Dimensionality Reduction

| # | Q | A |
|---|---|---|
| 39 | Curse of dimensionality? | More features → sparser data, meaningless distances, need exponentially more data. |
| 40 | PCA vs Feature Selection? | PCA creates new combined features. Feature selection picks existing ones. |
| 41 | PCA for visualisation? | Yes. Reduce to 2-3 components and scatter plot. |
| 42 | Is PCA supervised? | No. Unsupervised — only looks at variance, ignores labels. |

## Preprocessing

| # | Q | A |
|---|---|---|
| 43 | Why scale features? | Distance-based algorithms (SVM, KNN, PCA) are dominated by large-range features. |
| 44 | StandardScaler vs MinMaxScaler? | Standard: mean=0, std=1. MinMax: [0,1]. Standard is more robust to outliers. |
| 45 | What is one-hot encoding? | Categorical → binary columns. Color={R,G,B} → (1,0,0), (0,1,0), (0,0,1). |
| 46 | LabelEncoder vs OneHotEncoder? | Label: integers (0,1,2). OneHot: binary columns. Use OneHot for nominal data. |
| 47 | Why fit scaler on train only? | Prevent data leakage. Test data should be unseen. Fit train, transform both. |
| 48 | What is data leakage? | Test info leaks into training. Good test results, fails in production. |

## Gradient Descent & Optimisation

| # | Q | A |
|---|---|---|
| 49 | What is learning rate? | Step size for updates. Too high → diverge. Too low → slow. |
| 50 | Batch vs Stochastic GD? | Batch: all data per update (stable, slow). SGD: one sample (noisy, fast). Mini-batch: compromise. |
| 51 | What is convergence? | Loss stops decreasing. Model found a minimum. |
| 52 | Global vs local minimum? | Global = absolute lowest. Local = a low point. Linear regression is convex → only global. |
| 53 | Why add 1e-8 in log? | Prevents log(0) = -infinity. Keeps computation stable. |

## Sklearn Specifics

| # | Q | A |
|---|---|---|
| 54 | What is fit()? | Trains the model — learns parameters from data. |
| 55 | What is predict()? | Uses trained model to make predictions on new data. |
| 56 | What is transform()? | Applies learned transformation (scaling, PCA) to data. |
| 57 | fit_transform() vs fit then transform? | Same result but fit_transform is faster (one pass). Use on train only. |
| 58 | What is GridSearchCV? | Exhaustive hyperparameter search with cross-validation. Returns best combo. |
| 59 | What is a Pipeline? | Chains preprocessing + model into one object. Cleaner, prevents leakage. |
| 60 | What does stratify=y do? | Ensures train and test have same class proportions. |

---

# PART 4: Examiner Survival

---

## Top 5 Red Flags

1. **"I used SVM without scaling."** If you can't explain why features must be standardised, the examiner knows you just copied code.
2. **Saying "accuracy" for everything.** If data is imbalanced, say "I used F1-score because accuracy is misleading for imbalanced classes."
3. **Not knowing what fit() and predict() do.** These are the two most basic sklearn concepts.
4. **Confusing Precision and Recall.** Say: "Precision = of my predicted positives, how many were correct. Recall = of actual positives, how many did I catch."
5. **Can't explain your own confusion matrix.** If your code prints a matrix, know which cell is TP, FP, FN, TN.

## Pivot Strategy

- **Don't know a specific algorithm?** → "That algorithm's core idea is similar to [thing you know]. For example, in SVM we find the optimal boundary by maximising the margin..."
- **Asked about math you forgot?** → "The key intuition is..." then explain in plain English with an example.
- **Asked about a library function?** → Pull up your code and say "Let me show you what happens when I change this parameter..."

## Morning Checklist

- [ ] Can I write 15-line sklearn regression? (load → split → fit → predict → RMSE/R²)
- [ ] Can I write 15-line sklearn SVM? (load → scale → split → SVC → accuracy → confusion_matrix)
- [ ] Can I write gradient descent from scratch in 10 lines?
- [ ] Can I explain confusion matrix (TP/FP/TN/FN)?
- [ ] Can I explain why scaling is important for SVM?
- [ ] Can I explain overfitting and how max_depth prevents it?
- [ ] Do I know Gini vs Entropy?
- [ ] Can I explain PCA in 2 sentences?

**You've got this. Go ace it.** 🎯
