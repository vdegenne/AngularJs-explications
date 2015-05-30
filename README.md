# AngularJS explications

Pour les exemples qui vont suivre, considérons le code d'initialisation suivant :

```javascript
/* le module pour nos tests */
var app = angular.module('appTest', []);

/* un controller par défaut où appCtrl désigne la fonction qui gère le controller 'appCtrl' */
app.controller('appCtrl', appCtrl);
```

## scopes isolés

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
