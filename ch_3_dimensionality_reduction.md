## 3. Dimensionality reduction

### 3.1 Introduzione

Molte fonti di dati possono essere viste come matrici di grandi dimensioni (web, social network [matrici di adiacenza], sistemi di raccomandazione [matrice di utilità]). Una matrice può essere riassunta da matrici di dimensione minore, con cui è più efficiente effettuare operazioni. Queste vengono ricavate con metodi di riduzione della dimensionalità. Si effettua riduzione della dimensionalità poiché alcune feature possono risultare irrilevanti, poiché vi è necessità di visualizzare i dati ad alta dimensionalità o poiché la dimensione intrinseca può essere inferiore al numero di feature. 



#### 3.1.1 Unsupervised feature selection

Nella *feature selection supervisionata* vengono selezionate le feature più interessanti rispetto ad una certa etichetta di classe. La dimensionality reduction può essere considerata una feature selection *non supervisionata*, poiché non si basa su etichette di classe, anche se le feature non vengono selezionate, bensì vengono definite in funzione di quelle originali.



#### 3.1.2 Visualizzazione della riduzione

Supponiamo di avere dei punti distribuiti in uno spazio di dimensione $D$. Può capitare che i punti cadano tutti vicini (o direttamente su) uno spazio a dimensione minore $d$. In questo caso gli assi del sottospazio $d$ sono l’effettiva rappresentazione dei dati. 



<img src="assets\ch3\dim_red_ex.PNG" alt="dimensionality reduction" style="zoom: 20%;" />



#### 3.1.3 Richiami di algebra lineare 

* Il **rango** di una matrice $A$ è un numero intero non negativo associato alla matrice $A$. Ne indica il numero di righe (o colonne) linearmente indipendenti, ovvero non ricavabili attraverso combinazioni lineari di altre righe (o colonne).
* Si dice **base di uno spazio vettoriale** un insieme di vettori grazie ai quali possiamo ricostruire in modo  unico tutti i vettori dello spazio mediante combinazioni lineari. Disponendo di una base di uno spazio vettoriale conosciamo quindi,  automaticamente, l'intero spazio vettoriale.
* Si dicono **coordinate (o componenti) di un vettore rispetto a una base** gli scalari mediante cui il vettore si esprime come combinazione lineare dei vettori della base. Equivalentemente, fissata una base di  uno spazio vettoriale, le coordinate di un vettore rispetto alla base  scelta sono i coefficienti della combinazione lineare con cui si esprime il vettore in termini degli elementi della base.
* Sia $M$ una matrice quadrata. Sia $\lambda$ una costante ed $\bar e$ un vettore colonna non-zero con lo stesso numero di righe di $M$. Diciamo che $\lambda$ è un **autovalore** di $M$ ed $\bar e$ è il suo corrispondente **autovettore** di $M$ se $M \bar e = \lambda \bar e$. La coppia $(\lambda, \bar e)$ prende il nome di **autocoppia**.
* Se $\bar e$ è un autovettore di $M$ con autovalore $\lambda$ e $c$ è una qualsiasi costante, allora anche $c \cdot \bar e$ è un autovettore di $M$ con lo stesso autovalore $\lambda$. Moltiplicare il vettore per una costante cambia il suo modulo ma non la sua direzione. Per evitare ambiguità assumeremo che ogni autovettore sia un vettore unitario (*unit vector*), ovvero di modulo 1. 
* Sia $M$ una matrice quadrata con autovalori $\lambda_1, \dots, \lambda_n$ e corrispondenti autovettori $\bar e_1, \dots, \bar e_n$. Se succede che $|\lambda_1| > \dots > |\lambda_n|$ allora chiameremo $\lambda_1$ **autovalore principale**



#### 3.1.4 Esempio pratico

Supponiamo di avere in input la seguente matrice A
$$
A = \begin{bmatrix}
1 & 1 & 1 & 0 & 0 \\
2 & 2 & 2 & 0 & 0 \\
1 & 1 & 1 & 0 & 0 \\
5 & 5 & 5 & 0 & 0 \\
0 & 0 & 0 & 2 & 2 \\
0 & 0 & 0 & 3 & 3 \\
0 & 0 & 0 & 1 & 1
\end{bmatrix}
$$
La matrice $A$ è bidimensionale, questo poiché è rappresentabile a partire dalla base
$$
[1,1,1,0,0], [0,0,0,1,1] 
$$
Se si volesse ricostruire la quarta riga, sarebbe possibile sfruttare una combinazione lineare dei vettori (generatori) della base: 
$$
5 \cdot [1,1,1,0,0] + 0 \cdot [0,0,0,1,1] = [5,5,5,0,0]
$$
Il rango della matrice è quindi l'effettiva dimensione (intrinseca) della matrice. Facciamo un altro esempio: 
$$
A = \begin{bmatrix}
1 & 2 & 1 \\
-2 & -3 & 1 \\
3 & 5 & 0 \\
\end{bmatrix}
$$
La terza riga è ottenibile dalla differenza tra la prima e la seconda riga, per cui non è linearmente indipendente. Questo vuol dire che il rango è minore di 3. Determinato il rango $\rho(A) = 2$, indichiamo una nuova base per la matrice: 
$$
[1,2,1], [-2,-3,1]
$$
Ed otteniamo delle nuove coordinate per le tre righe:
$$
[1, 0], [0, 1], [1, -1]
$$
I vettori della base sono gli assi di rappresentazione dei dati, per cui possiamo rappresentare i dati come coefficienti di tali vettori e lavorare su dimensioni minori (2 anziché 3). Per ritornare da coordinate $(a, b)$ alla base di partenza è sufficiente calcolare la combinazione lineare $a \cdot [1,2,1] + b \cdot [-2,-3,1]$. 



#### 3.1.5 Idea principale

L'obiettivo della dimensionality reduction è proprio quello di identificare gil assi dei dati. Molto spesso i dati non giacciono esattamente su una dimensione minore, per cui è necessario ammettere un *margine di errore*.  Dato un insieme di punti in uno spazio $d$-dimensionale, l'idea principale è quella di proiettare i dati in uno spazio con meno dimensioni preservando quanta più informazione possibile. Scegliamo la proiezione che minimizza il *quadrato dell'errore* quando ricostruiamo i dati originali. 



### 3.2 Calcolo di autovalori ed autovettori

Nei richiami di algebra lineare abbiamo scritto che, per ogni autocoppia $(\lambda, \bar{e})$ si ha che:
$$
M \bar e = \lambda \bar{e}
$$
Possiamo riscrivere l'equazione nel seguente modo:
$$
(M - \lambda I)e = \bar 0
$$


Dove $I$ è una matrice identità delle stesse dimensioni di $M$. Tale equazione in forma matriciale è rappresentabile come un sistema di equazioni lineari. La matrice $(M - \lambda I)$ corrisponde alla matrice $M$ la cui diagonale è ridotta di una fattore $\lambda$. Sia $\lambda$ incognito, vogliamo trovare gli autovalori e gli autovettori della matrice $M$. 

Per i teoremi sull'algebra lineare, affinché si risolva l'equazione $(M - \lambda I)e = \bar 0$ per un vettore $\bar e \ne \bar 0$, il determinante della matrice $M - \lambda I$ deve essere 0. Sebbene il determinante di una matrice $n \times n$ abbia $n!$ termini, questo può essere calcolato in diversi modi in tempo $O(n^3)$, di seguito vedremo uno tra questi metodi. 



#### 3.2.1 Chiò pivotal condensation 

Il metodo *Chio pivotal condensation* (condensazione pivotale) permette di calcolare il determinante di una matrice $A$ di dimensione $n \times n$ andando a dividere il determinante di una nuova matrice $B$ di dimensione $(n-1) \times (n-1)$ per il primo elemento della matrice $A$ elevato ad $n-2$: 
$$
\det(A) = \frac{\det(B)}{a_{11}^{n-2}}
$$
Ipotesi necessaria è che la diagonale della matrice $A$ sia non nulla, quindi che $a_{ii} \ne 0$. La matrice $B$ va costruita in funzione della matrice $A$. Il generico elemento $b_{ij}$ è ottenuto come segue:
$$
b_{ij} = a_{11} \times a_{i+1,j+1} - a_{1, j+1} \times a_{i+1, 1}
$$
Si applica ricorsivamente il metodo alla matrice $B$ sino a che non si arriva ad una matrice il quale determinante è trattabile con metodi diretti. 



#### 3.2.2 Risolvere l'equazione 

Dato che il determinante della matrice $(M - \lambda I)$ è un polinomio di grado $n$ dove $\lambda$ è l'incognita, allora possiamo ottenere $n$ soluzioni, ovvero ottenere tutti ed $n$ gli autovalori della matrice $M$. Un valore $c$ qualsiasi tra queste $n$ soluzioni risolverà l'equazione $M \bar{e} = c \bar{e}$. 

Per ogni autovalore $\lambda$ trovato, è possibile ricavare il corrispondente autovettore $\bar{e}$ risolvendo il sistema lineare di $n$ equazioni in $n$ incognite, ovvero le componenti dell'autovettore $\bar e$: 
$$
(M - \lambda I)\bar{e} = \bar{0}
$$
Per semplicità imponiamo che ogni autovettore sia unitario, trovando così una sola soluzione. Facciamo un esempio banale: 
$$
M = \begin{bmatrix}
2 & 1  \\
3 & 2 \\
\end{bmatrix}
$$
Quindi impostiamo l'equazione: 
$$
\left( 
\begin{bmatrix}
2 & 1  \\
3 & 2 \\
\end{bmatrix} 
- 
\begin{bmatrix}
\lambda & 0  \\
0 & \lambda \\
\end{bmatrix} 
\right)
\cdot
\begin{bmatrix}
e_1 \\
e_2 \\
\end{bmatrix} 
= 
\begin{bmatrix}
0 \\
0 \\
\end{bmatrix}
$$
Risolvendo la sottrazione all'interno delle parentesi otteniamo: 
$$
(M-\lambda I) = \begin{bmatrix}
2 - \lambda & 1  \\
3 & 2 - \lambda \\
\end{bmatrix}
$$
Il determinante di questa matrice deve essere 0 poiché il sistema lineare abbia soluzioni: 
$$
\det \left(
\begin{bmatrix}
2 - \lambda & 1  \\
3 & 2 - \lambda \\
\end{bmatrix}
\right) = 
\left[ (2 - \lambda) \times (2 - \lambda) \right] - (3 \times 1) = \lambda^2 -4\lambda +1 = 0
$$
Risolvendo il polinomio di grado $n=2$ troviamo i due autovalori: 
$$
\lambda_1 = 2 + \sqrt{3} \text{ ; } \lambda_2 = 2 - \sqrt{3}
$$
Prendiamo la soluzione $\lambda_1$ e sostituiamola all'equazione precedente: 
$$
\left( 
\begin{bmatrix}
2 & 1  \\
3 & 2 \\
\end{bmatrix} 
- 
\begin{bmatrix}
\lambda_1 & 0  \\
0 & \lambda_1 \\
\end{bmatrix} 
\right)
\cdot
\begin{bmatrix}
e_1 \\
e_2 \\
\end{bmatrix} 
= 
\begin{bmatrix}
0 \\
0 \\
\end{bmatrix}
$$
Ovvero: 
$$
\begin{cases}
(2 - \lambda_1)e_1 + e_2 = 0 \\
3e_1 + (2-\lambda_1)e_2 = 0 \\ 
\sqrt{e_1^2 + e_2^2} = 1 \text{ (vincolo)}
\end{cases}
$$
Risolviamo il sistema lineare ed otteniamo le componenti dell'autovettore $\bar{e}^{(1)}$ associato all'autovalore $\lambda_1$. Ripetiamo il processo con l'autovalore $\lambda_2$. 

 

#### 3.2.3 Power iteration

Nella pratica, per matrici molto grandi, la soluzione precedente non è ammissibile. Studiamo un metodo alternativo computazionalmente meno oneroso, chiamato *power iteration*. 
Sia $M$ una matrice di dimensioni $n \times n$ per la quale desideriamo calcolare le autocoppie. 

Partiamo da un vettore generato casualmente $\bar{x}_1$ di dimensione $n$. Calcoliamo un nuovo vettore $\bar{x}_2$ come segue:
$$
\bar{x}_2 = \frac{M \cdot \bar{x}_1}{||M \cdot \bar{x}_1||}
$$
dove con $||M||$ intendiamo la *norma di Frobenius*: 
$$
||M|| = \sqrt{\sum_{i,j}m_{ij}^2} 
$$
Osserviamo che, così facendo, il vettore $\bar{x}_2$ sarà normalizzato ad 1. Procediamo iterativamente calcolando il generico vettore $\bar{x}_i$ (per $i > 1$) come segue: 
$$
\bar{x}_i = \frac{M \cdot \bar{x}_{i-1}}{||M \cdot \bar{x}_{i-1}||}
$$
Fissato arbitrariamente un valore costante piccolo $\epsilon$, l'iterazione si fermerà quando 
$$
|| x_i - x_{i+1}|| \le \epsilon
$$
A questo punto, $x_i$ è approssimativamente l'autovettore ***principale*** di $M$. Calcoliamo l'autovalore corrispondente attraverso la formula inversa: 
$$
\lambda_1 = {\bar{x}_i}^T \cdot M \cdot \bar{x}_i
$$
Utilizzando questo metodo ricaveremo la prima autocoppia principale $(\lambda_1, \bar{x}_1)$ (oss. $\lambda_1$ è l'autovalore più grande). Per calcolare le rimanenti autocoppie è necessario enunciare il seguente teorema.



#### 3.2.4 Teorema 

Sia $A$ una matrice di dimensione $n \times n$ con $\lambda_1, \dots, \lambda_n$ autovalori e $\bar{v}_1, \dots, \bar{v}_n$ autovettori.

Se gli autovettori sono unitari (quindi $\bar{v}_i^T \cdot \bar{v}_i = 1$), allora posso costruire una nuova matrice $B$ come segue:
$$
B = A - \lambda_1 \cdot \bar{v}_1 \cdot \bar{v}_1^T
$$
Se gli autovettori non sono unitari, allora $\exists \bar{x} : \bar{v}_1 \cdot \bar{x} = 1$, per cui utilizzo $\bar{x}$ nella formula di costruzione della matrice $B$:
$$
B = A - \lambda_1 \cdot \bar{v}_1 \cdot \bar{x}
$$
La matrice $B$ avrà un autovalore nullo (sottratto nell'espressione precedente) con corrispondente autovettore $\bar v_1$. I restanti $n-1$ autovalori $\lambda_2, \dots, \lambda_n$ saranno uguali a quelli della matrice di partenza $A$, mentre i corrispondenti autovettori $\bar u_2, \dots, \bar u_n$ saranno diversi. 

La power iteration calcola l'autocoppia principale (autovalore più grande): se otteniamo tramite power iteration l'autocoppia principale da $A$, una volta annullato l'autovalore più grande (su $B$) sarà possibile ri-eseguire la power iteration su $B$ ed ottenere il secondo autovalore più grande $\lambda_2$ e il corrispondente autovettore $\bar u_2$. Per ricondurci all'autovettore corrispondente a $\lambda_2$ su $A$ è necessario applicare la seguente formula: 
$$
\bar v_2 = (\lambda_1 - \lambda_2) \cdot \bar u_2 + \lambda_1 \cdot (\bar v_1^T \cdot \bar u_2) \cdot \bar v_1
$$
Generalizzando per ogni $\bar u_i$ scriviamo
$$
\bar v_i = (\lambda_1 - \lambda_i) \cdot \bar u_i + \lambda_1 \cdot (\bar v_1^T \cdot \bar u_i) \cdot \bar v_1 \text{ per } i = 2, \dots, n
$$


#### 3.2.5 Procedura 

```

    def calculate_eigen_values_and_vectors(A):
        # initialization
        eigen_value[1], eigen_vector[1] = power_iteration(A)
        B = A
        for i = 1 to n-1:
            # azzeriamo iterativamente tutti gli autovalori di A 
            B = B - (eigen_value[i] * eigen_vector[i] * transpose(eigen_vector[1]))
            # utilizzando la power iteration per calcolare i restanti
            eigen_value[i], eigen_vector[i] = power_iteration(B)
        return eigen_value, eigen_vector

```



> LEZIONE 7 ore 00:41:11