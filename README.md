# AngularJS explications

## scopes isolés

Lors de la déclaration de nouvelles directives, il est possible de définir un scope isolé, pour éviter des conflits ou des utilisations non désirées d'entités qui appartiennent au scope global dans lequel la directive a été appliquées.

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
