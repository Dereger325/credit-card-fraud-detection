# Credit Card Fraud Detection

Detecting fraudulent transactions with 88.8% recall at extreme class imbalance (0.17% fraud rate, 577:1 ratio).

## Results

| Model | Accuracy | Precision | Recall | F1 | PR-AUC |
|-------|----------|-----------|--------|-----|--------|
| Baseline | 99.83% | 0% | 0% | 0% | 0.50 |
| Naive XGB | 99.95% | 91.6% | 77.6% | 84% | - |
| SMOTE XGB | 99.73% | 38.1% | 87.8% | 53% | - |
| Undersampled XGB | 95.63% | 3.5% | 90.8% | 6.7% | - |
| Combined XGB | 99.71% | 36.1% | 89.8% | 51% | - |
| **Weighted XGB (optimal)** | **99.87%** | **58.8%** | **88.8%** | **70.7%** | **0.87** |

**At optimal threshold (0.28):**
- Catches 87 out of 98 frauds (88.8%)
- Only 11 missed frauds (vs 22 for naive)
- 61 false alarms (vs 156 for resampling)
- Total cost: $14,050 (vs $98,000 baseline)
- Savings: $83,950 (85.7%)

## Key Insights

### Extreme Imbalance Challenge
- 0.17% fraud rate (492 frauds in 284,807 transactions)
- 577:1 imbalance ratio
- Accuracy is meaningless (99.83% by predicting "all legitimate")

### Top Fraud Predictors
1. V14 (importance: 48.3%) - dominates all other features
2. V17 (5.6%)
3. V4 (4.5%)
4. V12 (3.4%)
5. Amount (2.5%) - surprisingly low importance

### Optimal Approach: Class Weights
- scale_pos_weight=100 beats resampling (SMOTE/undersampling)
- Better precision (58.8% vs 36%) with comparable recall
- No data manipulation - cleaner for production

### Threshold Optimization
- Default 0.5 is wrong for fraud detection
- Optimal: 0.28 (business cost-driven)
- Saves $1,900 per batch vs default threshold

## Dataset

- **Source:** [Kaggle - Credit Card Fraud Detection](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
- **Size:** 284,807 transactions (492 frauds, 0.17%)
- **Features:** 30 (Time, Amount, V1-V28)
- **V1-V28:** PCA-transformed (anonymized for privacy)
- **Time span:** 2 days of transactions

## Tech Stack

- Python 3.x, Pandas, NumPy
- Scikit-learn - preprocessing, metrics, train/test split
- XGBoost - gradient boosted trees with class weights
- imbalanced-learn - SMOTE, undersampling
- Matplotlib, Seaborn - visualization

## Project Structure
```
├── fraud_detection.ipynb    # Main analysis notebook
├── README.md                # This file
├── requirements.txt         # Dependencies
└── .gitignore
```

## Usage
```bash
# Clone repository
git clone https://github.com/Dereger325/credit-card-fraud-detection.git
cd credit-card-fraud-detection

# Install dependencies
pip install -r requirements.txt

# Download dataset from Kaggle (143 MB)
# https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud

# Run notebook
jupyter notebook fraud_detection.ipynb
```

## Model Performance Details

### Confusion Matrix (Threshold = 0.28)
```
                Predicted
              Legit   Fraud
Actual Legit [56803]  [61]   ← 61 false alarms
Actual Fraud  [11]    [87]   ← Caught 87/98 (88.8%)
```

### Why Class Weights Beat Resampling

**Class Weights (scale_pos_weight=100):**
- 88.8% recall, 58.8% precision
- Only 61 false alarms
- Trains on real data distribution
- Better precision-recall balance

**Resampling (SMOTE/Undersampling):**
- 90%+ recall
- 3-38% precision (poor)
- 156+ false alarms
- Trains on synthetic/reduced data

### PR Curve vs ROC Curve

- **ROC-AUC: 0.9749** - looks great but misleading
- **PR-AUC: 0.8705** - more honest for imbalanced data
- PR curve directly shows precision-recall tradeoff
- Always use PR-AUC for rare event detection

## Key Learnings

1. Accuracy is a trap for imbalanced data (99.83% baseline catches zero frauds)
2. Default threshold (0.5) is arbitrary - optimize for business cost
3. Class weights > resampling for production (cleaner, better precision)
4. PR-AUC > ROC-AUC for honest evaluation of imbalanced problems
5. Feature importance guides investigation - V14 is the smoking gun

## Week 2 of Month 2 - ML Learning Journey

**Days 1-6 breakdown:**
- Day 1: Load data, extreme imbalance analysis (577:1)
- Day 2: Naive models fail (77% recall, miss 22 frauds)
- Day 3: SMOTE + undersampling (90% recall, 3-38% precision)
- Day 4: Class weights optimal (85% recall, 81% precision)
- Day 5: Threshold tuning (88.8% recall at 0.28)
- Day 6: PR curve analysis, feature importance, final evaluation

**Time invested:** 30-45 min/day × 6 days = 3-4.5 hours  
**Outcome:** Production-ready fraud detection with 85.7% cost reduction

## Business Impact

**Per 57K transactions:**
- Baseline (no model): $98,000 in fraud losses
- Our model: $14,050 total cost
- Net savings: $83,950
- ROI: 6x

**Operational metrics:**
- Flag 0.26% of transactions (148 per batch)
- Investigation team handles 61 false alarms + 87 real frauds
- Miss only 11 frauds (11.2% slip-through)

## Next Steps

- Deploy model to production
- A/B test threshold variations
- Monthly retraining pipeline
- Monitor PR-AUC drift
- Investigate V14 feature engineering opportunities

---

**Author:** [David]  
**Part of:** 4-year ML Engineering Roadmap (Month 2, Week 2)  
**GitHub:** [github.com/Dereger325](https://github.com/Dereger325)

Built to handle extreme imbalance - where 99.83% accuracy means nothing.