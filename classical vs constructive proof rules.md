In **constructive logic** (also known as **intuitionistic logic**), not all proof rules from **classical logic** are accepted. The key difference lies in the interpretation of truth: constructive logic requires that the existence of a mathematical object or the truth of a statement be demonstrated **constructively**—that is, by providing an explicit example or a method to construct it.

Here's a breakdown of which classical rules are **allowed** and which are **not** in constructive logic:

---

### ✅ **Allowed in Constructive Logic**

These rules are shared between classical and constructive logic:

1. **Modus Ponens**:  
    If $A$ and $A \rightarrow B$ are both provable, then $B$ is provable.
    
2. **Conjunction Introduction and Elimination**:  
    From $A$ and $B$, infer $A \land B$; from $A \land B$, infer $A$ and $B$.
    
3. **Disjunction Introduction**:  
    From $A$, infer $A \lor B$; from $B$, infer $A \lor B$.
    
4. **Implication Introduction**:  
    If assuming $A$ leads to $B$, then infer $A \rightarrow B$.
    
5. **Universal Quantifier Rules**:
    
    - Introduction: If $P(x)$ holds for arbitrary $x$, then $\forall x\, P(x)$.
    - Elimination: From $\forall x\, P(x)$, infer $P(t)$ for any term $t$.
6. **Existential Quantifier Elimination**:  
    From $\exists x\, P(x)$ and a proof that $P(x) \rightarrow Q$ for arbitrary $x$, infer $Q$.
    

---

### ❌ **Not Allowed in Constructive Logic**

These are **not valid** in constructive logic because they assert the existence or truth of something without providing a constructive method:

1. **Law of Excluded Middle (LEM)**:  
    $A \lor \neg A$ — not accepted unless $A$ is decidable.
    
2. **Double Negation Elimination**:  
    From $\neg\neg A$, infer $A$ — not generally valid.
    
3. **Proof by Contradiction (Reductio ad Absurdum)**:  
    From assuming $\neg A$ leads to a contradiction, infer $A$ — only valid in a limited form.
    
4. **Peirce’s Law**:  
    $((A \rightarrow B) \rightarrow A) \rightarrow A$ — not constructively valid.
    

---

### Summary Table

|Rule|Classical Logic|Constructive Logic|
|---|---|---|
|Modus Ponens|✅|✅|
|Conjunction Rules|✅|✅|
|Disjunction Introduction|✅|✅|
|Disjunction Elimination|✅|✅ (with care)|
|Implication Introduction|✅|✅|
|Universal Quantifier Rules|✅|✅|
|Existential Quantifier Intro|✅|✅|
|**Law of Excluded Middle**|✅|❌|
|**Double Negation Elimination**|✅|❌|
|**Proof by Contradiction**|✅|❌ (in general)|
|**Peirce’s Law**|✅|❌|

---

Would you like a visual diagram of these rules or an example of how a proof differs between classical and constructive logic?