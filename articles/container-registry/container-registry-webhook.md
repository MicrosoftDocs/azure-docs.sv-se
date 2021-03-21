---
title: Webhooks för att svara på register åtgärder
description: Lär dig hur du använder Webhooks för att utlösa händelser när push-eller pull-åtgärder sker i dina register databaser.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5374b58ba72727500294a173c26e9a131b29fe34
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101722253"
---
# <a name="using-azure-container-registry-webhooks"></a>Använda Azure Container Registry Webhooks

Ett Azure-containerregister lagrar och hanterar privata Docker-containeravbildningar, på samma sätt som Docker Hub lagrar offentliga Docker-avbildningar. Det kan också vara värd för databaser för [Helm-diagram](container-registry-helm-repos.md) (för hands version), ett paket format för att distribuera program till Kubernetes. Du kan använda Webhooks för att utlösa händelser när vissa åtgärder utförs i någon av dina register databaser. Webhooks kan svara på händelser på register nivån, eller så kan de begränsas till en angiven lagrings plats. Med ett  [geo-replikerat](container-registry-geo-replication.md) register konfigurerar du varje webhook för att svara på händelser i en speciell regional replik.

Slut punkten för en webhook måste vara offentligt tillgänglig från registret. Du kan konfigurera register webhook-begäranden för att autentisera till en skyddad slut punkt.

Mer information om webhook-begäranden finns i [Azure Container Registry webhook schema Reference](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure-containerregister – Skapa ett containerregister i din Azure-prenumeration. Använd till exempel [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md). [Azure Container Registry tjänst nivåerna](container-registry-skus.md) har olika Webhooks-kvoter.
* Docker CLI – Om du vill konfigurera den lokala datorn som en Docker-värd och komma åt Docker CLI-kommandona installerar du [Docker-motorn](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Skapa webhook – Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till behållar registret där du vill skapa en webhook.
1. Under **tjänster** väljer du **Webhooks**.
1. Välj **Lägg till** i verktygsfältet webhook.
1. Fyll i formuläret *skapa webhook* med följande information:

| Värde | Beskrivning |
|---|---|
| Webhook-namn | Det namn som du vill ge webhooken. Det får bara innehålla bokstäver och siffror och måste vara 5-50 tecken långt. |
| Location | För ett [geo-replikerat](container-registry-geo-replication.md) register anger du Azure-regionen för register repliken. 
| Tjänst-URI | URI: n där webhooken ska skicka POST meddelanden. |
| Anpassade rubriker | Rubriker som du vill skicka tillsammans med POST-begäran. De bör vara i formatet "nyckel: värde". |
| Utlösnings åtgärder | Åtgärder som utlöser webhooken. Åtgärderna omfattar push-överföring av bilder, bild borttagning, Helm-diagram-push, Helm Chart Delete och image Quarantine. Du kan välja en eller flera åtgärder för att utlösa webhooken. |
| Status | Status för webhooken när den har skapats. Den är aktive rad som standard. |
| Omfång | Den omfattning där webhooken fungerar. Om detta inte anges är omfånget för alla händelser i registret. Den kan anges för en lagrings plats eller en tagg med formatet "databas: tagg" eller "databas: *" för alla Taggar under en lagrings plats. |

Exempel på webhook-formulär:

![Skärm bild som visar ACR-webhook-genereringen i Azure Portal.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Skapa webhook – Azure CLI

Om du vill skapa en webhook med Azure CLI använder du kommandot [AZ ACR webhook Create](/cli/azure/acr/webhook#az-acr-webhook-create) . Följande kommando skapar en webhook för alla bild borttagnings händelser i registret *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testa webhook

### <a name="azure-portal"></a>Azure Portal

Innan du använder webhooken kan du testa den med **ping** -knappen. Ping skickar en allmän POST-begäran till den angivna slut punkten och loggar svaret. Med hjälp av ping-funktionen kan du kontrol lera att du har konfigurerat webhooken korrekt.

1. Välj den webhook som du vill testa.
2. I det översta verktygsfältet väljer du **ping**.
3. Kontrol lera slut punktens svar i kolumnen **http-status** .

![ACR webhook-gränssnittet för skapande av webhook i Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Om du vill testa en ACR-webhook med Azure CLI använder du kommandot [AZ ACR webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping) .

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Om du vill se resultatet använder du kommandot [AZ ACR webhook List-Events](/cli/azure/acr/webhook) .

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Ta bort webhook

### <a name="azure-portal"></a>Azure Portal

Du kan ta bort varje webhook genom att välja webhooken och sedan knappen **ta bort** i Azure Portal.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Nästa steg

### <a name="webhook-schema-reference"></a>Referens för webhook-schema

Mer information om format och egenskaper för JSON-händelsens nytto laster som genereras av Azure Container Registry finns i referens för webhook-schemat:

[Azure Container Registry webhook schema-referens](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid händelser

Förutom de interna register-webhook-händelser som beskrivs i den här artikeln kan Azure Container Registry generera händelser till Event Grid:

[Snabb start: skicka behållar register händelser till Event Grid](container-registry-event-grid-quickstart.md)
