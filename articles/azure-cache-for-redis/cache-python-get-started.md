---
title: 'Snabb start: Använd Azure cache för Redis i python'
description: I den här snabb starten får du lära dig hur du skapar en python-app som använder Azure cache för Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.date: 11/05/2019
ms.openlocfilehash: 048c31bedb56bdb66dc3b80d12c3014652e34625
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121158"
---
# <a name="quickstart-use-azure-cache-for-redis-in-python"></a>Snabb start: Använd Azure cache för Redis i python

I den här artikeln införlivar du Azure cache för Redis i en python-app för att få åtkomst till en säker, dedikerad cache som är tillgänglig från alla program i Azure.

## <a name="skip-to-the-code-on-github"></a>Hoppa till koden på GitHub

Om du vill hoppa över direkt till koden går du till [python-snabb](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/python) starten på GitHub.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Python 2 eller 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Skapa en Azure Cache for Redis-instans
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Installera redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) är ett Python-gränssnitt för Azure Cache for Redis. Använd verktyget python packages, *pip* för att installera *Redis-py-* paketet från en kommando tolk. 

I följande exempel användes *PIP3* för python 3 för att installera *Redis-py* på Windows 10 från en administratörs kommando tolk.

![Installera Redis-py python-gränssnittet till Azure cache för Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Läsa och skriva till cachen

Kör python från kommando raden och testa cacheminnet med hjälp av följande kod. Ersätt `<Your Host Name>` och `<Your Access Key>` med värdena från Azure-cachen för Redis-instansen. Värd namnet har formatet form *\<DNS name> . Redis.cache.Windows.net*.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> För Azure cache för Redis version 3,0 eller högre tillämpas TLS/SSL-certifikat kontroll. ssl_ca_certs måste anges explicit vid anslutning till Azure cache för Redis. För RedHat Linux finns ssl_ca_certs i */etc/PKI/TLS/certs/ca-Bundle.CRT* -modulen för certifikat.

## <a name="create-a-python-sample-app"></a>Skapa en python-exempel App

Skapa en ny textfil, Lägg till följande skript och spara filen som *PythonApplication1.py*. Ersätt `<Your Host Name>` och `<Your Access Key>` med värdena från Azure-cachen för Redis-instansen. Värd namnet har formatet form *\<DNS name> . Redis.cache.Windows.net*.

```python
import redis

myHostname = "<Your Host Name>"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Kör *PythonApplication1.py* med python. Du bör se resultatet som i följande exempel:

![Kör Python-skript för att testa cache-åtkomst](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med den Azure-resurs grupp och de resurser som du skapade i den här snabb starten kan du ta bort dem för att undvika avgifter.

> [!IMPORTANT]
> Att ta bort en resurs grupp går inte att ångra, och resurs gruppen och alla resurser i den tas bort permanent. Om du har skapat Azure cache för Redis-instansen i en befintlig resurs grupp som du vill behålla, kan du ta bort bara cachen genom att välja **ta bort** på sidan cache- **Översikt** . 

Så här tar du bort resurs gruppen och dess Redis Cache för Azure-instansen:

1. Sök efter och välj **resurs grupper** från [Azure Portal](https://portal.azure.com).
1. I text rutan **Filtrera efter namn** anger du namnet på den resurs grupp som innehåller din cache-instans och väljer sedan den från Sök resultaten. 
1. Välj **Ta bort resursgrupp** på din resursgruppssida.
1. Skriv namnet på resurs gruppen och välj sedan **ta bort**.
   
   ![Ta bort din resurs grupp för Azure cache för Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkel ASP.NET-webbapp som använder Azure Cache for Redis.](./cache-web-app-howto.md)

