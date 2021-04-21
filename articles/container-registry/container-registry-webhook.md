---
title: Webhooks för att svara på registeråtgärder
description: Lär dig hur du använder webhooks för att utlösa händelser när push- eller pull-åtgärder inträffar i dina registerdatabaser.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 4f6fb719f8d9d51429a19616aa5548b32a2687e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773408"
---
# <a name="using-azure-container-registry-webhooks"></a>Använda Azure Container Registry webhooks

Ett Azure-containerregister lagrar och hanterar privata Docker-containeravbildningar, på samma sätt som Docker Hub lagrar offentliga Docker-avbildningar. Den kan också vara värd för lagringsplatsen för [Helm-diagram](container-registry-helm-repos.md) (förhandsversion), ett paketeringsformat för distribution av program till Kubernetes. Du kan använda webhooks för att utlösa händelser när vissa åtgärder sker i en av dina registerdatabaser. Webhooks kan svara på händelser på registernivå, eller så kan de vara begränsade till en specifik lagringsplatstagg. Med ett  [geo-replikerat register](container-registry-geo-replication.md) konfigurerar du varje webhook för att svara på händelser i en specifik regional replik.

Slutpunkten för en webhook måste vara offentligt tillgänglig från registret. Du kan konfigurera webhook-begäranden för registret för att autentisera till en skyddad slutpunkt.

Mer information om webhook-begäranden finns i [Azure Container Registry webhook schema reference](container-registry-webhook-reference.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure-containerregister – Skapa ett containerregister i din Azure-prenumeration. Du kan till exempel använda [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md). De [Azure Container Registry tjänstnivåer](container-registry-skus.md) har olika webhooks-kvoter.
* Docker CLI – Om du vill konfigurera den lokala datorn som en Docker-värd och komma åt Docker CLI-kommandona installerar du [Docker-motorn](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Skapa webhook – Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till det containerregister där du vill skapa en webhook.
1. Under **Tjänster** väljer du **Webhooks**.
1. Välj **Lägg** till i webhook-verktygsfältet.
1. Fyll i *formuläret Skapa webhook* med följande information:

| Värde | Beskrivning |
|---|---|
| Webhook-namn | Det namn som du vill ge webhooken. Det får bara innehålla bokstäver och siffror och måste vara mellan 5 och 50 tecken långt. |
| Location | För ett [geo-replikerat register](container-registry-geo-replication.md) anger du Azure-regionen för registerrepliken. 
| Tjänst-URI | Den URI där webhooken ska skicka POST-meddelanden. |
| Anpassade rubriker | Rubriker som du vill skicka tillsammans med POST-begäran. De ska vara i formatet "key: value". |
| Utlösaråtgärder | Åtgärder som utlöser webhooken. Åtgärderna omfattar push-åtgärder för avbildningar, borttagning av avbildningar, Push-push för Helm-diagram, Borttagning av Helm-diagram och avbildnings karantän. Du kan välja en eller flera åtgärder för att utlösa webhooken. |
| Status | Status för webhooken när den har skapats. Den är aktiverad som standard. |
| Omfång | Omfånget där webhooken fungerar. Om inget anges gäller omfånget för alla händelser i registret. Den kan anges för en lagringsplats eller en tagg med formatet "repository:tag" eller "repository:*" för alla taggar under en lagringsplats. |

Exempelwebhookformulär:

![Skärmbild som visar genereringen av ACR-webhooken U I i Azure Portal.](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Skapa webhook – Azure CLI

Om du vill skapa en webhook med hjälp av Azure CLI använder du [kommandot az acr webhook create.](/cli/azure/acr/webhook#az_acr_webhook_create) Följande kommando skapar en webhook för alla borttagningshändelser för avbildningar i *registret mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testa webhook

### <a name="azure-portal"></a>Azure Portal

Innan du använder webhooken kan du testa den med **knappen Ping.** Ping skickar en allmän POST-begäran till den angivna slutpunkten och loggar svaret. Med pingfunktionen kan du kontrollera att webhooken är korrekt konfigurerad.

1. Välj den webhook som du vill testa.
2. I det översta verktygsfältet väljer du **Pinga**.
3. Kontrollera slutpunktens svar i kolumnen **HTTP STATUS.**

![Användargränssnitt för att skapa ACR-webhook i Azure Portal](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Om du vill testa en ACR-webhook med Azure CLI använder du [pingkommandot az acr webhook.](/cli/azure/acr/webhook#az_acr_webhook_ping)

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Om du vill se resultatet använder du [kommandot az acr webhook list-events.](/cli/azure/acr/webhook)

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Ta bort webhook

### <a name="azure-portal"></a>Azure Portal

Varje webhook kan tas bort genom att välja webhooken och sedan **knappen Ta** bort i Azure Portal.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Nästa steg

### <a name="webhook-schema-reference"></a>Webhook-schemareferens

Mer information om format och egenskaper för JSON-händelsenyttolaster som genereras av Azure Container Registry finns i webhook-schemareferensen:

[Azure Container Registry webhook-schemareferens](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Event Grid händelser

Förutom de interna webhook-händelser i registret som beskrivs i den här artikeln kan Azure Container Registry generera händelser till Event Grid:

[Snabbstart: Skicka containerregisterhändelser till Event Grid](container-registry-event-grid-quickstart.md)
