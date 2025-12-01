# Mathematical Details & Deep Dive

## Detailed Mathematical Explanations for Each Application

### 1. DNA Sequencing - De Bruijn Graphs

**Problem Formulation:**
Given a set of DNA fragments (reads), reconstruct the original sequence.

**Graph Construction:**
- **k-mer**: substring of length k
- **Node**: each unique (k-1)-mer
- **Edge**: each k-mer, connecting its prefix to suffix
- Example: k=3, "ATG" creates edge from "AT" to "TG"

**Algorithm:**
```
1. Extract all k-mers from reads
2. Build De Bruijn graph
3. Find Eulerian path (visits each edge once)
4. Path spells out the sequence
```

**Complexity:**
- Graph construction: O(n) where n = total read length
- Eulerian path: O(E) where E = number of edges
- Space: O(V + E)

**Why Eulerian Path?**
- Eulerian path exists if graph has 0 or 2 vertices with odd degree
- Hierholzer's algorithm finds it in linear time
- More efficient than Hamiltonian path (NP-complete)

---

### 2. Spotify Recommendations - Collaborative Filtering

**Jaccard Similarity:**
$$J(A,B) = \frac{|A \cap B|}{|A \cup B|}$$

**Example:**
- User A likes: {Song1, Song2, Song3, Song4}
- User B likes: {Song2, Song3, Song4, Song5}
- Intersection: {Song2, Song3, Song4} → |A ∩ B| = 3
- Union: {Song1, Song2, Song3, Song4, Song5} → |A ∪ B| = 5
- J(A,B) = 3/5 = 0.6 (high similarity)

**Cosine Similarity (Alternative):**
$$\cos(\theta) = \frac{A \cdot B}{||A|| \cdot ||B||}$$

**Matrix Factorization:**
- User-item matrix R (m users × n items)
- Factorize: R ≈ U × V^T
- U: m × k user features
- V: n × k item features
- Minimize: ||R - UV^T||²

---

### 3. Traffic Light Synchronization - Graph Coloring

**Chromatic Number:**
The minimum number of colors needed to color a graph such that no adjacent vertices share the same color.

**Theorem:** For any graph G, χ(G) ≤ Δ(G) + 1
where Δ(G) = maximum degree

**Greedy Coloring Algorithm:**
```
1. Order vertices (various strategies)
2. For each vertex v:
   - Assign smallest color not used by neighbors
3. Return coloring
```

**Complexity:**
- Greedy: O(V + E)
- Optimal coloring: NP-complete
- Approximation ratio: 2 for greedy

**Welsh-Powell Algorithm:**
1. Sort vertices by degree (descending)
2. Apply greedy coloring
3. Often produces good results

---

### 4. Reed-Solomon Codes - Finite Field Arithmetic

**Galois Field GF(256):**
- Elements: 0 to 255
- Addition: XOR operation
- Multiplication: polynomial multiplication mod irreducible polynomial

**Encoding:**
1. Message: m(x) = m₀ + m₁x + ... + m_{k-1}x^{k-1}
2. Generator: g(x) = (x - α⁰)(x - α¹)...(x - α^{n-k-1})
3. Codeword: c(x) = m(x) · x^{n-k} + remainder(m(x) · x^{n-k} / g(x))

**Error Correction Capacity:**
- Can correct up to t errors where 2t ≤ n - k
- n = total symbols, k = data symbols
- Example: QR Code Level H: n=26, k=9 → t=8 errors

**Decoding (Simplified):**
1. Calculate syndromes
2. Find error locator polynomial
3. Find error positions (Chien search)
4. Calculate error values (Forney algorithm)

---

### 5. Social Network Analysis - Centrality Measures

**Betweenness Centrality:**
$$C_B(v) = \sum_{s \neq v \neq t} \frac{\sigma_{st}(v)}{\sigma_{st}}$$

where:
- σ_{st} = number of shortest paths from s to t
- σ_{st}(v) = number of those paths passing through v

**Closeness Centrality:**
$$C_C(v) = \frac{n-1}{\sum_{u \neq v} d(v,u)}$$

where d(v,u) = shortest path distance

**Eigenvector Centrality:**
$$x_v = \frac{1}{\lambda} \sum_{u \in N(v)} x_u$$

Solved by finding principal eigenvector of adjacency matrix.

**PageRank (Google's Algorithm):**
$$PR(v) = \frac{1-d}{N} + d \sum_{u \in B(v)} \frac{PR(u)}{L(u)}$$

where:
- d = damping factor (0.85)
- N = total pages
- B(v) = pages linking to v
- L(u) = outgoing links from u

---

### 6. Protein Folding - HP Model

**Hydrophobic-Polar (HP) Model:**
- Simplify amino acids to H (hydrophobic) or P (polar)
- Protein = sequence on 2D/3D lattice
- Energy = -1 for each H-H contact (not adjacent in sequence)
- Goal: Minimize energy

**Example:**
Sequence: HPHPPHHPHH
```
H - P - H
    |
    P - P - H
        |
        H - P - H - H
```
Energy = -3 (three H-H contacts)

**Complexity:**
- NP-complete even on 2D square lattice
- 3D cubic lattice: also NP-complete
- Approximation algorithms exist

**Contact Map Prediction:**
- Binary matrix C where C[i,j] = 1 if residues i,j are close in 3D
- Predict C using machine learning
- Reconstruct 3D structure from C

---

### 7. Airline Crew Scheduling - Set Cover

**Set Cover Problem:**
- Universe U = {flights to cover}
- Sets S₁, S₂, ..., Sₙ = possible crew schedules
- Each Sᵢ ⊆ U with cost cᵢ
- Find minimum cost collection covering U

**Integer Linear Programming Formulation:**
```
Minimize: Σ cᵢ · xᵢ
Subject to:
  Σ xᵢ ≥ 1  for each flight f (where f ∈ Sᵢ)
  xᵢ ∈ {0,1}
  Additional constraints (rest time, certifications, etc.)
```

**Greedy Approximation:**
1. While uncovered flights exist:
   - Choose set with best cost/coverage ratio
   - Add to solution
2. Approximation ratio: ln(n) where n = |U|

---

### 8. Blockchain - Cryptographic Primitives

**SHA-256 Hash Function:**
- Input: arbitrary length bit string
- Output: 256-bit hash
- Properties:
  - Preimage resistance: Given h, hard to find m where H(m) = h
  - Collision resistance: Hard to find m₁ ≠ m₂ where H(m₁) = H(m₂)
  - Avalanche effect: 1-bit change → ~50% output bits flip

**Merkle Tree:**
```
        Root Hash
       /          \
    H(AB)        H(CD)
    /   \        /   \
  H(A) H(B)   H(C) H(D)
   |    |      |    |
   A    B      C    D
```
- Verify transaction in O(log n) time
- Proof size: O(log n) hashes

**Elliptic Curve Cryptography:**
- Curve: y² = x³ + ax + b (mod p)
- Point addition: geometric operation
- Scalar multiplication: k·P = P + P + ... + P (k times)
- Discrete log problem: Given P and Q = k·P, find k (hard!)

**Bitcoin Mining:**
Find nonce such that:
```
SHA256(SHA256(version || prev_hash || merkle_root || time || bits || nonce)) < target
```
Target adjusted every 2016 blocks (~2 weeks) to maintain 10-minute average.

---

### 9. Compiler Optimization - Register Allocation

**Chaitin's Algorithm:**
```
1. Build interference graph G
   - Nodes = variables
   - Edge if variables live simultaneously
2. Simplify:
   - Remove node with degree < k (k = # registers)
   - Push onto stack
   - Repeat
3. If no such node exists:
   - Choose node to spill (heuristic)
   - Remove and mark for spilling
4. Color:
   - Pop stack
   - Assign color different from neighbors
   - If can't color, spill to memory
```

**Live Range Analysis:**
- Variable is "live" from definition to last use
- Use data flow analysis to compute live ranges
- Build interference graph from live ranges

**Spilling Heuristics:**
- Spill cost = (uses + defs) / degree
- Minimize spill cost
- Prefer spilling variables used in loops less

---

### 10. Vehicle Routing Problem - Mathematical Formulation

**VRP with Time Windows:**
```
Minimize: Σᵢ Σⱼ cᵢⱼ · xᵢⱼ

Subject to:
  Σⱼ xᵢⱼ = 1  ∀i (each customer visited once)
  Σᵢ xᵢⱼ = 1  ∀j (each customer left once)
  Σᵢ xᵢⱼ = Σₖ xⱼₖ  ∀j (flow conservation)
  aᵢ ≤ tᵢ ≤ bᵢ  ∀i (time windows)
  Σᵢ dᵢ · yᵢₖ ≤ Q  ∀k (capacity)
  xᵢⱼ ∈ {0,1}
```

**2-opt Improvement:**
```
For each pair of edges (i,j) and (k,l):
  If cost(i,k) + cost(j,l) < cost(i,j) + cost(k,l):
    Reverse route segment between j and k
```
Complexity: O(n²) per iteration

**Christofides Algorithm (TSP):**
1. Find minimum spanning tree (MST)
2. Find minimum matching on odd-degree vertices
3. Combine to form Eulerian graph
4. Find Eulerian tour
5. Convert to Hamiltonian tour (skip repeated vertices)
Approximation ratio: 1.5 (optimal for TSP)

---

### 11. Image Compression - Huffman Coding

**Algorithm:**
```
1. Count frequency of each symbol
2. Create leaf node for each symbol
3. While more than one node:
   - Remove two nodes with lowest frequency
   - Create parent with frequency = sum
   - Add parent back to queue
4. Root is Huffman tree
5. Assign codes: left=0, right=1
```

**Example:**
```
Frequencies: A=45, B=13, C=12, D=16, E=9, F=5

Tree:
         100
        /    \
      55      45(A)
     /  \
   25   30
  / \   / \
12(C)13(B)14 16(D)
         / \
       5(F) 9(E)

Codes: A=1, B=011, C=010, D=001, E=0001, F=0000
```

**Shannon Entropy:**
$$H = -\sum_{i=1}^{n} p_i \log_2(p_i)$$

Example: Fair coin (p=0.5)
H = -(0.5·log₂(0.5) + 0.5·log₂(0.5)) = 1 bit

**Kraft Inequality:**
For prefix-free code with lengths l₁, l₂, ..., lₙ:
$$\sum_{i=1}^{n} 2^{-l_i} \leq 1$$

Equality holds for optimal codes.

---

### 12. Constraint Satisfaction Problems

**Backtracking with Forward Checking:**
```
function backtrack(assignment):
  if assignment is complete:
    return assignment
  var = select_unassigned_variable()
  for value in order_domain_values(var):
    if value is consistent with assignment:
      add {var = value} to assignment
      inferences = forward_check(var, value)
      if inferences ≠ failure:
        result = backtrack(assignment)
        if result ≠ failure:
          return result
      remove {var = value} and inferences
  return failure
```

**Arc Consistency (AC-3):**
```
function AC3(csp):
  queue = all arcs in csp
  while queue not empty:
    (Xi, Xj) = queue.pop()
    if revise(Xi, Xj):
      if domain(Xi) is empty:
        return false
      for Xk in neighbors(Xi) - {Xj}:
        queue.add((Xk, Xi))
  return true

function revise(Xi, Xj):
  revised = false
  for x in domain(Xi):
    if no y in domain(Xj) satisfies constraint(x,y):
      remove x from domain(Xi)
      revised = true
  return revised
```

---

### 13. Pattern Matching - Aho-Corasick Algorithm

**Trie Construction:**
```
1. Build trie of all patterns
2. Add failure links (like KMP failure function)
3. Add output links (patterns ending at each node)
```

**Search:**
```
function search(text, automaton):
  state = root
  for i = 0 to len(text)-1:
    while state ≠ root and no transition on text[i]:
      state = failure_link(state)
    if transition on text[i] exists:
      state = transition(state, text[i])
    if state has output:
      report matches at position i
```

**Complexity:**
- Preprocessing: O(m) where m = sum of pattern lengths
- Search: O(n + z) where n = text length, z = # matches
- Space: O(m)

---

### 14. Music Theory - Mathematical Operations

**Transposition in Z₁₂:**
$$T_n(x) = (x + n) \mod 12$$

Example: Transpose C major up 5 semitones
- C major: {0, 4, 7}
- T₅({0, 4, 7}) = {5, 9, 0} = {0, 5, 9} = F major

**Inversion:**
$$I_n(x) = (n - x) \mod 12$$

Example: Invert C major around C
- I₀({0, 4, 7}) = {0, 8, 5} = {0, 5, 8} = C minor

**Twelve-Tone Matrix:**
For row: {0, 11, 3, 4, 8, 2, 10, 1, 9, 5, 6, 7}
```
P0:  0 11  3  4  8  2 10  1  9  5  6  7
P1:  1  0  4  5  9  3 11  2 10  6  7  8
...
I0:  0  1  9  8  4 10  2 11  3  7  6  5
I1: 11  0  8  7  3  9  1 10  2  6  5  4
...
```

---

### 15. Epidemic Modeling - SIR on Networks

**Discrete-Time SIR:**
```
At each time step t:
  For each infected node i:
    For each susceptible neighbor j:
      With probability β:
        j becomes infected
    With probability γ:
      i becomes recovered
```

**Basic Reproduction Number:**
$$R_0 = \beta \cdot \langle k \rangle / \gamma$$

where ⟨k⟩ = average degree

**Epidemic Threshold:**
- Random network: R₀ > 1
- Scale-free network: No threshold (always spreads)

**Percolation Theory:**
- Remove fraction p of nodes (vaccination)
- Critical threshold: $p_c = 1 - 1/R_0$
- Above p_c, epidemic cannot spread

**Network Generation Models:**

1. **Erdős-Rényi:** Connect each pair with probability p
2. **Watts-Strogatz:** Start with ring, rewire with probability p
3. **Barabási-Albert:** Preferential attachment, power-law degree distribution

---

## Complexity Classes

**P:** Problems solvable in polynomial time
- Shortest path: O(V²) or O(E log V)
- Minimum spanning tree: O(E log V)
- Sorting: O(n log n)

**NP:** Problems verifiable in polynomial time
- Hamiltonian path
- Graph coloring
- Satisfiability (SAT)

**NP-Complete:** Hardest problems in NP
- If any NP-complete problem has polynomial solution, P = NP
- Examples: TSP, Set Cover, Graph Coloring

**NP-Hard:** At least as hard as NP-complete
- May not be in NP
- Examples: Halting problem, some optimization problems

---

## Approximation Algorithms

**Approximation Ratio:**
For minimization problem with optimal solution OPT:
- Algorithm A has ratio α if A(I) ≤ α · OPT(I) for all instances I

**Examples:**
- Vertex Cover: 2-approximation (greedy)
- Set Cover: ln(n)-approximation (greedy)
- TSP (metric): 1.5-approximation (Christofides)
- TSP (general): No constant approximation (unless P=NP)

---

## Further Reading

### Classic Papers
1. Dijkstra (1959): "A Note on Two Problems in Connexion with Graphs"
2. Huffman (1952): "A Method for the Construction of Minimum-Redundancy Codes"
3. Reed & Solomon (1960): "Polynomial Codes over Certain Finite Fields"
4. Chaitin (1982): "Register Allocation & Spilling via Graph Coloring"

### Modern Applications
1. AlphaFold 2 (2020): Nature paper on protein structure prediction
2. Bitcoin Whitepaper (2008): Satoshi Nakamoto
3. PageRank (1998): Brin & Page
4. Aho-Corasick (1975): "Efficient String Matching"

### Textbooks
1. "Introduction to Algorithms" - CLRS
2. "Discrete Mathematics and Its Applications" - Rosen
3. "Graph Theory" - Diestel
4. "Computers and Intractability" - Garey & Johnson
