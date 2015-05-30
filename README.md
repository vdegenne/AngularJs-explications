# AngularJS explications

Pour les exemples qui vont suivre, consid�rons le code d'initialisation suivant :

```javascript
/* le module pour nos tests */
var app = angular.module('appTest', []);

/* un controller par d�faut o� appCtrl d�signe la fonction qui g�re le controller 'appCtrl' */
app.controller('appCtrl', appCtrl);
```

## scopes isol�s

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
