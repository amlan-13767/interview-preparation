# Machine Learning + Deep Learning + LLM/RAG --- Interview Notes

## 1. AI vs ML vs DL

``` mermaid
flowchart TD
    AI[Artificial Intelligence] --> ML[Machine Learning]
    ML --> DL[Deep Learning]
    DL --> T[Transformers]
    T --> LLM[Large Language Models]
```

-   **AI:** broad field of intelligent systems.
-   **ML:** systems learn patterns from data.
-   **DL:** ML using multi-layer neural networks.
-   **LLM:** large neural language models, commonly transformer-based.

------------------------------------------------------------------------

# 2. Types of ML

### Supervised learning

Labeled data.

Tasks:

-   classification
-   regression

### Unsupervised learning

No target labels.

Tasks:

-   clustering
-   dimensionality reduction

### Reinforcement learning

Agent interacts with environment and learns from rewards.

### Semi-supervised learning

Small labeled dataset + larger unlabeled dataset.

### Self-supervised learning

Labels/signals are generated from the data itself.

------------------------------------------------------------------------

# 3. ML pipeline

``` mermaid
flowchart LR
    D[Data] --> C[Cleaning]
    C --> E[EDA]
    E --> F[Features]
    F --> S[Train/Validation/Test]
    S --> M[Model]
    M --> T[Tuning]
    T --> V[Evaluation]
    V --> P[Production]
    P --> Mon[Monitoring]
```

------------------------------------------------------------------------

# 4. Data preprocessing

Common tasks:

-   missing values
-   duplicates
-   outliers
-   categorical encoding
-   scaling
-   feature engineering
-   feature selection

Be careful not to leak test-set information into training preprocessing.

------------------------------------------------------------------------

# 5. Data leakage

Leakage occurs when information unavailable at prediction time enters
the training process.

Example:

Using future target-related information to create a feature.

Result:

-   unrealistically high validation/test score
-   poor real-world performance

------------------------------------------------------------------------

# 6. Train / validation / test

### Train

Learn parameters.

### Validation

Choose model/hyperparameters.

### Test

Final evaluation.

For time-series data, random splitting can be inappropriate because
future information may leak backward.

------------------------------------------------------------------------

# 7. Linear regression

``` text
y = w1x1 + w2x2 + ... + b
```

Common objective:

``` text
MSE = average((y - y_hat)^2)
```

Assumptions matter for statistical inference, although prediction can
still work when some assumptions are imperfect.

------------------------------------------------------------------------

# 8. Logistic regression

Despite its name, it is primarily a classification model.

``` text
p = sigmoid(wᵀx + b)
```

``` text
sigmoid(z) = 1 / (1 + e^-z)
```

Threshold converts probability to class.

------------------------------------------------------------------------

# 9. Decision tree

Splits data recursively.

Advantages:

-   interpretable
-   handles nonlinear rules
-   little feature scaling required

Risk:

-   overfitting

------------------------------------------------------------------------

# 10. Random forest

Ensemble of decision trees using bootstrapping and random feature
selection.

Benefits:

-   lower variance than one tree
-   robust baseline
-   nonlinear relationships

------------------------------------------------------------------------

# 11. Gradient boosting

Builds models sequentially, where later learners focus on
errors/residuals of earlier learners.

Examples:

-   Gradient Boosting
-   XGBoost
-   LightGBM
-   CatBoost

------------------------------------------------------------------------

# 12. KNN

Predict based on nearest training examples.

Sensitive to:

-   feature scaling
-   distance metric
-   k choice

Inference can be expensive for large datasets without
indexing/approximation.

------------------------------------------------------------------------

# 13. K-means

Unsupervised clustering.

Algorithm:

1.  choose k centroids
2.  assign points to nearest centroid
3.  recompute centroids
4.  repeat until convergence/stopping condition

Sensitive to:

-   scaling
-   k
-   initialization
-   non-spherical clusters

------------------------------------------------------------------------

# 14. Bias and variance

High bias:

``` text
underfit
```

High variance:

``` text
overfit
```

Goal:

``` text
good generalization
```

------------------------------------------------------------------------

# 15. Regularization

Adds penalty to discourage overly complex models.

### L1

Can drive some coefficients exactly to zero.

Useful for sparsity/feature selection.

### L2

Shrinks coefficients toward zero.

Often improves stability.

------------------------------------------------------------------------

# 16. Evaluation

For classification:

### Confusion matrix

``` text
                 Predicted
              Pos      Neg
Actual Pos    TP       FN
Actual Neg    FP       TN
```

### Accuracy

``` text
(TP + TN) / (TP + TN + FP + FN)
```

### Precision

``` text
TP / (TP + FP)
```

### Recall

``` text
TP / (TP + FN)
```

### F1

``` text
2PR/(P+R)
```

### ROC-AUC

Measures ranking/discrimination across classification thresholds.

------------------------------------------------------------------------

# 17. Imbalanced datasets

Accuracy can be misleading.

Example:

99% negatives.

A model predicting everything negative gets 99% accuracy but zero recall
for positives.

Use:

-   precision
-   recall
-   F1
-   PR-AUC
-   ROC-AUC
-   class weights
-   resampling

depending on the business objective.

------------------------------------------------------------------------

# 18. Feature scaling

### Standardization

``` text
z = (x - mean) / std
```

### Min-max normalization

Often maps values into a fixed range such as \[0,1\].

Scaling matters especially for distance/gradient-based algorithms.

Tree models usually do not require feature scaling.

------------------------------------------------------------------------

# 19. Gradient descent

Parameter update:

``` text
theta = theta - learning_rate * gradient
```

Learning rate too high:

-   unstable/divergent behavior

Too low:

-   slow convergence

Variants:

-   batch GD
-   stochastic GD
-   mini-batch GD

------------------------------------------------------------------------

# 20. Neural network

``` mermaid
flowchart LR
    X[Input] --> H1[Hidden Layer]
    H1 --> H2[Hidden Layer]
    H2 --> Y[Output]
```

Neuron:

``` text
z = W x + b
a = activation(z)
```

------------------------------------------------------------------------

# 21. Activation functions

### ReLU

``` text
max(0,x)
```

### Sigmoid

Maps to (0,1).

### Tanh

Maps to (-1,1).

### Softmax

Converts logits to a probability distribution over classes.

------------------------------------------------------------------------

# 22. Loss functions

Examples:

-   MSE for regression
-   binary cross entropy for binary classification
-   categorical cross entropy for multiclass classification

Loss is the objective optimized during training.

------------------------------------------------------------------------

# 23. Backpropagation

``` mermaid
flowchart LR
    A[Forward pass] --> B[Loss]
    B --> C[Backward pass]
    C --> D[Gradients]
    D --> E[Parameter update]
```

Backpropagation applies the chain rule to compute gradients.

------------------------------------------------------------------------

# 24. CNN

Convolutional neural networks are effective for spatial/local patterns,
especially images.

Concepts:

-   convolution
-   filters
-   feature maps
-   pooling
-   receptive field

------------------------------------------------------------------------

# 25. RNN/LSTM

RNNs process sequences with recurrent state.

LSTM adds gating mechanisms to help preserve useful information over
longer sequences.

Transformers have largely replaced RNNs for many modern language
workloads.

------------------------------------------------------------------------

# 26. Transformers

Core idea: attention allows tokens to dynamically weigh other tokens.

Self-attention:

``` text
Q = XWq
K = XWk
V = XWv

Attention(Q,K,V)
= softmax(QKᵀ / sqrt(dk))V
```

Do not memorize only the equation.

Explain:

> Query represents what the current token is looking for; keys represent
> what each token offers for matching; values contain the information
> aggregated according to attention weights.

------------------------------------------------------------------------

# 27. Embeddings

An embedding maps an object such as text into a dense vector.

Semantic similarity can be estimated with vector similarity.

Common similarity:

``` text
cos(A,B) = A·B / (||A|| ||B||)
```

------------------------------------------------------------------------

# 28. RAG

Retrieval-Augmented Generation:

``` mermaid
flowchart TD
    Q[Question] --> E[Query Embedding]
    E --> R[Retriever]
    R --> C[Top-k Context]
    Q --> P[Prompt + Context]
    C --> P
    P --> L[LLM]
    L --> A[Grounded Answer]
```

Why RAG?

-   external knowledge
-   domain-specific content
-   more current information
-   citations/evidence
-   reduces some hallucination risk

RAG does not automatically eliminate hallucinations.

------------------------------------------------------------------------

# 29. Vector database

Stores embeddings plus metadata and supports similarity search.

Examples:

-   Chroma
-   FAISS-based systems
-   Pinecone
-   Milvus
-   Weaviate

Distinguish:

-   **vector index/database** = retrieval infrastructure
-   **embedding model** = converts content/query into vectors
-   **LLM** = generates output

------------------------------------------------------------------------

# 30. Chunking

Long documents are split into chunks before embedding.

Trade-off:

Small chunks:

-   precise retrieval
-   less context

Large chunks:

-   more context
-   less precise retrieval
-   more token cost

Overlap can preserve context across boundaries.

------------------------------------------------------------------------

# 31. Hybrid retrieval

Combine:

``` text
BM25 / lexical search
+
dense vector search
```

Why?

Keyword search is strong for exact names/codes.

Dense retrieval is strong for semantic similarity.

The uploaded learning-path repository contains both
BM25/retrieval-related and embedding/vector components, making this a
relevant improvement/architecture discussion.

------------------------------------------------------------------------

# 32. Reranking

Retriever returns candidate documents.

A reranker then scores candidates more carefully.

``` text
Query
 ↓
retrieve top 50
 ↓
rerank
 ↓
top 5
 ↓
LLM
```

This can improve relevance.

------------------------------------------------------------------------

# 33. Prompt injection

Retrieved documents are untrusted input.

A malicious document might contain instructions such as:

> Ignore previous instructions...

The model should treat retrieved content as data, not automatically as
higher-priority instructions.

Defenses:

-   isolate system instructions
-   label retrieved content as untrusted
-   restrict tools
-   validate outputs
-   use allowlists
-   monitor suspicious retrieval

------------------------------------------------------------------------

# 34. Hallucination reduction

Use:

-   high-quality retrieval
-   reranking
-   source grounding
-   structured outputs
-   tool validation
-   explicit "don't invent" instructions
-   answer abstention when evidence is missing
-   evaluation datasets

------------------------------------------------------------------------

# 35. Important Q&A

### Q: ML vs DL?

DL is a subset of ML based on multi-layer neural networks and
representation learning.

### Q: Precision vs recall?

Precision asks: "Of what I predicted positive, how much was actually
positive?"

Recall asks: "Of all actual positives, how many did I find?"

### Q: Why RAG instead of fine-tuning?

RAG is useful when knowledge changes frequently or must be sourced
dynamically. Fine-tuning changes model behavior/weights and is useful
for style, behavior or domain adaptation, but it is not a simple
replacement for a live knowledge base.

### Q: Does RAG eliminate hallucinations?

No. It can reduce unsupported generation by supplying relevant context,
but retrieval can fail and the model can still misinterpret or invent
information.

### Q: Embedding vs LLM?

An embedding model maps content to vectors for similarity/search. An LLM
generates or transforms language.

### Q: Why cosine similarity?

It measures orientation between vectors and is often useful when vector
magnitude is less important than semantic direction.


# 36. Additional ML topics

## Naive Bayes

Probabilistic classifier based on Bayes' theorem with a conditional-independence assumption.

Useful for:

- text classification
- spam detection

## SVM

Finds a decision boundary maximizing margin between classes.

Kernel methods can represent nonlinear boundaries.

Important hyperparameters include C and kernel-specific parameters.

## PCA

Principal Component Analysis projects data into directions of maximum variance.

Uses:

- dimensionality reduction
- visualization
- noise/compression in suitable cases

PCA is unsupervised.

## Cross-validation

K-fold CV:

```text
fold 1 validation
fold 2 validation
...
fold k validation
```

Average validation performance gives a more robust estimate for model selection than one arbitrary split.

Do not use test data repeatedly for tuning.

## Hyperparameters vs parameters

Parameters are learned from data:

- neural network weights
- linear regression coefficients

Hyperparameters are selected outside the training optimization:

- learning rate
- tree depth
- k in KNN
- regularization strength

## Ensemble learning

Combines multiple models.

### Bagging

Parallel models reduce variance.

Random Forest is a major example.

### Boosting

Sequentially focuses on correcting errors.

Examples: Gradient Boosting, XGBoost.

## Calibration

A calibrated classifier's predicted probabilities should correspond reasonably to observed frequencies.

Probability quality matters in risk-sensitive systems.

## Feature selection vs feature extraction

Feature selection chooses existing features.

Feature extraction creates transformed representations.

PCA is feature extraction.

## MLOps

Production ML needs:

- data/version tracking
- model versioning
- deployment
- monitoring
- drift detection
- retraining
- reproducibility
- CI/CD
- rollback

## Model drift

Performance can decline because real-world distributions or relationships change.

Types:

- data drift
- concept drift

## Retrieval evaluation for RAG

Do not evaluate only the final answer.

Measure:

- retrieval recall
- precision/relevance
- reranker quality
- groundedness
- answer correctness
- citation correctness

## RAG vs fine-tuning

Use RAG when knowledge should be retrieved dynamically.

Use fine-tuning when you want to change model behavior/style/task specialization.

They can be combined.

