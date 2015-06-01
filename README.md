# AngularJS explications

Pour les exemples qui vont suivre, considérons le code d'initialisation suivant :

```javascript
/* le module pour nos tests */
var app = angular.module('appTest', []);

/* un controller 'appCtrl' par défaut avec la fonction appCtrl associée */
app.controller('appCtrl', appCtrl);
```



## Chapitres
1. [Scopes isolés](#scopes-isolés)
2. [Transclude](#transclude)
3. [Propriété link d'une directive](#propriété-link-d-une-directive)
4. [Différences entre $observe et $watch](#différences-entre-$observe-et-$watch)
5. [testing (Protractor)](#testing-protractor)
6. [testing (Karma)](#testing-karma)



## Scopes isolés

Lors de la déclaration de nouvelles directives, il est possible de définir un scope isolé, pour éviter des conflits ou des utilisations non désirées d'entités qui appartiennent au scope global dans lequel la directive a été appliquées.

Par exemple,

```javascript
function appCtrl ($scope) { $scope.beer = 'Goudale'; }

app.directive('beerCenter', function () {
	return {
		scope: {}, // la bière n'est plus accessible ! snif !
		template: '<p>{{ beer }}</p>' 
	}
});
```

```html
<div ng-controller="appCtrl">
	<beer-center></beer-center> <!-- will show nothing -->
</div>
```

Cependant il est possible de passer des variables du scope courant dans le scope isolé de la directive

```javascript

app.directive('beerCenter', function () {
	return {
		scope: {
			beer: '=beerName'
			/* ici on vient de passer la valeur de l'attribut beerName (voir html)
			   a une nouvelle variable du scope isolé 'beer' */
		},
		template: '<p>{{ beer }}</p>' // ici 'beer' fait référence à 'beer' du scope isolé 
	}
});
```

```html
<div ng-controller="appCtrl">
	<beer-center beer-name='beer'></beer-name> <!-- affiche Goudale ! -->
</div>
```

Ce qui est important de voir c'est que l'attribut beer-name fait référence à la variable beer du controller et non a une chaine de caractère 'beer'. Cela veut aussi dire que l'on peut donc passé tout type d'objet contenu dans le controller.

```javascript
function appCtrl ($scope) {
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

C'est déjà plus parlant. À noter que dans l'objet scope de la directive j'ai écrit directement '=' car l'attribut porte le même nom que la variable du scope isolé qu'on initialise.
Il existe d'autres opérateurs pour associer des valeurs au scope isolé de la directive :

- '&' : permet d'associer une fonction du controller en permettant la spécification de ses attributs au moment de son invocation dans la directive.
```javascript
function appCtrl ($scope) {
	$scope.changeValue = function (a) {
		$scope.value = a;
	}

	$scope.$watch('value', function (n, o) {
		if (n !== o)
			alert('the value has changed to ' + $scope.value);
	});
}

app.directive('myDirective', function () { return {
	scope: {
		'BindFct': '&externalFct'
	},
	link: function (scope, element, attrs) {
		scope.fct({
			arg1: '"cheese"'
		});
	}
}})
```
```html
<my-directive external-fct="changeValue(arg1)"></my-directive>
```

- '@' : permet d'associer la valeur d'un attribut à une variable du scope
```javascript
return {
	scope: {
		inner: '@attributeName'
	},
	...
}
```
```html
<my-directive attribute-name="{{ externalValue }}"></my-directive>
<!-- chaque fois que 'attribute-name' change, scope.inner est mis à jour -->
```


## Transclude

En plus de pouvoir passer des modèles à une directive, il est possible de passer des templates.
```javascript
function appCtrl ($scope) {
	$scope.name = 'Goudale';
}

app.directive('beerCenter', function () {
	return {
		template: '#<span style="color:red;" ng-transclude></p>#',
		transclude: true,
	}
});
```
```html
<div ng-controller=appCtrl>
	<beer-center>this will produce {{ name }}.</beer-center>
	<!--
		affiche '#this will produce Goudale.#' en rouge
		entouré par des # en blanc.	
	-->
</div>
```
## Propriété link d'une directive

Une directive peut, si elle ne possède pas de scope isolé, avoir accès aux différents éléments (variables, fonctions) du scope dans lequel elle est utilisé. En revanche si un scope isolé est introduit, cet accès doit s'effectué par l'intermédiaire des attributs définis sur la directive. Mais les attributs doivent être utilisés pour personaliser le service qu'offre la directive. Si la directive représente un service, qu'il soit léger ou complexe, toute la logique des traitements doit être contenue dans la directive en elle-même et non dans le controller englobant.

Pour cela, la propriété link permet d'associer une fonction et ainsi d'encapsuler les traitements spécifiques à l'élement.

La syntaxe de la fonction **link** est la suivante :

```javascript
function link (scope, element, attributes) {
	
}
```

- **scope** représente soit le scope englobant, soit le scope isolé s'il a été défini.
- **element** représente l'élément sur lequel la directive s'applique; autrement dit l'élément du DOM.
	**element** est un objet jqLite (objet jQuery simplifié) et permet donc de manipuler le DOM de l'élément aisément. On peut donc par exemple utiliser .on() combiné aux évenement d'écoute d'Angular (e.g. element.on('$destroy', function () { ... })).
- **attributes** représente les attributs de l'élément.

Comme vu avec les scopes isolés, il est possible d'utiliser des variables du scope père. Cependant il est est aussi possible d'écouter tout changement de valeur d'attributs de la directive :

```javascript
attributes.$observe('attributeName', function (n, o) {
	scope.attributeName = attrs.attributeName;
})
```
Voir [exemples/chameleon.html](https://github.com/vdegenne/AngularJs-explications/blob/master/exemples/chameleon.html) pour plus de détails.

À noter que le code ci-dessus peut-être remplacé par la liaison,
```javascript
return {
	scope: {
		attributeName: '@attributeName'
	},
	...
}
```
À chaque changement de l'attribut, la variable de scope est mise à jour.




## Différences entre $observe et $watch

http://stackoverflow.com/a/30551084/773595


## ngRepeat et scopes

en réalité, 


## $destroy

AngularJs possède des évenements personalisés comme c'est le cas de $destroy


## Quelques notations intéressantes

On peut associer une fonction controller directement à une partie du DOM :

```javascript
function MyCtrl ($scope) {
	$scope.greets = 'yo';
}
```
```html
<div ng-controller=MyCtrl>{{ greets }}</div>
```

## testing (Protractor)

Protactor est surtout utilisé pour faire des tests d'interface. Protactor lance un navigateur et en fonction des instructions de tests, manipule l'application comme un réel utilisateur (pour tester la logique interne des applications, voir Karma, ci-dessous).

https://angular.github.io/protractor/#/

Étapes pour l'installation et la configuration

1. Installation de protractor

```npm install -g protractor```
2. mise à jour de webdriver-manager

```webdriver-manager update```
3. lancement du serveur qui controlera le navigateur

```webdriver-manager start```
4. création d'un fichier de test (avec code Jasmine)
5. création d'un fichier de configuration (*conf.js*)

```exports.config = {
  seleniumAddress: 'http://localhost:4444/wd/hub',
  specs: ['todo-spec.js']
};```
6. lancement du test

```protractor conf.js```

## testing (Karma)

Permet de pouvoir tester le code interne AngularJs de l'application.

http://karma-runner.github.io/0.12/index.html

git cloner le projet tuto d'angular phoneApp est un bon moyen de comprendre comment fonctionne les tests de modules avec Karma : 

[PhoneApp Page](https://docs.angularjs.org/tutorial/)
```git clone --depth=14 https://github.com/angular/angular-phonecat.git``` 

Un fichier de config karma.conf.js type est le suivant :

```javascript
module.exports = function (config) {
    config.set({

        basePath: '../',

        files: [
            'components/angular/angular.js',
            'components/angular-mocks/angular-mocks.js',
            'js/**/*.js',
            'test/unit/**/*.js'
        ],

        autoWatch: true,

        frameworks: ['jasmine'],

        browsers: ['Chrome'],

        plugins: [
            'karma-chrome-launcher',
            'karma-jasmine'
        ],

        junitReporter: {
            outputFile: 'test_out/unit.xml',
            suite: 'unit'
        }

    });
};
```

Il ne faut pas oublié d'inclure angular-mocks dans la liste des fichiers javascript de test. Sinon jasmine ne peut pas importer des modules.

le *package.json* donne la possibilité d'exécuter des scripts avec l'outil npm run,
```json
{
	"scripts": {
		"pretest" : "npm install",
		"test" : "node node_modules/karma/bin/karma start test/karma.conf.js"
	}
}
```
Si l'on exécute ```npm run test```, 'pretest' est lancé avant 'test' et a pour effet d'installer les dépendances du projet dans *node_modules*. Puis 'test' est lancé.