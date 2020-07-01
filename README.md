# IDAO

Ce sera le premier d'une série de tutoriels expliquant les bases de l'apprentissage automatique du point de vue d'un programmeur. Dans la partie 1, je montrerai comment vous pouvez intégrer l'apprentissage automatique de base dans un projet C ++ à l'aide de la bibliothèque GRT .

# Qu'est-ce que l'apprentissage automatique?
L'apprentissage automatique est une approche de l'informatique qui permet aux programmes de générer une sortie prévisible basée sur une entrée donnée sans utiliser de logique explicitement définie.
Par exemple, en utilisant la programmation basée sur la logique traditionnelle, nous pourrions écrire une fonction qui classe les fruits, qui prend la couleur et les dimensions en entrée et sort le nom du fruit. Quelque chose comme ça:

```**C++**
string classifyFruit (Color c, Dimensions d) 
{ 
    if (c.similar ({255, 165, 0})) // green 
    { 
        if (d.similar ({10, 9, 11})) // round-ish 
        { 
             retourner "Apple"; 
        } 
        else 
        { 
             return "Pear"; 
        } 
    } 
    if (c.similar ({255, 255, 0})) // yellow 
    { 
         return "Banana"; 
    } 
    if (c.similar ({255, 165, 0})) // orange 
    { 
         return "Orange"; 
    } 
    
    retourne "Inconnu"; 
}
```

On voit que cette approche pose toutes sortes de problèmes. Notre fonction ne connaît que quatre types de fruits, donc si nous voulons l'étendre pour classer également les clémentines, nous aurions besoin de déclarations supplémentaires les différenciant des oranges. Nous mélangerions également les poires et les pommes en fonction de la forme exacte du fruit et de la définition de notre similar()méthode. Pour créer une fonction qui classerait une large gamme de fruits avec un bon degré de précision, les choses deviendraient très complexes.
L'apprentissage automatique résout ce problème en représentant la relation entre l'entrée et la sortie sous forme d' état plutôt que par le biais de règles logiques. Cela signifie qu'au lieu de programmer notre classificateur à l'aide d' if / then / elseinstructions, nous pouvons utiliser des given / thenexemples pour exprimer notre intention. Ainsi, le code pour déterminer le comportement de notre fonction pourrait ressembler à ceci:
```C++
ml.given ({0, 255, 0, 10, 9, 11}) .then ("Apple"); 
ml.given ({0, 255, 0, 15, 7, 8}) .then ("Pear"); 
ml.given ({255, 255, 0, 20, 4, 4}) .then ("Banana"); 
ml.given ({255, 165, 0, 10, 10, 10}) .then ("Orange");
```
Ici, mlreprésente notre objet d'apprentissage automatique, l'argument de liste pour given()représenter les tuples de couleur et de dimension pour différents types de fruits et l'argument de chaîne pour then()représenter la sortie que nous attendons du classificateur étant donné l'entrée.
Si la prochaine étape de notre programme ressemblait à quelque chose, x = ml.classify({255, 165, 0, 10, 10, 10})nous nous attendrions à ce que la valeur de xsoit «Orange».
L'avantage de cette approche est que puisque nous avons découplé l' état de la logique , le processus de spécification des relations entrée / sortie peut être automatisé.
Quelque chose comme:

```C++
lignes automatiques = csv.read ();
pour (auto & r: lignes) 
{ 
    ml.given (r.colour, r.dimension) .then (r.name); 
}
```

Maintenant, nous pouvons ajouter autant de types de fruits que nous le souhaitons, ou donner de nombreux exemples différents du même type de fruits sans modifier notre code! La seule chose que nous devons changer est la taille et le contenu des données d'entrée, dans ce cas à partir d'un fichier CSV .
Le lecteur averti va maintenant penser: n'avons-nous pas juste créé une grande table de recherche? La réponse est «non», car cela ne classerait que les fruits qui correspondent exactement à la couleur et aux dimensions d'un exemple. Nous voulons plutôt que le système classe les nouveaux fruits qui portent le même nom que l'un de nos exemples mais avec des dimensions ou des couleurs variables. Donc, x = c.classify({245, 145, 0, 11, 9, 10})nous nous attendrions à ce que la sortie soit «Orange», même si la couleur et les dimensions ne correspondent pas exactement aux exemples que nous avons fournis dans nos given / thendéclarations.
Pour y parvenir, les systèmes d'apprentissage automatique utilisent un ensemble de paramètres statistiques qui définissent la probabilité d'une sortie donnée en fonction des entrées existantes. Chaque fois qu'un nouvel exemple est fourni, ces paramètres sont mis à jour pour rendre le système plus précis. C'est l'essence même de l'apprentissage automatique supervisé.

# Récapitulatif de la terminologie:

Dans l' apprentissage automatique supervisé, nous avons un ensemble d'exemples qui définissent la relation attendue entre l'entrée et la sortie du système. C'est ce qu'on appelle un ensemble de données .
Chaque ligne de l'ensemble de données est constituée d'une étiquette qui détermine la classe de l'entrée (par exemple «Apple») et d'un vecteur d'{245, 145, 0, 11, 9, 10} entités qui détermine ses propriétés (par exemple, contient des entités pour la couleur et les dimensions).
Notre objectif est de créer une représentation de la relation statistique entre les entrées (vecteurs de caractéristiques) et les sorties (étiquettes de classe). C'est ce qu'on appelle un modèle .
Pour ce faire, nous utilisons un système pour générer un modèle à partir de l' ensemble de données d'apprentissage et effectuer une classification à l'aide du modèle. C'est ce qu'on appelle un algorithme d' apprentissage automatique .
Ainsi, un algorithme d'apprentissage automatique est censé «apprendre» à partir d'un ensemble de données afin de générer un modèle qui peut être utilisé pour classer des vecteurs d'entités en entrée non présents dans l'ensemble de données d'origine.

# À quoi cela sert-il?

Il existe de nombreuses variétés d'apprentissage automatique. Dans cet article, je me concentre sur la classification supervisée. Classification supervisée est utile lorsque nous voulons construire un système qui peut classer automatiquement un certain nombre de distinctsclasses d'objets. Le mot «distinct» est important car les algorithmes de classification fonctionnent mieux lorsque les classes d'objets ont des caractéristiques distinctes (couleur, forme, taille, poids, etc.) qui les séparent les unes des autres. La classification fonctionne moins bien si nos objets sont assez similaires et ne peuvent être distingués que par des variations mineures de fonctionnalités. Donc, cela fonctionnerait bien pour quelque chose comme des fruits ou des visages humains (couleur des yeux, couleur des cheveux, poils du visage, forme du visage, etc.) différents, mais aurait du mal à identifier l'emplacement d'une personne en fonction de la température de l'air et de la couleur du ciel. Cette capacité à distinguer différents objets est appelée séparabilité et est un concept important dans l'apprentissage automatique. Une bonne règle de base est que si un humain avait du mal à distinguer différentes classes d'objets, une machine le ferait aussi.

# Comment intégrer l'apprentissage automatique dans mon projet C ++?

Nous avons maintenant une compréhension de base de l'apprentissage automatique, comment l'intégrer dans notre projet C ++? Une bonne bibliothèque pour cela est le Gesture Recognition Toolkit ou GRT. GRT a été développé pour la reconnaissance des gestes en temps réel et convient à une gamme de tâches d'apprentissage automatique. Il est disponible sur Windows, Mac et Linux, sous une licence MIT et peut donc être utilisé dans des projets open source ou fermés. La documentation complète de classe pour GRT peut être trouvée ici .
GRT se compose d'un certain nombre de classes C ++ qui implémentent chacune un algorithme d'apprentissage automatique spécifique. Presque toutes les classes GRT utilisent la convention suivante:

```C++
train(...)utilise les données d'entrée (...)pour former un nouveau modèle qui peut ensuite être utilisé pour la classification.
predict(...)utilise le vecteur d'entrée (...)et un modèle entraîné pour effectuer la classification.
getPredictedClassLabel() renvoie l'étiquette de classe prédite à partir du vecteur d'entrée
save(...) enregistre un modèle ou un ensemble de données dans un fichier.
load(...) charge un modèle ou un jeu de données pré-formé à partir d'un fichier.
clear() efface un objet ML, supprimant tous les modèles pré-formés
```

# Immeuble GRT

Les instructions suivantes sont spécifiques aux plaftorms Mac et Linux, les utilisateurs de Windows doivent se référer au guide de construction officiel .
Pour construire GRT, CMake est requis. CMake peut être installé à partir de la page du projet . Sur macOS, je recommande d'utiliser Homebrew pour installer CMake.
Une fois CMake installé, téléchargez GRT depuis le dépôt git:

```C++
$ git clone https://github.com/jamiebullock/grt
Ensuite:
$ cd grt / build 
$ mkdir tmp && cd tmp 
$ cmake .. -DBUILD_PYTHON_BINDING = OFF 
$ make
Si des erreurs de génération se produisent, elles peuvent être signalées sur le suivi des problèmes du projet . Sinon, la version peut être testée comme suit:
$ ./KNNExample ../../data/IrisData.grt
Cela devrait produire quelque chose de semblable au suivant:
[TRAINING KNN] Précision de l'ensemble d'entraînement: 97,5
TestSample: 0 ClassLabel: 3 PredictedClassLabel: 3
TestSample: 1 ClassLabel: 2 PredictedClassLabel: 2
...
TestSample: 29 ClassLabel: 2 PredictedClassLabel: 2
Précision du test: 93,3333%
```

Ceci exécute le code trouvé dans examples/ClassificationModulesExamples/KNNExample/KNNExample.cpp- un classifieur pour les fleurs d'Iris basé sur les données de l' ensemble de données Iris . Pour plus d'explications, voir ici .

### Apprentissage automatique Bonjour tout le monde!
Nous sommes maintenant prêts à construire un simple classificateur de fruits en utilisant GRT!
Nous devons d'abord créer un nouveau fichier source (appelons-le fruit.cpp) et inclure l'en-tête GRT. Il s'agit du seul en-tête nécessaire pour utiliser la plupart des fonctionnalités de la bibliothèque GRT.

```C++
#include "GRT.h" en 
utilisant l'espace de noms GRT;
typedef std :: vector <double> v_;
int main (int argc, const char * argv []) 
{ 
}
  ```
  
Ensuite, nous allons ajouter des données de notre ensemble de données de formation. Aux fins de cet exemple, nous utiliserons les mêmes «données sur les fruits» en haut de cet article. Pour ce faire, nous utilisons la classe GRT ClassificationData pour créer un ensemble de données simple. Au lieu de chaînes, dans l'apprentissage automatique, des étiquettes numériques sont utilisées, nous supposons ici un mappage de: 1 = pomme, 2 = poire, 3 = banane, 4 = orange. Nous ajoutons donc à notre main()fonction:

### Ensemble de données ClassificationData; 
```C++
dataset.setNumDimensions (6);
// Ajoutez 3 exemples pour chaque élément pour donner à notre classificateur suffisamment de données 
pour (int i = 0; i <3; ++ i) 
{ 
    dataset.addSample (1, v_ {0, 255, 0, 10, 9, 11} ); // Apple 
    dataset.addSample (2, v_ {0, 255, 0, 15, 7, 8}); // 
    Ensemble de données Pear.addSample (3, v_ {255, 255, 0, 20, 4, 4}); // Banana 
    dataset.addSample (4, v_ {255, 165, 0, 10, 10, 10}); // Orange 
}
```

Dans le code réel, nous ajouterions de nombreux autres exemples de formation afin que notre classificateur puisse bien généraliser à une variété d'entrées. Nous utiliserions également la loadDatasetFromCSVFile()méthode pour charger l'ensemble de données à partir d'un fichier afin que les données puissent être séparées de notre code. La documentation pour cela peut être trouvée ici .
Ensuite, nous chargeons notre ensemble de données et formons le classificateur. Ici, nous utilisons un KNNclassificateur, qui implémente l' algorithme k-NN , mais tout autre classificateur dans GRT fonctionnerait. Comme exercice, le lecteur est encouragé à essayer d'échanger différents classificateurs différents de GRT à la place de KNN.
// La classe de classification. Essayez aussi SVM! 
```C++
Classificateur KNN;
// Former notre classificateur 
classifier.train (jeu de données);        
C'est tout! Nous avons maintenant un modèle formé, utilisant l'algorithme k-NN basé sur l'ensemble de données d'entrée que nous avons fourni. Maintenant, pour tester le classificateur…
VectorFloat testVector = v_ {0, 255, 0, 10, 9, 11};
// Prédiction de la sortie basée sur testVector 
classifier.predict (testVector);
// Récupère l'étiquette 
auto classLabel = classifier. getPredictedClassLabel ();
std :: cout << "Label de classe:" << classLabel << std :: endl;
Enfin, pour démontrer que notre classifieur peut généraliser à des entrées non présentes dans l'ensemble de données d'origine, nous lui demanderons de classer un vecteur d'entité similaire, mais pas identique à l'un de nos exemples de formation.
VectorFloat differentVector = v_ {10, 240, 40, 8, 10, 9};
classifier.predict (differentVector); 
auto otherLabel = classifier.getPredictedClassLabel ();
std :: cout << "Autre étiquette:" << otherLabel << std :: endl;
Puisque notre differentVectorest le plus similaire à notre datasetvecteur pour une pomme, nous nous attendrions à ce que le programme sorte Other label: 1. Voyons si cela fonctionne!
Liste complète des codes
#include "GRT.h" en 
utilisant l'espace de noms GRT;
typedef std :: vector <double> v_;
int main (int argc, const char * argv []) 
{ 
    ClassificationData dataset; 
    dataset.setNumDimensions (6);
    // Ajoutez 3 exemples chacun pour donner à notre classificateur suffisamment de données 
    pour (int i = 0; i <3; ++ i) 
    { 
        // Apple 
        dataset.addSample (1, v_ {0, 255, 0, 10, 9, 11 }); 
        // 
        Ensemble de données Pear.addSample (2, v_ {0, 255, 0, 15, 7, 8}); 
        // Banana 
        dataset.addSample (3, v_ {255, 255, 0, 20, 4, 4}); 
        // 
        Ensemble de données orange.addSample (4, v_ {255, 165, 0, 10, 10, 10});   
    }
    // La classe de classification. Essayez également le 
    classificateur SVM KNN;
    // Former notre classificateur 
    classifier.train (jeu de données);
    // Créer un vecteur de test 
    VectorFloat testVector = v_ {0, 255, 0, 10, 9, 11};
    // Prédiction de la sortie basée sur testVector 
    classifier.predict (testVector);
    // Récupère l'étiquette 
    auto classLabel = classifier. getPredictedClassLabel ();
    std :: cout << "Label de classe:" << classLabel << std :: endl;
    // Essayez un vecteur d'entrée qui ne se trouve pas dans le jeu de données d'origine 
    VectorFloat differentVector = v_ {10, 240, 40, 8, 10, 9};
    classifier.predict (differentVector); 
    auto otherLabel = classifier.getPredictedClassLabel ();
    std :: cout << "Autre étiquette:" << otherLabel << std :: endl;
    retourner 0;
}
```
      
## Pour compiler le code sur macOS ou Linux, tapez ce qui suit (à partir du même tmprépertoire que nous avons créé au début du tutoriel).
```C++
g ++ -std = c ++ 14 fruit.cpp -ofruit -lgrt -L. -I ../../ GRT
```

Cela indique au compilateur de se lier libgrt.so dans le répertoire courant, avec les en-têtes ../../GRT, si vous déplacez GRT ailleurs, les arguments -Let -I devront être ajustés.

## Enfin, nous exécutons notre programme:

```C++
./fruit
```

Et la sortie devrait être:
```
Étiquette de classe: 1 
Autre étiquette: 1
```

Toutes nos félicitations! Vous venez d'écrire un classificateur à usage général dans 26 lignes de code C ++ 🤩. Si nous chargeions notre ensemble de données à partir d'un fichier CSV, ce serait beaucoup moins.
J'espère que ce tutoriel a été utile. Restez à l'écoute pour plus à l'avenir!
