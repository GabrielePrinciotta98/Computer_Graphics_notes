# Mesh Poliedrali

## Descrizione generale

Le **mesh poliedrali** sono l'estensione volumetrica delle mesh poligonali. Infatti invece di modellare una superficie con poligoni attaccati lato a lato, ==modelliamo un volume con poliedri attaccati faccia a faccia.== 
Tipicamente i poliedri sono i *tetraedri*, ottenendo una ***tetra-mesh***, oppure gli *esaedri*, ottenendo una ***hexa-mesh***.

<img src="C:\Uni\CG\Appunti\Immagini_appunti\Annotazione 2020-05-22 112642.png" style="zoom:60%;" />



Le **mesh poliedrali** saranno composte in modo simile alle mesh:

* **geometria**:
  * vertici (x, y, z)
* **connettività**:
  * poliedri (3D) ✨​
  * facce (2D)
  * edge (1D)
* **attributi**:
  * sui vertici 
  * interpolati dentro gli elementi

Data questa descrizione, è facile capire che anche i dati in memoria saranno rappresentati in modo simile alle mesh poligonali. Infatti anche in questo caso avremo una lista di vertici, ma ==invece di una lista facce avremo una lista di poliedri==. 
Esiste anche una variante basata sugli *half-edge*.

| <img src="C:\Uni\CG\Appunti\Immagini_appunti\hexalab.png" style="zoom: 50%;" /> |
| :----------------------------------------------------------: |
|                   *hexa-mesh di una sfera*                   |

---

## Caratteristiche

### Risoluzione

* La risoluzione è data dal numero di poliedri.
* È possibile avere **risoluzione adattiva**, anzi spesso si usa per alleggerire il modello. 
* Permette un approccio **multirisoluzione**.

### Regolarità

Esiste un concetto di **regolarità** anche per le mesh poliedrali, ma invece di essere legato alla **valenza** di un vertice, è rispetto agli edge:

> Un edge di una hexa-mesh è regolare se e solo se è condiviso da 4 esaedri.

> Uh edge di una tetra-mesh è regolare se e solo se è condiviso da 6 tetraedri.

---

## Applicazioni

In genere, questi tipi di modello risultano troppo pesanti per essere usati in applicazioni reali. ==L'unico campo in cui vengono utilizzate volentieri sono le **simulazioni fisiche (FEM - Finite Element Method)**==. Infatti, il fatto di modellare anche l'interno dell'oggetto in modo abbastanza "preciso", risulta utile per verificare virtualmente le proprietà strutturali di un oggetto.

<img src="C:\Uni\CG\Appunti\Immagini_appunti\Annotazione 2020-05-22 115625.png" style="zoom:60%;" />



Un problema difficile è costruire una "buona "mesh poliedrale a partire da una mesh poligonale:



<img src="C:\Uni\CG\Appunti\Immagini_appunti\Annotazione 2020-05-22 115902.png" style="zoom: 50%;" />

Ovviamente la mesh deve essere 2-manifold, chiusa e ben orientata. .

---

## Tetra-Mesh

Le tetra-mesh sono costituite da **tetraedri**, che sono la struttura *simpliciale* del volume, così come i triangoli sono la struttura simpliciale del piano.

> Un **tetraedro** è il luogo dei punti che sono l'interpolazione lineare fra i suoi quattro vertici.

In altre parole, ogni punto all'interno di un tetraedro può essere calcolato con una combinazione lineare dei quattro vertici, e i pesi di tale combinazione sono le **coordinate baricentriche** del punto. Questo meccanismo analogo a quello visto per le tri-mesh, ci consente anche qui l'interpolazione degli attributi. 

Inoltre, proprio come ogni poligono può essere scomposto in triangoli, ==ogni poliedro può essere scomposto in tetraedri==:

<img src="C:\Uni\CG\Appunti\Immagini_appunti\Annotazione 2020-05-22 122126.png" style="zoom: 50%;" />



