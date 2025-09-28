# CMOS architecture using reversible differential codes

* Leave generalised with Boolean affine transformations as instruction primitives, $y=Ax+c$.
* Now permutations are a subset of $A$ but also encompasses all linear codes natively.
* Have $A$ (scales as $n^2$) fed by decode units that map out micro-instructions (ie operating on a smaller number of bits) to scalable SIMD thread bundles. Eg:
  * Block replication.
  * Hierarchically described block or permutation decompositions that arbitrarily scale upwards.
  * Mappings to avoid faulty hardware level execution pathways.
 * https://chatgpt.com/share/6843a26f-1a28-8009-b982-4daeb7096201
 * Defining x,y,c as ‘registers’ the decompositional structure of $A$ has a heierarchical block structure reflecting thread pipeline execution topology.
 * Downward register maps (into parallelisation) follow a composable form between and across levels of threads.

Extend ring algebra (three fold) form to include branch register, the difference term between register swaps. The sequence of all branch register values over time compose to form all execution pathways and compose. Consider compiler/delegation optimisation in terms of *branch algebra*. Eg path length minimisation. 

* Parity encoded registers via tuples, $R=[x,y]$, where $x,y\in\{0,1\}^n$.
* Register evaluates to logical value $E(R)=x\oplus y$.
* Registers now have factor 2 overhead.
// * , encoding input and output (pre- and post-computation).
// Register $R=[x,y]$ is parity encoded such that $R_\text{out}=x\oplus y$.
// Define initial state of register as $R=[0,x]$. Then evaluation of register is given by tuple sum $R_\text{out} = x$.

## Coding conventions

We’ll use the convention that $x$ denotes an execution point reference and $y$ holds the computational state.

Example evolution:
* $R_0 = [x,y]$,
	* $E(R_0) = x \oplus y$.
* $R_1 = [x \oplus y, y\oplus f(x \oplus y)]$,
	* $E(R_1) = x \oplus f(x \oplus y)$.
* $R_2 = [x \oplus f(x \oplus y), y \oplus f(x \oplus y) \oplus g(x \oplus f(x \oplus y))]$,
	* $E(R_2) = x \oplus y \oplus g(x \oplus f(x \oplus y))$.

Example evolution 2:
* Let $y=0$ as initial state by convention, where first application of $f$ defines initial state relative to identity and carries in quasi-functional form.
* Let $f’(y) = f(x\oplus y)$.


</br>
* $R_0 = [x, f(x\oplus y)]$,
	* $E(R_0) = x \oplus f'(y)$.
* $R_1 = [x \oplus f(y), f(y) \oplus f(x \oplus y)]$,
	* $E(R_1) = x \oplus f'(y)$.
* $R_2 = [x \oplus f(x \oplus y), f(y) \oplus f(x \oplus y) \oplus g(x \oplus f(x \oplus y))]$,
	* $E(R_2) = x \oplus f'(y\oplus x) \oplus g'(f'(y))$.

Example computational evaluation:
* $C_{0\to 1}(x) = x \oplus E(R_0) \oplus E(R_1) = x \oplus x \oplus f(x) = f(x)$
* $C_{0\to 2}(x) = x \oplus E(R_0) \oplus E(R_2) = x \oplus x \oplus x \oplus g(f(x)) = x\oplus g(f(x))$

Encoding form:
* $R = [x\oplus y, y \oplus f(x \oplus y)]$

## Function evaluation

* Function evaluation is given by,
    * $f: [x,y] \to [x \oplus y, y \oplus f(y)]$.
    // * $f: [x,y] \to [x \oplus y, f(y)]$.
* Output of computation obtained via composition of register evaluation between execution points,
	* $R_0 = [x,y]$, $E(R_0)=x\oplus y$.
	* $R_1 = [x \oplus y, y \oplus f(y)]$, $E(R_1) = x \oplus f(y)$.
* Evaluation of computation between flow points,
	* $C_{0\to 1}(R) = E(R_0)\oplus E(R_1) = y \oplus f(y)$.
	* Yields the difference 
* Then with control flow starting point $x=0$,
	   * $R \oplus R' = [0, x] \oplus [x, f(x)] = [x, x \oplus f(x)]$.
	   * $C(R \oplus R') = f(x)$.
* Tuple algebra is abelian but composition requires evaluation, imposing time-ordering.
* Incorrect branch prediction in instruction pipelines can be corrected retrospectively, allowing out of order execution, or parallelised evaluation.

## Interpretation

For,
// $$R_0 = [0,x], \,\, C_0 = x, \\
// R_1 = [x, x\oplus f(x)],\,\, C_1 = f(x)$$

$$R_0 = [x,y], \,\, C_0 = x\oplus y, \\
R_1 = [x \oplus y, f(y)],\,\, C_1 = x\oplus y \oplus f(y), \\
C_0 \oplus C_1 = f(y)$$
composition of $C_0$ and $C_1$ provides function evaluation between execution points starting at location $x$.

we can interpret the first tuple element as a (non-linear, hash-like) reference for the execution point, where $0$ here by convention denotes program start, but needn’t follow this convention.

More generally the first element can be seen as a sub-routine starting reference point. Then execution along an in-series subprogram is always obtained via composition $R_0\oplus R_1$ for any program sub-section, which can be stitched together or evaluated independently/parallelised if starting reference is known.

‘Composition of flow’

## Branching

* Output obtained from sum of tuple elements. Gives evaluation relative to first tuple element.
* Composition of pathways allows partial execution.
* General approach for ISA execution pathway decomposition and approach to considering branch prediction. Unifies with approach for concurrency via distinct execution paths undergoing partial or potential/predictive execution.
* Compatible with reversible computing and CMOS implementation.
* Unifies with homomorphic encryption/computation.

* Consider this a composition of execution pathways.
* Provides graph representation under the composition algebra.
* Edges = instructions/computations in an execution/pipeline pathway.
* Vertices = composition of outcomes under abelian algebra.
* Generalise to vertices with higher order by extending to tuples with $>2$ elements, i.e generalised parity codes.

## Conditional branches

* Let $R = [x, y]$,
	* If $b=0: I(R) = [x \oplus y, y]$, $C=R \oplus I(R) = y$.
	* If $b=1: f(R) = [x \oplus y, f(y)]$, $C=R \oplus f(R) = f(y)$.

// Now expand to triple-register encoding where,
// $f: [$

* Using the perm+add algebra circuit depth stems from the number of conditional operations.
* All non-conditional operations can be compressed into a single iteration with sufficient ancillary space for a given logical operation.

* How do standard RISC operations like ADD, MUL translate into this format?
* Consider time-space (depth vs ancilla) tradeoff.

## Standardised computational units (model for generic CUDA type implementation)

* Consider boolean algebra over $n$ bits:
	* $A = (S_n,\oplus)$, acting on $C_2^{\oplus n}$.
	* Algebra is a direct sum over cyclic groups (here order 2), with permutations over elements of the direct sum decomposition.

* Let $f_{\pi,c}(x) = x_\pi \oplus c$ (permute + add).
    * Where $\pi\in S_n$ and $c\in \{0,1\}^n$.
    * Then $f(x)$, parameterised by $\{\pi,c\}$, is a universal primitive for reversible boolean logic.
    * Taking the dual-parity-encoded register form we have:
        * $R = [0, x]$
        * $f(R) = [x, f(x)] = [x, x_\pi \oplus c]$.
        * $C(R\oplus f(R)) = x_\pi \oplus c$.
        * Circuit has fixed $O(n)$ depth:
            * Arbitrary permutation implemented by $n\times n$ grid router with circuit depth $n$.
            * $\oplus c$ term corresponds to a linear array of bitwise XORs with fixed $O(1)$ circuit depth.
    * This provides a standardised universal primitive with fixed depth for a streaming architecture.
    * Constant execution time, agnostic to computation.
    * Naturally provides all crypto primitives.
    * Acting on the same register, function composition obeys simplification $g\circ h \equiv f$.

## Homomorphic computation

* Using the $A = (S_n,\oplus)$ algebra as the computational primitives, all computations are homomorphic under common permutations.
* $\phi: S_n \times A \to A$.

* For $R=[x,y]$ and $f(R)=[x\oplus y, y_\pi\oplus c]$ we have,
	* $R_{\hat\pi} = [x \circ \hat\pi, y \circ \hat\pi]$ (applying the homomorphic permutation from the right).
	* $f(R \circ \hat\pi)=[(x \circ \hat\pi) \oplus (y \circ \hat\pi), (y_\pi \circ \hat\pi) \oplus c]$.
	* Now replace $c\to c \circ \hat\pi$, or $c$ is taken from another register encoded under the same $\hat\pi$ we have,
	* $f(R \circ \hat\pi)=[(x \oplus y) \circ \hat\pi, (y_\pi \oplus c) \circ \hat\pi] = [x \oplus y, y_\pi \oplus c]\circ\hat\pi = f(R)\circ\hat\pi$.
	* When $\hat\pi$ is secret this keeps the computation homomorphically hidden under $\hat\pi$, providing a zero-overhead homomorphic channel.

## Instruction set

* $\tt{INIT \,\, R, val}$
	* Initialise register $R\leftarrow val$
* $\tt{MOV} \,\, R_1, R_2$
	* Copy register $R_1$ to register $R_2$.
* $\tt{MOV} \,\, R, addr$
	* Move register $R$ to memory address $\tt{addr}$.
* $\tt{MOV} \,\, addr, R$
	* Move memory address $\tt{addr}$ to register $R$.
* $\tt{COMP \,\, R, perm, const}$
	* Perform isolated computation on register $R$ with permutation $\tt{perm}\in\{0,1\}^{n\times n}$ and constant $\tt{const}\in\{0,1\}^n$.
	* $\tt{perm}$ specifies the permutation (Benes network control sequence).
	* $\tt{const}$ in a bit-vector of length $n$.
	* $\tt{perm}$ and $\tt{const}$ could be specified either as coded constants or refer to registers (implicitly corresponding to different hardware level implementations).

* How to optimise these for scaling to minimise layers of operations for universal operations combining multiple registers?
* In a concurrency model if $\tt{perm}$ or $\tt{const}$ are taken from other registers these constitute dependencies, i.e an implied asynchronous $\tt{await}$.

## Compilation and circuit optimisation

* Does this provide a universal and optimal approach for optimising compilation.
	* Implementing f over all data ($X$) isn’t viable, and must be decomposed down into sub-computations on realistically sized registers ($x$).
        * $X$ can be parallelised when the $\pi$ acting on it distributes over registers $x$. Otherwise the $\pi$ acting on $X$ must be emulated by reducing independent smaller permutations in parallel to combine into a joint one.
        * MapReduce via composition of permutations to construct larger permutations.
    * For $\pi$ acting on $X$, computation may be parallelised when the permutation separates into a sum of independent permutations, $\pi\equiv \pi_1\oplus \pi_2$.
    * All computations in series over $n$ bits simplify under composition.
    * Time-ordering in computations arises from non-commutativity of $S_n$.

## Permutation decomposition

* Arbitrary permutation over $S_{2n}$ decomposes into partition-wise permutations and transpositions via a 3-round permutation-transposition network,
* $(S_n \times S_n) \to T_1 \to (S_n \times S_n) \to T_2 \to (S_n \times S_n)$
* $T_1$ and $T_2$ denote odd and even pairwise swaps between partitions.
* Nb: $\text{SWAP}(a,b) = (a \vee b, a \vee b)\oplus (a,b)$.
* Use hierarchical decomposition with $\mathrm{log}_2(N/n)$ overhead for scaling, where $N$ is data size and $n$ is register size.
* https://chatgpt.com/share/67fb2bcc-4840-8009-96fb-2db627789e0c

* Optimal implementation of permutations using Benes network with $O(\text{log}_2(n))$ circuit depth and $O(n\,\text{log}_2(n))$ transistor count.
* Recursive form, naturally scales.
* Number of control bits to specify permutation on $n$ bits: $n \log_2(n) - \frac{n}{2}$, where $n=2^k$.
* https://chatgpt.com/share/67fb4531-8d98-8009-81d4-6d8499069eaa

## Considerations

* To make a generic and scalable model what’s the optimal way to decompose a permutation on $n$ bits into sequences of permutations over $n/2$ bits?
* With an appropriate decomposition, cores using different register sizes (and limited in permutation size) can nonetheless cooperate under the same model.

## Topological interpretation

* Isolated execution on a single register can be interpreted as a topological strand.
* Conditional operations correspond to intersections between strands.
* Homomorphic encryption under a fixed permutation corresponds to twisting of strands.

## Notes

* Consider structured permutations where $S_n$ decomposes into sums of smaller permutations, e.g $\pi\in S_{n/k}^{\oplus k}\in S_n$. Then the actions of each $S_{n/k}$ are on computational subdomains executing in parallel.
* Equivalent to the hierarchical approach for combining larger permutations, but without the need for mixing, instead independent execution.

## Error detection & correction

* Does the constrained form of operations lend itself to novel ECC or the ability to circumvent the need for it?
	* E.g a register operating in the form of a bundle of parallel threads via a separable permutation structure ($\pi \in S_{n/k}^{\oplus k}$) allows some threads to be ignored at the software level.

* Two-column form is equivalent to error-detecting code.
* Row-sums equivalent to parity checksums.
* Generalise to multiple redundant columns to enable-majority vote codes.
* Add random (or structured) permutation across or within columns to randomise this into a tornado code.
	* Applying a tornado permutation at each step can be implemented with zero-overhead by complementing the existing permutation control sequence with a mask.
	* If the tornado permutation has fixed points those lines with be stationary. Choosing a cyclic permutation ensures no stationary points.
	* If error-detection (at whatever level) identifies faulty lines at the hardware level, modify permutation to introduce stationary points at those locations (or within respective thread blocks) to isolate them.
		* In the Benes network structure can this be achieved by referring to the control bit in charge of the transposition at the bottom of the hierarchy in which the respective bit resides?

	* This reconciles directly with the DCN model for dynamically randomised data storage.
		* Randomised object allocation via random permutations as opposed to independent per-object randomisation guarantees no collisions as exact uniform distribution rather than statistical average case uniform distribution.
		* Less relevant for distributed storage but necessary at the hardware memory allocation level (e.g registers).

* Avenue for unifying model across entire memory hierarchy?
* Failures and inaccessible space effectively breaks Benes tree into a forest of independent trees?

## Junk

Eg:
In = x
v_0 = (0,x) [Nb: In ‎ =  sum(v_0) = 0+x = x]
v_1 = f(v_0) = (0,x)+(x,f(x)) = (x,x+f(x))
Out = sum(v_1) = x+x+f(x) = f(x)

Notes:

Identify map f_id:
v_0 = (0,x) 
f_id(v_0) = (x,0)
Hence (0,x)==(x,0)

v_0 = (1,x') 
f_id(v_1) = (x,1)

Hence complimentary representations with equivalent algebra are provided by:
v_0 = (0,x)
v_0' = (1,x')

Combine two encodings to make self-dualised form, native CMOS:
* $R^c = [z,\bar{z}]$.
* $f(R^c) = [z\oplus f(\bar{z}),\bar{z} \oplus f(z)]$
* $C = R^c\oplus f(R^c) = [f(\bar{z}),f(z)]$

* Add permutation primitives at vertices to generalise to Merkle-graph type structure with asymmetric and hash primitives at vertices.
