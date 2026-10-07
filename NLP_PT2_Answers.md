# Natural Language Processing (NLP) — MU Sem 7 Unit Test 2 (PT-2)
*Mumbai University | BE Computer Engineering | Thadomal Shahani Engineering College*

> [!NOTE]
> **Complete exam-ready guide.** Every question from the QB is answered with structured theory, diagrams, examples, and the CYK parsing numerical is fully traced cell-by-cell.

---

## Part A: Question Bank Analysis & Strategy

### Exam Pattern
* **Total:** 20 marks | **Duration:** 1 hour
* **Chapters covered:** Ch 4 (Semantic Analysis — WSD, WordNet, Lexical Relations), Ch 5 (Pragmatic & Discourse Processing — Reference Resolution, Anaphora), Ch 6 (Applications of NLP — Text Summarization, CYK Parsing)
* **Nature of this PT:** Heavily **theory-driven** with one **algorithmic numerical** (CYK parsing). Unlike ML/BDA, scoring here depends on writing **crisp definitions + examples + diagrams**.

### Priority Matrix

| Priority | Questions | Topic | Type |
| :--- | :--- | :--- | :--- |
| 🔴 **HIGHEST** | Q10 | CYK/CKY Parsing Algorithm (numerical) | Numerical — full table trace |
| 🔴 **HIGHEST** | Q2, Q4 | WSD approaches, Lesk/Yarowsky/Hyperlex algorithms | Algorithm + Theory |
| 🟠 **HIGH** | Q5, Q6, Q8 | Reference Resolution, Constraints, Hobbs & Centering | Theory + Algorithm |
| 🟡 **MEDIUM** | Q1, Q3, Q7 | Lexicon/Lexeme relations, WordNet, Referring Expressions | Theory + Diagram |
| 🟢 **STANDARD** | Q9 | Text Summarization System | Theory |

> [!TIP]
> **Pattern insight:** Q2 + Q4 cover the same WSD topic from different angles — master Lesk's algorithm and you answer both. Q5 + Q6 + Q7 + Q8 all revolve around Reference Resolution — understanding the framework once covers four questions. **Q10 (CYK) is a guaranteed 10-marker** — practice filling the triangular table.

### Core Tips
1. **CYK is the highest-ROI question.** It's the only numerical — examiners love it. Practice the table-filling method until it's automatic.
2. **WSD algorithms** (Lesk, Yarowsky, Hyperlex) — know the one-paragraph description + example for each. Don't mix them up.
3. **Reference Resolution** questions (Q5–Q8) overlap heavily — a single deep understanding covers all four.
4. **Use examples everywhere.** NLP theory without examples scores poorly. Always add "e.g., …" after every definition.
5. **Time split:** ~15 min CYK numerical, ~25 min theory (Q1–Q9), ~5 min review.

---

## Part B: Complete Answers

---

# 📘 CHAPTER 4 — Semantic Analysis

---

## Q1) Explain lexicon, lexeme and the different types of relations among Lexemes & their senses

### Definitions

**Lexicon:**
- A **lexicon** is the vocabulary or dictionary of a language — the complete set of all words (lexemes) and their associated information (meaning, part of speech, pronunciation, usage).
- In NLP, a **computational lexicon** stores words along with their syntactic category, semantic features, and other linguistic properties.
- Example: The English lexicon contains entries like: *bank* (noun, verb), *run* (verb, noun), *good* (adjective), etc.

**Lexeme:**
- A **lexeme** is the fundamental, abstract unit of meaning in a language — it represents a word in all its inflected forms.
- A single lexeme can have multiple **word forms** but they share a core meaning.
- Example: The lexeme **RUN** includes the forms: *run, runs, running, ran*
- Example: The lexeme **GOOD** includes: *good, better, best*

**Sense:**
- A **sense** is a specific meaning of a lexeme. One lexeme can have multiple senses.
- Example: The lexeme **BANK** has senses:
  - Sense 1: Financial institution ("I deposited money in the bank")
  - Sense 2: River bank ("We sat on the bank of the river")
  - Sense 3: To bank (verb) — "to tilt an aircraft"

### Relations Among Lexemes and Their Senses

```text
┌─────────────────────────────────────────────────────┐
│          LEXICAL RELATIONS                          │
├──────────────────┬──────────────────────────────────┤
│ BETWEEN LEXEMES  │  BETWEEN SENSES                 │
│ (Word Forms)     │  (Meanings)                      │
├──────────────────┼──────────────────────────────────┤
│ • Homonymy       │  • Synonymy                      │
│ • Polysemy       │  • Antonymy                      │
│                  │  • Hyponymy / Hypernymy           │
│                  │  • Meronymy / Holonymy            │
│                  │  • Metonymy                       │
└──────────────────┴──────────────────────────────────┘
```

#### 1. Synonymy (Same meaning, different words)
- Two lexemes whose senses are **identical or nearly identical** in a given context.
- Example: *big* ↔ *large*, *happy* ↔ *joyful*, *car* ↔ *automobile*
- **Note:** Perfect synonymy is rare — most synonyms differ in connotation, register, or usage.

#### 2. Antonymy (Opposite meaning)
- Two lexemes with **opposite** meanings.
- Types:
  - **Gradable:** *hot* ↔ *cold*, *big* ↔ *small* (degrees exist between them)
  - **Complementary:** *alive* ↔ *dead*, *true* ↔ *false* (no middle ground)
  - **Relational:** *buy* ↔ *sell*, *teacher* ↔ *student* (one implies the other)

#### 3. Homonymy (Same form, unrelated meanings)
- Two **different** lexemes that happen to have the **same spelling/pronunciation** but completely **unrelated** meanings.
- Example:
  - *bank* (financial) vs. *bank* (river) — unrelated origins
  - *bat* (animal) vs. *bat* (cricket bat)
  - *pen* (writing tool) vs. *pen* (animal enclosure)

#### 4. Polysemy (One word, multiple related meanings)
- A **single** lexeme with **multiple related** senses (unlike homonymy where meanings are unrelated).
- Example:
  - *head*: head of a body, head of a department, head of a nail — all related to "top/leader"
  - *mouth*: mouth of a person, mouth of a river, mouth of a cave
- **Key difference from Homonymy:** Polysemous senses share a **common origin** or metaphorical connection.

#### 5. Hyponymy & Hypernymy (IS-A relationship)
- **Hyponymy:** X is a hyponym of Y if X **IS-A** type of Y.
- **Hypernymy:** Y is a hypernym of X (the broader category).
- Example:
  - *dog* IS-A *animal* → dog is a **hyponym** of animal
  - *animal* is the **hypernym** of dog
  - *rose* IS-A *flower* IS-A *plant*

```text
          ENTITY (hypernym)
           /       \
      ANIMAL      PLANT
       / \          |
    DOG   CAT    FLOWER
                  /   \
               ROSE  LILY   (hyponyms)
```

#### 6. Meronymy & Holonymy (PART-OF relationship)
- **Meronymy:** X is a meronym of Y if X is a **PART-OF** Y.
- **Holonymy:** Y is a holonym of X (the whole).
- Example:
  - *wheel* is a **meronym** of *car* (wheel is PART-OF car)
  - *car* is the **holonym** of *wheel*
  - *finger* PART-OF *hand* PART-OF *body*

#### 7. Metonymy (Using one entity to refer to a related entity)
- Using the name of one thing to refer to something associated with it.
- Example:
  - "The **White House** announced…" → refers to the US President/Administration
  - "I read **Shakespeare**" → refers to works written by Shakespeare
  - "**Hollywood** produces blockbusters" → refers to the film industry

### Summary Table

| Relation | Definition | Example |
|----------|-----------|---------|
| Synonymy | Same/similar meaning | *big* ↔ *large* |
| Antonymy | Opposite meaning | *hot* ↔ *cold* |
| Homonymy | Same form, unrelated meaning | *bat* (animal) vs *bat* (sports) |
| Polysemy | Same word, related meanings | *head* (body/dept/nail) |
| Hyponymy | IS-A (specific → general) | *dog* IS-A *animal* |
| Meronymy | PART-OF | *wheel* PART-OF *car* |
| Metonymy | Associated reference | *White House* = US President |

---

## Q2) Explain the different approaches to Word Sense Disambiguation (WSD)

### What is WSD?

**Word Sense Disambiguation** is the task of determining which **sense** (meaning) of a word is used in a given context, when the word has multiple possible meanings.

- Example: "I went to the **bank** to deposit money" → bank = financial institution (not river bank)

### Classification of WSD Approaches

```text
┌─────────────────────────────────────────────────┐
│           WSD APPROACHES                        │
├──────────────┬──────────────┬───────────────────┤
│  Knowledge-  │  Supervised  │  Semi/Unsupervised│
│  Based       │  (ML-based)  │                   │
├──────────────┼──────────────┼───────────────────┤
│ • Lesk Algo  │ • Naive Bayes│ • Yarowsky (Semi) │
│ • WordNet    │ • Decision   │ • Hyperlex (Unsup)│
│   overlap    │   List       │                   │
│ • Selectional│ • SVM, kNN   │                   │
│   Restrictions│             │                   │
└──────────────┴──────────────┴───────────────────┘
```

---

### 1. Knowledge-Based Approach — Lesk's Algorithm

**Idea:** Choose the sense whose **dictionary definition** (gloss) has the most **word overlap** with the context.

**How it works:**
1. For the ambiguous word, retrieve all senses from a dictionary (e.g., WordNet)
2. For each sense, get its **gloss** (definition text + example sentences)
3. Count the **number of overlapping words** between the gloss and the surrounding context
4. Pick the sense with the **maximum overlap**

**Example:** "The **bank** can guarantee deposits will eventually cover future tuition costs."

| Sense | Gloss | Overlap with context |
|-------|-------|:--------------------:|
| bank¹ (financial) | "A financial institution that accepts **deposits** and channels money into lending" | **deposits** → overlap = 1 |
| bank² (river) | "Sloping land beside a body of water" | No overlap = 0 |

→ Choose **bank¹** (financial institution). ✅

**Limitation:** Dictionary glosses are short → small overlap → unreliable for short contexts.

---

### 2. Supervised — Naïve Bayes Classifier

**Idea:** Train a classifier on a **sense-tagged corpus** (where humans have labeled the correct sense). Use surrounding words as features.

**How it works:**
1. **Training:** From the tagged corpus, compute:
   - $P(s_k)$ = prior probability of each sense $s_k$
   - $P(w_j | s_k)$ = probability of word $w_j$ appearing near sense $s_k$
2. **Classification:** For a new context with words $w_1, w_2, \ldots, w_n$:

$$\hat{s} = \arg\max_{s_k} P(s_k) \prod_{j=1}^{n} P(w_j | s_k)$$

**Example:**
- Training data has 100 instances of "bank": 70 are financial, 30 are river-bank
- $P(\text{financial}) = 0.7$, $P(\text{river}) = 0.3$
- If context contains "money": $P(\text{money}|\text{financial}) = 0.5$, $P(\text{money}|\text{river}) = 0.01$
- $P(\text{financial}|\text{money}) \propto 0.7 \times 0.5 = 0.35$ ≫ $0.3 \times 0.01 = 0.003$
- → Choose **financial** ✅

**Limitation:** Requires large sense-tagged corpora (expensive to create).

---

### 3. Supervised — Decision List

**Idea:** Create an ordered list of **if-then rules** based on collocational features, sorted by strength (log-likelihood ratio).

**How it works:**
1. Extract features from tagged corpus (e.g., "word to the right is X", "word in window is Y")
2. For each feature, compute:
$$\text{Score}(f) = \left|\log_2 \frac{P(s_1|f)}{P(s_2|f)}\right|$$
3. Sort features by score (highest first) → this is the **Decision List**
4. For a new instance, go down the list; the **first matching rule** determines the sense

**Example Decision List for "bank":**

| Rank | Feature | Predicted Sense | Score |
|:----:|---------|:--------------:|:-----:|
| 1 | "interest" in window | financial | 8.2 |
| 2 | "river" in window | river-bank | 7.5 |
| 3 | "money" in window | financial | 6.1 |
| 4 | "water" in window | river-bank | 5.8 |
| ... | default | most frequent sense | 0 |

---

### 4. Semi-Supervised — Yarowsky Algorithm

**Idea:** Start with a **small seed set** of labeled examples, then iteratively **self-train** — label unlabeled data using the current model and retrain.

**How it works:**
1. **Seed step:** Use a few hand-labeled examples or simple heuristics
   - e.g., "bank" near "money" → financial; "bank" near "river" → river-bank
2. **Bootstrap loop:**
   - Train a classifier (decision list) on currently labeled data
   - Apply it to unlabeled data → label high-confidence predictions
   - Add newly labeled data to training set
   - Repeat until convergence
3. **Key heuristic — One sense per collocation:** A word used near the same collocation almost always has the same sense.
4. **Key heuristic — One sense per discourse:** Within a single document, a word almost always has the same sense.

```text
┌────────────────┐
│  Seed labels   │──→ Train classifier ──→ Label unlabeled data
│  (few examples)│         ↑                       │
└────────────────┘         │                       ↓
                           └──── Add high-confidence labels
                                 (repeat until convergence)
```

**Advantage:** Needs very few labeled examples.

---

### 5. Unsupervised — HyperLex

**Idea:** Build a **co-occurrence graph** of words that appear near the ambiguous word. Different senses form different **clusters** (hubs) in the graph.

**How it works:**
1. Collect all contexts of the ambiguous word from a large corpus
2. Build a **co-occurrence graph:**
   - Nodes = words that co-occur with the target word
   - Edges = two nodes are connected if they frequently co-occur with each other
3. Identify **hub nodes** — nodes with high connectivity that link to many other nodes
4. Each hub represents a different **sense** of the ambiguous word
5. To disambiguate a new instance: find which hub's neighborhood overlaps most with the context

**Example for "bank":**
```text
        money ── deposit ── account
           \       |       /
            \      |      /
             FINANCIAL HUB ← hub 1
                  |
      ---- bank (ambiguous) ----
                  |
             RIVER HUB     ← hub 2
            /      |      \
           /       |       \
        water ── shore ── stream
```

**Advantage:** No labeled data needed at all — purely corpus-driven.

### Comparison of WSD Approaches

| Approach | Data Required | Accuracy | Key Method |
|----------|:------------:|:--------:|------------|
| **Lesk** (Knowledge) | Dictionary only | Low-Medium | Gloss overlap |
| **Naïve Bayes** (Supervised) | Large tagged corpus | High | Probabilistic classification |
| **Decision List** (Supervised) | Tagged corpus | High | Ordered if-then rules |
| **Yarowsky** (Semi-supervised) | Few seed examples | Medium-High | Bootstrapping |
| **HyperLex** (Unsupervised) | Raw corpus only | Medium | Co-occurrence graph hubs |

---

## Q3) What is WordNet? Explain structure of WordNet with an example

### What is WordNet?

**WordNet** is a large **lexical database** of English (and other languages) developed at **Princeton University**. It groups words into sets of synonyms called **synsets** and records various semantic relations between them.

- It's like a **dictionary + thesaurus + ontology** combined
- Contains ~117,000 synsets and ~155,000 words (for English WordNet)
- Covers **nouns, verbs, adjectives, adverbs**
- Widely used in NLP for WSD, text similarity, information retrieval

### Structure of WordNet

```text
┌───────────────────────────────────────────────────────┐
│                    WORDNET STRUCTURE                   │
│                                                       │
│  ┌──────────────────────────────────┐                 │
│  │         SYNSET (Synonym Set)     │                 │
│  │  • Set of synonymous words       │                 │
│  │  • Definition (Gloss)            │                 │
│  │  • Example sentence(s)           │                 │
│  │  • POS tag (noun/verb/adj/adv)   │                 │
│  └──────────────────────────────────┘                 │
│                    │                                   │
│         Connected by RELATIONS:                        │
│  • Hypernymy / Hyponymy (IS-A)                        │
│  • Meronymy / Holonymy (PART-OF)                      │
│  • Synonymy (within synset)                           │
│  • Antonymy (opposite synsets)                        │
│  • Entailment (verb: X entails Y)                     │
│  • Troponymy (verb: manner-of)                        │
└───────────────────────────────────────────────────────┘
```

### Key Components

**1. Synset (Synonym Set):**
- The fundamental building block of WordNet
- A synset = {group of words} + {gloss} + {examples}
- Each synset represents ONE concept/meaning
- Example:
  - Synset: `{car, auto, automobile, motorcar}` → Gloss: "a motor vehicle with four wheels"

**2. Gloss:**
- The dictionary-style definition attached to each synset
- Example: For synset `{bank}₁` → Gloss: "a financial institution that accepts deposits"

**3. Semantic Relations (between synsets):**

| Relation | Meaning | Example |
|----------|---------|---------|
| **Hypernymy** | IS-A (parent) | `{dog}` → hypernym → `{canine}` → `{animal}` |
| **Hyponymy** | IS-A (child) | `{animal}` → hyponym → `{dog}`, `{cat}` |
| **Meronymy** | PART-OF | `{wheel}` → meronym → `{car}` |
| **Holonymy** | HAS-PART | `{car}` → holonym → `{wheel}` |
| **Antonymy** | Opposite | `{good}` ↔ `{bad}` |
| **Troponymy** | Manner of (verbs) | `{whisper}` is a troponym of `{speak}` |
| **Entailment** | X implies Y (verbs) | `{snore}` entails `{sleep}` |

### Example: WordNet Entry for "Dog"

```text
WORD: "dog"

Synset 1: {dog, domestic dog, Canis familiaris}
  POS: Noun
  Gloss: "a member of the genus Canis that has been domesticated"
  Example: "The dog barked at the mailman"
  Hypernym: {canine, canid} → {carnivore} → {mammal} → {animal} → {entity}
  Hyponyms: {poodle}, {dalmatian}, {retriever}, {terrier}, ...
  Meronyms: {paw}, {tail}, {fur}

Synset 2: {frump, dog}
  POS: Noun
  Gloss: "a dull unattractive unpleasant person"
  Example: "She's a real dog"

Synset 3: {dog, chase}
  POS: Verb
  Gloss: "to follow or pursue closely"
  Example: "The detective dogged the suspect"
```

### WordNet Hierarchy Example (Nouns)

```text
                        ENTITY
                       /      \
                PHYSICAL       ABSTRACT
                ENTITY          ENTITY
               /      \
          OBJECT     LIVING THING
            |           |
         ARTIFACT     ORGANISM
           |          /      \
        VEHICLE    ANIMAL    PLANT
         /    \      |
       CAR    BUS   DOG
       / \          / \
    SEDAN SUV  POODLE RETRIEVER
```

### Applications of WordNet in NLP
1. **Word Sense Disambiguation** — Lesk algorithm uses WordNet glosses
2. **Text Similarity** — Path length between synsets measures semantic similarity
3. **Information Retrieval** — Query expansion using synonyms
4. **Machine Translation** — Cross-lingual WordNets (e.g., EuroWordNet, IndoWordNet)
5. **Sentiment Analysis** — SentiWordNet assigns sentiment scores to synsets

### Other Language Dictionaries

| Resource | Description |
|----------|-------------|
| **BabelNet** | Multilingual encyclopedic dictionary + semantic network (combines WordNet + Wikipedia) |
| **FrameNet** | Focuses on semantic frames — situations/events with participants and roles |
| **VerbNet** | Verb-focused lexicon with syntactic and semantic information |
| **ConceptNet** | Common-sense knowledge graph |

---

## Q4) Explain the following algorithms with examples: a) Yarowsky, b) Hyperlex, c) Lesk

### a) Yarowsky Algorithm (Semi-Supervised WSD)

**Type:** Semi-supervised (bootstrapping / self-training)

**Key Idea:** Start with minimal labeled data (seeds) and iteratively expand the labeled set by leveraging two heuristics:
1. **One sense per collocation:** A word used with the same neighboring word almost always has the same sense
2. **One sense per discourse:** Within a single document, a word usually has only one sense

**Algorithm Steps:**

```
INPUT: Ambiguous word w, large unlabeled corpus, small seed set
OUTPUT: Sense labels for all instances of w

1. SEED: Label a small initial set using heuristics
   - e.g., "bank" + "money" → financial; "bank" + "river" → geographic
   
2. LOOP until convergence:
   a. Train a Decision List classifier on all currently labeled instances
   b. Apply classifier to ALL unlabeled instances of w
   c. For instances classified with HIGH CONFIDENCE:
      - Add them to the labeled training set with their predicted labels
   d. Optionally apply "one sense per discourse":
      - If any instance of w in a document was labeled,
        label ALL other instances of w in that document with the same sense
   e. Retrain and repeat
   
3. RETURN final sense labels
```

**Example:**

```text
Corpus with 1000 instances of "plant":

Step 0 — Seeds (5 labeled):
  "The plant manufactures cars" → plant = FACTORY
  "The plant has green leaves" → plant = VEGETATION

Step 1 — Train Decision List:
  Rule 1: "factory" in window → FACTORY (score: 9.1)
  Rule 2: "leaf/leaves" in window → VEGETATION (score: 8.5)
  Rule 3: "grow" in window → VEGETATION (score: 7.2)

Step 1 — Label high-confidence unlabeled instances:
  "Workers at the plant went on strike" → FACTORY (conf: 0.95) ✓ ADD
  "Water the plant daily" → VEGETATION (conf: 0.92) ✓ ADD
  ...now have 50 labeled instances

Step 2 — Retrain with 50 labels → discover more features:
  Rule 4: "worker" in window → FACTORY
  Rule 5: "garden" in window → VEGETATION
  ...label 200 more instances

Steps 3–N — Continue until no more high-confidence labels can be added
```

**Advantages:**
- Needs very few initial labeled examples
- Can leverage large unlabeled corpora
- Achieves near-supervised accuracy

---

### b) HyperLex Algorithm (Unsupervised WSD)

**Type:** Unsupervised (no labeled data needed)

**Key Idea:** Build a co-occurrence graph and identify **hubs** — highly connected nodes that represent different senses.

**Algorithm Steps:**

```
INPUT: Ambiguous word w, large corpus
OUTPUT: Sense clusters for w

1. COLLECT all paragraphs/sentences containing w from the corpus

2. BUILD co-occurrence graph G:
   - Nodes = content words that co-occur with w (above frequency threshold)
   - Edge between nodes u and v if they co-occur significantly
     (measured by mutual information, chi-square, etc.)
   - Edge weight = strength of co-occurrence

3. IDENTIFY HUBS in G:
   - A hub = node with degree (number of connections) above a threshold
   - Hubs represent the "core" of different sense clusters

4. CLUSTER:
   - Assign each non-hub node to the hub it's most strongly connected to
   - Each hub + its assigned nodes = one SENSE CLUSTER

5. DISAMBIGUATE new instance:
   - Find which sense cluster has the most overlap with the context words
   - Assign that sense to the instance
```

**Example for "crane":**

```text
Co-occurrence graph for "crane":

  steel ── construction ── building
      \         |          /
       \        |         /
        MACHINE HUB (hub 1) ← sense: construction crane
              |
     ---- crane ----
              |
         BIRD HUB (hub 2) ← sense: crane bird
        /       |        \
       /        |         \
    fly ── feather ── migrate ── wetland

Hub 1 neighbors: {steel, construction, building, lift, tower}
Hub 2 neighbors: {fly, feather, migrate, wetland, beak}

New sentence: "The crane lifted the steel beam"
  Context words: {lifted, steel, beam}
  Overlap with Hub 1: steel ✓, (lift ~ lifted) → MACHINE SENSE ✅
```

**Advantages:** No labeled data, no dictionary needed — purely corpus-driven
**Disadvantages:** Sensitive to corpus size and co-occurrence threshold

---

### c) Lesk Algorithm (Knowledge-Based WSD)

**Type:** Knowledge-based (uses dictionary/WordNet glosses)

**Key Idea:** Choose the word sense whose **dictionary definition (gloss)** shares the most words with the **context**.

**Algorithm:**

```
INPUT: Word w in context sentence S, dictionary D
OUTPUT: Best sense of w

1. Get all senses of w from dictionary: {s1, s2, ..., sn}
2. For each sense si:
   a. Get gloss(si) = definition text + example sentences
   b. Compute overlap = |words in gloss(si) ∩ words in S|
3. Return the sense with MAXIMUM overlap
```

**Detailed Example:**

**Sentence:** "The **bank** can guarantee deposits will eventually cover future tuition costs because it invests in growing companies."

**From WordNet:**

**Sense 1 — bank (financial institution):**
Gloss: "a financial institution that accepts **deposits** and channels the money into lending activities; he cashed a check at the **bank**; that **bank** holds the mortgage on my home"

**Sense 2 — bank (geographical):**
Gloss: "sloping land (especially the slope beside a body of water); they pulled the canoe up on the **bank**; he sat on the **bank** of the river and watched the currents"

**Overlap computation:**

| Sense | Gloss Words | Context Words | Overlapping Words | Count |
|-------|-------------|---------------|:-----------------:|:-----:|
| Sense 1 (financial) | {financial, institution, accepts, **deposits**, channels, money, lending, ...} | {guarantee, **deposits**, eventually, cover, future, tuition, costs, invests, growing, companies} | **deposits** | **1** |
| Sense 2 (geographic) | {sloping, land, slope, beside, body, water, canoe, river, currents, ...} | {guarantee, deposits, eventually, cover, future, tuition, costs, invests, growing, companies} | (none) | **0** |

**Result:** Sense 1 wins with overlap = 1 > 0

> **Boxed Answer:** Choose Sense 1 — bank = **financial institution** ✅

**Simplified Lesk (most commonly used):**
- Only compares gloss of each sense with the context sentence
- **Extended Lesk** also includes glosses of **related synsets** (hypernyms, hyponyms, etc.) to increase overlap

**Limitations:**
- Short glosses → small vocabulary → low overlap
- Doesn't account for word order or syntax
- Doesn't use word frequency information

---

# 📘 CHAPTER 5 — Pragmatic & Discourse Processing

---

## Q5) What is Reference Resolution? What are the components in Reference Resolution?

### Definition

**Reference Resolution** is the task of determining which real-world entity a linguistic expression refers to. It involves connecting **referring expressions** in text to the **entities (referents)** they denote.

- Example: "**John** went to the store. **He** bought milk." → "He" refers to "John"
- This is critical for understanding text — without resolving references, an NLP system can't track who did what.

### Types of Reference Resolution

| Type | Definition | Example |
|------|-----------|---------|
| **Coreference Resolution** | Determining which expressions refer to the **same entity** | "*John* said *he* was tired" → John = he |
| **Anaphora Resolution** | Resolving a reference that points **back** to a previous mention | "*The cat* sat. *It* purred." → It = the cat |
| **Cataphora Resolution** | Resolving a reference that points **forward** | "Before *he* left, *John* locked the door." → he = John |

### Components of Reference Resolution

```text
┌─────────────────────────────────────────────────────┐
│          REFERENCE RESOLUTION PIPELINE              │
│                                                     │
│  1. REFERRING EXPRESSION ──→ 2. DISCOURSE MODEL     │
│         (input)                  (entity tracker)    │
│                                      │               │
│  3. CONSTRAINTS & ──────────→ 4. RESOLUTION         │
│     PREFERENCES                  (output: referent)  │
└─────────────────────────────────────────────────────┘
```

#### Component 1: Referring Expressions
The linguistic expression that refers to an entity (see Q7 for all five types):
- Pronouns: *he, she, it, they*
- Definite NPs: *the book, the president*
- Proper nouns: *John, India, Google*
- Demonstratives: *this, that, these*

#### Component 2: Discourse Model
- A data structure that tracks all **entities** mentioned so far in the discourse
- Each entity has:
  - **Attributes:** gender, number, animacy, semantic type
  - **Salience:** how prominent/important the entity is at this point
  - **Recency:** when the entity was last mentioned
- Updated as each sentence is processed

#### Component 3: Constraints & Preferences
- **Constraints** (hard rules — must be satisfied):
  - **Number agreement:** "The dogs…*they*" (plural ↔ plural)
  - **Gender agreement:** "Mary…*she*" (female ↔ female pronoun)
  - **Person agreement:** "I went…*I* returned" (first person ↔ first person)
- **Preferences** (soft rules — preferred but not absolute):
  - **Recency:** Prefer the most recently mentioned entity
  - **Grammatical role:** Prefer subjects over objects
  - **Parallelism:** Prefer entities in the same grammatical position

#### Component 4: Resolution Algorithm
The algorithm that combines constraints and preferences to find the best referent:
- **Hobbs Algorithm:** Syntactic tree search
- **Centering Theory:** Tracks focus/center of attention
- **Machine Learning:** Mention-pair models, entity-mention models

---

## Q6) What are the syntactic and semantic constraints and preferences in co-reference resolution? Explain with suitable examples.

### Constraints (Hard Rules — Must be obeyed)

Constraints **filter out** impossible antecedents. If a constraint is violated, that antecedent is eliminated.

#### A) Syntactic Constraints

**1. Number Agreement:**
- The referring expression and its antecedent must agree in number (singular/plural).

| Example | Valid? |
|---------|:------:|
| "*The boy* lost *his* book." (singular ↔ singular) | ✅ |
| "*The boys* lost *his* book." (plural ↔ singular) | ❌ |
| "*The boys* lost *their* books." (plural ↔ plural) | ✅ |

**2. Person Agreement:**
- First/second/third person must match.

| Example | Valid? |
|---------|:------:|
| "*I* finished *my* work." (1st ↔ 1st) | ✅ |
| "*You* said *I* would help." (2nd ↔ 1st — different referents) | ✅ (different entities) |

**3. Gender Agreement:**
- The pronoun must match the gender of the antecedent.

| Example | Valid? |
|---------|:------:|
| "*Mary* said *she* was happy." (female ↔ she) | ✅ |
| "*John* said *she* was happy." (male ↔ she) | ❌ (different referents) |
| "*The table* lost *its* leg." (neuter ↔ its) | ✅ |

**4. Binding Theory Constraints (Chomsky):**

| Constraint | Rule | Example |
|-----------|------|---------|
| **Principle A** | A reflexive pronoun must be bound by its antecedent **within the same clause** | "*John*_i hurt *himself*_i" ✅; "*John*_i said *himself*_i was tired" ❌ |
| **Principle B** | A non-reflexive pronoun must be **free** (not bound) within its clause | "*He*_i hurt *him*_j" (he ≠ him, different entities) |
| **Principle C** | A full NP (name) cannot be bound by anything | "*He*_i said *John*_i left" ❌ (He can't bind John) |

---

#### B) Semantic Constraints

**1. Selectional Restrictions (Verb-argument compatibility):**
- The referent must satisfy the semantic requirements of its role in the sentence.

| Example | Analysis |
|---------|----------|
| "*The car* broke down. *It* was towed." | *It* = car ✅ (cars can be towed) |
| "*The idea* was new. *It* was towed." | *It* = idea? ❌ (ideas can't be towed — semantic violation) |

**2. Animacy:**
- Some predicates require animate/inanimate subjects or objects.

| Example | Analysis |
|---------|----------|
| "*John* ate lunch. *He* was full." | *He* = John ✅ (animate → can eat and be full) |
| "*The rock* fell. *He* was heavy." | *He* = rock? ❌ (*he* requires animate referent) |

---

### Preferences (Soft Rules — Used to rank candidates)

Preferences **rank** the remaining candidates after constraints have filtered out impossible ones.

**1. Recency Preference:**
- Prefer the **most recently mentioned** entity.
- "John met **Bill**. **He** was happy." → *He* likely = Bill (more recent)

**2. Grammatical Role Preference:**
- **Subject > Object > Other**
- "**John** told Mary that **he** was leaving." → *he* more likely = John (subject)

**3. Repeated Mention Preference:**
- Entities mentioned more frequently are preferred.
- "**John** went to the store. **John** bought milk. **He** went home." → *He* = John

**4. Parallelism Preference:**
- Prefer antecedents in the **same grammatical position**.
- "**John** beat Bill. **He** beat George." → *He* = John (subject ↔ subject parallelism)
- "John beat **Bill**. George beat **him**." → *him* = Bill (object ↔ object parallelism)

**5. Coherence / World Knowledge Preference:**
- Use common-sense knowledge.
- "The city council refused the demonstrators a permit because **they** feared violence."
  - *they* = city council (councils fear violence → grant permits)
- "The city council refused the demonstrators a permit because **they** advocated violence."
  - *they* = demonstrators (demonstrators advocated violence)

### Summary

| Category | Type | Rule | Example |
|----------|:----:|------|---------|
| **Constraint** | Syntactic | Number agreement | *boys*…*they* ✅ |
| **Constraint** | Syntactic | Gender agreement | *Mary*…*she* ✅ |
| **Constraint** | Syntactic | Binding theory | *John hurt himself* ✅ |
| **Constraint** | Semantic | Selectional restriction | *car…towed* ✅ |
| **Constraint** | Semantic | Animacy | *John…he* ✅ |
| **Preference** | Soft | Recency | Prefer most recent mention |
| **Preference** | Soft | Grammatical role | Prefer subjects |
| **Preference** | Soft | Parallelism | Same position preferred |
| **Preference** | Soft | World knowledge | Common sense reasoning |

---

## Q7) What are the five types of referring expressions with examples? Explain the three types of referents that complicate the reference resolution problem.

### Five Types of Referring Expressions

A **referring expression** is any linguistic expression used to refer to an entity in the world or in the discourse.

#### 1. Indefinite Noun Phrases
- Introduce a **new** entity into the discourse (first mention).
- Formed with: *a, an, some, certain*
- Example: "***A man*** walked into the room." → Introduces a new entity "man"
- Example: "I saw ***some students*** in the library."

#### 2. Definite Noun Phrases
- Refer to an entity that is **already known** or uniquely identifiable.
- Formed with: *the* + noun
- Example: "A man walked in. ***The man*** sat down." → Refers back to the previously introduced man
- Example: "Please open ***the door***." → Assumes there is one identifiable door

#### 3. Pronouns
- Short functional words that refer to a previously mentioned or contextually salient entity.
- Personal: *he, she, it, they, him, her, them*
- Possessive: *his, her, its, their*
- Reflexive: *himself, herself, itself, themselves*
- Demonstrative: *this, that, these, those*
- Example: "***Mary*** went home. ***She*** was tired." → *She* = Mary

#### 4. Proper Nouns (Names)
- Refer to a **specific, named** entity.
- Example: "***Barack Obama*** gave a speech. ***Obama*** discussed healthcare."
- Example: "***Google*** announced a new product."
- Can be used for **first mention** (unlike definite NPs which usually need prior context).

#### 5. Demonstrative Pronouns / Demonstrative NPs
- Use demonstratives (*this, that, these, those*) to point to entities.
- Example: "I saw two movies. ***This one*** was better." → Points to a specific movie
- Example: "***That idea*** won't work." → Points to a previously discussed idea
- Often have a **deictic** (pointing) function.

### Summary Table of Five Types

| # | Type | Signal Words | Function | Example |
|:-:|------|:-------------|----------|---------|
| 1 | Indefinite NP | a, an, some | Introduce NEW entity | "**A dog** barked" |
| 2 | Definite NP | the + noun | Refer to KNOWN entity | "**The dog** sat down" |
| 3 | Pronoun | he, she, it, they | Short reference to SALIENT entity | "**He** was happy" |
| 4 | Proper Noun | names | Refer to SPECIFIC named entity | "**John** left" |
| 5 | Demonstrative | this, that, these, those | POINT to entity | "**That** is wrong" |

---

### Three Types of Referents That Complicate Reference Resolution

#### 1. Inferrables (Bridging References)
- The referent has **not been explicitly mentioned** but can be **inferred** from a mentioned entity.
- The connection requires world knowledge or part-whole reasoning.
- Example: "I walked into ***the room***. ***The ceiling*** was painted white."
  - "The ceiling" was never mentioned before, but it can be **inferred** because rooms have ceilings (meronymy).
- Example: "I took ***a flight*** to Mumbai. ***The pilot*** was friendly."
  - "The pilot" is inferrable from "flight" (flights have pilots).
- **Why it's hard:** The system needs to know that rooms have ceilings, flights have pilots, etc. — this requires common-sense knowledge.

#### 2. Discontinuous Set References (Split Antecedents)
- The referent is a **group** formed by combining **multiple separately mentioned** entities.
- Example: "***John*** went to the store. He met ***Mary*** there. ***They*** decided to go to a restaurant."
  - "They" = John + Mary (combined from two separate mentions)
- Example: "***The manager*** praised ***the intern***. ***They*** had worked well together."
  - "They" = manager + intern
- **Why it's hard:** The pronoun doesn't refer to a single previously mentioned NP — the system must recognize that multiple entities can be grouped.

#### 3. Non-Referring Expressions (Pleonastic / Expletive Pronouns)
- Expressions that **look like** referring expressions but **don't actually refer** to any entity.
- Example: "***It*** is raining." → "It" doesn't refer to anything — it's a **pleonastic** (dummy) pronoun
- Example: "***It*** seems that John is late." → "It" is expletive, not referential
- Example: "***There*** are three books on the table." → "There" is existential, not referential
- **Why it's hard:** The system must distinguish between referential "it" ("The book fell. **It** broke.") and pleonastic "it" ("**It** is important to study.").

### Why These Three Complicate Resolution

| Referent Type | Problem | Required Knowledge |
|:---:|---------|:---:|
| **Inferrables** | Antecedent was never explicitly mentioned | World knowledge / ontology |
| **Discontinuous sets** | Referent is formed by combining multiple mentions | Set formation logic |
| **Non-referring** | Expression looks referential but isn't | Syntactic pattern recognition |

---

## Q8) Explain the following algorithms for anaphora resolution: a) Hobbs, b) Centering

### What is Anaphora Resolution?

**Anaphora resolution** is the specific task of finding the antecedent (referent) for a **pronoun** or other anaphoric expression that refers back to a previously mentioned entity.

- Example: "***The cat*** sat on the mat. ***It*** was purring." → *It* = the cat

---

### a) Hobbs Algorithm (Syntactic Tree Search)

**Key Idea:** Search for the antecedent of a pronoun by traversing the **parse tree** in a specific order that reflects syntactic preferences (recency, subject preference, etc.).

**Algorithm Steps:**

```
INPUT: A pronoun P in a parse tree
OUTPUT: The antecedent NP of P

1. Begin at the NP node immediately dominating the pronoun P
2. Go UP to the first NP or S node. Call this node X.
   Set path-to-X = the path you climbed.

3. Traverse ALL branches below X, to the LEFT of the path,
   in LEFT-TO-RIGHT, BREADTH-FIRST order:
   - For each NP encountered:
     a. If it agrees with P in number, gender, person → PROPOSE it as antecedent
     b. If it passes selectional restrictions → ACCEPT it ✅
     c. Else → continue searching

4. If no antecedent found below X:
   - Go UP to the next NP or S node above X. Call this new node X.
   - Repeat step 3 (search left branches below new X)

5. If X is an S node (sentence), also search the PREVIOUS SENTENCES
   in recency order (most recent first):
   - Search each sentence's parse tree in LEFT-TO-RIGHT, BREADTH-FIRST order
   - Propose NPs that agree with P

6. Return the FIRST acceptable NP found.
```

**Example:**

```text
Sentence: "The nurse told the patient that she would be discharged."

Parse tree:
          S
         / \
       NP    VP
       |    /   \
   The nurse  told  NP    S'
                     |   /    \
                The patient  that  S
                                  / \
                                NP    VP
                                |   would be discharged
                               she

Resolving "she":
Step 1: Start at NP dominating "she"
Step 2: Go up to S' (the embedded clause)
Step 3: Search left of path — no NPs to the left inside S'
Step 4: Go up to VP of main clause → NP "the patient" found!
        Gender: patient could be female ✓
        → Propose "the patient" as antecedent

But wait — continue: Go up to S (main clause)
        → NP "the nurse" found (to the left)
        → Gender: nurse could be female ✓

Both are valid! Hobbs prefers the one found FIRST in the search order.
In this case: "the patient" was found first → she = the patient

(However, pragmatic knowledge might override this — nurses typically
 tell patients about discharge, so "she" more likely = "the patient" ✓)
```

**Characteristics of Hobbs Algorithm:**

| Feature | Detail |
|---------|--------|
| Type | Purely syntactic (tree-based) |
| Requires | Full parse tree |
| Strengths | Simple, deterministic, handles recency naturally |
| Weaknesses | Ignores semantics and world knowledge |
| Accuracy | ~80% on pronoun resolution |

---

### b) Centering Algorithm (Centering Theory)

**Key Idea:** Track the **center of attention** (focus) in a discourse. The entity that is the current **focus** is the most likely antecedent for a pronoun.

**Core Concepts:**

| Concept | Definition |
|---------|-----------|
| **Cf (Forward-looking centers)** | The set of all entities **mentioned** in the current utterance, **ranked** by grammatical role: Subject > Object > Other |
| **Cp (Preferred center)** | The **highest-ranked** entity in Cf — the entity most likely to be talked about next |
| **Cb (Backward-looking center)** | The entity in the current utterance that was the **highest-ranked** member of the previous utterance's Cf — i.e., what the discourse is currently "about" |

**Ranking:** Subject > Object(direct) > Object(indirect) > Other

**Transitions between utterances:**

| Transition | Condition | Meaning |
|-----------|-----------|---------|
| **CONTINUE** | Cb(Uₙ) = Cb(Uₙ₋₁) AND Cb(Uₙ) = Cp(Uₙ) | Same topic, still in focus |
| **RETAIN** | Cb(Uₙ) = Cb(Uₙ₋₁) AND Cb(Uₙ) ≠ Cp(Uₙ) | Same topic, but focus shifting |
| **SMOOTH SHIFT** | Cb(Uₙ) ≠ Cb(Uₙ₋₁) AND Cb(Uₙ) = Cp(Uₙ) | Topic changed, new focus established |
| **ROUGH SHIFT** | Cb(Uₙ) ≠ Cb(Uₙ₋₁) AND Cb(Uₙ) ≠ Cp(Uₙ) | Topic changed, focus unstable |

**Preference ordering:** CONTINUE > RETAIN > SMOOTH SHIFT > ROUGH SHIFT

**Centering Rule 1:** If any element of Cf(Uₙ₋₁) is realized as a pronoun in Uₙ, then **Cb(Uₙ) must be realized as a pronoun** too.

**Centering Rule 2:** Prefer transitions in the order: CONTINUE > RETAIN > SMOOTH SHIFT > ROUGH SHIFT.

**Example:**

```text
U1: "John went to his favourite restaurant."
    Cf(U1) = [John, restaurant]  (Subject > Object)
    Cp(U1) = John
    Cb(U1) = undefined (first utterance)

U2: "He ordered pasta."
    Cf(U2) = [He=John, pasta]
    Cp(U2) = He = John
    Cb(U2) = John (highest-ranked entity from Cf(U1) mentioned in U2)
    Transition: Cb undefined → John = CONTINUE ✅

U3: "It was delicious."
    Cf(U3) = [It=pasta]  — but who is "it"?
    
    Option A: It = pasta
      Cb(U3) = pasta? But pasta was NOT the highest-ranked entity from Cf(U2).
      The highest-ranked from Cf(U2) mentioned in U3: pasta (John not mentioned)
      Cb(U3) = John? (John is highest ranked in Cf(U2) but not in U3)
      Actually Cb(U3) = highest-ranked element of Cf(U2) that appears in U3
      Only pasta appears → Cb(U3) = pasta
      Transition: Cb changed (John → pasta) = SMOOTH SHIFT
    
    Since "It" is a pronoun referring to pasta (the food being discussed),
    this makes discourse sense → It = pasta ✅

U4: "He paid the bill."
    Cf(U4) = [He=John, bill]
    Cb(U4) = highest-ranked from Cf(U3) in U4 = ? 
    (pasta not in U4, so Cb must come from broader context → John)
    Transition: back to John = SMOOTH SHIFT
```

**Comparison: Hobbs vs. Centering**

| Feature | Hobbs | Centering |
|---------|:-----:|:---------:|
| Approach | Tree search | Discourse focus tracking |
| Data needed | Parse tree | Utterance sequence |
| Handles multi-sentence | Yes (searches previous sentences) | Yes (tracks Cb across utterances) |
| Semantic knowledge | No | No (but uses discourse coherence) |
| Key strength | Simple, deterministic | Models discourse focus naturally |
| Key weakness | No discourse model | More complex to implement |

---

# 📘 CHAPTER 6 — Applications of NLP

---

## Q9) Explain the working of Text Summarization System

### Definition

**Text Summarization** is the task of producing a concise version of a document (or set of documents) that retains the most important information.

### Types of Summarization

```text
┌──────────────────────────────────────────────────┐
│            TEXT SUMMARIZATION                      │
├────────────────────┬─────────────────────────────┤
│    EXTRACTIVE      │      ABSTRACTIVE            │
│  (Select sentences)│  (Generate new sentences)   │
├────────────────────┼─────────────────────────────┤
│ Pick the most      │ Understand the content and  │
│ important sentences│ rewrite in new words        │
│ from the source    │ (like a human would)        │
│ text verbatim      │                             │
├────────────────────┼─────────────────────────────┤
│ Easier to implement│ More natural, but harder    │
│ Grammatically safe │ May have factual errors     │
│ Less coherent      │ More coherent               │
└────────────────────┴─────────────────────────────┘
```

### Working of a Text Summarization System

```text
┌─────────────┐   ┌──────────────┐   ┌──────────────┐   ┌──────────┐
│  INPUT      │──→│  CONTENT     │──→│  SENTENCE    │──→│ OUTPUT   │
│  Document   │   │  ANALYSIS    │   │  SELECTION / │   │ Summary  │
│             │   │              │   │  GENERATION  │   │          │
└─────────────┘   └──────────────┘   └──────────────┘   └──────────┘
```

#### Stage 1: Preprocessing
1. **Tokenization** — Split text into sentences and words
2. **Stop word removal** — Remove common words (the, is, at, etc.)
3. **Stemming/Lemmatization** — Reduce words to root form
4. **POS tagging** — Identify parts of speech

#### Stage 2: Content Analysis (Feature Extraction)
Score each sentence based on various features:

| Feature | Description | How it helps |
|---------|-------------|:----------:|
| **Word Frequency** (TF-IDF) | Important words appear frequently | Identifies key topics |
| **Sentence Position** | First/last sentences of paragraphs are often important | Captures introductions & conclusions |
| **Title/Heading Words** | Sentences containing title words are important | Aligns with document topic |
| **Cue Phrases** | Phrases like "in summary", "importantly", "in conclusion" | Flags summary-worthy sentences |
| **Sentence Length** | Very short/long sentences may be less suitable | Filters noise |
| **Named Entities** | Sentences with proper nouns (people, places, orgs) | Identifies key actors/events |
| **Numerical Data** | Sentences with statistics, dates, numbers | Captures factual content |

#### Stage 3: Sentence Selection (Extractive) or Generation (Abstractive)

**Extractive Methods:**
1. **Frequency-based:** Score = sum of TF-IDF scores of words in sentence
2. **Graph-based (TextRank/LexRank):**
   - Build a graph where nodes = sentences, edges = similarity between sentences
   - Run PageRank-like algorithm to find most "central" sentences
   - Select top-ranked sentences
3. **Machine Learning:** Train a classifier (SVM, neural network) to classify each sentence as "include" or "exclude"

**Abstractive Methods:**
1. **Encoder-Decoder (Seq2Seq):** Encode the document, decode a summary
2. **Transformer-based:** BERT, GPT, T5, BART — state-of-the-art for abstractive summarization
3. **Template-based:** Fill predefined templates with extracted information

#### Stage 4: Post-processing
1. **Sentence ordering** — Arrange selected sentences in logical/chronological order
2. **Redundancy removal** — Remove duplicate or near-duplicate sentences
3. **Coherence improvement** — Add connectives, adjust references
4. **Length control** — Trim to desired summary length

### Example

**Input Document:**
> "The Indian Space Research Organisation (ISRO) successfully launched the Chandrayaan-3 mission on July 14, 2023. The spacecraft entered lunar orbit after several manoeuvres. On August 23, 2023, the Vikram lander made a successful soft landing near the lunar south pole. This made India the fourth country to achieve a successful Moon landing and the first to land near the south pole. The Pragyan rover deployed from the lander and conducted experiments for 14 days."

**Extractive Summary (top 2 sentences):**
> "The Indian Space Research Organisation (ISRO) successfully launched the Chandrayaan-3 mission on July 14, 2023. This made India the fourth country to achieve a successful Moon landing and the first to land near the south pole."

**Abstractive Summary:**
> "ISRO's Chandrayaan-3 mission made India the first country to soft-land near the Moon's south pole on August 23, 2023."

### Applications
- News article summarization (Google News, Apple News)
- Email summarization (Gmail)
- Legal document summarization
- Medical record summarization
- Meeting transcript summarization

---

## Q10) CYK/CKY Algorithm — Parse "The man read this book"

### What is the CYK Algorithm?

The **Cocke-Younger-Kasami (CYK)** algorithm is a **bottom-up** parsing algorithm that determines whether a string can be generated by a given **Context-Free Grammar (CFG)** in **Chomsky Normal Form (CNF)**. It also builds the parse tree.

**Requirement:** Grammar must be in **Chomsky Normal Form (CNF):**
- Rules of the form: $A \rightarrow BC$ (two non-terminals)
- Rules of the form: $A \rightarrow a$ (one terminal)
- No $\epsilon$-productions (except possibly $S \rightarrow \epsilon$)

### Given Grammar

```
S  → NP VP          Det  → that | this | a | the
S  → Aux NP VP      Noun → book | flight | meal | man
S  → VP             Verb → book | include | read
NP → Det NOM        Aux  → does
NOM → Noun
NOM → Noun NOM
VP → Verb
VP → Verb NP
```

### Step 0: Verify Grammar is in CNF

Let's check each rule:

| Rule | Form | CNF? |
|------|------|:----:|
| $S \rightarrow NP\ VP$ | $A \rightarrow BC$ | ✅ |
| $S \rightarrow Aux\ NP\ VP$ | $A \rightarrow BCD$ (3 symbols!) | ❌ — needs conversion |
| $S \rightarrow VP$ | $A \rightarrow B$ (unit production) | ❌ — needs conversion |
| $NP \rightarrow Det\ NOM$ | $A \rightarrow BC$ | ✅ |
| $NOM \rightarrow Noun$ | $A \rightarrow B$ (unit production) | ❌ — needs conversion |
| $NOM \rightarrow Noun\ NOM$ | $A \rightarrow BC$ | ✅ |
| $VP \rightarrow Verb$ | $A \rightarrow B$ (unit production) | ❌ — needs conversion |
| $VP \rightarrow Verb\ NP$ | $A \rightarrow BC$ | ✅ |
| $Det \rightarrow the, this, ...$ | $A \rightarrow a$ | ✅ |
| $Noun \rightarrow book, man, ...$ | $A \rightarrow a$ | ✅ |
| $Verb \rightarrow book, read, ...$ | $A \rightarrow a$ | ✅ |

**Conversion to CNF:**

For the CYK algorithm, we handle unit productions ($A \rightarrow B$) and long productions ($A \rightarrow BCD$) as follows:

**Unit productions — propagate upward:**
- $NOM \rightarrow Noun$: Wherever $Noun$ appears, $NOM$ also applies
- $VP \rightarrow Verb$: Wherever $Verb$ appears, $VP$ also applies
- $S \rightarrow VP$: Wherever $VP$ appears, $S$ also applies

**Long production — introduce intermediate symbol:**
- $S \rightarrow Aux\ NP\ VP$ becomes:
  - $S \rightarrow Aux\ X1$ where $X1 \rightarrow NP\ VP$

**Note:** In CYK practice, we typically handle unit productions by **adding derived categories** to the table cells rather than formally rewriting the grammar. This is the standard approach used in exams.

### Step 1: Set up the CYK Table

The input sentence has 5 words: **"The man read this book"**

| Position | 1 | 2 | 3 | 4 | 5 |
|:---:|:---:|:---:|:---:|:---:|:---:|
| Word | The | man | read | this | book |

The CYK table is a **triangular matrix** where cell $[i, j]$ contains all non-terminals that can derive the substring from word $i$ to word $j$.

```text
Table[i,j] = set of non-terminals that generate words i through j

         j=1     j=2     j=3     j=4     j=5
i=1    [1,1]   [1,2]   [1,3]   [1,4]   [1,5]
i=2            [2,2]   [2,3]   [2,4]   [2,5]
i=3                    [3,3]   [3,4]   [3,5]
i=4                            [4,4]   [4,5]
i=5                                    [5,5]
```

### Step 2: Fill Row 1 (Diagonal — single words)

Look up which non-terminals can produce each terminal word. **Include unit production chains.**

**Cell [1,1]: "The"**
- $Det \rightarrow the$ ✅
- Categories: **{Det}**

**Cell [2,2]: "man"**
- $Noun \rightarrow man$ ✅
- $NOM \rightarrow Noun$ (unit production) → NOM ✅
- Categories: **{Noun, NOM}**

**Cell [3,3]: "read"**
- $Verb \rightarrow read$ ✅
- $VP \rightarrow Verb$ (unit production) → VP ✅
- $S \rightarrow VP$ (unit production) → S ✅
- Categories: **{Verb, VP, S}**

**Cell [4,4]: "this"**
- $Det \rightarrow this$ ✅
- Categories: **{Det}**

**Cell [5,5]: "book"**
- $Noun \rightarrow book$ ✅
- $NOM \rightarrow Noun$ → NOM ✅
- $Verb \rightarrow book$ ✅
- $VP \rightarrow Verb$ → VP ✅
- $S \rightarrow VP$ → S ✅
- Categories: **{Noun, NOM, Verb, VP, S}**

### Step 3: Fill Row 2 (spans of length 2)

For cell $[i, j]$ where $j - i = 1$: check all splits $k$ where $i \leq k < j$.

**Cell [1,2]: "The man" (split at k=1: [1,1]+[2,2])**
- [1,1] = {Det}, [2,2] = {Noun, NOM}
- Check all pairs:
  - $Det + NOM$: $NP \rightarrow Det\ NOM$ ✅ → **NP**
  - $Det + Noun$: No rule
- Categories: **{NP}**

**Cell [2,3]: "man read" (split at k=2: [2,2]+[3,3])**
- [2,2] = {Noun, NOM}, [3,3] = {Verb, VP, S}
- Check all pairs:
  - $Noun + Verb$: No rule
  - $Noun + VP$: No rule
  - $Noun + S$: No rule
  - $NOM + Verb$: No rule
  - $NOM + VP$: No rule
  - $NOM + S$: No rule
- Categories: **{ }** (empty)

**Cell [3,4]: "read this" (split at k=3: [3,3]+[4,4])**
- [3,3] = {Verb, VP, S}, [4,4] = {Det}
- Check all pairs:
  - $Verb + Det$: No rule
  - $VP + Det$: No rule
  - $S + Det$: No rule
- Categories: **{ }** (empty)

**Cell [4,5]: "this book" (split at k=4: [4,4]+[5,5])**
- [4,4] = {Det}, [5,5] = {Noun, NOM, Verb, VP, S}
- Check all pairs:
  - $Det + NOM$: $NP \rightarrow Det\ NOM$ ✅ → **NP**
  - $Det + Noun$: No rule
  - $Det + Verb$: No rule
  - $Det + VP$: No rule
- Categories: **{NP}**

### Step 4: Fill Row 3 (spans of length 3)

**Cell [1,3]: "The man read" — try splits at k=1 and k=2**

Split k=1: [1,1] + [2,3]
- [1,1] = {Det}, [2,3] = { } (empty)
- No combinations → nothing

Split k=2: [1,2] + [3,3]
- [1,2] = {NP}, [3,3] = {Verb, VP, S}
- Check:
  - $NP + Verb$: No rule
  - $NP + VP$: $S \rightarrow NP\ VP$ ✅ → **S**
  - $NP + S$: No rule
- Categories: **{S}**

**Cell [2,4]: "man read this" — try splits at k=2 and k=3**

Split k=2: [2,2] + [3,4]
- [2,2] = {Noun, NOM}, [3,4] = { } (empty)
- No combinations → nothing

Split k=3: [2,3] + [4,4]
- [2,3] = { } (empty), [4,4] = {Det}
- No combinations → nothing
- Categories: **{ }** (empty)

**Cell [3,5]: "read this book" — try splits at k=3 and k=4**

Split k=3: [3,3] + [4,5]
- [3,3] = {Verb, VP, S}, [4,5] = {NP}
- Check:
  - $Verb + NP$: $VP \rightarrow Verb\ NP$ ✅ → **VP**; then $S \rightarrow VP$ → **S**
  - $VP + NP$: No rule
  - $S + NP$: No rule
- Categories: **{VP, S}**

Split k=4: [3,4] + [5,5]
- [3,4] = { } (empty) → nothing new

### Step 5: Fill Row 4 (spans of length 4)

**Cell [1,4]: "The man read this" — splits at k=1,2,3**

Split k=1: [1,1] + [2,4]
- [1,1] = {Det}, [2,4] = { } → nothing

Split k=2: [1,2] + [3,4]
- [1,2] = {NP}, [3,4] = { } → nothing

Split k=3: [1,3] + [4,4]
- [1,3] = {S}, [4,4] = {Det}
- $S + Det$: No rule → nothing
- Categories: **{ }** (empty)

**Cell [2,5]: "man read this book" — splits at k=2,3,4**

Split k=2: [2,2] + [3,5]
- [2,2] = {Noun, NOM}, [3,5] = {VP, S}
- $Noun + VP$: No rule
- $Noun + S$: No rule
- $NOM + VP$: No rule
- $NOM + S$: No rule → nothing

Split k=3: [2,3] + [4,5]
- [2,3] = { } → nothing

Split k=4: [2,4] + [5,5]
- [2,4] = { } → nothing
- Categories: **{ }** (empty)

### Step 6: Fill Row 5 (full sentence — span of length 5)

**Cell [1,5]: "The man read this book" — splits at k=1,2,3,4**

Split k=1: [1,1] + [2,5]
- [1,1] = {Det}, [2,5] = { } → nothing

Split k=2: [1,2] + [3,5]
- [1,2] = {NP}, [3,5] = {VP, S}
- $NP + VP$: $S \rightarrow NP\ VP$ ✅ → **S** ✅
- $NP + S$: No rule

Split k=3: [1,3] + [4,5]
- [1,3] = {S}, [4,5] = {NP}
- $S + NP$: No rule

Split k=4: [1,4] + [5,5]
- [1,4] = { }, [5,5] = {Noun, NOM, Verb, VP, S} → nothing

- Categories: **{S}** ✅

### Complete CYK Table

```text
              The         man          read         this         book
          ┌──────────┬──────────┬──────────┬──────────┬──────────┐
[1,1]     │   Det    │          │          │          │          │
          ├──────────┼──────────┤          │          │          │
[1,2]     │    NP    │ Noun,NOM │          │          │          │
          ├──────────┼──────────┼──────────┤          │          │
[1,3]     │   ★ S   │    —     │Verb,VP,S │          │          │
          ├──────────┼──────────┼──────────┼──────────┤          │
[1,4]     │    —     │    —     │    —     │   Det    │          │
          ├──────────┼──────────┼──────────┼──────────┼──────────┤
[1,5]     │  ★★ S   │    —     │  VP, S   │    NP    │Noun,NOM, │
          │          │          │          │          │Verb,VP,S │
          └──────────┴──────────┴──────────┴──────────┴──────────┘
```

**Formatted table (standard CYK triangular layout):**

|  | Col 1 (The) | Col 2 (man) | Col 3 (read) | Col 4 (this) | Col 5 (book) |
|:---:|:---:|:---:|:---:|:---:|:---:|
| **Row 1** (len=1) | {Det} | {Noun, NOM} | {Verb, VP, S} | {Det} | {Noun, NOM, Verb, VP, S} |
| **Row 2** (len=2) | {NP} [1,2] | { } [2,3] | { } [3,4] | {NP} [4,5] | |
| **Row 3** (len=3) | **{S}** [1,3] | { } [2,4] | {VP, S} [3,5] | | |
| **Row 4** (len=4) | { } [1,4] | { } [2,5] | | | |
| **Row 5** (len=5) | **{S}** [1,5] ✅ | | | | |

### Step 7: Result

**Cell [1,5] contains S** → The sentence **"The man read this book" is grammatically VALID** ✅

### Parse Tree

```text
                    S  [1,5]
                   / \
                 /     \
              NP [1,2]  VP [3,5]
             / \        / \
           /     \    /     \
        Det    NOM  Verb    NP [4,5]
         |      |    |     / \
        the   Noun  read Det  NOM
               |         |    |
              man       this  Noun
                              |
                             book
```

> **Boxed Final Answer:**
> The CYK algorithm confirms that **"The man read this book"** is a valid sentence under the given grammar.
> Parse: $S \rightarrow NP\ VP$, where $NP \rightarrow Det\ NOM$ ("The man") and $VP \rightarrow Verb\ NP$ ("read this book").
> Cell [1,5] = **{S}** ✅

---

# Part C: Quick Revision Sheet

---

## 📋 Key Concepts at a Glance

| Topic | Key Point to Remember |
|-------|----------------------|
| **Lexicon** | Complete vocabulary/dictionary of a language |
| **Lexeme** | Abstract unit of meaning (all inflected forms: run/runs/ran) |
| **Sense** | One specific meaning of a lexeme |
| **Synonymy** | Same meaning: *big* ↔ *large* |
| **Antonymy** | Opposite meaning: *hot* ↔ *cold* |
| **Homonymy** | Same form, UNRELATED meanings: *bat* (animal) vs *bat* (sports) |
| **Polysemy** | Same word, RELATED meanings: *head* (body/dept/nail) |
| **Hyponymy** | IS-A: *dog* IS-A *animal* |
| **Meronymy** | PART-OF: *wheel* PART-OF *car* |
| **WSD** | Determine which sense of a word is used in context |
| **Lesk** | Pick sense whose gloss overlaps most with context |
| **Naïve Bayes WSD** | $\hat{s} = \arg\max P(s_k) \prod P(w_j \| s_k)$ |
| **Decision List** | Ordered if-then rules sorted by log-likelihood ratio |
| **Yarowsky** | Semi-supervised bootstrapping from seed examples |
| **HyperLex** | Unsupervised — co-occurrence graph hubs = senses |
| **WordNet** | Lexical DB: synsets + hypernymy/hyponymy/meronymy relations |
| **Synset** | Set of synonyms + gloss + examples |
| **Reference Resolution** | Find which entity a referring expression points to |
| **Anaphora** | Backward reference: "The cat sat. **It** purred." |
| **Cataphora** | Forward reference: "Before **he** left, **John** locked up." |
| **Constraints** | Hard rules: number, gender, person agreement; binding theory |
| **Preferences** | Soft rules: recency, grammatical role, parallelism |
| **Inferrables** | Referent not mentioned but inferred (room → ceiling) |
| **Pleonastic it** | "**It** is raining" — "it" refers to nothing |
| **Hobbs Algorithm** | Search parse tree left-to-right, bottom-up for antecedent |
| **Centering Theory** | Track Cb (backward center), Cf (forward centers), Cp (preferred) |
| **Centering Transitions** | CONTINUE > RETAIN > SMOOTH SHIFT > ROUGH SHIFT |
| **Extractive Summary** | Select important sentences verbatim |
| **Abstractive Summary** | Generate new sentences (paraphrase) |
| **TextRank** | Graph-based extractive summarization (PageRank on sentences) |
| **CYK Algorithm** | Bottom-up CFG parser using triangular table; needs CNF grammar |
| **CNF** | Rules: $A \rightarrow BC$ or $A \rightarrow a$ only |

---

## 📋 CYK Table Quick Reference

| Step | What to do |
|:----:|-----------|
| 1 | Fill diagonal: look up terminal → non-terminal rules (include unit productions!) |
| 2 | For each span length 2, 3, …, n: try ALL possible splits |
| 3 | For split at k: check if $[i,k]$ × $[k+1,j]$ matches any $A \rightarrow BC$ rule |
| 4 | If cell $[1,n]$ contains $S$ → sentence is **VALID** |
| 5 | Backtrack to build parse tree |

---

## 📋 Common Exam Mistakes That Cost Marks

| # | Mistake | Fix |
|:-:|---------|-----|
| 1 | **Homonymy vs Polysemy:** Mixing them up | Homonymy = UNRELATED meanings (different etymologies); Polysemy = RELATED meanings (same origin) |
| 2 | **Lesk:** Not showing the overlap computation | Write out the gloss words and context words explicitly, then count overlaps |
| 3 | **Yarowsky:** Forgetting the two key heuristics | Always mention: (1) One sense per collocation, (2) One sense per discourse |
| 4 | **CYK:** Not handling unit productions | When a cell contains $Verb$, also add $VP$ (from $VP \rightarrow Verb$) and $S$ (from $S \rightarrow VP$) |
| 5 | **CYK:** Wrong cell indexing | Cell $[i,j]$ = words from position $i$ to position $j$. Don't confuse row/column |
| 6 | **CYK:** Missing a split point | For cell $[i,j]$, you must try ALL splits: $k = i, i+1, \ldots, j-1$ |
| 7 | **Centering:** Confusing Cb, Cf, Cp | Cf = ALL entities in current utterance (ranked). Cp = HIGHEST ranked in Cf. Cb = HIGHEST entity from PREVIOUS Cf that appears in current utterance |
| 8 | **Hobbs:** Not explaining the tree traversal direction | Emphasize: LEFT branches first, BREADTH-first, then go UP |
| 9 | **Reference Resolution:** Not distinguishing constraints from preferences | Constraints = hard (violation = impossible). Preferences = soft (just ranking) |
| 10 | **Referring expressions:** Forgetting one of the five types | Mnemonic: **I-D-P-P-D** = Indefinite, Definite, Pronoun, Proper noun, Demonstrative |
| 11 | **Text Summarization:** Only describing extractive | Always mention BOTH extractive and abstractive approaches |
| 12 | **Not using examples** | Every NLP answer needs at least one example sentence. Theory alone scores 50% |
| 13 | **WordNet:** Drawing synset without gloss | Always include the gloss (definition) when showing a synset |
| 14 | **Not drawing diagrams** | Draw the WordNet hierarchy, CYK table, and parse tree — examiners award marks for visuals |

---

> [!IMPORTANT]
> **Last-minute strategy:**
> 1. **Must-do:** CYK parsing (Q10) — guaranteed high-mark question. Practice filling the table mechanically.
> 2. **High ROI:** WSD approaches (Q2/Q4) — write Lesk example + Yarowsky steps + HyperLex diagram. Covers two questions at once.
> 3. **Reference Resolution block** (Q5–Q8) — understanding the framework once lets you answer all four. Focus on Hobbs algorithm steps and Centering's Cb/Cf/Cp.
> 4. **Always add examples** — NLP examiners expect illustrative examples with every concept. No examples = half marks.
> 5. **Draw diagrams** — WordNet hierarchy tree, CYK triangular table, parse tree, co-occurrence graph. These are easy marks.

---

*Guide generated for NLP PT-2 | Sem 7 Computer Engineering | Mumbai University*
*Last updated: October 2026*
