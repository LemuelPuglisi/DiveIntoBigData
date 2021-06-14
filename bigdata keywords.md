Market Basket Analysis

* Apriori (**monotonicità**)
* PCY, Park-Chen-Yu (**funzioni hash**)
* Random Sampling (**sampling per entrare in memoria principale**)
* SON, Savasere-Omiecinski-Navathe (**partizionamento carrelli, alg. distribuito**)
* Toivonen (**frontiera negativa**)
* Insiemi frequenti massimali (**superinsieme non frequente**, **lossy**)
* Insiemi frequenti chiusi (**superinsiemi con supporto minore, lossless**)
* Candidate generation (**gen-to-spec(e vic.), bidirectional, BFS o DFS, prefix-tree**)
* ECLAT (**database invertito, intersezione**)
* FP-growth (**fp-tree, pattern condizionali**)
* MS-apriori, Multiple Minsup (**perdità dell'anti-monotonicità**, **minimo tra supporti**)
* rule generation (**$\text{confidence}(ABC \to D) \ge \text{confidence}(AB \to CD)$**)

Dimensionality Reduction

* Unsupervised feature selection
* Ottenere le autocoppie dell'eq. $(M - \lambda I)e=0$ (**determinante non nullo**)
* Chiò pivotal condensation (**determinante da una nuova matrice B**)
* Power Iteration (**iterare $Mx$ normalizzato fino a convergenza**)
* Generalizzazione P.I. (**Matrice $B$ a cui viene sottratto $\lambda v_1 v_1^T$**)
* PCA (**trovare autocoppie di $M^TM$, formare $E$ e ridurla**)
* SVD (**trovare U, V e $\Sigma$, approssimare a rango k**)
* CUR (**seleziona, normalizza, riduci doppioni, lower bound**)
* NNMF (**W contiene le basi, H i coefficienti**)

Sistemi di raccomandazione

* LSI (**Latent Semantic Indexing, in pratica usa l'SVD**)
* NBI (**Trasferimento risorse, calcolo matrice pesi proiezione insieme U**)
* Bellkor 
  * CF con bias (**contributo globale**)
  * Ottimizzazione su similarità
  * Ottimizzazione SVD con regolarizzazione
  * Finale: ottimizzazione SVD e bias (con ottimizzazione)

Locality Sensitive Hashing

* Jaccard-Sim, documenti e plagio, Shingle, k-Shingle
* Characteristic matrix, min-hash, Signature Matrix, **teoremi** 
* Row hashing 
* LSH con distanza di hamming e con minhash
* Famiglie Locality Sensitive, costruzione in OR ed in AND 
* Famiglie per distanza di hamming, minhash, coseno e euclidea

PageRank

* PageRank (**formulazione ricorsiva, matriciale, deadend e spidertrap**)
* PageRank con teleport (**formulazione ricorsiva e matriciale**)
* Ingegnerizzazioni del PR (**r in memoria, r non entra (Block-Stripe Update alg.)**)
* Topic Specific PageRank 
* SimRank
* Webspam, spam farm (**dimostrazione matematica**)
* TrustRank (**algoritmo, costruzione seed set**)
* HITS (**algoritmo, algoritmo in not. vett. power method**) 

Reti e modelli random

* Reti small world
* Erdos-Renyi
* Watts-Strogatz
* Kleinberg (Problema navigazione)
* Configuration model
* Reti scale-free
* Barabasi-Albert

Comunità

* Teoria di Granovetter
* Algoritmo di Girvan-Newman (**betweenness, modularità**)
* Ottimizzazione diretta della modularità (**bipartizione, teo. autodecomp.**)
* Graph cut, Normalized Cut, Volume
* Spectral Graph Partitioning (**Autocoppia di Fiedler, Teorema di Rayleigh**)
* Sweep (**conduttanza, approximated PageRank**)
* Motif-Based Clustering (**Sweep con motif**)
* Louvain (**greedy, ottimizzare modularità, mod. gain, supernodi**)
* Trawling (**MBI, g bipartito, $K_{s,t}$**)

