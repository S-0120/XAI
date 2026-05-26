# XC-MD
A malware detector that also explains the cause of malware for both windows and android.

# Overview
XC-MD addresses two critical gaps in modern malware detection:

Platform fragmentation — most existing tools target either Android or Windows, not both
Black-box verdicts — even the best models offer no explanation for their decisions

XC-MD solves both by combining a stacking ensemble of Random Forest, XGBoost and LightGBM under a Logistic Regression meta-learner, paired with SHAP global explanations and LIME local justifications — all within one unified framework.

# Results
| Platform | Dataset | Accuracy | AUC |
|---|---|---|---|
| Android | CICMalDroid 2020 | 95.22% | 99.64% |
| Windows | Ember 2018 | 96.74% | 99.55% |

## Project Structure

```
XC-MD/
│
├── 📓 notebooks/
│   ├── Android_Pipeline.ipynb
│   ├── Windows_Pipeline.ipynb
│   └── Cross_Platform_Analysis.ipynb
│
├── 🖥️ app/
│   └── app.py
│
├── 🤖 models/
│   ├── rf_base.pkl
│   ├── xgb_base.pkl
│   ├── lgbm_base.pkl
│   └── lr_meta_tuned.pkl
│
├── 📊 datasets/
│   ├── preprocessed/
│   └── ember_preprocessed/
│
├── 🧪 demo/
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
├── requirements.txt
└── README.md
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

# Installation
bash# Clone the repository
git clone https://github.com/yourusername/XC-MD.git<br>
cd XC-MD

# Install dependencies
pip install -r requirements.txt

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

# Running the Demo
Hugging Face Spaces:
Live demo available at: https://sheeesshhhh-xc-md.hf.space/

# How to Use the Demo

Open the app and select Android APK or Windows EXE tab 
Upload a feature CSV file — use the provided demo samples in /demo/ to test
OR click Demo buttons to check the demos
View the prediction verdict, confidence scores and SHAP feature importance chart

Input format: Single row CSV containing the feature vector extracted from the file. Android expects 100 system call frequency features. Windows expects 100 PE structural features.

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

# Acknowledgements

CICMalDroid 2020 dataset — Canadian Institute for Cybersecurity<br>
Ember 2018 dataset — Anderson et al., Endgame Inc.<br>
SHAP — Lundberg and Lee, 2017<br>
LIME — Ribeiro et al., 2016<br>
