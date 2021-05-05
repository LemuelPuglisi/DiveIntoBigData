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

Per semplicità, abbreviamo: 
$$
sim^*(S_i, S_j) \equiv sim^*(h(S_i), h(S_j))
$$
Sia $s$ una soglia di similarità al di sopra della quale consideriamo due insiemi di shingle "altamente simili". Supponiamo che $s$ sia limitato inferiormente da una costante $c$. Allora vale il seguente teorema: 

Siano $0 < \delta < 1$, $\epsilon > 0$, e $k > 2 \delta^{-2}c^{-1}\log{\epsilon^{-1}}$, allora per ogni coppia di insiemi $S_i, S_j$ valgono le seguenti proprietà:
$$
\text{a) }  sim(S_i, S_j) \ge s \ge c \Longrightarrow P(sim^*(S_i, S_j) \ge (1-\delta)s) \ge 1 - \epsilon \\
\text{b) }  sim(S_i, S_j) \le c \Longrightarrow P(sim^*(S_i, S_j) \le (1-\delta)c) \ge 1 - \epsilon \\
$$
Dove $\epsilon$ e $\delta$ sono parametri arbitrari; il teorema ci permette di limitare il numero di falsi positivi e falsi negativi (controllare l'errore). Dimostriamo la proprietà a) 
$$
\text{a) }  sim(S_i, S_j) \ge s \ge c \Longrightarrow P(sim^*(S_i, S_j) \ge (1-\delta)s) \ge 1 - \epsilon
$$
Scriviamo: 
$$
P(sim^*(S_i, S_j) < (1-\delta)s) < \epsilon
$$
Banalmente, poiché se l'evento $A$ accade con probabilità $p$, allora l'*evento complementare o contrario* $A^C$ accade con probabilità $1-p$. 

Grazie al teorema precedente, possiamo asserire: 
$$
P\left(h_{\pi}(S_i) = h_{\pi}(S_j)\right) = \frac{|S_i \cap S_j |}{|S_i \cup S_j |}
$$
Usiamo $k$ funzioni hash, quindi $k$ permutazioni e definiamo una variabile aleatoria $X$ come segue: 
$$
X = X_1 + X_2 + \dots + X_k
$$
Con
$$
X_l = \begin{cases}
1 \text{ se } h_{l}(S_i) = h_{l}(S_j) \\
0 \text{ altrimenti}
\end{cases} \text{ per } l = 1, \dots, k
$$
Osserviamo che
$$
P(X_l = 1) = \frac{|S_i \cap S_j |}{|S_i \cup S_j |} = p
$$
La variabile aleatoria $X$ è distribuita secondo una distribuzione binomiale, $X \sim Bi(k, p)$. Il valore atteso è noto e corrisponde a: 
$$
E[X] = kp = k \cdot \frac{|S_i \cap S_j |}{|S_i \cup S_j |} = k \cdot sim(S_i, S_j)
$$
Poiché la similarità originale $sim$ corrisponde proprio alla similarità di Jaccard. 

Sappiamo che la similarità tra signatures $sim^*$ corrisponde al numero di volte in cui i minhash corrispondono (casi favorevoli) rispetto al numero di minhash totali, quindi scriviamo: 
$$
sim^*(S_i, S_j) = \frac{X}{k}
$$
Anche questa è una variabile aleatoria, il cui valore atteso corrisponde a: 
$$
E\left[\frac{X}{k}\right] = \frac 1 k E[X] = \frac 1 k \cdot k \cdot sim(S_i, S_j) = sim(S_i, S_j) 
$$
Quindi la similarità tra signatures è un estimatore corretto della similarità di Jaccard tra gli insiemi. Questo vuol dire che al crescere del numero di funzioni hash applicate, per la legge dei grandi numeri, l'estimatore converge al parametro stimato. Utilizziamo adesso un importante teorema, chiamato **Chernoff Bound**, che enuncia: 
$$
P(X < (1 - \delta) E[X]) < \exp(-\frac{\delta^2E[X]}{2})
$$
Riscriviamo l'espressione dettata dal Chernoff Bound sostituendo i protagonisti e ricordandoci di aver considerato il complementare nell'espressione (17):
$$
P(sim^*(S_i, S_j) < (1 - \delta)s )
= P\left(\frac{X}{k} < (1-\delta)s\right) 
= P\left(X < (1-\delta)sk\right)
$$
Per ipotesi, sappiamo che: 
$$
sim(S_i, S_j) \ge s
$$
Quindi possiamo sostituire nella disequazione (26) $s$ con la similarità di Jaccard: 
$$
P(X < (1 - \delta) \cdot k \cdot sim(S_i, S_j))
$$
Essendoci ricondotti al valore atteso di $X$ (vedasi espressione 22), è possibile applicare il Chernoff Bound: 
$$
P(X < (1 - \delta) \cdot k \cdot sim(S_i, S_j)) < \exp(-\frac{\delta^2E[X]}{2}) = \\ 
= \exp(-\frac{\delta^2 k \cdot sim(S_i, S_j)}{2}) \le \\
\le \exp(-\frac{\delta^2 k \cdot s}{2}) < \epsilon
$$
Fissati $\epsilon$ e $\delta$, ricaviamo la quantità $k$ (funzioni hash necessarie ad ottenere un certo controllo dell'errore) risolvendo la disequazione: 
$$
\exp(-\frac{\delta^2 k \cdot s}{2}) < \epsilon		\Longrightarrow 
-\frac{\delta^2 k \cdot s}{2} < \log \epsilon		\Longrightarrow 
\frac{\delta^2 k \cdot s}{2} > \log \epsilon^{-1}	\Longrightarrow
k > 2\delta^{-2}s^{-1}\log \epsilon^{-1}
$$
Quindi la proprietà è dimostrata. 



#### 5.3.4 Row hashing 

Effettuare una permutazione è una operazione molto dispendiosa. Una ingegnerizzazione del min-hashing, chiamata row-hashing, permette di effettuare lo stesso procedimento rapidamente. Supponiamo di avere $d$ documenti $S_1, \dots, S_d$ e $k$ funzioni hash $h_1, \dots, h_k$ del tipo: 
$$
h(x) = [(ax + b) \mod p] \mod N
$$
Dove $a$ e $b$ sono due interi arbitrari, $p$ è un numero primo, $N$ è il numero di shingle e si ha che $p > N$.  Sappiamo a priori che la matrice delle signatures avrà dimensione $k \times d$. La procedure consiste nei seguenti passi: 

* Inizializzare la matrice delle signatures $S$ ad $\infty$.
* Per ogni riga (termine) $i = 0, \dots, N$ 
  * Per ogni funzione hash $j = 0, \dots, k$ 
    * Generare l'indice $h_j(i)$ della riga $i$ nella permutazione data da $h_j$ 
    * Per ogni documento $l = 0, \dots, d$ controllare se all'indice $h_j(i)$ vi è un 1. 
      * Se è così, allora se l'elemento $S_{jl}$ è maggiore di $h_j(i)$ 
        * $S_{jl} \leftarrow h_j(i)$

Vediamo un esempio: 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c c c c}
	0 \\
	1 \\
	2 \\
	3 \\
	4 \\
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
Supponiamo di avere $k=2$ funzioni hash definite come segue: 
$$
h_1(x) = (x+1) \mod{5} \\
h_2(x) = (3x+1) \mod{5}
$$
Che generano le due permutazioni: 
$$
\begin{array}{c c} &
	\begin{array}{c c} h1 & h_2\
	\end{array} \\
	\begin{array}{c c c c c}
	0 \\
	1 \\
	2 \\
	3 \\
	4 \\
	\end{array}
& \left[
	\begin{array}{c c}
	1 & 1 \\
	2 & 4 \\
	3 & 2 \\
	4 & 0 \\
	0 & 3 \\
	\end{array}
\right]
\end{array}
$$
Inizializziamo la matrice delle signatures: 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c}
	h_1 \\
	h_2 \\
	\end{array}
& \left[
	\begin{array}{c c c c}
	\infty & \infty & \infty & \infty \\
	\infty & \infty & \infty & \infty \\

	\end{array}
\right]
\end{array}
$$
La riga $i=0$ della characteristic matrix ha indici $(1,1)$ nelle permutazioni generate dalle funzioni hash. I documenti $S_1$ ed $S_4$ hanno valore 1 nella prima riga, quindi possiamo aggiornare la matrice delle signatures come segue: 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c}
	h_1 \\
	h_2 \\
	\end{array}
& \left[
	\begin{array}{c c c c}
	1 & \infty & \infty & 1 \\
	1 & \infty & \infty & 1 \\

	\end{array}
\right]
\end{array}
$$
La riga $i=1$ della characteristic matrix ha indici $(2,4)$ e solo il documento $S_3$ ha valore 1, aggiorniamo: 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c}
	h_1 \\
	h_2 \\
	\end{array}
& \left[
	\begin{array}{c c c c}
	1 & \infty & 2 & 1 \\
	1 & \infty & 4 & 1 \\

	\end{array}
\right]
\end{array}
$$
La riga $i=2$ della characteristic matrix ha indici $(3,2)$; i documenti $S_2$ ed $S_4$ hanno valore 1, aggiorniamo i valori minori di quelli esistenti nella matrice delle signatures (ad esempio $S_4$ rimane lo stesso poiché $1 < 3$ e $1 < 2$): 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c}
	h_1 \\
	h_2 \\
	\end{array}
& \left[
	\begin{array}{c c c c}
	1 & 3 & 2 & 1 \\
	1 & 2 & 4 & 1 \\

	\end{array}
\right]
\end{array}
$$
Allo stesso modo, per $i=3$: 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c}
	h_1 \\
	h_2 \\
	\end{array}
& \left[
	\begin{array}{c c c c}
	1 & 3 & 2 & 1 \\
	0 & 2 & 0 & 0 \\

	\end{array}
\right]
\end{array}
$$
E per $i=4$: 
$$
\begin{array}{c c} &
	\begin{array}{c c c c} S_1 & S_2 & S_3 & S_4 \\
	\end{array} \\
	\begin{array}{c c}
	h_1 \\
	h_2 \\
	\end{array}
& \left[
	\begin{array}{c c c c}
	1 & 3 & 0 & 1 \\
	0 & 2 & 0 & 0 \\

	\end{array}
\right]
\end{array}
$$
Abbiamo costruito la matrice delle signatures senza generare effettivamente le permutazioni delle righe di ogni colonna. Questa ingegnerizzazione rende il minhashing molto più rapido. 