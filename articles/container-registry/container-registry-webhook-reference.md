---
title: Referens för register-webhook-schema
description: Referens för JSON-nyttolast för webhook-begäranden i ett Azure Container Registry, som genereras när Webhooks har Aktiver ATS för artefakt-push eller ta bort händelser
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "74455970"
---
# <a name="azure-container-registry-webhook-reference"></a>Azure Container Registry webhook-referens

Du kan [Konfigurera webhookar](container-registry-webhook.md) för behållar registret som genererar händelser när vissa åtgärder utförs mot den. Aktivera till exempel Webhooks som utlöses när en behållar avbildning eller ett Helm-diagram flyttas till ett register eller tas bort. När en webhook utlöses, Azure Container Registry problem med en HTTP-eller HTTPS-begäran som innehåller information om händelsen till en slut punkt som du anger. Din slut punkt kan sedan bearbeta webhooken och agera enligt detta.

Följande avsnitt innehåller information om schemat för webhook-begäranden som genereras av händelser som stöds. Händelse avsnitten innehåller nytto Last schema för händelse typen, ett exempel på begäran om nytto last och ett eller flera exempel kommandon som skulle utlösa webhooken.

Information om hur du konfigurerar Webhooks för Azure Container Registry finns i [använda Azure Container Registry Webhooks](container-registry-webhook.md).

## <a name="webhook-requests"></a>Webhook-begäranden

### <a name="http-request"></a>HTTP-begäran

En utlöst webhook gör en HTTP- `POST` begäran till URL-slutpunkten som du angav när du konfigurerade webhooken.

### <a name="http-headers"></a>HTTP-rubriker

Webhook-begäranden innehåller en `Content-Type` av `application/json` om du inte har angett någon `Content-Type` anpassad rubrik för webhooken.

Inga andra huvuden läggs till i begäran utöver de anpassade huvuden som du kan ha angett för webhooken.

## <a name="push-event"></a>Push-händelse

Webhook utlöses när en behållar avbildning flyttas till en lagrings plats.

### <a name="push-event-payload"></a>Nytto last för push-händelse

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|Sträng|ID för webhook-händelsen.|
|`timestamp`|DateTime|Tiden då webhook-händelsen utlöstes.|
|`action`|Sträng|Den åtgärd som utlöste webhook-händelsen.|
|[fokusera](#target)|Komplex typ|Målet för den händelse som utlöste webhook-händelsen.|
|[anmoda](#request)|Komplex typ|Den begäran som genererade webhook-händelsen.|

### <a name="target"></a><a name="target"></a>fokusera

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|Sträng|MIME-typen för det refererade objektet.|
|`size`|Int32|Antalet byte för innehållet. Samma som längd fält.|
|`digest`|Sträng|Sammanfattningen av innehållet, som definieras i HTTP API-specifikationen för registret v2.|
|`length`|Int32|Antalet byte för innehållet. Samma som storleks fält.|
|`repository`|Sträng|Namnet på databasen.|
|`tag`|Sträng|Namnet på bildtaggen.|

### <a name="request"></a><a name="request"></a>anmoda

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`id`|Sträng|ID för den begäran som initierade händelsen.|
|`host`|Sträng|Det externt tillgängliga värd namnet för register instansen, enligt vad som anges i HTTP-värd rubriken för inkommande begär Anden.|
|`method`|Sträng|Metoden för begäran som genererade händelsen.|
|`useragent`|Sträng|Användar agent rubriken för begäran.|

### <a name="payload-example-image-push-event"></a>Nytto Last exempel: bild-push-händelse

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Exempel på [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/) -kommando som utlöser avbildningens **push** -händelse-webhook:

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Diagram-push-händelse

Webhook utlöses när ett Helm-diagram flyttas till en lagrings plats.

### <a name="chart-push-event-payload"></a>Nytto last för diagrammets push-händelse

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|Sträng|ID för webhook-händelsen.|
|`timestamp`|DateTime|Tiden då webhook-händelsen utlöstes.|
|`action`|Sträng|Den åtgärd som utlöste webhook-händelsen.|
|[fokusera](#helm_target)|Komplex typ|Målet för den händelse som utlöste webhook-händelsen.|

### <a name="target"></a><a name="helm_target"></a>fokusera

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|Sträng|MIME-typen för det refererade objektet.|
|`size`|Int32|Antalet byte för innehållet.|
|`digest`|Sträng|Sammanfattningen av innehållet, som definieras i HTTP API-specifikationen för registret v2.|
|`repository`|Sträng|Namnet på databasen.|
|`tag`|Sträng|Diagram etikettens namn.|
|`name`|Sträng|Diagrammets namn.|
|`version`|Sträng|Diagram versionen.|

### <a name="payload-example-chart-push-event"></a>Exempel på nytto last: diagram push-händelse

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exempel på [Azure CLI](/cli/azure/acr) -kommando som utlöser **chart_push** Event-webhook:

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Ta bort händelse

Webhook utlöses när en avbildnings lagrings plats eller ett manifest tas bort. Utlöses inte när en tagg tas bort.

### <a name="delete-event-payload"></a>Ta bort händelse nytto Last

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|Sträng|ID för webhook-händelsen.|
|`timestamp`|DateTime|Tiden då webhook-händelsen utlöstes.|
|`action`|Sträng|Den åtgärd som utlöste webhook-händelsen.|
|[fokusera](#delete_target)|Komplex typ|Målet för den händelse som utlöste webhook-händelsen.|
|[anmoda](#delete_request)|Komplex typ|Den begäran som genererade webhook-händelsen.|

### <a name="target"></a><a name="delete_target"></a> fokusera

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|Sträng|MIME-typen för det refererade objektet.|
|`digest`|Sträng|Sammanfattningen av innehållet, som definieras i HTTP API-specifikationen för registret v2.|
|`repository`|Sträng|Namnet på databasen.|

### <a name="request"></a><a name="delete_request"></a> anmoda

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`id`|Sträng|ID för den begäran som initierade händelsen.|
|`host`|Sträng|Det externt tillgängliga värd namnet för register instansen, enligt vad som anges i HTTP-värd rubriken för inkommande begär Anden.|
|`method`|Sträng|Metoden för begäran som genererade händelsen.|
|`useragent`|Sträng|Användar agent rubriken för begäran.|

### <a name="payload-example-image-delete-event"></a>Nytto Last exempel: händelse för borttagning av bild

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Exempel på [Azure CLI](/cli/azure/acr) -kommandon som utlöser en **Delete** -händelse-webhook:

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Händelse för borttagning av diagram

Webhook utlöses när ett Helm-diagram eller en lagrings plats tas bort. 

### <a name="chart-delete-event-payload"></a>Ta bort händelse nytto Last i diagrammet

|Element|Typ|Beskrivning|
|-------------|----------|-----------|
|`id`|Sträng|ID för webhook-händelsen.|
|`timestamp`|DateTime|Tiden då webhook-händelsen utlöstes.|
|`action`|Sträng|Den åtgärd som utlöste webhook-händelsen.|
|[fokusera](#chart_delete_target)|Komplex typ|Målet för den händelse som utlöste webhook-händelsen.|

### <a name="target"></a><a name="chart_delete_target"></a> fokusera

|Element|Typ|Beskrivning|
|------------------|----------|-----------|
|`mediaType`|Sträng|MIME-typen för det refererade objektet.|
|`size`|Int32|Antalet byte för innehållet.|
|`digest`|Sträng|Sammanfattningen av innehållet, som definieras i HTTP API-specifikationen för registret v2.|
|`repository`|Sträng|Namnet på databasen.|
|`tag`|Sträng|Diagram etikettens namn.|
|`name`|Sträng|Diagrammets namn.|
|`version`|Sträng|Diagram versionen.|

### <a name="payload-example-chart-delete-event"></a>Nytto Last exempel: händelse för borttagning av diagram

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Exempel på [Azure CLI](/cli/azure/acr) -kommando som utlöser **chart_delete** Event-webhook:

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Nästa steg

[Använda Azure Container Registry Webhooks](container-registry-webhook.md)