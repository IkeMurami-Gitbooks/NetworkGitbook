---
description: Единственный протокол динамической маршрутизации
---

# BGP

## Practical BGP

AS - **autonomous system —** набор маршрутизаторов, имеющих единые правила маршрутизации, управляемых одной технической администрацией и работающих на одном из протоколов IGP (для внутренней маршрутизации AS может использовать и несколько IGP).

Есть инстурменты типа "looking glass", которые предоставляют возможность  запустить ping или traceroute из их подсети к вашей, обычно этого достаточно для отладки. Для BGP подобные сервисы предоставляют: Level3, Telia, Hurricane Electric и другие.

На примере Hurricane Electric: у них вообще поднят telnet сервер для этого, что оч удобно:

```
$ telnet route-server.he.net
...
Лучший маршрут до хоста через AS
route-server.he.net> show ip bgp 178.248.232.27 bestpath
...
 3491 197068 197068 197068
 ....
 
Все маршруты на текущий момент:
route-server.he.net> show ip bgp 178.248.232.27

Потом на https://bgp.he.net/AS197068 можем узнать информацию о AS
```

Как минимум здесь красивый график

![](<../.gitbook/assets/изображение (3).png>)

Можно предположить какими сетями пользуется Заказчик. Можно получить подсети, которые ходят через конкретный AS(?)&#x20;

![](<../.gitbook/assets/изображение (4).png>)

## Links

[https://xakep.ru/2019/09/27/bgp/](https://xakep.ru/2019/09/27/bgp/)\
[https://tools.ietf.org/html/rfc4272](https://tools.ietf.org/html/rfc4272)\
[https://xakep.ru/2020/04/10/bgp-route-leaks/](https://xakep.ru/2020/04/10/bgp-route-leaks/)\
[https://blog.catchpoint.com/2019/10/25/vulnerabilities-of-bgp/](https://blog.catchpoint.com/2019/10/25/vulnerabilities-of-bgp/)\
[http://xgu.ru/wiki/BGP](http://xgu.ru/wiki/BGP)\
[https://www.cisco.com/c/ru\_ru/support/docs/ip/border-gateway-protocol-bgp/26634-bgp-toc.html](https://www.cisco.com/c/ru\_ru/support/docs/ip/border-gateway-protocol-bgp/26634-bgp-toc.html)\
[https://habr.com/ru/post/450814/](https://habr.com/ru/post/450814/)\


## Unknown tools

есть вот такой инструмент, для чего — не успел разобрать: [https://github.com/smartbgp/yabgp](https://github.com/smartbgp/yabgp)

## Vulnerabilities

BGP Route Leak

BGP Hijacking

Вот такой комментарий от другого специалиста: "Как правило пиринг настроен с аутентификацией. Мне кажется это безнадежный вектор. Если есть что-то еще, лучше переключиться."

## Mitigations

1. **Setting MAXPREF**: [AS1273](https://www.peeringdb.com/asn/1273) (also registered to Vodafone) and [AS9498](https://www.peeringdb.com/net/1903) (registered to Bharti Airtel Ltd.) could have set a [MAXPREF](https://www.cisco.com/c/en/us/support/docs/ip/border-gateway-protocol-bgp/25160-bgp-maximum-prefix.html) on their connection to AS55410. This is a simple mechanism that automatically disables a BGP connection when a downstream network suddenly starts sending an unexpectedly large number of BGP routes.\
   &#x20;&#x20;
2. **Configuring filters:** Using internet routing registry data, AS1273 and AS9498 could have been filtering the routes accepted from AS55410 knowing that they shouldn't receive US BGP routes, for example.\
   &#x20;&#x20;
3. **Deploying RPKI**: Leaked routes with Route Origin Authorizations (ROAs) configured in the [RPKI](https://help.apnic.net/s/article/Resource-Public-Key-Infrastructure-RPKI) should have been dropped.
