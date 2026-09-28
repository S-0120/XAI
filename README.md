# XC-MD
A malware detector that also explains the cause of malware for both windows and android.

# Overview
XC-MD addresses two critical gaps in modern malware detection:

Platform fragmentation — most existing tools target either Android or Windows, not both
Black-box verdicts — even the best models offer no explanation for their decisions

XC-MD solves both by combining a stacking ensemble of Random Forest, XGBoost and LightGBM under a Logistic Regression meta-learner, paired with SHAP global explanations and LIME local justifications — all within one unified framework.

# Project demo
Watch the XC-MD Demo to see how it actually works 
(XC-MD is a novel work and is not yet applicable for real world data in this demo video I 've used my own generated raw data from my used dataset)

https://github.com/user-attachments/assets/ab0a9c4a-e67f-4f93-8f0a-e01e6ad2e0e1

# Results
| Platform | Dataset | Accuracy | AUC |
|---|---|---|---|
| Android | CICMalDroid 2020 | 95.22% | 99.64% |
| Windows | Ember 2018 | 96.74% | 99.55% |

## Project Structure

```
XC-MD/
│
├── 🧪 demo test files/
│   ├── demo_adware.csv
│   ├── demo_banking.csv
│   ├── demo_sms.csv
│   ├── demo_riskware.csv
│   ├── demo_benign.csv
│   ├── demo_windows_benign.csv
│   └── demo_windows_malicious.csv
│
├── 📈 results/
│   ├── shap_beeswarm_sms.png
│   ├── shap_beeswarm_adware_lgbm.png
│   ├── shap_vs_lime_comparison.png
│   ├── cross_platform_shap_comparison.png
│   ├── combined_confusion_matrix.png
│   ├── combined_roc_curves.png
│   └── complete_results.json
│
├──  README.md
├──  XAI_Project.ipynb
│    ├── Main project notebook with Android dataset preprocessing, model training, explainability and Streamlit UI  
├──  ember.ipnynb
│    ├── Windows complete workflow, dataset preprocessing, model training and explainability
```


## Architecture

```
         Raw Feature Vector
                 │
                 ▼
    ┌─── Feature Selection ───┐
    │   Mutual Information    │
    │      Top 100            │
    └────────────┬────────────┘
                 │
       ┌─────────┼─────────┐
       ▼         ▼         ▼
  ┌─────────┐ ┌───────┐ ┌─────────┐
  │  Random │ │XGBoost│ │LightGBM │
  │  Forest │ │       │ │         │
  └────┬────┘ └───┬───┘ └────┬────┘
       └──────────┼──────────┘
                  ▼
     ┌────────────────────────┐
     │      Meta-Features     │
     │  15-dim (Android)      │
     │   6-dim (Windows)      │
     └────────────┬───────────┘
                  ▼
     ┌────────────────────────┐
     │   Logistic Regression  │
     │      Meta-learner      │
     └────────────┬───────────┘
                  ▼
     ┌────────────────────────┐
     │    Final Prediction    │
     └────────────┬───────────┘
                  ▼
       ┌──────────┴──────────┐
       ▼                     ▼
  ┌─────────┐          ┌─────────┐
  │  SHAP   │          │  LIME   │
  │ Global  │          │  Local  │
  └─────────┘          └─────────┘
```

# Datasets
| Dataset | Platform | Samples | Features | Classes |
|---|---|---|---|---|
| CICMalDroid 2020 | Android | 11,598 | 470 (→100 after MI) | 5 |
| Ember 2018 | Windows | — | 2,381 (→100 after MI) | 2 |

Android classes: Adware, Banking Malware, SMS Malware, Riskware, Benign<br>
Windows classes: Benign, Malicious


# Requirements
streamlit<br>
scikit-learn>=1.3<br>
xgboost>=2.0<br>
lightgbm>=4.0<br>
shap>=0.44<br>
lime<br>
numpy<br>
pandas<br>
matplotlib<br>
joblib<br>
tensorflow>=2.15<br>
imbalanced-learn<br>
pyarrow


# XAI Explainability
XC-MD integrates two complementary explanation methods:<br>
SHAP (Global) — identifies which features most strongly influence predictions across the entire dataset. Uses TreeExplainer for exact Shapley value computation on XGBoost base learner.<br>
LIME (Local) — explains individual predictions by approximating the model's decision boundary locally around a specific sample.

# Key findings:

Android malware is characterised by system call patterns — getDeviceId, sendto, mprotect<br>
Windows malware is driven by PE structural features — section entropy, virtual size ratios<br>
Zero feature overlap between platforms confirms platform-specific malware signatures<br>


# Experimental Environment
| Component | Details |
|---|---|
| Platform | Google Colaboratory |
| GPU | NVIDIA T4 (16GB VRAM) |
| RAM | 12GB |
| Python | 3.12 |
| scikit-learn | 1.3 |
| XGBoost | 2.0 |
| LightGBM | 4.0 |
| TensorFlow | 2.15 |
| SHAP | 0.44 |

# Research Paper
This project accompanies the research paper:
"XC-MD: eXplainable Cross-Platform Malware Detection Using Stacking Ensemble Learning and Interpretable AI"
Submitted to IEEE conference. Full paper available in this repository.

# Team
| Member | Contribution |
|---|---|
| Shikha | Android pipeline, stacking ensemble, SHAP/LIME, GUI |
| Ananya | Windows pipeline, Ember preprocessing, Windows evaluation |
| Mukesh Kumar | Guide

# Acknowledgements

CICMalDroid 2020 dataset — Canadian Institute for Cybersecurity<br>
Ember 2018 dataset — Anderson et al., Endgame Inc.<br>
SHAP — Lundberg and Lee, 2017<br>
LIME — Ribeiro et al., 2016<br>
