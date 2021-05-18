## 6. Link analysis

Il web è un grafo orientato i cui nodi sono rappresentati dalle pagine web, mentre gli archi corrispondono ai link tra le pagine. Data una query di ricerca, il problema del web search consiste nel restituire dei siti web coerenti con la query, ma soprattutto autorevoli. Il primo approccio alla navigazione web fu' la *web directory*, ovvero un elenco di siti web curati manualmente e suddivisi in maniera gerarchica. Una web directory non è un motore di ricerca e risulta essere molto limitante. Il secondo tentativo fu' proprio il web search, che consiste in due problemi: il primo problema è il più semplice, consiste nel trovare un insieme di pagine che contengono parole in comune con la query, il secondo problema è più complesso e consiste nel ritornare solo pagine web autorevoli ed evitare lo spam. 



### 6.1 PageRank

Il PageRank è un algoritmo di link analysis creato da Page, uno dei due fondatori di Google. Supponiamo di dare un valore di importanza iniziale a tutti i nodi, interpretiamo i link come dei "voti" e, se un nodo ha 10 link uscenti, allora dividerà la propria importanza equamente tra tutti e 10 i nodi. I link da pagine web importanti contano di più e tale concetto è implementabile attraverso la ricorsione. 



#### 6.1.1 Formulazione ricorsiva di base

Ogni voto di un link è proporzionale all'importanza della sua pagina sorgente. Se la pagina $j$ con importanza $r_j$ ha $n$ link uscenti, ogni link prende $\frac{r_j}{n}$ voti. L'importanza di $j$ è la somma dei voti dei suoi link entranti. Possiamo osservare che un voto da una pagina importante vale di più e che una pagina è importante se viene puntata da altre pagine importanti. Definiamo il rank $r_j$ per la pagina $j$, allora scriviamo
$$
r_j = \sum_{i\to j} \frac{r_i}{d_i^{out}}
$$
Quindi il rank $r_j$ è dato dalla somma, per tutti i nodi $i$ che puntano a $j$, del voto del nodo $i$, che è calcolato dividendo la sua importanza per il suo grado uscente. Vediamo un grafo d'esempio

![image-20210518093255551](ch_6_ Link_analysis.assets/image-20210518093255551.png)

In questo caso avremo che 
$$
\begin{cases}
r_m = \frac{r_a}{2} \\
r_a = r_m + r_y \\ 
r_y = \frac{r_a}{2}
\end{cases}
$$
Questo è un sistema di 3 equazioni in 3 incognite con infinite soluzioni (equivalenti a meno di un fattore di scala). Un vincolo addizionale rende la soluzione unica: 
$$
r_y + r_a + r_m = 1
$$
Da cui otteniamo che $r_a = \frac 1 2$, $r_y = \frac 1 4$ ed $r_m = \frac 1 4$. Con un risolutore di equazioni lineari è possibile trovare le soluzioni per piccoli grafi. Tuttavia, il grafo del web è massivo e necessita di una nuova formulazione. 



#### 6.1.2 Formulazione matriciale

Supponiamo di avere una matrice di adiacenza $M$ per il grafo del web e consideriamo la formulazione stocastica di tale matrice, ovvero:
$$
M_{ij} = \begin{cases}
\frac{1}{d_i^{out}} \text{ if } i \to j \\
0 \text{ otherwise }
\end{cases}
$$
La riga $i$-esima della matrice indica gli archi uscenti da $i$, se $i$ ha $n$ archi uscenti, allora se $i$ si collega a $j$ l'elemento $(i,j)$ varrà $\frac 1 n$ anziché $1$. Strutturando così la matrice avremo che la somma delle colonne in una riga risulterà $1$. 

Il vettore rank $r$ è un vettore con un elemento per ogni pagina, in cui $r_i$ indica lo score di importanza della pagina $i$. Ovviamente varrà che $\sum_i r_i = 1$.  (???)

> Riprendere a 1:14:44 

