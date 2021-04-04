---
title: Så här hanterar du hemligheter när du arbetar med ett Azure dev-utrymme
services: azure-dev-spaces
ms.date: 12/03/2019
ms.topic: conceptual
description: Lär dig hur du använder Kubernetes hemligheter vid körning eller bygg tid när du utvecklar program med Azure dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, behållare
ms.custom: devx-track-js
ms.openlocfilehash: 8791480f420dfd76d5291ce82e8ebf7412a41326
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972976"
---
# <a name="how-to-manage-secrets-when-working-with-an-azure-dev-space"></a>Så här hanterar du hemligheter när du arbetar med ett Azure dev-utrymme

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Dina tjänster kan kräva vissa lösen ord, anslutnings strängar och andra hemligheter, t. ex. för databaser eller andra säkra Azure-tjänster. Genom att ange värden för dessa hemligheter i konfigurationsfiler kan du göra dem tillgängliga i koden som miljövariabler.  Dessa konfigurationsfiler måste hanteras med försiktighet för att undvika att Hemligheternas säkerhet äventyras.

## <a name="storing-and-using-runtime-secrets"></a>Lagra och använda körnings hemligheter

Azure dev Spaces innehåller två rekommenderade, strömlinjeformade alternativ för att lagra hemligheter i Helm-diagram som genereras av Azure dev Spaces-klient verktyg: i `values.dev.yaml` filen och infogas direkt i `azds.yaml` . Vi rekommenderar inte att du lagrar hemligheter i `values.yaml` .

> [!NOTE]
> Följande metoder visar hur du lagrar och använder hemligheter för Helm-diagram som genereras av klient verktyget. Om du skapar ett eget Helm-diagram kan du använda Helm-diagrammet direkt för att hantera och lagra hemligheter.

### <a name="using-valuesdevyaml"></a>Använda Values. dev. yaml

I ett projekt som du redan har förberett med Azure dev Spaces skapar du en `values.dev.yaml` fil i samma mapp som `azds.yaml` för att definiera dina hemliga nycklar och värden. Exempel:

```yaml
secrets:
  redis:
    port: "6380"
    host: "contosodevredis.redis.cache.windows.net"
    key: "secretkeyhere"
```

Verifiera `azds.yaml` fil referenserna `values.dev.yaml` som valfria med hjälp av en `?` . Exempel:

```yaml
install:
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
```

Om du har ytterligare hemliga filer kan du även lägga till dem här.

Uppdatera eller verifiera att tjänsten refererar till dina hemligheter som miljövariabler. Exempel:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Kör dina uppdaterade tjänster med hjälp av `azds up` .

```console
azds up
```
 
Använd `kubectl` för att verifiera att dina hemligheter har skapats.

```console
kubectl get secret --namespace default -o yaml 
```

> [!IMPORTANT]
> Vi rekommenderar inte att du lagrar hemligheter i käll kontroll. Om du använder git lägger `values.dev.yaml` du till i `.gitignore` filen för att undvika att bekräfta hemligheter i käll kontrollen.

### <a name="using-azdsyaml"></a>Använda azds. yaml

I ett projekt som du redan har förberett med Azure dev Spaces lägger du till hemliga nycklar och värde med hjälp av *$PLACEHOLDER* syntax under *konfigurationer. utvecklar. install. Set* in `azds.yaml` . Exempel:

```yaml
configurations:
  develop:
    ...
    install:
      set:
        secrets:
          redis:
            port: "$REDIS_PORT"
            host: "$REDIS_HOST"
            key: "$REDIS_KEY"
```

> [!NOTE]
> Du kan ange hemliga värden direkt utan att använda *$PLACEHOLDER* syntax i `azds.yaml` . Den här metoden rekommenderas dock inte eftersom `azds.yaml` är lagrad i käll kontroll.
     
Skapa en `.env` fil i samma mapp som `azds.yaml` för att definiera *$PLACEHOLDER* värden. Exempel:

```
REDIS_PORT=3333
REDIS_HOST=myredishost
REDIS_KEY=myrediskey
```

> [!IMPORTANT]
> Vi rekommenderar inte att du lagrar hemligheter i käll kontroll. Om du använder git lägger `.env` du till i `.gitignore` filen för att undvika att bekräfta hemligheter i käll kontrollen.

Uppdatera eller verifiera att tjänsten refererar till dina hemligheter som miljövariabler. Exempel:

```javascript
var redisPort = process.env.REDIS_PORT
var host = process.env.REDIS_HOST
var theKey = process.env.REDIS_KEY
```
    
Kör dina uppdaterade tjänster med hjälp av `azds up` .

```console
azds up
```
 
Använd `kubectl` för att verifiera att dina hemligheter har skapats.

```console
kubectl get secret --namespace default -o yaml 
```

## <a name="using-secrets-as-build-arguments"></a>Använda hemligheter som build-argument

Föregående avsnitt visade hur du lagrar och använder hemligheter som ska användas vid kör tid för behållare. Du kan också använda vilken hemlighet som helst vid behållar Bygg tiden, till exempel ett lösen ord för en privat NuGet, med hjälp av `azds.yaml` .

I `azds.yaml` ställer du in Bygg tids hemligheter i *konfigurationer. utveckla. Build. args* med `<variable name>: ${secret.<secret name>.<secret key>}` syntaxen. Exempel:

```yaml
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
        MYTOKEN: ${secret.mynugetsecret.pattoken}
```

I exemplet ovan är *mynugetsecret* en befintlig hemlighet och *pattoken* är en befintlig nyckel.

>[!NOTE]
> Hemliga namn och nycklar kan innehålla- `.` tecknen. Används `\` för att undvika att `.` Skicka hemligheter som build-argument. Om du till exempel vill skicka en hemlighet med namnet *foo.bar* med nyckeln för *token*: `MYTOKEN: ${secret.foo\.bar.token}` . Dessutom kan hemligheter utvärderas med prefix-och postfix-text. Till exempel `MYURL: eus-${secret.foo\.bar.token}-version1`. Dessutom kan hemligheter som är tillgängliga i överordnad och föräldrars utrymmen skickas som build-argument.

I din Dockerfile använder du *arg* -direktivet för att använda hemligheten och använder sedan samma variabel senare i Dockerfile. Exempel:

```dockerfile
...
ARG MYTOKEN
...
ARG NUGET_EXTERNAL_FEED_ENDPOINTS="{'endpointCredentials': [{'endpoint':'PRIVATE_NUGET_ENDPOINT', 'password':'${MYTOKEN}'}]}"
...
```

Uppdatera de tjänster som körs i klustret med dessa ändringar. Kör kommandot på kommando raden:

```
azds up
```

## <a name="next-steps"></a>Nästa steg

Med dessa metoder kan du på ett säkert sätt ansluta till en databas, en Azure-cache för Redis eller komma åt säkra Azure-tjänster.
 
