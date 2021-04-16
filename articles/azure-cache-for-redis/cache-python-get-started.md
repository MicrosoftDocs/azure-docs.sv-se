---
title: 'Snabbstart: Använda Azure Cache for Redis i Python'
description: I den här snabbstarten lär du dig hur du skapar en Python-app som använder Azure Cache for Redis.
author: yegu-ms
ms.author: yegu
ms.date: 11/05/2019
ms.topic: quickstart
ms.service: cache
ms.devlang: python
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
- mode-api
ms.openlocfilehash: 40990dfb651817cf52cd5b5a039566e3209d6ac7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532045"
---
# <a name="quickstart-use-azure-cache-for-redis-in-python"></a>Snabbstart: Använda Azure Cache for Redis i Python

I den här artikeln införlivar Azure Cache for Redis i en Python-app för att få åtkomst till en säker, dedikerad cache som kan nås från alla program i Azure.

## <a name="skip-to-the-code-on-github"></a>Hoppa till koden på GitHub

Om du vill hoppa direkt till koden kan du gå till [Python-snabbstarten](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/python) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration [– skapa en kostnadsfritt](https://azure.microsoft.com/free/)
- [Python 2 eller 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Skapa en Azure Cache for Redis-instans
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Installera redis-py

[Redis-py](https://github.com/andymccurdy/redis-py) är ett Python-gränssnitt för Azure Cache for Redis. Använd verktyget Python-paket, *pip*, för att installera *redis-py-paketet* från en kommandotolk. 

I följande exempel användes *pip3* för Python 3 för att installera *redis-py* på Windows 10 från en administratörskommandouppmaning.

![Installera Python-gränssnittet redis-py för att Azure Cache for Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Läsa och skriva till cachen

Kör Python från kommandoraden och testa ditt cacheminne med hjälp av följande kod. Ersätt `<Your Host Name>` och med värdena från din `<Your Access Key>` Azure Cache for Redis instans. Värdnamnet har formen *\<DNS name> .redis.cache.windows.net*.

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
> För Azure Cache for Redis version 3.0 eller senare tillämpas TLS/SSL-certifikatkontroll. ssl_ca_certs måste anges explicit när du ansluter till Azure Cache for Redis. För RedHat Linux ssl_ca_certs finns i *certifikatmodulen /etc/pki/tls/certs/ca-bundle.crt.*

## <a name="create-a-python-sample-app"></a>Skapa en Python-exempelapp

Skapa en ny textfil, lägg till följande skript och spara filen som *PythonApplication1.py*. Ersätt `<Your Host Name>` och med värdena från din `<Your Access Key>` Azure Cache for Redis instans. Värdnamnet har formen *\<DNS name> .redis.cache.windows.net*.

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

Kör *PythonApplication1.py* med Python. Du bör se resultat som i följande exempel:

![Kör Python-skript för att testa cacheåtkomst](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med Azure-resursgruppen och de resurser som du skapade i den här snabbstarten kan du ta bort dem för att undvika kostnader.

> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och resursgruppen och alla resurser i den tas bort permanent. Om du har skapat Azure Cache for Redis-instansen i en befintlig resursgrupp som du vill  behålla kan du ta bort bara cachen genom att välja Ta bort på översiktssidan **för** cachen. 

Så här tar du bort resursgruppen och dess Redis Cache för Azure-instansen:

1. Från [Azure Portal](https://portal.azure.com)du efter och väljer **Resursgrupper**.
1. I **textrutan Filtrera efter** namn anger du namnet på den resursgrupp som innehåller din cacheinstans och väljer den sedan bland sökresultaten. 
1. Välj **Ta bort resursgrupp** på din resursgruppssida.
1. Ange resursgruppens namn och välj sedan Ta **bort.**
   
   ![Ta bort resursgruppen för Azure Cache for Redis](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkel ASP.NET-webbapp som använder Azure Cache for Redis.](./cache-web-app-howto.md)
