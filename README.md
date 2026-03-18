Certamente! Questo è un progetto eccellente, molto ben definito e perfetto per far toccare con mano agli studenti le differenze tra SML (Streaming Machine Learning) e CL (Continual Learning), introducendoli al paradigma SCL (Streaming Continual Learning). 

Avendo a disposizione il paper originale, ho estratto i dettagli implementativi cruciali (come la pre-elaborazione delle immagini MNIST tramite UMAP a 30 componenti e la dimensione della rolling window per la valutazione prequenziale) per assicurarmi che gli studenti abbiano tutte le specifiche per riprodurre fedelmente l'esperimento.

Ecco la struttura del README in formato Markdown, scritta in inglese per mantenere la coerenza con il materiale del corso:

***

# 2025/2026: (8) Reproducing "A Practical Guide to Streaming Continual Learning" in CapyMOA

Optional project of the [Streaming Data Analytics](https://emanueledellavalle.org/teaching/streaming-data-analytics-2025-26/) course provided by [Politecnico di Milano](https://www11.ceda.polimi.it/schedaincarico/schedaincarico/controller/scheda_pubblica/SchedaPublic.do?&evn_default=evento&c_classe=837284&__pj0=0&__pj1=36cd41e96fcd065c47b49d18e46e3110).

Student: **[To be assigned]**

_____
# Brief Description
This project requires you to reproduce the core experiments from the [2026 paper](https://arxiv.org/abs/2603.01677) *"A Practical Guide to Streaming Continual Learning"* by Cossu et al. The paper highlights a fundamental dichotomy in learning from data streams: while **Streaming Machine Learning (SML)** focuses on rapid adaptation to concept drifts, **Continual Learning (CL)** aims to retain past knowledge and mitigate catastrophic forgetting.

Your primary objective is to replicate the paper's comparison between SML and CL methodologies. However, you will modernize the implementation by porting the data stream handling and the SML components from their original `River` implementation to the [CapyMOA framework](https://capymoa.org/index.html). For the Continual Learning strategies, you will continue to use the [Avalanche library](https://avalanche.continualai.org/).

You will use two MNIST-based data streams simulating both **Virtual Drift** and **Real Drift** scenarios. Using these streams, you will evaluate how an SML baseline (Adaptive Random Forest) compares against various CL strategies (Experience Replay, A-GEM) in terms of predictive performance and knowledge retention.

______

# Background: SML vs. CL and Concept Drifts
Learning from evolving data streams implies dealing with **concept drifts**—unforeseeable changes in the underlying data distribution $P(X_t, y_t)$.

* **Streaming Machine Learning (SML)** prioritizes rapid adaptation. When a drift occurs, SML models quickly update their boundaries to perform well on the current concept, often discarding past information (forgetting). 
* **Continual Learning (CL)** specifically prioritizes the preservation of previously acquired knowledge. It aims to incorporate new information without forgetting the old one, achieving a stability-plasticity balance.

The type of drift profoundly impacts how these models perform:
* **Virtual Drift**: The input distribution $P(X_t)$ changes (e.g., exposing the model to a new region of the feature space), but the underlying classification rule remains valid. CL models usually excel here, as past knowledge does not contradict new knowledge.
* **Real Drift**: The relationship between features and labels $P(y_t | X_t)$ changes. The decision boundary shifts, meaning that past and current concepts may conflict. CL strategies often struggle here, while SML models quickly overwrite the obsolete knowledge.

# Project Goals

## **1. Implement the Learning Models**
You will implement and compare four different models across the two ecosystems:
* **Adaptive Random Forest (ARF):** The SML baseline. You must implement this using **CapyMOA**.
* **Naive Classifier:** A simple Neural Network continuously fine-tuned on the stream without any strategy to prevent forgetting. (Use **Avalanche**).
* **Experience Replay (ER):** A CL strategy that stores a small subset of past examples in a memory buffer and replays them to prevent forgetting. (Use **Avalanche**).
* **A-GEM:** A CL strategy that constrains gradient updates using past examples to ensure new learning does not interfere with old knowledge. (Use **Avalanche**).

**(Optional/Bonus: Integrate and evaluate additional SML and CL strategies available in the CapyMOA and Avalanche libraries).**

## **2. Evaluation Protocol**
You must set up a dual-evaluation protocol to capture both rapid adaptation (SML focus) and knowledge retention (CL focus). Because the real drift scenarios contain imbalanced classes, you must use **Cohen's Kappa** as your primary metric.

### **Prequential Evaluation (Measuring Plasticity) with CapyMOA**
* Evaluate each incoming data point $X_t$ to get $\hat{y}_t$, then train the model using $(X_t, y_t)$.
* For Avalanche models (OCL), train using mini-batches (e.g., 10 examples). For CapyMOA (ARF), perform instance-by-instance learning.
* Compute a **rolling Cohen's Kappa** using a window of $1000$ data points. 
* **Crucial:** Reset the rolling window immediately after each concept drift to focus purely on the adaptation to the new concept.

### **Continual Learning Evaluation (Measuring Stability)**
* Hold out a fixed test set for each concept.
* At the end of each concept, pause the prequential training and evaluate the model's checkpoint on the test sets of all the concepts seen so far.
* Compute the **Average Cohen's Kappa ($K_{avg}$)** and the **Backward Transfer (BWT)** metric to quantify how much past knowledge has been retained or forgotten.

# Kickoff Materials
- The [reference paper](https://arxiv.org/abs/2603.01677) *"A Practical Guide to Streaming Continual Learning"* (Cossu et al., 2026)
- [The original code](https://github.com/federicogiannini13/scl-practical-guide)
- [CapyMOA Documentation](https://capymoa.org/)
- [Avalanche Documentation](https://avalanche.continualai.org/)

# Deliverables
For this project, you are required to refactor the original experimental setup and package it into a clean, reproducible repository.

1. **Code Implementation**
   - Port all the River code to CapyMOA, ensuring seamless data flow between the stream generator and the CapyMOA ARF classifier.
   - *Bonus* add new SML/CL models.
   
2. **Prepare a Notebook / Presentation**
   - Demonstrate the execution of the experiments on both scenarios.
   - Plot the prequential evaluation results (rolling Cohen's Kappa over time), highlighting the moments of concept drift.
   - Present a final table/plot showing the CL metrics ($K_{avg}$ and BWT).
   - Discuss your findings: Verify the hypotheses that CL strategies struggle with Real Drifts, while SML models catastrophically forget past knowledge.

## Note for Students

* Clone the created repository offline;
* Add your name and surname into the Readme file;
* Make any changes to your repository, according to the specific assignment;
* Add a `requirements.txt` file for code reproducibility and instructions on how to replicate the results;
* Commit your changes to your local repository;
* Push your changes to your online repository.
