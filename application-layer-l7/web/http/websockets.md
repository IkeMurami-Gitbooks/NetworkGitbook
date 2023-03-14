---
description: Работает как расширение поверх http (switch protocol и все дела)
---

# WebSockets

Python: Отключение проверки сертификата в websocket

Помогает, если встретили ошибку: `cafile, capath and cadata cannot be all omitted`

```python
Please set sslopt to {"cert_reqs": ssl.CERT_NONE}.
WebSocketApp sample
.. code:: python
ws = websocket.WebSocketApp("wss://echo.websocket.org")
ws.run_forever(sslopt={"cert_reqs": ssl.CERT_NONE})
create_connection sample
.. code:: python
ws = websocket.create_connection("wss://echo.websocket.org",
sslopt={"cert_reqs": ssl.CERT_NONE})
WebSocket sample
.. code:: python
ws = websocket.WebSocket(sslopt={"cert_reqs": ssl.CERT_NONE})
ws.connect("wss://echo.websocket.org")
```
