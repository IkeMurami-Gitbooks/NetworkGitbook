---
description: Quick UDP Internet Connections
---

# QUIC

Клиентская часть протокола доступна по адресу ([Git](https://ru.wikipedia.org/wiki/Git)): [https://chromium.googlesource.com/chromium/src/net/+/master/quic/](https://chromium.googlesource.com/chromium/src/net/+/master/quic/) и [https://src.chromium.org/chrome/trunk/src/net/quic/](https://src.chromium.org/chrome/trunk/src/net/quic/)

Экспериментальный сервер с поддержкой QUIC доступен как часть проекта chromium: [https://code.google.com/p/chromium/codesearch#chromium/src/net/tools/quic/\&ct=rc\&cd=2\&q=quic\&sq=package:chromium](https://code.google.com/p/chromium/codesearch#chromium/src/net/tools/quic/\&ct=rc\&cd=2\&q=quic\&sq=package:chromium)

HTTP-сервер может объявить клиенту о поддержке протокола QUIC с помощью дополнительного заголовка "Alternate-Protocol: 80:quic" или "Alternate-Protocol: 443:quic".

Имеется серверная реализация на языке Go, что позволяет использовать её в других проектах. 11 июля 2017 года LiteSpeed Technologies, Inc. начали официально поддерживать QUIC в своём балансировщике нагрузки (WebADC) и веб-сервере (LiteSpeed Web Server).

В конце 2020 года появилась реализация IETF QUIC протокола от Microsoft - MsQuic, написанная на языке С. Утверждается, что MsQuic имеет отличия от других вариантов библиотек.
