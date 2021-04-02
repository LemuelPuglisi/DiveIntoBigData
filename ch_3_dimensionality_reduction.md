## 3. Dimensionality reduction

### 3.1 Introduzione

Molte fonti di dati possono essere viste come matrici di grandi dimensioni (web, social network [matrici di adiacenza], sistemi di raccomandazione [matrice di utilità]). Una matrice può essere riassunta da matrici di dimensione minore, con cui è più efficiente effettuare operazioni. Queste vengono ricavate con metodi di riduzione della dimensionalità. Si effettua riduzione della dimensionalità poiché alcune feature possono risultare irrilevanti, poiché vi è necessità di visualizzare i dati ad alta dimensionalità o poiché la dimensione intrinseca può essere inferiore al numero di feature. 



#### 3.1.1 Unsupervised feature selection

Nella *feature selection supervisionata* vengono selezionate le feature più interessanti rispetto ad una certa etichetta di classe. La dimensionality reduction può essere considerata una feature selection *non supervisionata*, poiché non si basa su etichette di classe, anche se le feature non vengono selezionate, bensì vengono definite in funzione di quelle originali.



#### 3.1.2 Visualizzazione della riduzione

Supponiamo di avere dei punti distribuiti in uno spazio di dimensione $D$. Può capitare che i punti cadano tutti vicini (o direttamente su) uno spazio a dimensione minore $d$. In questo caso gli assi del sottospazio $d$ sono l’effettiva rappresentazione dei dati. 



<img src="C:\Users\Charlemagne\Documents\Repository Universitarie\DiveIntoBigData\assets\ch3\dim_red_ex.PNG" alt="dimensionality reduction" style="zoom: 20%;" />



#### 3.1.3 Richiami di algebra lineare 

* Il **rango** di una matrice $A$ è un numero intero non negativo associato alla matrice $A$. Ne indica il numero di righe (o colonne) linearmente indipendenti, ovvero non ricavabili attraverso combinazioni lineari di altre righe (o colonne).
* Si dice **base di uno spazio vettoriale** un insieme di vettori grazie ai quali possiamo ricostruire in modo  unico tutti i vettori dello spazio mediante combinazioni lineari. Disponendo di una base di uno spazio vettoriale conosciamo quindi,  automaticamente, l'intero spazio vettoriale.
* Si dicono **coordinate (o componenti) di un vettore rispetto a una base** gli scalari mediante cui il vettore si esprime come combinazione lineare dei vettori della base. Equivalentemente, fissata una base di  uno spazio vettoriale, le coordinate di un vettore rispetto alla base  scelta sono i coefficienti della combinazione lineare con cui si esprime il vettore in termini degli elementi della base.
* Sia $M$ una matrice quadrata. Sia $\lambda$ una costante ed $e$ un vettore colonna non-zero con lo stesso numero di righe di $M$. Diciamo che $\lambda$ è un **autovalore** di $M$ ed $e$ è il suo corrispondente **autovettore** di $M$ se $Me = \lambda e$. La coppia $(\lambda, e)$ prende il nome di **autocoppia**.
* Se $e$ è un autovettore di $M$ con autovalore $\lambda$ e $c$ è una qualsiasi costante, allora anche $c \cdot e$ è un autovettore di $M$ con lo stesso autovalore $\lambda$. Moltiplicare il vettore per una costante cambia il suo modulo ma non la sua direzione. Per evitare ambiguità assumeremo che ogni autovettore sia un vettore unitario (*unit vector*), ovvero di modulo 1. 
* Sia $M$ una matrice quadrata con autovalori $\lambda_1, \dots, \lambda_n$ e corrispondenti autovettori $e_1, \dots, e_n$. Se succede che $|\lambda_1| > \dots > |\lambda_n|$ allora chiameremo $\lambda_1$ **autovalore principale**



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



> LEZIONE 7: 00:09:13, calcolo degli autovalori e autovettori

