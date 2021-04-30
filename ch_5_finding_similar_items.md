## 5. Finding Similar Items 

Un problema noto è la ricerca di item simili: supponiamo di voler esaminare le pagine web per cercare casi di plagio. Un approccio naive consisterebbe nel calcolare la distanza di ogni coppia di pagine; tuttavia questo approccio risulta proibitivo nel caso di grandi dataset poiché quadratico.



### 5.1 Similarità tra insiemi

Supponiamo di rappresentare i documenti (i.e. pagine web) come insiemi. Un modo di calcolare la similarità tra due insiemi $S$ e $T$ è la similarità di Jaccard, definita come il rapporto tra la cardinalità dell'intersezione e dell'unione dei due insiemi: 
$$
sim(S,T) = \frac{|S \cup T|}{|S \cap T|} \in [0,1]
$$

> Dare una lettura al sottocapitolo 3.1 di 'Mining of massive datasets' di Ullman et al. 



### 5.2 Shingling 

Il metodo più efficace per rappresentare insiemisticamente dei documenti consiste nel creare un insieme a partire dalle stringhe contenute nel testo. Due documenti simili avranno stringhe (es. parole o frasi) simili. Una semplice tecnica per ottenere un insieme da un documento testuale è lo shingling. 



#### 5.2.1 $k$-Shingles 

Un documento è una stringa di caratteri. Si definisca un $k$-shingle di un documento come una qualsiasi sottostringa di lunghezza $k$ nel documento. Dopodiché vengono associati al documento un insieme di $k$-shingles e la loro frequenza assoluta.



#### 5.2.2 Lunghezza dello shingle 

Scegliendo un valore $k$ troppo piccolo, ci aspetteremo che quasi tutte le sequenze di $k$ caratteri siano presenti nella maggioranza dei documenti. Così facendo, ogni coppia di documenti avrebbe una similarità di Jaccard molto alta.  

> $k$ deve assumere un valore grande abbastanza tale che la probabilità che un qualsiasi shingle appaia in un qualsiasi documento sia bassa. 

Nel caso di un corpus di email, $k = 5$ dovrebbe funzionare bene: supponendo che si incontrino solo caratteri alfabetici e spazi bianchi, allora avremo $27^5=14,348,907$ shingle. Essendo che una email è molto più corta del numero indicato, la probabilità che uno tra gli shingle appaia nel documento risulta essere bassa. 



#### 5.2.3 Hashing shingles

Sia $A$ l'insieme dei caratteri possibili in un documento e supponiamo, per semplicità, che essi siano 27. Per $k = 9$ abbiamo $(27)^9$ possibili shingle. Ogni carattere occupa un byte, quindi un intero shingle occuperà 9 byte. Possiamo applicare una funzione di hashing che mappa ogni shingle in un numero tra $0$ e $(27)^9-1$: 
$$
h: A \to \{0, 1, \dots, (27)^9-1\}
$$
Così facendo, ogni shingle sarà rappresentato da un intero a 4 byte. Lo spazio occupato sarà $\frac 4 9$ rispetto alla rappresentazione originale. 



#### 5.2.4 Costruire shingle da parole



