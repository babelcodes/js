# Conventions JavaScript

> Cette documentation a été mise à jour en janvier 2022, et se base donc sur la spécification ECMAScript ES2020 ou ES11, de juin 2020.

## 1/ Features ECMAScript

Il semble important pour le développeur JavaScript de connaitre les fonctionnalités offertes par le langage dans ses versions successives (le dernier update de la norme datant de 2020). Par exemple :

- [ECMAScript Features or Cheatsheet](https://github.com/sudheerj/ECMAScript-features)



### Destructuring

Les objets (et les tableaux) supportent le "destructuring", à savoir qu'il est possible d'etraire tout ou partie de leurs valeurs et de les assigner à des variables :

```javascript
// Tableau
const location = [10, 50];
const [x, y] = location; // Affectation de la 1ere valeur a x et de la seconde a y
console.log(x); // => 10
console.log(y); // => 50

// Objet
const action = {payload: 'Penser à rédiger la doc', type: 'todos/add'};
const {type, payload} = action; // Se base sur le nom des proprietes, donc l'ordre n'est pas important !!
console.log(type); // => 'todos/add'
console.log(payload); // => 'Penser à rédiger la doc'
```

Dans le cas d'un objet, il est possible de renommer la propriété :

```javascript
const action = {payload: 'Penser à rédiger la doc', type: 'todos/add'};
const {type: actionType} = action; // On recupere la propriete type de action mais on la stoque dans la constate actionType
console.log(actionType); // => 'todos/add'
console.log(type); // => Provoque une erreur ("Uncaught ReferenceError: b is not defined") car "type" n'est pas connu !
```



### Spread operator

Il permet d'itérer sur toutes les propriétés d'un objet (ou tableau), et de les affecter au nouvel objet, en surchargeant les éventuelles clefs existant déjà :

```javascript
const captainAmerica = {name: 'America', age: 33};
const captain = {...captainAmerica, name: 'Steve Rogers'};
console.log(captainAmerica) // => {name: 'America', age: 33}
console.log(captain) // => {name: 'Steve Rogers', age: 33}

captain.age = 42;
console.log(captainAmerica) // => {name: 'America', age: 33}
console.log(captain) // => {name: 'Steve Rogers', age: 42}
```

#### Attention

Ce sucre syntaxique ne copie que le premier niveau des propriétés. Si une propriété est elle même un objet elle est reprise (même référence) : si l'on modifie cette propriété dans l'original ou la copie, l'autre le sera aussi !

```javascript
const a1 = {b: {c: 1};
const a2 = {...a1};
const a2.b.c = 2;
console.log(a1) // => {b: {c: 2}
console.log(a2) // => {b: {c: 2}
```

En cas de doute, il est donc recommandé d'utiliser l'une ou l'autre de ces deux écritures :

- `const copy = JSON.parse(JSON.stringify(original));`
- `const copy = Object.assign({}, original); ` - Attention: le premier paramètre de `.assign()` est l'objet destitation, donc un objet vide par défaut, et si ce premier paramètre est oublié, alors c'est l'objet original qui est muté.



### Rest and Spread

Une propriété utile en JavaScript est le fait de pouvoir récupérer un groupe d'arguments restant dans la signature d'une fonction grâce à l'opérateur `...` :

```javascript
const aTail = (head, ...tail) => tail;
console.log(aTail(1, 2, 3)); // => [2, 3]
```

"rest" rassemble des éléments individuels ensemble dans un tableau, alors que l'opérateur "spread" fait le contraire : il ventile les éléments d'un tableau en éléments individuels :

```javascript
const shiftToLast = (head, ...tail) => [...tail, head];
console.log(shiftToLast(1, 2, 3)); // => [2, 3, 1]
```



### var, let, const

Selon l'un des principes fondateurs de la programmation fonctionnelle, qui l'est bon de respecter même d'en d'autres paradigmes, comme la programmation orientée objet, il est fortement recommandé de travailler avec des variables immutables :

- `const` permet justement de définir une constante qui ne pourra pas être ré-affectée (voir le Attention plus bas)
- `var` permet de définir une variable, modifiable, et de portée globale, soit la pire source de bug : **A PROSCRIRE**.
- `let` permet de définir une variable (re-affectable), dans un scope donné : à n'utiliser que quand cela est vraiment nécessaire, ce qui ne devrait pas arriver souvent
    - Par exemple, l'affectation d'une variable `let` via un `if/else` peut être remplacée par l'affectation d'une variable `const` via un opérateur ternaire

```javascript
// Version avec un let / if
let age = 0;
if ('America' === name) {
  age = 42;
} else {
  age = 33;
}

// Version avec un const / ?
const age2 = 'America' === name ? 42 : 33;
```

#### Motivations

- cela limite les effets de bord, et oblige le plus souvent à recret un nouvel objet
- cela facilite la relecture du code, donc sa compréhension, donc sa maintenance : quand on voit qu'une variable est `const`on sait que sa valeur ne changera pas dans le code

#### Attention

`const` garantit que la variable ne pourra pas être re-affectée, ce qui assure l'immutabilité pour des types primitifs ( `string`, `number`... ), par contre pour des tableaux ou des objets, "l'intérieur" peut être modifié.

```JavaScript
const age = 33;
age = 42; // PROVOQUE UNE ERREUR, car la variable ne peut pas etre re-affectee

const ages = [33, 42];
ages.push(100); // FONCTIONNE, bien que cela modifie le contenu de la variable
ages = [33, 42, 100]; // PROVOQUE UNE ERREUR, car la variable ne peut pas etre re-affectee

const captain = {age: 33};
captain.name = 'America'; // FONCTIONNE, bien que cela modifie le contenu de la variable
captain = {name: 'Haddock'}; // PROVOQUE UNE ERREUR, car la variable ne peut pas etre re-affectee
```

#### Convention

Bien que ca soit possible, il est donc recommandé de ne pas modifier l'intérieur d'un objet (ou d'un tableau) définit avec `const` mais à la place de créer un nouvel objet, éventuellement en utilisant le `spread operator` (voir plus haut).



### Egalités `===` vs. `==`

En JavaScript il existe 2 opérateurs de comparaison :

- `==` ou "égal" ou "double égal", qui vérifie que les valeurs sont les mêmes
- `===` ou "triple égal", qui lui vérifie en plus que les deux valeurs sont du même type

Il est fortement recommandé d'utiliser le "triple égal" `===`.

```javascript
console.log(42 == '42'); // => true
console.log(42 === '42'); // => false
```



## 2/ Array

- [Array - JavaScript - MDN Web Docs](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array)
- une grande partie du code consiste en la manipulation de tableaux
- il est recommandé d'utiliser le vanilla javascript
- sinon la librairie `lodash`

### Quelques propriétés

- la longueur d'un tableau n'est pas fixe, même quand le tableau est dans une constante : on peut ajouter ou supprimer des éléments
- le type des éléments d'un tableau n'est pas fixe : un tableau peut contenir des éléments de types différents

```javascript
// Créer un tableau
const fruits = ['Apple', 'Banana'];

// Créer un tableau de 5 éléments
console.log(Array.from({length: 5}));                  // => [ undefined, undefined, undefined, undefined, undefined ]
console.log(Array.from({length: 5}).map((v, i) => i)); // => [ 0, 1, 2, 3, 4 ]

// Obtenir la longueur d'un tableau
console.log(fruits.length); // => 2

// Renverser un tableau
console.log(fruits.reverse()); // => [ 'Banana', 'Apple' ] => Retourne un nouveau tableau, l'initial restant immuté

// Trier un tableau
console.log(['c', 'a', 'b'].sort()); // => [ 'a', 'b', 'c' ] => Retourne un nouveau tableau, l'initial restant immuté
console.log(['c', 'aaa', 'bb'].sort((a, b) => a.length - b.length)); // => [ 'c', 'bb', 'aaa' ] - Idem avec un callback

// Savoir si on manipule un tableau (méthode statique sur la classe Array)
Array.isArray([1, 2, 3]);   // true
Array.isArray({toto: 123}); // false
Array.isArray("tototruc");  // false
Array.isArray(undefined);   // false

// Coller les elements d'un tableau
const elements = ['Fire', 'Air', 'Water'];
console.log(elements.join());    // => "Fire,Air,Water"
console.log(elements.join(''));  // => FireAirWater"
console.log(elements.join('-')); // => "Fire-Air-Water"
```

### Fonctions pures ou non

Comme présenté plus dans la partie programmation objet, il est possible d'obtenir la liste des méthodes accessibles sur un tableau via :

```JavaScript
console.log(Array.prototype);
```

Voyons les principales méthodes de l'objet `Array` en restant vigilant sur lesquelles sont des fonctions pures qui ne mutent pas le tableau initial et lesquelles mutent le tableau initial...

- https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array

### `forEach` - boucler sans retour

La méthode `.forEach()`, qui ne retourne rien, permet de boucler sur tous les éléments d'un tableau en leur applicant une fonction passée en paramètre :

```javascript
fruits.forEach((item, index, array) => {
  console.log(item, index);
});
// => Apple 0
// => Banana 1
```

**Attention :**

En respectant le principe de fonction pure (qui ne modifie pas son environnement extérieur, mais se contente de prendre tout ce dont elle a besoin en paramètre et de retourner tout ce qu'elle produit ou modifie) et celui d'immutabilité des variables, cette méthode `.forEach()` doit très rarement être utilisée.

### Trouver un élément : [] - at() - includes() - indexOf() - lastIndexOf() - findIndex - find()

Il y a plusieurs moyen d'accéder à un élément d'un tableau en fonction du besoin exact. Toutes les méthodes que nous allons voir dans ce paragraphe laissent le tableau immuté / inchangé.

#### []

`array[index]` permet d'obtenir l'élément se trouvant à la position `index` :

```javascript
const first = fruits[0];                // => 'Apple'
const last = fruits[fruits.length - 1]; // => 'Banana'
const fromTheEnd = fruits.at(-1);       // => 'Banana'
```

#### at()

`array.at(index)` fonctionne comme `array[index]` ci-dessus, à la différence que si ìndex` est négatif, l'accès se fait en partant de la fin :

```javascript
const last = fruits[fruits.length - 1]; // => 'Banana'
const fromTheEnd = fruits.at(-1);       // => 'Banana'
```

Très prométeuse, cette méthode est expérimentale (pas disponible dans les anciennes versions et sans garantie pour la suite).

#### includes()

`array.includes(element)` permet de déterminer si un tableau contient une valeur et renvoie `true` si c'est le cas, `false` sinon :

```javascript
const hasBanana = fruits.includes('Banana'); // => true
```

#### indexOf()

`array.indexOf(element)` permet de retrouver l'index d'un élément d'un tableau :

```javascript
const position = ['Banana', 'Apple', 'Banana'].indexOf('Banana'); // => 0
```

#### lastIndexOf()

`array.lastIndexOf(element)` permet de retrouver le dernier index d'un élément d'un tableau (même fonctionnement que `.indexOf()` mais en partant de la fin du tableau) :

```javascript
const lastPosition = ['Banana', 'Apple', 'Banana'].lastIndexOf('Banana'); // => 2
```

#### findIndex()

`array.findIndex(callback)` renvoie l'**indice** du **premier élément** du tableau qui satisfait une condition donnée par une fonction callback. Si la fonction renvoie faux pour tous les éléments du tableau, le résultat vaut -1 :

```javascript
const position = fruits.findIndex((fruit) => fruit.startsWith('Ba')); // => 1
```

#### find()

`array.find(callback)` renvoie la **valeur** du **premier élément trouvé** dans le tableau qui respecte la condition donnée par la fonction callback de test passée en argument. Sinon, la valeur [`undefined`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/undefined) est renvoyée (même principe que `.findIndex()`mais retourne l'élément au lieu de retourner son index) :

```javascript
const firstBanana = fruits.find((fruit) => fruit.startsWith('Ba')); // => 'Banana'
```

### concat() vs. push()

Il y a deux principales méthodes pour ajouter des éléments dans un tableau :

- `.push(element)` qui ajoute l'élément souhaité à la fin du tableau (et donc le mute / modifie) et retourne la nouvelle longueur
    - `.unshift(element)` fait de même mais au début du tableau
- `.concat(valeur1, valeur2, valeur3)` qui ajoute les paramètre (tableau ou élément) à un nouveau tableau qui est retourné, laissé immuté / inchangé le tableau initial
    - voir [`Array.prototype.concat()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)
- `[element1, ...originalArray, element2]` qui utilise le "spread operator" pour extraire les éléments individuels du tableau initial (copie superficielle), on peut alors placer l'élément à ajouter avant ou après. On obtient également un nouveau tableau, le tableau initial étant immuté / inchangé.

**Il est donc demandé d'utiliser la méthode `concat()` ou le "spread operator".**

### filter() & find() vs. pop() or shift() or splice()

Pour supprimer un élément d'un tableau, là encore plusieurs stratégies existent :

- `.pop()` qui supprime le dernier élément du tableau (qui est donc muté / modifié) et retourne cet élément
    - `.shift()` fait de même mais avec le premier élément du tableau
    - `.splice(index, n)` supprime les `n` éléments du tableau (qui est donc muté / modifié) en commençant à la position `index` et retourne les éléments supprimés
- `.filter(callback)` utilise la fonction passée en paramètre, pour identifier les éléments à retourner (ceux pour lesquels la fonction en paramètre retourne `true`) : le tableau initial reste donc immuté / inchangé, et le retour de cette méthode est un nouveau tableau avec les éléments souhaités... Il suffit que la fonction retourne `false` pour les éléments que l'on souhaite supprimer du tableau initial pour que le tableau résultant convienne.
    - Voir [`Array.prototype.filter()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
    - `.find(callback)` fonctionne comme la méthode `.filter()` mais retourne le premier élément pour lequel la fonction en paramètre retourne `true` ou sinon `undefined` : bien que le tableau initial reste immuté / inchangé, il faut bien comprendre que `.find()` retourne un élément et non un tableau

**Il est donc recommandé d'utiliser la méthode `.filter()`.**

### every() et some()

Il est possible de vérifier une condition sur des éléments du tableau :

- si **au moins un** élément d'un tableau vérifie une condition (via une fonction callback)
    - `fruits.some((fruit) => fruit.startsWith('Ba'));`
- si **tous** les éléments d'un tableau vérifient une condition (via une fonction callback)
    - `fruits.every((fruit) => fruit.length > 3);`

### map()

`array.map(callback)` crée un nouveau tableau avec les résultats de l'appel d'une fonction callback fournie sur chaque élément du tableau appelant :

```javascript
const tags = fruits.map(fruit => fruit.toUpperCase()); // ['APPLE', 'BANANA']
```

Cette méthode s'appel sur un tableau d'éléments et applique à chacun la function passée en paramètre et retourne un nouveau tableau avec les éléments retournés par la fonction :

- le tableau initial est immuté / inchangé
- le tableau retourné contient autant d'éléments que le tableau initial

### reduce() & reduceRight()

- [`Array.prototype.reduce()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)

`array.reduce()` applique à chaque élément du tableau une fonction qui prend un « accumulateur » et (chaque) élément du tableau afin de la réduire à une seule valeur :

```javascript
const sum = (acc, value) => acc + value;
const total = [1, 2, 3].reduce(sum); // => 6
const total = [1, 2, 3].reduce(sum, 10); // => 10 + 6 = 16
```

Le second paramètre, est la valeur de départ de l'accumulateur (0 par défaut).

Nombre de personnes estiment que l'on peut tout faire avec un `.reduce()`, et notamment remplacer un `.filter()` ou un `.map()` : cette remarque ne vise surtout pas à inciter à le faire et donc rendre moins lisible le code, mais à faire comprendre la puissance du `.reduce()`.

`array.reduceRight()`  fonctionne sur le même principe mais en partant de la fin du tableau.

### flat() & flatMap

`.flat()` permet d'applatir un tableau de tableau, en pouvant préciser la profondeur de départ :

```javascript
[1, 2, [3, 4]].flat(); // => [1, 2, 3, 4]

[1, 2, [3, 4, [5, 6]]].flat(); // => [1, 2, 3, 4, [5, 6]]

[1, 2, [3, 4, [5, 6]]].flat(2); //  => [1, 2, 3, 4, 5, 6]
```

`flatMap()` permet d'appliquer une fonction à chaque élément du tableau puis d'aplatir le résultat en un tableau. Cela correspond à l'enchaînement de [`Array.prototype.map()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/map) suivi de [`Array.prototype.flat()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/flat) de profondeur 1. `flatMap` est plus efficace que la combinaison de ces deux opérations, souvent réalisées conjointement.

### entries(), keys(), values() et iterateurs

TODO

### Conclusion

Voici donc les fonctions à utiliser préférablement sans modération car se sont des fonctions pures sans effet de bord :

1. [`Array.prototype.at()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/at)
2. [`Array.prototype.concat()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/concat)
3. [`Array.prototype.entries()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/entries)
4. [`Array.prototype.every()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/every)
5. [`Array.prototype.filter()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)
6. [`Array.prototype.find()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/find)
7. [`Array.prototype.findIndex()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/findIndex)
8. [`Array.prototype.flat()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/flat)
9. [`Array.prototype.flatMap()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/flatMap)
10. [`Array.from()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/from)
11. [`Array.prototype.includes()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/includes)
12. [`Array.prototype.indexOf()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/indexOf)
13. [`Array.isArray()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/isArray)
14. [`Array.prototype.join()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/join)
15. [`Array.prototype.keys()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/keys)
16. [`Array.prototype.lastIndexOf()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/lastIndexOf)
17. [`Array.prototype.map()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/map)
18. [`Array.of()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/of)
19. [`Array.prototype.reduce()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce)
20. [`Array.prototype.reduceRight()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/ReduceRight)
21. [`Array.prototype.reverse()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/reverse)
22. [`Array.prototype.some()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/some)
23. [`Array.prototype.sort()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/sort)
24. [`Array.prototype.toLocaleString()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/toLocaleString)
25. [`Array.prototype.toSource()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/toSource)
26. [`Array.prototype.unshift()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/unshift)
27. [`Array.prototype.values()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/values)

Et celles qu'il est déconseillé d'utiliser car elles mutent le tableau initial :

1. [`Array.prototype.copyWithin()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/copyWithin)
2. [`Array.prototype.fill()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/fill)
3. [`Array.prototype.forEach()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)
4. [`Array.prototype.pop()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/pop)
5. [`Array.prototype.push()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/push)
6. [`Array.prototype.shift()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/shift)
7. [`Array.prototype.slice()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/slice)
8. [`Array.prototype.splice()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/splice)
9. [`Array.prototype.toString()`](https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Array/toString)




## 3/ Programmation objet: `class` vs. `prototype`

### Prototype

> Les prototypes sont un mécanisme au sein de JavaScript qui permettent aux objets JavaScript d'hériter des propriétés d'autres objets.

Ressources
- https://developer.mozilla.org/fr/docs/Learn/JavaScript/Objects/Object_prototypes
- https://developer.mozilla.org/fr/docs/Web/JavaScript/Inheritance_and_the_prototype_chain
- https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Global_Objects/Object/create

Il est recommandé de ne pas utiliser les prototypes pour mettre en place l'héritage objet.

Motivations :
- La notion de chaine de prototype est complexe à appréhender
- Du coup il est fort probable d'cérire du code avec des effets indésirable
- Et même quand on connait le sujet, définir une classe avec le mot clef `function` n'est pas intuitif en première lecture
- La contre-proposition d'utiliser le mot clef `class` est davantage compréhensible pour des personnes ayant simplement une connaissance de la programmation objet sans connaitre cette fonctionnalité propre à JavaScript

Par exemple, quel est le résultat des 4 dernières lignes ?

```JavaScript
function Person(name) {
	this.name = name;
};
const person = new Person('John DOE');

console.log(Person.prototype);
console.log(Person.__proto__);
console.log(person.prototype);
console.log(person.__proto__);
```

Ou encore, que donne le code suivant :

```JavaScript
function Foo() {};
Foo.prototype = {
  fooName: "T Foo"
};

function Bar() {}
const extendedFoo = Object.create(
  Foo.prototype,
  {
    barName: {
      value: "bar //"
    }
  }
);
Bar.prototype = extendedFoo;

const baBar = new Bar();
console.log(baBar.fooName);
console.log(baBar.barName);
```

Un exemple de code, chez nous, que l'on peut écrire avec les prototypes, difficile à assimiler tant pour un humain que pour un IDE et sa complétion :
- http://10.0.0.42/prestoplatform/spr/-/blob/master/common/PrestoSDK/P1000/P1000.js#L52



#### La ré-ouverture de classe

La seule exception acceptée pour utiliser les prototypes est la 'ré-ouverture' de classe
existantes, à savoir la possibilité d'ajouter des méthodes sur une classe existante 
pour `String` par exemple, sachant que cette pratique est fortement déconseillée.

Cela permet par exemple d'écrire le code suivant:

```JavaScript
console.log(0.5.hours().minutes);     // 1/2h === 30 minutes [sur la classe Number (0.5)]
console.log("1:10:20".hms().seconds); // 1h10m20s === (1 * 60 * 60) + (10 * 60) + 20 secondes [sur la classe String ("1:10:20")]
```

### class

- https://developer.mozilla.org/fr/docs/Web/JavaScript/Reference/Classes

A la place, il est donc recommandé d'utiliser le mot clef `class` pour définir 
des classes, afin de limiter les effets indésirables et faciliter la compréhension 
des personnes ayant seulement un bagage de programmation orientée objet.

```JavaScript
class Person {
	constructor(name) {
		this.name = name;
	}

	identity() {
		return `Name: ${this.name}`;
	}
};
class Colleague extends Person {
	constructor(name, manager) {
		super(name);
		this.manager = manager;
	}

	identity() {
		return super.identity() + ` [Manager: ${manager.identity() || '-'}]`;
	}
}

const bat = new Person('Bruce Wayne');
const aso = new Colleague('aso');
const jco = new Colleague('jco', aso);

console.log(aso.identity()); // => "Name: Bruce Wayne"
console.log(aso.identity()); // => "Name: aso [Manager: -]"
console.log(jco.identity()); // => "Name: jco [Manager: aso]"
```



## 4/ Method Chaining

TODO, p54

- pour remplacer les paramètres nommés
- Builder design pattern
- Fluent Interface design pattern



## 5/ Définition de fonctions

- a/ Utiliser le mot clé [const](https://www.w3schools.com/js/js_const.asp) lors de la définition de fonctions : elle doit être assignée à une variable de type `const`.
    - **Motivation** : Empêcher les redéfinition de fonctions
- b/ Définir les fonctions en appliquant le nommage optionnel.
    - **Motivation** : Permet de préserver une stack trace claire, les appels à la fonction sont ainsi bien visibles dans la stack trace
- c/ Utiliser le même nom pour la variable et le nom optionnel de la fonction
    - **Motivation** : Permet de garantir l'unicité dans le nommage et la cohérence entre la stack trace et la réalité du code

Exemple avant (dans le MS `antai-proxy` (app/routes/v1/cities/cases/router.js), doublon de fonction repéré ligne 9 et 59):
```js
async function putRecourseUpdate(req, res, next) {}
...
// fonction redéfinie dans le même fichier avec le même nom + signature
async function putRecourseUpdate(req, res, next) {}
 

// pas d'erreur au niveau JS
```

Exemple après:
```js
const putRecourseUpdate = async function putRecourseUpdate(req, res, next) {}
...
// fonction redéfinie avec 'const'
const putRecourseUpdate = async function putRecourseUpdate(req, res, next) {}
 

// ERREUR 'already declared'
```

### Paramètres (anonymes ou nommés)

Il y a deux façons de définir les paramètres dans une fonction :

- soit anonyme, et l'ordre et la présence compte
    - Exemple : `const checkCondition = (mustBeTrue, error) => { /* ... */ };`
    - Ce qui donne à l'appel : `checkCondition(!!id, "ID is required");`
- soit en nommant les paramètres, ce qui repose sur le "destructuring" vu plus haut, dans ce cas l'ordre et la présence ne sont pas important
    - Exemple : `const checkCondition = ({isTrue, error}) => { /* ... */ };
    - Ce qui donne à l'appel : `checkCondition({mustBeTrue: !!id, error: "ID is required"});`

La première façon, anonyme, est recommandée dans 2 cas :

- Lorsque les paramètre ne sont pas trop nombreux
- Lorsque les paramètres sont des paramètres obligatoires

La seconde façon, nommée, est recommandée dans deux cas (sachant qu'elle est plus sure mais plus verbeuse) :

- Lorsque les paramètres sont nombreux, ce qui est un anti-pattern
- Lorsque les paramètres sont des paramètres optionnels

Et il est autorisé de combiner les deux :

```javascript
const checkCondition = (mustBeTrue, error, {doesLog}) => { /* ... */ }; 
// La condition et le message d'erreur sont obligatoires
// L'option de savoir si on doit logger ou non est optionnelle (avec une valeur par défaut implicite a false par construction)
```

Il est possible d'attribuer une valeur par défaut aux paramètres, **et cela est imposé pour les paramètres nommés** pour éviter une erreur quand le programme cherchera à accéder à l'un des paramètres nommés :

```javascript
const checkCondition = (mustBeTrue, error = `Condition should be true!`, {doesLog} = {}) => { /* ... */ }; 
// La condition et le message d'erreur sont obligatoires
// L'option de savoir si on doit logger ou non est optionnelle (avec une valeur par défaut implicite a false par construction)
```



## 5/ Nomenclature

#### :new: IDs

Les variables représentant des ID sont soumises aux règles suivantes :
- L'ID d'un objet mongo s'appelle `_id` et est du type `ObjectId`
- Tout autre ID est préfixé par le nom de la ressource (ex: `eventId` ou `layerId`) et est forcément de type `string`
- Un ID est forcément un moyen unique d'identifier une ressource, sinon on parlera de code.
