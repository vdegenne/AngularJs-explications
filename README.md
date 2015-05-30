# AngularJS explications

## scopes isol�s

Lors de la d�claration de nouvelles directives, il est possible de d�finir un scope isol�, pour �viter des conflits ou des utilisations non d�sir�es d'entit�s qui appartiennent au scope global dans lequel la directive a �t� appliqu�es.

Par exemple,

<code>
var app = angular.module('appTest', []);

app.controller('appCtrl', function ($scope) {
	$scope.beer = 'Goudale';
});

app.directive('myDirective', function () {
	return {
		scope: {}, /* beer is not accessible anymore in the directive, snif.. */
		template: '<p>{{ beer }}</p>' 
	}
});

<div ng-controller="appCtrl">
	<my-directive></my-directive>
</div>
</code>
