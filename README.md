# AngularJS explications

Pour les exemples qui vont suivre, consid�rons le code d'initialisation suivant :

```javascript
/* le module pour nos tests */
var app = angular.module('appTest', []);

/* un controller 'appCtrl' par d�faut avec la fonction appCtrl associ�e */
app.controller('appCtrl', appCtrl);
```



## Chapitres
1. [Scopes isol�s](#scopes-isol�s)
2. [Propri�t� link d'une directive](#propri�t�-link-d-une-directive)
3. [Diff�rences entre $observe et $watch](#diff�rences-entre-$observe-et-$watch)
5. [testing (Protractor)](#testing-protractor)

## Scopes isol�s

Lors de la d�claration de nouvelles directives, il est possible de d�finir un scope isol�, pour �viter des conflits ou des utilisations non d�sir�es d'entit�s qui appartiennent au scope global dans lequel la directive a �t� appliqu�es.

Par exemple,

```javascript
function appCtrl ($scope) { $scope.beer = 'Goudale'; }

app.directive('beerCenter', function () {
	return {
		scope: {}, // la bi�re n'est plus accessible ! snif !
		template: '<p>{{ beer }}</p>' 
	}
});
```

```html
<div ng-controller="appCtrl">
	<beer-center></beer-center> <!-- will show nothing -->
</div>
```

Cependant il est possible de passer des variables du scope courant dans le scope isol� de la directive

```javascript

app.directive('beerCenter', function () {
	return {
		scope: {
			beer: '=beerName'
			/* ici on vient de passer la valeur de l'attribut beerName (voir html)
			   a une nouvelle variable du scope isol� 'beer' */
		},
		template: '<p>{{ beer }}</p>' // ici 'beer' fait r�f�rence � 'beer' du scope isol� 
	}
});
```

```html
<div ng-controller="appCtrl">
	<beer-center beer-name='beer'></beer-name> <!-- affiche Goudale ! -->
</div>
```

Ce qui est important de voir c'est que l'attribut beer-name fait r�f�rence � la variable beer du controller et non a une chaine de caract�re 'beer'. Cela veut aussi dire que l'on peut donc pass� tout type d'objet contenu dans le controller.

```javascript
function appCtrl = function ($scope) {
	$scope.beer = {
		name: 'Goudale'
	}
}

app.directive('beerCenter', function () {
	return {
		scope: {
			beer: '='
		},
		template: '<p>{{ beer.name }}</p>'
	}
});
```
```html
<beer-center beer='beer'></beer-center> <!-- affiche Goudale ! -->
```

c'est d�j� plus parlant. � noter que dans l'objet scope de la directive j'ai �crit directement '=' car l'attribut porte le m�me nom que la variable du scope isol� qu'on initialise.


## Propri�t� link d'une directive

Une directive peut, si elle ne poss�de pas de scope isol�, avoir acc�s aux diff�rents �l�ments (variables, fonctions) du scope dans lequel elle est utilis�. En revanche si un scope isol� est introduit, cet acc�s doit s'effectu� par l'interm�diaire des attributs d�finis sur la directive. Mais les attributs doivent �tre utilis�s pour personaliser le service qu'offre la directive. Si la directive repr�sente un service, qu'il soit l�ger ou complexe, toute la logique des traitements doit �tre contenue dans la directive en elle-m�me et non dans le controller englobant.

Pour cela, la propri�t� link permet d'associer une fonction et ainsi d'encapsuler les traitements sp�cifiques � l'�lement.

La syntaxe de la fonction **link** est la suivante :

```javascript
function link (scope, element, attributes) {
	
}
```

- **scope** repr�sente soit le scope englobant, soit le scope isol� s'il a �t� d�fini.
- **element** repr�sente l'�l�ment sur lequel la directive s'applique; autrement dit l'�l�ment du DOM.
	**element** est un objet jqLite (objet jQuery simplifi�) et permet donc de manipuler le DOM de l'�l�ment ais�ment. On peut donc par exemple utiliser .on() combin� aux �venement d'�coute d'Angular (e.g. element.on('$destroy', function () { ... })).
- **attributes** repr�sente les attributs de l'�l�ment.

Comme vu avec les scopes isol�s, il est possible d'utiliser des variables du scope p�re. Cependant il est est aussi possible d'�couter tout changement de valeur d'attribut de la directive. (Voir exemples/chameleon.html)


## Diff�rences entre $observe et $watch

http://stackoverflow.com/a/30551084/773595


## ngRepeat et scopes

en r�alit�, 


## $destroy

AngularJs poss�de des �venements personalis�s comme c'est le cas de $destroy


## Quelques notations int�ressantes

On peut associer une fonction controller directement � une partie du DOM :

```javascript
function MyCtrl ($scope) {
	$scope.greets = 'yo';
}
```
```html
<div ng-controller=MyCtrl>{{ greets }}</div>
```

## testing (Protractor)

https://angular.github.io/protractor/#/

�tapes pour l'installation et la configuration

1. Installation de protractor

	```npm install -g protractor```
2. mise � jour de webdriver-manager

	```webdriver-manager update```
3. lancement du serveur qui controlera le navigateur

	```webdriver-manager start```
4. cr�ation d'un fichier de test (avec code Jasmine)
5. cr�ation d'un fichier de configuration (*conf.js*)

	```exports.config = {
	  seleniumAddress: 'http://localhost:4444/wd/hub',
	  specs: ['todo-spec.js']
	};```
6. lancement du test

	```protractor conf.js```