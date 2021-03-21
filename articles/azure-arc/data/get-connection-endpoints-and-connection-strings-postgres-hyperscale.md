---
title: Hämta anslutnings slut punkter och form anslutnings strängar för din ARC-aktiverad PostgreSQL-Server grupp
titleSuffix: Azure Arc enabled data services
description: Hämta anslutnings slut punkter och form anslutnings strängar för din ARC-aktiverad PostgreSQL-Server grupp
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: de7d23689ae984ea0abece5edb03cf8a0c3a9be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670349"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Hämta anslutnings slut punkter och form anslutnings strängar för din ARC-aktiverad PostgreSQL-Server grupp

I den här artikeln förklaras hur du kan hämta anslutnings slut punkter för Server gruppen och hur du formar anslutnings strängar som du kommer att använda med dina program och/eller verktyg.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Hämta slut punkter för anslutning:

### <a name="from-cli-with-azdata"></a>Från CLI med azdata
#### <a name="1-connect-to-your-arc-data-controller"></a>1. Anslut till data styrenheten för bågen:
- Om du redan har en session som har öppnats på värden av data styrenheten för bågen: kör följande kommando:
```console
azdata login
```

- Om du inte har öppnat en session på värden för data styrenheten Arc: kör du följande kommando 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. Visa anslutnings slut punkter
Kör följande kommando:
```console
azdata arc postgres endpoint list -n <server group name>
```
Till exempel:
```console
azdata arc postgres endpoint list -n postgres01
```

Den visar listan över slut punkter: PostgreSQL-slutpunkten som du använder för att ansluta ditt program och använda databasen, Kibana och Grafana-slutpunkter för Log Analytics och övervakning. Exempel: 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
Använd de här slut punkterna för att:
- Skapa anslutnings strängar och Anslut till dina klient verktyg eller program
- Öppna Grafana- och Kibana-instrumentpanelerna från din webbläsare

Du kan till exempel använda slut punkten med namnet _postgresql-instans_ för att ansluta till psql till din server grupp. Exempel:
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - Lösen ordet för den _postgres_ -användare som anges i slut punkten med namnet "_postgresql instance_" är det lösen ord som du valde när du distribuerar Server gruppen.
> - Om azdata: lånet som är kopplat till anslutningen varar cirka 10 timmar. Efter det måste du ansluta igen. Om lånet har upphört att gälla får du följande fel meddelande när du försöker köra ett kommando med azdata (annat än azdata-inloggning): _fel: (401)_ 
>  _Orsak: otillåtna_ 
>  _HTTP-svarshuvuden: HTTPHeaderDict ({Date: "Sun, 06 sep 2020 16:58:38 GMT, Content-Length: ' 0 ', ' www-autentisera ': '_ 
>  _grundläggande sfär =" inloggnings_ uppgifter krävs ", Bearer-fel =" invalid_token ", error_description =" token har upphört att gälla "}) _ när detta inträffar måste du återansluta till azdata enligt beskrivningen ovan.

## <a name="from-cli-with-kubectl"></a>Från CLI med kubectl
- Om Server gruppen är av postgres version 12 (standard) klickar du på följande kommando:
```console
kubectl get postgresql-12/<server group name> -n <namespace name>
```
- Om din server grupp är av postgres version 11 gör du följande kommando:
```console
kubectl get postgresql-11/<server group name> -n <namespace name>
```

De här kommandona skapar utdata som de som visas nedan. Du kan använda den informationen för att skapa anslutnings strängar:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>Formulär anslutnings strängar:
Använd tabellen nedan med mallar för anslutnings strängar för Server gruppen. Du kan sedan kopiera/klistra in och anpassa dem efter behov:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Nästa steg
- Läs mer om [att skala ut (lägga till arbetsnoder)](scale-out-postgresql-hyperscale-server-group.md) i Server gruppen
- Läs mer om att [skala upp eller ned (öka/minska minnes-och virtuella kärnor)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) i Server gruppen


