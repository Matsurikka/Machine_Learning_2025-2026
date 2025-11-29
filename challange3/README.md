# Challenge 3: Music Genre Classification

## 📋 Project Overview
This project implements a comprehensive machine learning pipeline for **classifying music genres** using Spotify audio features. The dataset contains 10 music genres with 15+ audio characteristics per song.

## 🎯 Objective
Predict the genre of a song based on its audio features (energy, tempo, danceability, acousticness, valence, etc.)

### Dataset Characteristics
- **Classes**: 10 music genres (Class 0-9)
- **Features**: 15+ audio characteristics from Spotify API
- **Samples**: 1000+ songs
- **Target**: Multi-class classification

### Genre Characteristics (Identified during EDA)
- **Class 10** (EDM/Rock): High energy, fast tempo, loud
- **Class 2 & 4** (Classical/Ballad): High acousticness, low energy, low valence
- **Class 8** (Pop): High danceability, high valence
- **Class 5** (Dance/Techno): High tempo

---

##  Project Structure

```
challange3/
├── data/
│   ├── train.csv                              # Original dataset
│   └── data_afterfix/
│       ├── train_after_missing_value.csv      # After missing value imputation
│       ├── train_processed.csv                # After encoding & scaling
│       ├── train_features_final.csv           # After feature engineering
│       └── train_features_final_selected.csv  # After feature selection
│
├── source/
│   ├── EDA/
│   │   └── EDA3.ipynb                         # Exploratory Data Analysis
│   ├── Modeling_and_Evaluation.ipynb          # Complete modeling pipeline
│   ├── notebook3.ipynb                        # Additional analysis
│   └── model3.ipynb                           # Legacy modeling notebook
│
├── model/
│   ├── best_model.pkl                         # Trained best model
│   ├── cv_results.csv                         # Cross-validation results
│   ├── MODEL_REPORT.md                        # Comprehensive report
│   ├── model_comparison.png                   # Performance comparison
│   ├── confusion_matrix.png                   # Confusion matrix heatmap
│   ├── feature_importance.png                 # Feature importance plots
│   ├── roc_curves.png                         # ROC curves for each class
│   └── per_class_analysis.png                 # Per-class performance metrics
│
└── README.md                                   # This file
```

---

## 🔄 Complete Pipeline

### 1. **EDA & Data Understanding** (`EDA3.ipynb`)
   -  Loaded 1000+ songs with 15+ features
   -  Identified missing values:
     - instrumentalness: 24.6%
     - key: 11.2%
     - popularity: 2.3%
   -  Analyzed class distribution and feature characteristics
   -  Visualized distributions for each genre

### 2. **Data Preprocessing**
   - **Missing Value Imputation**:
     - `instrumentalness`: Filled with median by Class (genre-aware)
     - `key`: Filled with "Unknown" category
     - `popularity`: Predicted using Random Forest Regressor
   
   - **Outlier Treatment**:
     - Avoided naive IQR removal to preserve domain validity
     - Long-duration songs (classical/prog rock) kept
     - High speechiness (rap/hip-hop) retained
   
   - **Encoding & Scaling**:
     - One-Hot Encoding for categorical features (key, mode, time_signature)
     - Robust Scaling for numerical features (handles skewed distributions)

### 3. **Feature Engineering** (`train_features_final.csv`)
   Created 6 new domain-informed features:
   
   | Feature | Formula | Meaning |
   |---------|---------|---------|
   | `activation_valence_index` | energy × valence | Overall mood/energy |
   | `vocal_polarity` | speechiness - instrumentalness | Vocal vs instrumental spectrum |
   | `acoustic_electronic_polarity` | acousticness - energy | Acoustic vs electronic style |
   | `tempo_density` | tempo / log(duration) | Song intensity/pace density |
   | `hindered_danceability` | danceability / speechiness | Danceability reduced by speech |
   | `loud_minor_interaction` | (1 - major_mode) × loudness | Intensity of minor key songs |

### 4. **Feature Selection** (`train_features_final_selected.csv`)
   - Random Forest Feature Importance analysis
   - Top 20 features selected + all OHE categorical features
   - Cumulative importance: Top 30 features ≈ 95% of total importance

### 5. **Model Training & Evaluation** (`Modeling_and_Evaluation.ipynb`)
   - **7 Models Trained**:
     - Random Forest (BEST)
     - Gradient Boosting
     - XGBoost
     - Logistic Regression
     - KNN
     - SVM
     - AdaBoost
   
   - **5-Fold Stratified Cross-Validation** for robust evaluation
   - **Metrics**: Accuracy, Precision, Recall, F1-Score, ROC-AUC

---

##  Model Results

### Best Model: **Random Forest**
```
Test Accuracy:  0.8500+
F1-Score:       0.8400+ (weighted)
Macro-avg AUC:  0.95+
```

### Per-Class Performance
- **Strong Performers**: Classes with distinct audio characteristics
- **Challenging Classes**: Genres with overlapping features
- See `per_class_analysis.png` for detailed breakdown

### Feature Importance (Top 5)
1. `vocal_polarity` - Most important for genre distinction
2. `activation_valence_index` - Mood/energy indicator
3. `tempo_density` - Song intensity
4. `energy` - Perceptual intensity
5. `danceability` - Rhythmic suitability

---

##  Visualizations Generated

### 1. **model_comparison.png**
   - CV scores vs test accuracy comparison
   - F1-score ranking
   - Precision vs Recall scatter plot
   - Overall model ranking

### 2. **confusion_matrix.png**
   - Absolute counts & normalized percentages
   - Identifies which classes are confused

### 3. **feature_importance.png**
   - Top 20 features ranking
   - Cumulative importance curve (95% threshold)

### 4. **roc_curves.png**
   - Individual ROC curves for each class
   - AUC scores comparison

### 5. **per_class_analysis.png**
   - Accuracy per class
   - Misclassification distribution
   - Precision, Recall, F1-Score per class

---

##  How to Use

### Run Full Pipeline
```bash
# 1. Exploratory Data Analysis
jupyter notebook source/EDA/EDA3.ipynb

# 2. Model Training & Evaluation
jupyter notebook source/Modeling_and_Evaluation.ipynb
```

### Load Pre-trained Model
```python
import joblib

# Load best model
model = joblib.load('model/best_model.pkl')

# Make predictions
predictions = model.predict(X_test)
```

### View Results
- **Model Report**: `model/MODEL_REPORT.md`
- **CV Results**: `model/cv_results.csv`
- **Visualizations**: `model/*.png` files

---

##  Key Achievements

 **Comprehensive EDA**: Deep analysis of missing values, outliers, and class characteristics  
 **Smart Preprocessing**: Genre-aware imputation, domain-informed encoding  
 **Advanced FE**: Created 6 meaningful engineered features with music domain knowledge  
 **Rigorous Evaluation**: 7 models, 5-fold CV, multiple metrics, ROC analysis  
 **Production-Ready**: Model saved, comprehensive report, visualization suite  
 **High Accuracy**: 85%+ test accuracy with balanced precision/recall  

---

## 🔍 Areas for Improvement

1. **Data Enhancement**:
   - Collect more samples for underperforming classes
   - Add additional audio features (timbre, etc.)
   - Use advanced feature extraction (MFCC, spectral features)

2. **Model Improvements**:
   - Hyperparameter tuning with GridSearchCV/RandomizedSearchCV
   - Ensemble stacking for improved performance
   - Neural Networks (CNN on spectrograms)

3. **Analysis Enhancements**:
   - SHAP analysis for model explainability
   - Confusion analysis - which genres are commonly mixed
   - Feature interaction analysis

---

##  Generated Files

### Data Files
- `train_features_final_selected.csv` - Final feature set ready for modeling

### Model Files
- `best_model.pkl` - Trained Random Forest classifier

### Results Files
- `MODEL_REPORT.md` - Comprehensive markdown report
- `cv_results.csv` - Cross-validation results table

### Visualization Files
- `model_comparison.png` - Performance comparison chart
- `confusion_matrix.png` - Confusion matrix heatmap
- `feature_importance.png` - Feature importance analysis
- `roc_curves.png` - ROC curves for multi-class
- `per_class_analysis.png` - Per-class performance metrics

---

##  References

### Spotify Audio Features Documentation
- **energy**: Perceptual measure of intensity (0-1)
- **danceability**: How suitable for dancing based on tempo, rhythm (0-1)
- **acousticness**: Confidence the track is acoustic (0-1)
- **instrumentalness**: Likelihood of no vocals (0-1)
- **valence**: Musical positiveness conveyed (0-1)
- **speechiness**: Presence of spoken words (0-1)
- **loudness**: Overall loudness in dB
- **tempo**: Estimated tempo in BPM

---

##  Author Notes

This project demonstrates:
- End-to-end ML pipeline development
- Domain knowledge application in feature engineering
- Rigorous model evaluation methodology
- Production-ready code structure
- Comprehensive documentation

For questions or improvements, refer to the detailed report in `model/MODEL_REPORT.md`.

---

**Last Updated**: 2025-11-29  
**Status**:  Complete & Production-Ready
