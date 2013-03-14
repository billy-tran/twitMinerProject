Projet BDA : TwitMiner
======================

Objectif :
----------

Extraire les règles d'association à partir de données extraites de
Twitter. Cette année, au lieu de ne s'intérésser qu'au *"Trends"*,
nous allons essayer d'extraire des connaissances directement à partir 
des tweets venant du monde entier.

Vous pouvez poser vos questions soit par mail à l’adresse
[sebastien.nedjar@univ-amu.fr](mailto:sebastien.nedjar@univ-amu.fr) en
faisant figurer [Projet_BDA] au début du sujet du message, soit
directement par Twitter (@nedseb) en utilisant le hashtag #TwitMiner
pour que tout le monde puisse bénéficier de la réponse.

À chaque fin du projet (voir ci-dessous pour les dates) vous devrez
envoyer une archive au format zip contenant le travail réalisé ainsi
qu'un fichier nommé "README.txt" expliquant clairement (10-15 lignes
environs) ce que vous avez fait ainsi que la procédure à suivre pour
tester votre travail. Le sujet du mail devra avoir le format suivant :
 `[Projet_BDA] Rendu Phase X: Nom1, Nom2`

Travail à réaliser :
--------------------

### Phase 0 : Récupération des données

Le travail à réaliser durant cette phase consiste à se constituer un jeu
de données qui sera étudié dans les phases suivantes. Sur Twitter, les 
utilisateurs échanges des messages soit pour discuter soit pour échanger 
des informations. En extrayant des règles d'association à partir de ces données, 
on cherche à comprendre quels sont les liens entre les mots apparaissant simultanément 
dans des tweets que l'on aura transformé en transactions. 

**Le travail à réaliser durant cette phase est le suivant** :

-   Créer un compte Twitter et enregistrer une application
-   Faire un programme (en Java vous pouvez utiliser la bibliothèque
    [Twitter4J](http://twitter4j.org/en/index.html) ) qui se connecte à
    Twitter et qui récupère tous les tweets sur un sujet choisi. 
-   Stocker les résultats dans un fichier au format
    [CSV](http://fr.wikipedia.org/wiki/Comma-separated_values). Le pays, 
    la date et le pseudo de l'utilisateur ayant envoyé le tweet pouvant 
    jouer un rôle dans l'analyse, chaque transaction devra contenir un champ 
    date, pays et pseudo. Pour que notre analyse ne tienne pas compte de l'ordre 
    des différents mots dans une transaction, vous pouvez les trier par ordre alphabétique.

Voici un exemple de ce à quoi votre fichier csv pourrait ressembler :

```
01/02/2012 10:20 UTC; Australia; #tigerblood; #tilltheworldends; #winning; ...
01/02/2012 10:20 UTC; Monde; #blackpeoplemovies; #tigerblood; #tilltheworldends; ...
01/02/2012 10:20 UTC; France; #ff; #plusbelleladroite; #vasescommunicants; 3DS ...
01/02/2012 10:20 UTC; Canada; #blackpeoplemovies; #tigerblood; #tilltheworldends ...
01/02/2012 10:20 UTC; United Kingdom; #barnsley; #blackpeoplemovies; #tigerblood ...
01/02/2012 10:20 UTC; United States; #blackpeoplemovies; #tigerblood; #tilltheworldends;  ...
01/02/2012 10:30 UTC; Australia; #tigerblood; #tilltheworldends; #winning; Bear Grylls;  ...
01/02/2012 10:30 UTC; Monde; #blackpeoplemovies; #tigerblood; #tilltheworldends;  ...
01/02/2012 10:30 UTC; France; #ff; #plusbelleladroite; #vasescommunicants; 3DS;  ...
01/02/2012 10:30 UTC; Canada; #blackpeoplemovies; #tigerblood; #tilltheworldends; ...
01/02/2012 10:30 UTC; United Kingdom; #barnsley; #blackpeoplemovies; #tigerblood; ...
01/02/2012 10:30 UTC; United States; #blackpeoplemovies; #tigerblood; #tilltheworldends; ...
01/02/2012 10:40 UTC; Australia; #tigerblood; #tilltheworldends; #winning; Bear Grylls; ...
...                 `
```

Votre fichier csv devra contenir au minimum 10 000 transactions.

### Phase 1 : Extraction des motifs fréquents

Dans le cours de FED, vous avez vu que la découverte des motifs
fréquents (c'est-à-dire des ensembles d'items apparaissant ensemble avec
une fréquence supérieure à un seuil MinFreq donné par l'utilisateur)
constitue l'étape principale de l’extraction de règles d’association.
L'objectif de cette phase est d'utiliser une implémentation de
l'algorithme Apriori pour calculer l'ensemble des motifs fréquents du
jeu de données extrait précédemment.

**Rappel des définitions de base:**

-   I = {i1, i2, …, im} l’ensemble des items apparaissant dans la base
    de données non structurée (ou base de données transactionnelles) D.
-   Un motif M est un sous-ensemble d'items : M ⊆ I.
-   Une transaction T ∈ D est un motif appartenant à D (une ligne de D).
-   Support d'un d'un motif X : \
     Supp(X, D) = Nombre de transaction de D contenant X = |{T ∈ D tel
    que X ⊆ T }|
-   Fréquence d'un d'un motif X :\
     Freq(X, D) = (Nombre de transaction de D contenant X) / (Nombre
    transaction de D) = |{T ∈ D tel que X ⊆ T }|/|D| = Supp(X,
    D)/Supp(∅, D)

L'implémentation de l'algorithme Apriori choisie
([apriori.tar.bz2](https://raw.github.com/IUTInfoAix/twitMiner/master/apriori.tar.bz2)) 
utilise un format de fichier de données différent du notre. Une partie du travail à
réaliser consistera donc à écrire un programme de conversion entre les
deux formats. L'algorithme prend en entrée un fichier texte où chaque
ligne constitue une transaction. Chaque transaction est constituée
d'items (codés par des entiers) séparés par des espaces. Ci-dessous le
fichier exemple `test.trans` contenant trois transactions dans ce format
:

```
1 2 3 4
2 3
3 4 5
```

L'exécution de la commande `./apriori test.trans 2 test.out` calcul tous
les motifs fréquents du fichier `test.trans` ayant un support supérieur
à 2 (fréquence supérieure à 2/3) et écrit le résultat dans le fichier
`test.out`. Le contenu de ce fichier est le suivant :

```
(3)
2 (2)
3 (3)
4 (2)
2 3 (2)
3 4 (2)                 `
```
La première ligne de ce fichier indique que le motif vide (∅) a un
support de 3. C'est à dire qu'il y a 3 lignes du fichier `test.trans`
qui contiennent ce motif. Les autres lignes correspondent aux autres
motifs fréquents, elle se lisent de la même façon.

**Le travail à réaliser durant cette phase est le suivant :**

-   Télécharger l'archive suivante
    https://raw.github.com/IUTInfoAix/twitMiner/master/apriori.tar.bz2.
    La décompresser et importer le contenu dans un nouveau projet sous
    Eclipse.
-   Faire un programme permettant de transformer votre fichier "csv" en
    un fichier "trans".
-   Faire un programme permettant de transformer le fichier "trans" ou
    le fichier "out" en un fichier "csv".
-   En veillant à choisir un seuil pertinent (ni trop haut sous peine
    d'avoir aucun résultat, ni trop bas sous peine d'avoir trop de
    résultats), calculer l'ensemble des motifs fréquents de votre jeu de
    données.

Vous devrez rendre les sources des programmes de transcodage et le fichier
contenant les motifs fréquents. Ce fichier résultat devra faire moins de
1Mo (s'il fait plus tronquez le).

### Phase 2 : Extraction des règles d'association

L'ensemble des motifs fréquents étant maintenant calculable, il reste à
écrire le programme extrayant les règles d'association à partir du
résultat de l'algorithme Apriori.

**Rappel des définitions de base:**

-   Une règle d’association X → Y où : X ⊆ I, Y ⊆ I et X ∩ Y = ∅.
-   Fréquence d'une règle d’association : Freq(X → Y, D) = Freq(X ∪ Y,
    D)
-   Confiance d'une règle d’association : Conf(X → Y, D) = Freq(X ∪ Y,
    D)/Freq(X, D)

La fréquence et la confiance sont utilisées pour identifier les règles «
intéressantes ». L'utilisateur doit définir deux seuils MinFreq et
MinConf à partir desquels une règle X → Y sera considérée comme «
intéressantes » si et seulement si Freq(X → Y, D) ≥ MinFreq et Conf(X →
Y, D) ≥ MinConf. La génération des règles se fait donc à partir des
motifs (itemsets) fréquents (précédemment généré). Le principe de cette
génération est le suivant :

1.  Pour chaque itemset (motif) fréquent Y, trouver tous les
    sous-ensembles non vides de Y
2.  Pour chacun de ces sous-ensemble X, produire la règle X → (Y − X) si
    Conf(X → (Y − X), D) ≥ MinConf

**Le travail à réaliser durant cette phase est le suivant :**

-   Écrire le programme prenant en entrée le fichier "out" (le résultat
    d'Apriori) et donnant en sortie un fichier contenant toutes les
    règles d'association ayant une confiance supérieure au seuil donné
    par l'utilisateur (MinConf).
-   Sélectionner 10 règles d'association qui semblent pertinentes et
    essayer de les expliquer (2 phrases max) par rapport au contexte dans 
    lequel les données ont été récupérée.

Les résultats sont à envoyer par mail le 11/04. L'archive devra contenir
les sources des programmes d'extraction des règles d'association. Votre
fichier résultat devra faire moins de 1Mo (s'il fait plus tronquez le).

### Phase 3 : Nettoyage des données 

Lors de la phase précédente, vous avez généré un très grand nombre de
règles d'association. Certaines d'entre elles étaient pertinente
d'autres apportaient peu d'informations. L'objectif de cette phase est
double, tout d'abord faire un nettoyage du fichier de données afin d'en
améliorer sa qualité et ainsi obtenir des résultats plus précis. Puis
faire le nettoyage de l'ensemble des règles d'association pour éliminer
une partie des règles n'apportant peu (ou pas du tout) de connaissances
nouvelles.

**Nettoyage du fichier de données :**
 La méthode de nettoyage utilisée est relativement simple mais permet
d'éliminer la redondance introduite par les les mots de liaison. En effet, 
les mots comme les articles, pronoms, etc... sont peu porteurs de sens pour 
l'analyse Des régles d'association. 

Dans cette étape, vous devez tout d'abord établir un dictionnaire des mots sans
valeur sémantique que vous appellerez `motinutiles.txt`. À partir de ce fichier, 
vous devrez reprendre votre fichier d'origine pour éliminer des transactions tous 
les mots sans valeur.

**Nettoyage de l'ensemble des règles d'association :**\
 Malgré l'amélioration de la qualité des règles qu'a apporté le
nettoyage du fichier de données, il reste encore beaucoup de règles qui
apportent peu d'information nouvelle à l'utilisateur. Par exemple
supposons que nous rencontrions les règles AB → CDEF et AB → CD avec
Freq(AB → CDEF) = Freq(AB → CD) (cela implique aussi que Conf(AB → CDEF)
= Conf(AB → CD) ). La seconde règle ne nous apprend rien de plus (même
moins) que la première. Les règles de ce type, nommées règles *non max*,
peuvent être supprimées du résultat final sans perte d'information.

Il existe un second type de règle que l'on peut éliminer, ce sont les
règles dites *non min*. Cette fois ci l'objectif est de garder
prioritairement les règles ayant la plus petite partie gauche. Par
exemple si les règles AB → CD et A → BCD ont la même confiance (ce qui
implique Freq(AB) = Freq(A)) alors elles sont porteuses de la même
sémantique mais la seconde est plus simple à interpréter. Ainsi les
règles *non min* peuvent être supprimer sans perte d'information.

**Résumé des règles de nettoyage :**\
 Soit X et Y deux motifs fréquents tels que X ⊂ Y et X → (Y − X) une RA
valide (conf(X → (Y − X)) ≥ minConf)

-   Si il existe Y' ⊂ Y tel que X ⊂ Y' et Freq(Y') = Freq(Y) \
     alors X → (Y' − X) est *non max*.
-   Si il existe X' ⊂ X tel que Freq(X') = Freq(X) \
     alors X → (Y − X) est *non min*.

Les règles restantes sont dites *min-max*, ce sont celles qui sont
porteuses de la plus forte sémantique. Pour les étudiants qui le
souhaite, je peux vous envoyer un petit script *php* (shame on me) pour
afficher vos règles et les manipuler relativement simplement.

**Le travail à réaliser durant cette phase est le suivant :**

-   Ecrire le dictionnaire `motinutiles.txt`.
-   Écrire le programme de nettoyage du fichier d'entrée à partir d'un
    dictionnaire des mots sans valeur sémantique.
-   En repartant de votre programme de génération des règles
    d'association de la phase 2, écrire un programme d'élimination des
    règles d'association *non min* et *non max*.
-   Vérifier si les 10 règles sélectionnées lors de la phase précédente
    sont toujours présente dans votre résultat et si leur confiance n'a
    pas était modifiée.

Votre travail doit être envoyé par mail le 11/04 (pas de retard possible).

### Présentation du travail réalisé

Les présentations de vos projets auront lieu le 11/04 et le 12/04 en salle machine
selon un planning qui sera affiché avant le lundi 2 Avril. La soutenance
durera 10 minutes par projet. Vous devrez dans un premier temps
expliquer le travail réalisé puis dans un second temps répondre à des
questions liées à votre travail et à vos résultats.
