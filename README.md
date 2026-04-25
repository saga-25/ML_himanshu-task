# HMDA Mortgage Approval Case Study: Data Strategy & Methodology

This document outlines the strategic approach and methodology used to build the mortgage approval classification pipeline, addressing the specific nuances of the Home Mortgage Disclosure Act (HMDA) dataset.

## 1. Data Acquisition & The "Classic Data Science Trap"

When working with HMDA data, selecting the correct subset is critical. A common mistake is selecting the default option for **"Mortgages for first lien, owner-occupied, 1-4 family homes"** or **"All originated mortgages."**

### Why this is a trap:
*   **Missing Negative Class:** The "All originated mortgages" subset completely removes the negative class (denied applications). This makes it mathematically impossible to train a binary classifier to predict rejections.
*   **Target Imbalance:** To build a robust model, we require the full spectrum of applications, including denials and originations, to engineer a valid binary target variable ($1$ for approved/originated, $0$ for denied).

**Strategy:** We utilize the "All records" setting to ensure both outcomes are represented, allowing the model to learn the boundary between approval and denial.

## 2. Geographic & Temporal Scope

### Geographic Focusing (State-Level)
Instead of using the "Nationwide" dataset, this project focuses on a single, economically diverse state (e.g., **Texas, California, or New York**). 
*   **Resource Efficiency:** A nationwide file can exceed 15 million rows and over 1 GB, leading to RAM bottlenecks in standard Jupyter environments.
*   **Variance Control:** Focusing on a specific state inherently controls for macroeconomic variables and regional housing market variances. This allows the model to capture localized lending behaviors without requiring complex geographical feature engineering.

### Temporal Focus
While data for 2015-2017 is available, the most recent consistent historical year (**2017**) is prioritized to ensure the most relevant historical lending patterns are captured.

## 3. Data Format & Variable Selection

We utilize **"Plain language labels and HMDA codes"** rather than raw codes.
*   **Stakeholder Clarity:** This ensures that EDA results, feature importance plots, and SHAP (SHapley Additive exPlanations) summaries are immediately readable to both technical and business stakeholders during the interview process.
*   **Process Transparency:** It eliminates the need for complex external mapping dictionaries during the development phase.

## 4. Modeling Framework: Predictive Power vs. Transparency

The project explicitly contrasts two modeling philosophies:

### A. High-Performance Classifier (XGBoost/LightGBM)
*   **Objective:** Maximize predictive accuracy.
*   **Technique:** Gradient boosting frameworks are used to capture non-linear relationships, handle missing data patterns, and account for complex interactions between applicant demographics and financial data.
*   **Imbalance Handling:** Implementation of class weighting or SMOTE to address the fact that mortgage denials are typically a minority class.

### B. Glass Box Classifier (Logistic Regression / Decision Trees / EBMs)
*   **Objective:** Absolute transparency and explainability.
*   **Technique:** 
    *   **Logistic Regression with L1 (Lasso):** To enforce sparsity and identify the minimal set of predictive features.
    *   **Decision Trees:** Limited depth for visual auditability.
    *   **Explainable Boosting Machines (EBMs):** Advanced viewpoint that provides the power of GAMs with the exact interpretability of local feature contributions.

## 5. Next Steps: Evolving the Pipeline (2018+ FFIEC Data)

A critical consideration for future iterations is the migration of HMDA data to the **FFIEC platform (2018 onward)**.
*   **Expanded Features:** Post-2018 data includes highly predictive variables such as exact **Debt-to-Income (DTI) ratios**, detailed **Property Values**, and **Credit Scoring models**.
*   **Strategic Shift:** Sourcing this newer data would fundamentally alter the feature engineering strategy, moving away from proxy-based engineering toward direct utilization of the primary drivers of real-world lending decisions.
