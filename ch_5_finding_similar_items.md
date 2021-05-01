## 5. Finding Similar Items 

Un problema noto è la ricerca di item simili: supponiamo di voler esaminare le pagine web per cercare casi di plagio. Un approccio naive consisterebbe nel calcolare la distanza di ogni coppia di pagine; tuttavia questo approccio risulta proibitivo nel caso di grandi dataset poiché quadratico.



### 5.1 Similarità tra insiemi

Supponiamo di rappresentare i documenti (i.e. pagine web) come insiemi. Un modo di calcolare la similarità tra due insiemi $S$ e $T$ è la similarità di Jaccard, definita come il rapporto tra la cardinalità dell'intersezione e dell'unione dei due insiemi: 
$$
sim(S,T) = \frac{|S \cup T|}{|S \cap T|} \in [0,1]
$$

> Dare una lettura al sottocapitolo 3.1 di 'Mining of massive datasets' di Ullman et al. 



### 5.2 Shingling 

Il metodo più efficace per rappresentare insiemisticamente dei documenti, allo scopo di identificare documenti lessicalmente simili, consiste nel creare un insieme a partire dalle stringhe contenute nel testo. Due documenti simili avranno stringhe (es. parole o frasi) simili. Una semplice tecnica per ottenere un insieme da un documento testuale è lo shingling. 



#### 5.2.1 $k$-Shingles 

Un documento è una stringa di caratteri. Si definisca un $k$-shingle di un documento come una qualsiasi sottostringa di lunghezza $k$ nel documento. Dopodiché vengono associati al documento un insieme di $k$-shingles ed (opzionalmente) la loro frequenza assoluta.



#### 5.2.2 Lunghezza dello shingle 

Scegliendo un valore $k$ troppo piccolo, ci aspetteremo che quasi tutte le sequenze di $k$ caratteri siano presenti nella maggioranza dei documenti. Così facendo, ogni coppia di documenti avrebbe una similarità di Jaccard molto alta.  

> $k$ deve assumere un valore grande abbastanza, tale che la probabilità che un qualsiasi shingle appaia in un qualsiasi documento sia bassa. 

Nel caso di un corpus di email, $k = 5$ dovrebbe funzionare bene: supponendo che si incontrino solo caratteri alfabetici e spazi bianchi, allora avremo $27^5=14,348,907$ shingle. Essendo che una email è molto più corta del numero indicato, la probabilità che uno tra gli shingle appaia nel documento risulta essere bassa. 



#### 5.2.3 Hashing shingles

Sia $A$ l'insieme dei caratteri possibili in un documento e supponiamo, per semplicità, che essi siano 27. Per $k = 9$ abbiamo $(27)^9$ possibili shingle. Ogni carattere occupa un byte, quindi un intero shingle occuperà 9 byte. Possiamo applicare una funzione di hashing che mappa ogni shingle in un numero tra $0$ e $(27)^9-1$: 
$$
h: A^9 \to \{0, 1, \dots, (27)^9-1\}
$$
Così facendo, ogni shingle sarà rappresentato da un intero a 4 byte. Lo spazio occupato sarà $\frac 4 9$ rispetto alla rappresentazione originale. 



#### 5.2.4 Costruire shingle da parole

Supponiamo di osservare una pagina web contenente un articolo con un certo topic. Come distinguiamo le stringhe provenienti dall'articolo dagli altri contenuti della pagina? Si osserva che gli articoli, come la maggior parte dei contenuti scritti in prosa, contengono molte stop words (congiunzioni, articoli, punteggiatura), ovvero parole poco significative nella distinzione del contenuto. 

Tuttavia, si è dimostrato empiricamente che la tripla formata da una stop words e le due parole seguenti formano uno shingle molto efficace. In questo modo l'articolo contribuirà di più alla formazione degli shingle e la distinzione sarà basata su elementi provenienti dall'articolo piuttosto che dagli elementi circostanti della pagina web. La similarità di Jaccard sarà più alta per documenti con articoli simili rispetto a documenti con elementi circostanti all'articolo simili. 



### 5.3 Min-Hashing

Gli shingle possibili sono moltissimi e, all'aumentare dei documenti, potrebbe essere impossibile tenere tutto in memoria principale. L'obiettivo principale è quello di sostituire grandi insiemi di shingle con una rappresentazione molto più piccola, chiamata ***signature*** (*firma*). La proprietà più importante che vogliamo rispettare è che, comparando le *signature* di due documenti, la similarità di Jaccard sia approssimativamente analoga a quella che si otterrebbe comparando i loro rispettivi insiemi di shingle. 



#### 5.3.1 Rappresentazione matriciale degli insiemi

Prima di tutto occorre costruire una *characteristic matrix*. In questa matrice, la $j$-esima colonna rappresenta l'insieme di shingle estratto dal $j$-esimo documento, mentre la $i$-esima riga indica l'$i$-esimo shingle tra tutti gli shingle possibili. 

> Esempio: supponiamo di avere 100 documenti, 27 caratteri possibili e $k=5$. Per ogni documento estraiamo l'insieme di shingle. Nella matrice vi saranno 100 colonne (una per l'insieme di ogni documento) e $27^5$ righe (una per ogni possibile shingle). 

L'elemento $(i,j)$ della matrice varrà 1 se lo shingle $i$ è contenuto nell'insieme $S_j$ degli shingle del documento $j$, altrimenti varrà 0. Chiamiamo *universal set* l'insieme di tutti i possibili shingle.



#### 5.3.2 Min-hashing

Le signatures degli insiemi che vogliamo ottenere sono composte dai risultati di centinaia di calcoli, chiamati ***minash*** della characteristic matrix. Supponiamo di avere la seguente matrice: 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c c c c}
	a \\
	b \\
	c \\
	d \\
	e \\
	\end{array}
& \left[
	\begin{array}{c c c c}
	1 & 0 & 0 & 1 \\
	0 & 0 & 1 & 0 \\
	0 & 1 & 0 & 1 \\
	1 & 0 & 1 & 1 \\
	0 & 0 & 1 & 0 \\
	\end{array}
\right]
\end{array}
$$
Per calcolare il minhash è necessario effettuare una permutazione delle righe, supponiamo che la prima permutazione sia $\pi_1 = <b,e,a,d,c>$ e riscriviamo la matrice: 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c c c c}
	b \\
	e \\
	a \\
	d \\
	c \\
	\end{array}
& \left[
	\begin{array}{c c c c}
	0 & 0 & 1 & 0 \\
	0 & 0 & 1 & 0 \\
	1 & 0 & 0 & 1 \\
	1 & 0 & 1 & 1 \\
	0 & 1 & 0 & 1 \\
	\end{array}
\right]
\end{array}
$$
Dopodiché utilizziamo una funzione hash $h_{\pi_1}()$ su ogni insieme $S_i$ (colonne). Tale funzione ritorna in output la prima riga dell'insieme che presenti un 1:  
$$
h_{\pi_1}(S_1) = a \\
h_{\pi_1}(S_2) = c \\
h_{\pi_1}(S_3) = b \\
h_{\pi_1}(S_4) = a
$$
Quindi il minhash corrisponde a $(a,c,b,a)$. Questo processo si ripete $m$ volte, dove $m$ è solitamente molto più piccolo della cardinalità dell'insieme universale. Una volta calcolato il minhash da una permutazione, esso va inserito come riga di una matrice, chiamata ***signature matrix***: 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c c c c}
	\pi_1 \\
	\dots \\
	\dots \\
	\end{array}
& \left[
	\begin{array}{c c c c}
	a & c & b & a \\
	&\dots \\
	&\dots \\
	\end{array}
\right]
\end{array}
$$
Una volta ripetuto $m$ volte il processo con $m$ diverse permutazioni, la **signature** dell'insieme $S_i$ corrisponderà alla colonna $i$-esima della signature matrix. Per semplicità, definiamo una funzione hash $h$ (notare l'assenza di permutazione al pedice) che rappresenti il processo di calcolo di una signature $h(S_i)$ a partire da un insieme di shingle $S_i$. 



#### 5.3.3 Min-hashing e Similarità di Jaccard

Vogliamo che valga la proprietà fondamentale per cui: 
$$
sim(S_i, S_j) \approx sim^*(h(S_i), h(S_j))
$$
Dove $sim^*$ indica una particolare similarità tra signature che corrisponde alla frazione di minhash in cui esse coincidono:
$$
sim^*(h(S_i), h(S_j)) = \frac
{\text{numero di righe in cui } h_{\pi_j}(S_i) = h_{\pi_j}(S_j)}
{\text{numero totale di righe}}
$$

##### Teorema 1

Si può dimostrare che:  
$$
P( h_{\pi}(S_i) = h_{\pi}(S_j) ) = sim(S_i, S_j)
$$
Sia $S_i$ l'insieme di shingle e $y \in S_i$ un elemento dell'insieme, allora: 
$$
P(\pi(y) = h_{\pi}(S_i)) = \frac{1}{|S_i|}
$$
Dove con $\pi(y)$ indichiamo l'indice di $y$ nella permutazione $\pi$. Possiamo affermare ciò poiché ogni elemento dell'insieme ha equa probabilità di essere primo nella permutazione $\pi$. 

> Nota bene: la cardinalità $|S_i|$ corrisponde al numero di shingle contenuti nell'insieme, ovvero al numero di elementi nella colonna $i$-esima posti ad 1.

Vogliamo studiare la probabilità che si verifichino i due eventi: 
$$
\pi(y) = h_{\pi}(S_i) \text{ se } y \in S_i  \text{ e }
\pi(y) = h_{\pi}(S_j) \text{ se } y \in S_j \\
$$
Quindi che $y$ sia l'elemento di indice 1 nella permutazione $\pi$ in entrambi gli insiemi $S_i$ ed $S_j$ (ammesso che vi sia). La probabilità è sempre distribuita uniformemente, ma adesso è necessario considerare che tutti gli elementi dell'unione $S_i \cup S_j$ sono candidati ad essere primi, quindi: 
$$
P\left(\pi(y) = h_{\pi}(S_i) \and  \pi(y) = h_{\pi}(S_j)\right) = \frac{1}{|S_i \cup S_j |}
$$
Se volessimo trovare la probabilità che il primo elemento sia uguale in entrambi gli insiemi $S_i$ ed $S_j$, è necessario considerare tutti gli elementi della loro intersezione come possibili candidati, dove ogni elemento ha probabilità uniforme, quindi: 
$$
P\left(h_{\pi}(S_i) = h_{\pi}(S_j)\right) = \frac{|S_i \cap S_j |}{|S_i \cup S_j |} = sim(S_i, S_j)
$$
Il che dimostra la nostra tesi. 

##### Teorema 2

Si può dimostrare che la similarità tra insiemi di shingle è uguale al valore atteso della similarità tra le corrispondenti signature. 

Per semplicità, abbreviamo: 
$$
sim^*(S_i, S_j) \equiv sim^*(h(S_i), h(S_j))
$$
Definiamo una **soglia di similarità** $s$ al di sopra del quale consideriamo due insiemi *altamente simili*. Supponiamo che $s$ sia limitato inferiormente da una costante $c$. 

> Lezione da riprende a 1:22:42

> Più è alto il numero di minhash, più ci avviciniamo al valore atteso (teo. del limite centrale)