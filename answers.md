# CMPS 2200 Assignment 5
## Answers

**Name:**Lily Goldman

Place all written answers from `assignment-05.md` here for easier grading.


**1: Fixed-Length vs. Variable-Length Codes**

- **1d. (5 pts)** What trend do you see?

  | File | Fixed-length cost | Huffman cost | Huffman / Fixed |
  |---|---|---|---|
  | alice29.txt | 1039367 | 676374 | 0.651 |
  | asyoulik.txt | 876253 | 606448 | 0.692 |
  | f1.txt | 1340 | 826 | 0.616 |
  | fields.c | 78050 | 56206 | 0.720 |
  | grammar.lsp | 26047 | 17356 | 0.666 |

  Yes, there is a consistent trend: Huffman coding always beats fixed-length coding, using about 62% to 72% of the bits (a 28% to 38% savings). The savings are biggest when a few characters are very common, like `f1.txt` with its long runs of a's. The savings are smallest for `fields.c`, where characters are spread more evenly.

- **1e. (4 pts)** What is the expected
  cost of a Huffman encoding for the document? Is it consistent across
  documents?

  If every character has the same frequency, Huffman builds a balanced tree, so every character gets about $\log_2 |\Sigma|$ bits (either $\lfloor \log_2 |\Sigma| \rfloor$ or $\lceil \log_2 |\Sigma| \rceil$). For a document with $n$ characters the cost is about $n \log_2 |\Sigma|$. When $|\Sigma|$ is a power of 2, it is exactly $n \log_2 |\Sigma|$, the same as a fixed-length code.

  Yes, it is consistent across documents: the cost depends only on the document length $n$ and the alphabet size $|\Sigma|$, not on which characters appear. Huffman gives little or no savings over fixed-length coding in this case.


**2: Making Change**

- **2a. (5 pts)** Given a $N$ dollars, state a greedy algorithm for producing
as few coins as possible that sum to $N$.

  Repeatedly take the largest coin $2^i$ that is $\le N$ (and $\le 2^k$), add it to the solution, and subtract it from $N$. Stop when $N = 0$.

  (Equivalently: use $\lfloor N / 2^k \rfloor$ coins of value $2^k$, then write the remainder in binary and use one coin $2^i$ for each 1 bit.)

  To implement this efficiently, process each coin denomination from largest to smallest, taking as many coins of that denomination as possible at once using integer division.

- **2b. (5 pts)** Prove that this algorithm is optimal by proving the greedy
  choice and optimal substructure properties.

  **Key fact:** An optimal solution never uses two coins of the same value $2^j$ with $j < k$, because two $2^j$ coins could be swapped for one $2^{j+1}$ coin, giving fewer coins.

  **Greedy choice property:** Let $2^i$ be the largest coin $\le N$. No larger coin can be used (it is either bigger than $N$ or doesn't exist). By the key fact, the coins smaller than $2^i$ in an optimal solution are all different, so they add up to at most $2^0 + 2^1 + \dots + 2^{i-1} = 2^i - 1 < N$. So the optimal solution cannot reach $N$ using only smaller coins, which means it must contain a $2^i$ coin. Therefore some optimal solution includes the greedy choice.

  **Optimal substructure property:** Suppose an optimal solution $O$ for $N$ contains a coin $2^i$. Then $O$ minus that coin is an optimal solution for $N - 2^i$. If it weren't, there would be a solution for $N - 2^i$ with fewer coins, and adding the $2^i$ coin back would give a solution for $N$ with fewer coins than $O$, which is a contradiction.

  Since both properties hold, the greedy algorithm is optimal.

- **2c. (4 pts)** What is the work and span of your algorithm?

  Going from the largest coin down to $2^0$, each step does $O(1)$ work (one division and one remainder) and there are $O(\log N)$ coin sizes to consider, so the **work is $O(\log N)$**. Each step needs the remainder from the step before, so the steps can't run in parallel, and the **span is also $O(\log N)$**.


**3: Edit Distance**

- **3a.** State the optimal substructure property for this version of edit distance.

  Let $MED(S, T)$ be the minimum number of insertions, deletions, and substitutions to turn $S$ into $T$. Base cases: $MED(S, "") = |S|$ and $MED("", T) = |T|$. Otherwise:
  - If $S[0] = T[0]$: $MED(S, T) = MED(S[1:], T[1:])$
  - If $S[0] \ne T[0]$: $MED(S, T) = 1 + \min(MED(S, T[1:]),\ MED(S[1:], T),\ MED(S[1:], T[1:]))$

  The three options are insert $T[0]$, delete $S[0]$, or substitute $S[0]$ with $T[0]$. An optimal solution for $S, T$ must contain an optimal solution for whichever smaller subproblem it uses after the first step.