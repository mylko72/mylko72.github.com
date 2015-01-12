---
layout: post
title: "[예제] jQuery에서 $apply() 호출을 이용한 양뱡향 데이터 바인딩"
description: ""
category: AngularJS
tags: [$apply, jquery, 데이터바인딩]
---
{% include JB/setup %}

jQuery와 같은 3rd-party Library를 사용할 경우 jQuery의 이벤트 핸들러를 사용한다면 `$apply()`가 호출되지 않기 때문에 angular context 내부로 접근할 수가 없다.

즉, `$watch`를 통한 변경을 감지할 수 없게 되므로 실제로는 모델이 변경되었다 하더라도 뷰에 업데이트가 되지 않는다. 이럴 경우 직접 $apply()를 호출해줘야 한다.


[index.html]

	<!DOCTYPE html>
	<html ng-app="myApp">
	  <head>
		<meta charset="utf-8" />
		<title>AngularJS Plunker</title>
		<script>document.write('<base href="' + document.location + '" />');</script>
		<script src="http://ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js"></script>
		<script data-require="angular.js@1.3.x" src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.3.7/angular.js" data-semver="1.3.7"></script>
		<script src="app.js"></script>
	  </head>
	  <body ng-controller="MainCtrl">
		<div>{{text}}</div>
		<input id='btn1' type='button' value='AngularJS' ng-click='btnClick()' />
		<input id='btn2' type='button' value='jQuery' />
	  </body>

	</html>

'#btn1'은 `ng-click` 디렉티브를 이용하여 모델과 뷰가 바인딩되었기 때문에 양방향 데이터바인딩이 작동하는것을 확인할 수 있다. 하지만 아래 코드와 같이 '#btn2'는 jQuery로 `click`이벤트를 바인딩하였기 때문에 양방향 데이터바인딩이 안되는것을 확인 할 수 있다.

	app.controller('MainCtrl', function($scope) {
		$scope.text = "";
		$scope.btnClick = function(){
			$scope.text = "Hi AngularJS";   //변경 내용이 화면에 나타남
		};
		$("#btn2").click( function(){
			$scope.text = "Hi jQuery"; //변경 내용이 화면에 나타나지 않음
		});
	});

AngularJS에서는 모델값이 변경되면 자동으로 `$apply()` -> `$digest loop`가 호출되어 모델의 변경을 확인 후 뷰를 업데이트하지만 angular context 외부에서 변경(위의 예제처럼  jQuery를 이용한 click이벤트 바인딩의 경우)이 되었을 경우 직접 `$apply()`를 호출해줘야한다.

그러므로 아래 코드와 같이 `$apply()`를 호출함으로써 angular context 내부로 접근하게 하여 양방향 데이터 바인딩이 가능해진다.

`$apply()`는 3rd-party Library를 이용해 데이터바인딩을 구현하기 위한 함수로 순수 angularJS내에서 $apply()를 사용하면 동작하지 않는다

[app.js]

	var app = angular.module('myApp', []);

	app.controller('MainCtrl', function($scope) {
		$scope.text = "";
		$scope.btnClick = function(){
			$scope.text = "Hi AngularJS";   //변경 내용이 화면에 나타남
		};
		$('#btn2').click(function(){
			$scope.$apply(function(){
				$scope.text = "Hi jQuery"; // 수동으로 $apply를 호출하여 angular context로 접근하며 양방향 데이터 바인딩이 이루어짐.
			});
		});
	});


>##예제보기
>[http://embed.plnkr.co/lHt8gZvTTTLdydliWH2X/preview](http://embed.plnkr.co/lHt8gZvTTTLdydliWH2X/preview)
