# Advanced Cryptography & ML Security

## 📋 Summary

* **Core Concept:** As machine learning systems process increasingly sensitive data, a new intersection of cryptography and AI security has emerged. These techniques either allow computation over encrypted data (homomorphic encryption, secure MPC), enforce mathematical privacy guarantees on outputs (differential privacy), secure the distributed training process (federated learning), or describe how ML models themselves can be attacked (adversarial examples, model extraction).

> **Takeaways:**
> - **Homomorphic Encryption (HE)** allows a server to compute on encrypted data without ever decrypting it — but carries significant computational overhead (10³–10⁶× slower than plaintext computation).
> - **Secure Multi-Party Computation (MPC)** allows $n$ parties to jointly compute a function on their private inputs without revealing those inputs to each other.
> - **Differential Privacy (DP)** provides a mathematically rigorous, parameter-controlled privacy guarantee: the output of a computation reveals almost nothing about any single individual's data.
> - **Federated Learning** trains models on distributed data without centralizing it — but is itself vulnerable to gradient inversion, poisoning, and membership inference attacks.
> - **Adversarial attacks** exploit the geometry of ML decision boundaries: small, imperceptible input perturbations cause confident misclassification.
> - **Model extraction attacks** reconstruct a functional copy of a model through black-box query access — a commercial and IP threat increasingly relevant to deployed ML APIs.

---

## 📖 Definition

* **Homomorphic Encryption (HE):** A public-key cryptosystem that supports arithmetic operations directly on ciphertexts such that the result, when decrypted, equals the result of the same operations applied to the plaintexts: $\text{Dec}(f(\text{Enc}(x))) = f(x)$.

* **Partially Homomorphic Encryption (PHE):** Supports one operation type only — either addition (Paillier) or multiplication (RSA) — but not both.

* **Fully Homomorphic Encryption (FHE):** Supports both addition and multiplication over ciphertexts, enabling evaluation of arbitrary circuits. First construction by Gentry (2009). Current practical schemes include BFV, BGV, and CKKS (approximate arithmetic for ML).

* **Secure Multi-Party Computation (MPC):** A cryptographic protocol by which $n \geq 2$ parties, each holding a private input $x_i$, jointly compute $f(x_1, x_2, \ldots, x_n)$ such that no party learns any other party's input beyond what is implied by the output.

* **Secret Sharing:** A primitive used in MPC. A secret $s$ is split into $n$ shares such that any $t$ shares reconstruct $s$ (Shamir's $t$-of-$n$ scheme), but any $t-1$ shares reveal nothing about $s$.

* **Differential Privacy (DP):** A formal privacy definition. A randomized mechanism $\mathcal{M}$ satisfies $(\varepsilon, \delta)$-differential privacy if for all adjacent datasets $D, D'$ (differing in one individual's record) and all output sets $S$:
  $$\Pr[\mathcal{M}(D) \in S] \leq e^\varepsilon \cdot \Pr[\mathcal{M}(D') \in S] + \delta$$
  The parameter $\varepsilon$ (privacy budget) controls the privacy–utility trade-off; smaller $\varepsilon$ means stronger privacy.

* **Federated Learning (FL):** A distributed machine learning paradigm in which a global model is trained by aggregating locally computed gradient updates from client devices — raw training data never leaves the clients.

* **Gradient Inversion Attack:** An attack on federated learning in which an adversary reconstructs private training samples from the gradient updates shared during the FL protocol.

* **Adversarial Example:** An input $x' = x + \delta$ where $\|\delta\|_p \leq \epsilon$ is a small, bounded perturbation such that a model $f$ misclassifies $x'$ with high confidence: $f(x') \neq f(x)$, despite $x'$ and $x$ being perceptually indistinguishable to a human.

* **Model Extraction Attack:** A black-box attack in which an adversary queries a target model $f$ on chosen inputs and uses the query–response pairs to train a surrogate model $\hat{f}$ that closely approximates $f$'s behavior — effectively stealing the model's functionality.

* **Requirements for Differential Privacy:**
    * A sensitivity bound $\Delta f$ — the maximum change in query output from adding/removing one individual.
    * A noise mechanism calibrated to $\Delta f / \varepsilon$ (Laplace mechanism for numeric queries, Gaussian mechanism for approximate DP).
    * A privacy accounting method (e.g., Rényi DP, moments accountant) to track total privacy budget across multiple queries.

---

## 📊 Complexity Analysis

| Notation | Name | Growth Rate |
| :--- | :--- | :--- |
| $O(1)$ | Constant | Excellent |
| $O(\log n)$ | Logarithmic | Very Good |
| $O(n)$ | Linear | Good |
| $O(n^2)$ | Quadratic | Poor |

### Computational Overhead Comparison

| Technique | Computation Overhead | Communication Overhead | Maturity |
| :--- | :--- | :--- | :--- |
| Paillier PHE | ~100× vs plaintext | Low | Production-ready |
| CKKS (FHE, ML approx.) | $10^3$–$10^5$× vs plaintext | High | Research / early prod |
| MPC (2-party, GMW) | Low per gate, $O(n \cdot d)$ rounds | $O(n \cdot d)$ — circuit depth $d$ | Production-ready |
| DP (Gaussian mechanism) | $O(1)$ — add noise | None | Production-ready |
| Federated Learning | $O(C \cdot T)$ — clients $C$, rounds $T$ | $O(C \cdot \|w\|)$ — model size $\|w\|$ | Production-ready |

* **Adversarial Attack (FGSM):** $O(|\theta|)$ — one backward pass through the model. PGD: $O(k \cdot |\theta|)$ for $k$ steps.
* **Model Extraction:** $O(q)$ queries to the black-box API, where $q$ scales with model complexity and desired surrogate accuracy.
* **Worst-Case ($O$) — FHE inference:** $O(d \cdot n^2)$ where $d$ is circuit depth and $n$ is the polynomial modulus degree — the dominant cost of bootstrapping.
* **Best-Case ($\Omega$) — DP noise addition:** $\Omega(p)$ where $p$ is the number of model parameters — noise is added once per gradient update.

---

## ❓ Why We Study These

* **Privacy regulations create legal obligations:** GDPR, HIPAA, and CCPA require organizations to protect personal data. DP and FHE offer cryptographic proof of protection — not just procedural assurances.
* **Federated learning is already deployed at scale:** Google (Gboard), Apple (Siri), and Meta use FL in production. The attack surface of FL (gradient inversion, model poisoning) is therefore a real-world concern.
* **Adversarial examples affect safety-critical systems:** Autonomous vehicles, medical image classifiers, and content moderation models are all vulnerable. For a career at the intersection of AI and cybersecurity, this is a primary domain.
* **Model extraction undermines ML IP and safety:** Proprietary models (GPT-4, medical diagnostic models) can be functionally cloned through API access. Extracted models also bypass safety alignment fine-tuning.
* **These fields directly intersect your career goals:** AR/VR systems process biometric and spatial data (HE/DP relevance); they also run ML inference on-device under adversarial conditions (adversarial robustness relevance).

---

## ⚙️ How It Works

### Homomorphic Encryption

1. **Step 1 — Key generation:** Generate a public/private key pair $(pk, sk)$ under the HE scheme (e.g., CKKS for approximate real-number arithmetic).
2. **Step 2 — Encrypt inputs:** The data owner encrypts private data $x$ → $\text{Enc}_{pk}(x) = c$.
3. **Step 3 — Delegate computation:** Send ciphertext $c$ to an untrusted server. The server evaluates a function (e.g., a neural network layer) directly on $c$, producing $c' = \text{Eval}(f, c)$.
4. **Step 4 — Noise growth:** Each homomorphic operation introduces noise. FHE schemes include a *bootstrapping* procedure that refreshes the ciphertext — the most expensive operation in FHE.
5. **Step 5 — Decrypt result:** Only the key holder decrypts $c'$ → $\text{Dec}_{sk}(c') = f(x)$.

> **CKKS for ML:** CKKS (Cheon–Kim–Kim–Song) supports approximate arithmetic over real numbers, making it the most practical FHE scheme for neural network inference.

### Secure Multi-Party Computation

1. **Step 1 — Secret share inputs:** Each party splits its input using Shamir secret sharing: party $i$ distributes shares of $x_i$ to all other parties such that no subset of size $< t$ can reconstruct $x_i$.
2. **Step 2 — Evaluate circuit:** The function $f$ is expressed as an arithmetic or Boolean circuit. Parties jointly evaluate each gate using their shares — addition is free; multiplication requires a communication round.
3. **Step 3 — Reconstruct output:** After evaluating the final gate, parties combine their output shares to reconstruct $f(x_1, \ldots, x_n)$.
4. **Key property:** Security holds against a semi-honest adversary (follows protocol but records all messages) if $t < n/2$ parties are corrupted; against a malicious adversary with additional authentication.

### Differential Privacy

1. **Step 1 — Define the query:** Identify the function $f(D)$ to be computed on dataset $D$ (e.g., the average age, or a gradient update in DP-SGD).
2. **Step 2 — Compute global sensitivity:** $\Delta f = \max_{D, D'} \|f(D) - f(D')\|$ over all adjacent datasets $D, D'$.
3. **Step 3 — Add calibrated noise:** For the Laplace mechanism: $\mathcal{M}(D) = f(D) + \text{Lap}(0, \Delta f / \varepsilon)$. Larger $\varepsilon$ → less noise → weaker privacy.
4. **Step 4 — Track privacy budget:** Each query consumes $\varepsilon$ budget. Use composition theorems (basic, advanced, Rényi DP) to bound total exposure across many queries.
5. **Step 5 — DP-SGD for ML:** Clip per-sample gradients to bound sensitivity, add Gaussian noise to the clipped sum, then update model weights. The moments accountant tracks $(\varepsilon, \delta)$ across all training steps.

### Federated Learning Security

1. **Step 1 — Local training:** Each client $k$ trains on its local data for $E$ epochs, computing a gradient update $\Delta w_k$.
2. **Step 2 — Gradient upload:** Clients send $\Delta w_k$ to a central aggregator (not raw data).
3. **Step 3 — Aggregation:** The server computes $w \leftarrow w + \frac{1}{K}\sum_k \Delta w_k$ (FedAvg).
4. **Attack surface — Gradient inversion:** An adversary (or malicious server) applies optimization to find $x'$ such that $\nabla_w \mathcal{L}(f_w(x'), y') \approx \Delta w_k$, reconstructing training images from gradients.
5. **Attack surface — Poisoning:** A malicious client submits crafted $\Delta w_k$ to implant a backdoor in the global model — e.g., classifying images with a trigger pattern as a target class.
6. **Defense:** Combine DP noise on gradients (DP-SGD), secure aggregation (MPC so the server only sees the sum), and anomaly detection on gradient norms.

### Adversarial Attacks on ML Models

1. **Step 1 — Define threat model:** White-box (attacker has model weights and gradients) vs. black-box (attacker has only API access and output labels or probabilities).
2. **Step 2 — FGSM (Fast Gradient Sign Method):** Compute the gradient of the loss with respect to the input, then step in the sign direction to maximize the loss:
   $$x' = x + \epsilon \cdot \text{sign}(\nabla_x \mathcal{L}(f_\theta(x), y))$$
3. **Step 3 — PGD (Projected Gradient Descent):** Iterative extension of FGSM — apply $k$ smaller gradient steps, projecting back onto the $\ell_\infty$ ball of radius $\epsilon$ after each step.
4. **Step 4 — Black-box transfer:** Adversarial examples often transfer across models trained on the same data distribution — an example crafted against a local surrogate may fool the target model.
5. **Defense:** Adversarial training (include adversarial examples in training data), certified defenses (randomized smoothing provides $\ell_2$ robustness guarantees), and input preprocessing (feature squeezing, JPEG compression).

### Model Extraction Attack

1. **Step 1 — Choose a query strategy:** Sample inputs from the data distribution, use active learning, or craft synthetic inputs that maximize information about decision boundaries.
2. **Step 2 — Query the target API:** Send inputs to the black-box model $f$, collect output labels or probability vectors.
3. **Step 3 — Train a surrogate:** Use query–response pairs $(x, f(x))$ as a labelled training set to train a local surrogate $\hat{f}$.
4. **Step 4 — Refine:** Apply query-by-committee or disagreement-based active learning to focus queries on regions where $\hat{f}$ and $f$ disagree.
5. **Step 5 — Exploit:** Use $\hat{f}$ to craft adversarial examples, bypass API safety filters, or redistribute the model's functionality commercially.
6. **Defense:** Rate limiting queries, output perturbation (returning rounded or truncated probabilities), watermarking model outputs, and detecting anomalous query patterns.

---

## 💻 Usage / Example

```python
# ============================================================
# Advanced Cryptography & ML Security — Demonstrations
# ============================================================
# Prerequisites:
#   pip install tenseal numpy torch torchvision diffprivlib
# ============================================================

import numpy as np


# ------------------------------------------------------------------
# SECTION 1: Homomorphic Encryption (CKKS via TenSEAL)
# Compute a dot product on encrypted vectors.
# ------------------------------------------------------------------

def demo_homomorphic_encryption() -> None:
    """
    Encrypt two vectors and compute their dot product homomorphically.
    The server never sees the plaintext values.
    """
    try:
        import tenseal as ts

        # Key generation
        ctx = ts.context(
            ts.SCHEME_TYPE.CKKS,
            poly_modulus_degree=8192,
            coeff_mod_bit_sizes=[60, 40, 40, 60]
        )
        ctx.generate_galois_keys()
        ctx.global_scale = 2 ** 40

        # Plaintext data (private to data owner)
        a = [1.0, 2.0, 3.0, 4.0]
        b = [0.5, 1.5, 2.5, 3.5]
        expected_dot = sum(x * y for x, y in zip(a, b))

        # Encrypt
        enc_a = ts.ckks_vector(ctx, a)
        enc_b = ts.ckks_vector(ctx, b)

        # Homomorphic dot product (server computes on ciphertexts)
        enc_result = enc_a.dot(enc_b)

        # Decrypt
        result = enc_result.decrypt()[0]

        print("Homomorphic Encryption (CKKS dot product):")
        print(f"  Plaintext result   : {expected_dot:.4f}")
        print(f"  HE decrypted result: {result:.4f}  (approximate)")
        print(f"  Error              : {abs(result - expected_dot):.2e}\n")

    except ImportError:
        print("TenSEAL not installed. Run: pip install tenseal\n")


# ------------------------------------------------------------------
# SECTION 2: Differential Privacy — Laplace Mechanism
# Add calibrated noise to protect individual records.
# ------------------------------------------------------------------

def laplace_mechanism(true_value: float, sensitivity: float,
                       epsilon: float) -> float:
    """
    Apply the Laplace mechanism to a numeric query result.

    Args:
        true_value:  The exact query answer.
        sensitivity: Global sensitivity Δf (max change from one record).
        epsilon:     Privacy budget (smaller = stronger privacy, more noise).

    Returns:
        Noisy output satisfying ε-differential privacy.
    """
    scale = sensitivity / epsilon
    noise = np.random.laplace(0, scale)
    return true_value + noise


def dp_mean_query(data: list[float], epsilon: float,
                  data_range: tuple[float, float]) -> float:
    """
    Compute a differentially private mean.
    Clips values to [low, high] to bound sensitivity, then adds Laplace noise.
    """
    low, high = data_range
    clipped = [max(low, min(high, x)) for x in data]
    true_mean = sum(clipped) / len(clipped)

    # Sensitivity of the mean: (high - low) / n
    sensitivity = (high - low) / len(clipped)
    return laplace_mechanism(true_mean, sensitivity, epsilon)


def demonstrate_dp() -> None:
    ages = [22, 25, 28, 31, 19, 45, 33, 27, 24, 30]
    true_mean = sum(ages) / len(ages)

    print("Differential Privacy — Mean Age Query:")
    print(f"  True mean: {true_mean:.2f}")

    for eps in [10.0, 1.0, 0.1]:
        dp_result = dp_mean_query(ages, epsilon=eps, data_range=(0, 120))
        print(f"  ε={eps:4.1f}  →  DP mean: {dp_result:.2f}  "
              f"(noise ≈ ±{(120/len(ages)/eps):.2f})")
    print()


# ------------------------------------------------------------------
# SECTION 3: Adversarial Attack — FGSM (Fast Gradient Sign Method)
# Craft an adversarial example against a simple PyTorch classifier.
# ------------------------------------------------------------------

def fgsm_attack(model, image: "torch.Tensor", label: "torch.Tensor",
                epsilon: float) -> "torch.Tensor":
    """
    Fast Gradient Sign Method (Goodfellow et al., 2014).
    Perturbs the input in the direction of the loss gradient sign.

    Args:
        model:   A PyTorch nn.Module (must support backprop).
        image:   Input tensor, shape (1, C, H, W), requires_grad=True.
        label:   True label tensor.
        epsilon: Perturbation magnitude (L∞ budget).

    Returns:
        Adversarial example: image + ε * sign(∇_x Loss).
    """
    import torch
    import torch.nn.functional as F

    image.requires_grad_(True)

    output = model(image)
    loss = F.cross_entropy(output, label)
    model.zero_grad()
    loss.backward()

    # Perturbation: step in the direction that maximizes the loss
    perturbation = epsilon * image.grad.sign()
    adversarial = torch.clamp(image + perturbation, 0.0, 1.0)

    return adversarial.detach()


def demonstrate_fgsm() -> None:
    """
    Demonstrate FGSM on a minimal 2-class linear classifier.
    Shows confidence shift before and after perturbation.
    """
    try:
        import torch
        import torch.nn as nn
        import torch.nn.functional as F

        # Minimal linear classifier (2 input features, 2 classes)
        torch.manual_seed(0)
        model = nn.Linear(2, 2)

        x = torch.tensor([[2.0, 1.0]], requires_grad=True)   # Clean input
        y = torch.tensor([0])                                  # True label

        # Clean prediction
        with torch.no_grad():
            clean_probs = F.softmax(model(x), dim=1)
        clean_pred = clean_probs.argmax(dim=1).item()

        # FGSM adversarial example (ε = 0.3)
        x_adv = fgsm_attack(model, x.clone(), y, epsilon=0.3)

        with torch.no_grad():
            adv_probs = F.softmax(model(x_adv), dim=1)
        adv_pred = adv_probs.argmax(dim=1).item()

        print("FGSM Adversarial Attack:")
        print(f"  Clean input     : {x.detach().numpy().tolist()}")
        print(f"  Clean pred      : class {clean_pred}  "
              f"(confidence {clean_probs[0][clean_pred].item():.3f})")
        print(f"  Adv input       : {x_adv.numpy().tolist()}")
        print(f"  Adv pred        : class {adv_pred}  "
              f"(confidence {adv_probs[0][adv_pred].item():.3f})")
        print(f"  Perturbation L∞ : "
              f"{(x_adv - x.detach()).abs().max().item():.4f}\n")

    except ImportError:
        print("PyTorch not installed. Run: pip install torch\n")


# ------------------------------------------------------------------
# SECTION 4: Model Extraction — Surrogate Training
# Query a black-box model and train a surrogate.
# ------------------------------------------------------------------

def demo_model_extraction() -> None:
    """
    Demonstrate model extraction via random query strategy.
    Target: sklearn logistic regression (treated as black-box API).
    Surrogate: trained on target's responses to random inputs.
    """
    try:
        from sklearn.linear_model import LogisticRegression
        from sklearn.datasets import make_classification
        from sklearn.metrics import accuracy_score

        # Build target model (hidden from the attacker)
        X, y = make_classification(n_samples=500, n_features=10, random_state=42)
        target = LogisticRegression(max_iter=200).fit(X, y)

        # Attacker's black-box query budget
        n_queries = 100
        X_query = np.random.randn(n_queries, 10)          # Synthetic queries
        y_query = target.predict(X_query)                  # API responses (labels only)

        # Train surrogate on query-response pairs
        surrogate = LogisticRegression(max_iter=200).fit(X_query, y_query)

        # Evaluate agreement on held-out test set
        X_test = np.random.randn(200, 10)
        target_preds    = target.predict(X_test)
        surrogate_preds = surrogate.predict(X_test)
        agreement = accuracy_score(target_preds, surrogate_preds)

        print("Model Extraction Attack:")
        print(f"  Query budget        : {n_queries} random inputs")
        print(f"  Surrogate agreement : {agreement:.2%} with target API")
        print(f"  (Higher = more faithful copy of the target model)\n")

    except ImportError:
        print("scikit-learn not installed. Run: pip install scikit-learn\n")


# ============================================================
# MAIN DEMONSTRATION
# ============================================================

if __name__ == "__main__":
    np.random.seed(42)

    demo_homomorphic_encryption()
    demonstrate_dp()
    demonstrate_fgsm()
    demo_model_extraction()

# ============================================================
# Summary of Key Properties:
#   HE (CKKS)         : Exact privacy, ~10^4x overhead, approximate arithmetic
#   MPC               : No single point of trust, communication-intensive
#   DP (ε-DP)         : Provable privacy, utility-privacy trade-off via ε
#   Federated Learning : Data locality, vulnerable to gradient inversion
#   FGSM              : O(1 backward pass) to craft adversarial example
#   Model Extraction  : O(q) queries to clone black-box model behavior
# ============================================================
```

> **Key Insight:** The connection between these techniques is the question of *where trust is placed*. HE removes trust in the compute server. MPC distributes trust across parties. DP limits what any observer can infer from outputs. Adversarial and model extraction attacks remind us that trust in the model's robustness and exclusivity cannot be assumed — they must be engineered.

---

## References

* [Gentry, C. (2009) — "A Fully Homomorphic Encryption Scheme"](https://crypto.stanford.edu/craig/craig-thesis.pdf) — PhD thesis introducing the first FHE construction; the foundation of all modern FHE schemes.
* [Cheon et al. (2017) — "Homomorphic Encryption for Arithmetic of Approximate Numbers (CKKS)"](https://link.springer.com/chapter/10.1007/978-3-319-70694-8_15) — Introduces the CKKS scheme used for ML inference on encrypted data.
* [Yao, A.C. (1986) — "How to Generate and Exchange Secrets"](https://ieeexplore.ieee.org/document/4568207) — Foundational paper on garbled circuits and secure two-party computation.
* [Dwork, C. & Roth, A. (2014) — "The Algorithmic Foundations of Differential Privacy"](https://www.cis.upenn.edu/~aaroth/Papers/privacybook.pdf) — Comprehensive textbook treatment of DP theory and mechanisms.
* [McMahan et al. (2017) — "Communication-Efficient Learning of Deep Networks from Decentralized Data (FedAvg)"](https://arxiv.org/abs/1602.05629) — Original federated learning paper introducing the FedAvg algorithm.
* [Goodfellow et al. (2014) — "Explaining and Harnessing Adversarial Examples (FGSM)"](https://arxiv.org/abs/1412.6572) — Introduces FGSM and the linear hypothesis for adversarial vulnerability.
* [Tramèr et al. (2016) — "Stealing Machine Learning Models via Prediction APIs"](https://arxiv.org/abs/1609.02943) — Foundational model extraction attack paper covering linear models, SVMs, and neural networks.
* [Abadi et al. (2016) — "Deep Learning with Differential Privacy (DP-SGD)"](https://arxiv.org/abs/1607.00133) — Google paper introducing the moments accountant and DP-SGD for private ML training.
* *The Algorithmic Foundations of Differential Privacy* — Cynthia Dwork & Aaron Roth, Chapters 3–4 (Basic Mechanisms).