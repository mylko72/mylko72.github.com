---
layout: post
title: "$apply() 호출을 이용한 양뱡향 데이터 바인딩"
description: ""
category: 
tags: [$apply, jquery, 데이터바인딩]
---
{% include JB/setup %}

jQuery와 같은 3rd-party Library를 사용할 경우 jQuery의 이벤트 핸들러를 사용한다면 $apply()가 호출되지 않기 때문에 angular context 내부로 접근할 수가 없다.

즉, $watch를 통한 변경을 감지할 수 없게 되므로 실제로는 모델이 변경되었다 하더라도 뷰에 업데이트가 되지 않는다. 이럴 경우 직접 $apply()를 호출해줘야 한다.
$apply()를 호출함으로써 angular context 내부로 접근하게 하여 양방향 데이터 바인딩이 가능해진다.
$apply()는 3rd-party Library를 이용해 데이터바인딩을 구현하기 위한 함수로 순수 angularJS내에서 $apply()를 사용하면 동작하지 않는다.

[예제보기](http://embed.plnkr.co/lHt8gZvTTTLdydliWH2X/preview)
