# Strutture Dati Superficiali e Regolari

Ora vedremo tre nuove **strutture dati superficiali** che si basano su concetti molto simili:

* I campioni della superficie sono memorizzati su una ==**griglia regolare 2D**==
* Hanno ==**connettività implicita**==: ogni campione è *implicitamente* connesso ai propri vicini di griglia.

Esse sono:

* [Campi di altezze](##Campi-di-altezze )
* [Range Scans](##Range scans)
* [Geometry Images](##Geometry-Images)

### Vantaggi comuni di queste strutture

:heavy_check_mark: La **connettività** è *implicita*, quindi non va memorizzata

:heavy_check_mark: Le relazioni di **adiacenza** fra elementi sono *implicite*

:heavy_check_mark: **Multirisoluzione** semplice da ottenere (allo stesso modo delle immagini)

:heavy_check_mark: Il **geometry processing** su queste strutture è facile da parallelizzare 

:heavy_check_mark: Diverse *analogie* con le immagini 2D.

### Svantaggi comuni di queste strutture

:x: Risoluzione **non adattiva**

:x: Espressività *limitata*

---

## Campi di altezze 

<img src="C:\Users\sgabu\AppData\Roaming\Typora\typora-user-images\image-20200515140333824.png" alt="image-20200515140333824" style="zoom: 80%;"   /><img src="https://www.sidefx.com/docs/houdini/images/nodes/sop/convertheightfield_flatten_on.jpg" alt="Convert HeightField" style="zoom:55%;" /> 	

```java
float[][] height = new float[resX][resY];
```

Si tratta di un array bidimensionale di valori *scalari*, ciascuno dei quali rappresenta un'**altezza**. Ciò significa che il valore di coordinate `(x,y)` corrisponde al punto 3D `(x, y, height[x][y])`. Questo implica che 2 delle 3 coordinate sono implicite, poichè sono gli indici della griglia. Un'altra cosa implicita è la **connettività**, in quanto i vertici connessi ad un certo vertice sono quelli delle celle vicine. Ciò genera grosso risparmio di memoria.

| ![image-20200516192033319](C:\Users\sgabu\AppData\Roaming\Typora\typora-user-images\image-20200516192033319.png) |
| :----------------------------------------------------------: |
|    *La connettività implicita ne fa una triangular mesh*     |

In più questo tipo di rappresentazione ha i vantaggi delle immagini 2D riguardo la **multirisoluzione** e l'**editabilità**. Inoltre sarà **regolare** in quanto la *valenza* dei vertici è fissata dalla natura matriciale. Non è però una struttura dati adatta alla **risoluzione adattiva**, in quanto la risoluzione è fissa, e dipende dalla dimensione della griglia.  

In realtà questo tipo di modello viene definito più propriamente **==modello 2.5D==**, in quanto non tutti i punti nello spazio sono rappresentabili, e non quindi tutti i tipi di modelli; ad esempio gli oggetti cavi, o con delle rientranze. Questo poiché i vertici hanno coordinate *x* e *y* 'univoche', ma la terza coordinata non lo è, in quanto dipende dall'unico valore presente dentro la rispettiva cella. 

I campi di altezze sono però dei validissimi candidati per quanto riguarda la rappresentazione di terreni, proprio perchè questi difficilmente presentano rientranze.

Infine, spesso vengono rappresentati attraverso **scale di grigi**:

 <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/5/57/Heightmap.png/200px-Heightmap.png" alt="Heightmap - Wikipedia" style="zoom: 80%;" />

---

## Range scans

Le **range scan** sono una struttura dati interessante poiché sono ottenute spesso attraverso tecniche di acquisizione automatica. Essa è per certi versi simile ai campi di altezze, ma con una semantica opposta:

<img src="C:\Users\sgabu\AppData\Roaming\Typora\typora-user-images\image-20200517114751698.png" alt="image-20200517114751698" style="zoom: 50%;" />

Una range scan è un array 2D di scalari, come i campi di altezze, ma tali scalari non rappresentano altezze di punti in una certa località *(x, y)*, bensì la distanza fra un punto *(x, y)* e l'osservatore. 

```java
float[][] rangeScan = new float[resX][resY]
```

<img src="C:\Users\sgabu\AppData\Roaming\Typora\typora-user-images\image-20200517115850656.png" alt="image-20200517115850656" style="zoom: 67%;" />

`rangeScan[x][y]` può valere uno scalare *d~k~* oppure un valore invalido (ad es. 0) nel caso in cui si rappresenti un punto 'vuoto'. Per calcolare le coordinate 'spaziali' di un punto usiamo i 3 valori
 `(x, y, rangeScan[x][y])`, in particolare avremo:
$$
p_{base} + rangeScan[x][y] \cdot \vec{dir}(x, y) \tag1
$$
<img src="C:\Users\sgabu\AppData\Roaming\Typora\typora-user-images\image-20200517122810372.png" alt="image-20200517122810372" style="zoom: 67%;" />

$\color{darkblue}(1)$ funziona poiché noi stiamo scalando il vettore $\vec{dir}(x, y)$ con il valore memorizzato nella rispettiva cella, e sommiamo il vettore scalato al punto $p_{base}$ per ottenere la profondità spaziale del punto.

È quindi necessario conoscere sia $p_{base}$, cioè il PoV, sia il vettore $\vec{dir}(x, y)$, il quale è calcolabile nel seguente modo:
$$
p_{pix} - p_{base}
$$
con 
$$
p_{pix} = a + (b-a)\frac{x}{resX}+(c-a)\frac{y}{resY} \tag{2}
$$
<img src="C:\Users\sgabu\AppData\Roaming\Typora\typora-user-images\image-20200517124410000.png" alt="image-20200517124410000" style="zoom: 67%;" />

L'espressione $\color{darkblue}(2)$ funziona poiché noi stiamo scalando il vettore $b-a$ per il valore risultante da $\frac{x}{resX}$, e ciò che otteniamo sarà il vettore $x-a$. Allo stesso modo otteniamo $y-a$. Infine sommiamo al punto $a$ i due vettori, e arriveremo al punto $p_{pix}$.

Anche se come abbiamo visto la semantica è diversa, le range scan condividono molti pregi e difetti dei campi di altezza. Hanno **connettività implicita**, memorizzano solo 1 coordinata su 3, sono **regolari**, sono adatti alla **multirisoluzione** e sono editabili come immagini 2D. Non sono adatte alla **risoluzione adattiva**. Sono anch'esse rappresentabili come **scala di grigi**.

| <img src="C:\Uni\CG\Appunti\Immagini_appunti\image-20200517135438998.png" style="zoom:80%;" /> | ![](C:\Uni\CG\Appunti\Immagini_appunti\image-20200517134234630.png) |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|    *La connettività implicita genera una triangular mesh*    |               *Scala di grigi per range scan*                |

Anche le range scan sono **modelli 2.5D**, in quanto catturano 'solo un lato' dell'oggetto. Infatti potremmo avere dei ==**depth jumps**==, cioè dei punti non memorizzati poiché coperti da qualcos'altro in primo piano. Non potendo accedere a quei punti della superficie dell'oggetto, non possiamo dire di trovarci di fronte a un modello 3D vero e proprio.

| <img src="C:\Uni\CG\Appunti\Immagini_appunti\image-20200517135151822.png" style="zoom: 50%;" /> |
| :----------------------------------------------------------: |
|                         *Depth jump*                         |

È però possibile ottenere un modello effettivamente 3D acquisendo un **set di range scan** dello stesso oggetto da angolazioni diverse. Spesso l'output di questo processo viene convertito facilmente in una **point cloud**, e usando quindi le sue tecniche di geometry processing, come il calcolo delle normali attraverso il *best fitting plane* e l'algoritmo ICP per l'allineamento di range scan diverse.

---

## Geometry Images

![](C:\Uni\CG\Appunti\Immagini_appunti\Annotazione 2020-05-17 144601.png)

```java
Point[][] geoImage = new Point[resX][resY]
```

Anche in questo caso abbiamo un array 2D per memorizzare il modello, ma stavolta i valori all'interno delle celle saranno le tre coordinate *(x, y, z)*. Ogni cella sarà quindi un punto 3D della mesh, mantenendo tutti i vantaggi delle strutture viste in precedenza riguardo la **regolarità**, la **connettività implicita**, il risparmio di memoria, la **multirisoluzione**, la capacità di compressione e l'**editabiltà** come immagine 2D etc.
Non solo, in questo caso ci troviamo di fronte alla geometria di un vero e proprio modello 3D, in quanto ciascun punto della superficie è identificato dalle 3 coordinate spaziali. 

Anche gli attributi possono essere memorizzati, semplicemente usando ulteriori griglie regolari con risoluzione pari o superiore a quella della matrice della geometria.   

| <img src="C:\Uni\CG\Appunti\Immagini_appunti\Annotazione 2020-05-17 145703.png" style="zoom:60%;" /> |
| :----------------------------------------------------------: |
| *La griglia che rappresenta la geometria ha risoluzione inferiore a quella che rappresenta le normali della mesh* |

Tutti questi vantaggi vengono a un costo molto oneroso. Infatti ciò che ci permette di convertire una normale mesh in una geometry image è un ==*UV-mapping* ancora più restrittivo di quello delle *texture*==, in quanto deve essere completo e senza isole. 