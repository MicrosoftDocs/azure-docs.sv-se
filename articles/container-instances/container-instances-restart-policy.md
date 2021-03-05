---
title: Starta om princip för körnings aktiviteter
description: Lär dig hur du använder Azure Container Instances för att köra aktiviteter som körs till slut för ande, som i bygg-, test-eller bild åter givnings jobb.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 49280549fa834b82574f81494f1cf44817d8be5d
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203835"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Köra uppgifter i containrar med principer för omstart

Eftersom det går snabbt att distribuera containrar i Azure Container Instances är det en bra plattform för att köra engångsuppgifter som att skapa, testa och återge avbildningar i en containerinstans.

Med en konfigurerbar omstartsprincip kan du ange att dina containrar ska stoppas när deras processer har slutförts. Eftersom du faktureras per sekund för containerinstanser debiteras du endast för de beräkningsresurser som används när containern kör dina uppgifter.

I exemplen som presenteras i den här artikeln används Azure CLI. Du måste ha Azure CLI-version 2.0.21 eller senare [installerad][azure-cli-install]eller använda CLI i [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Princip för omstart av behållare

När du skapar en [behållar grupp](container-instances-container-groups.md) i Azure Container instances kan du ange en av tre princip inställningar för omstart.

| Omstartsprincip   | Beskrivning |
| ---------------- | :---------- |
| `Always` | Containrar i containergruppen startas alltid om. Det här är **standardvärdet** som används om du inte anger någon omstartsprincip när du skapar containern. |
| `Never` | Containrar i containergruppen startas aldrig om. Containers körs högst en gång. |
| `OnFailure` | Containrar i containergruppen startas bara om när processen som körs i containern inte slutförs utan fel (när den avslutas med en annan slutkod än noll). Containrar körs minst en gång. |

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="specify-a-restart-policy"></a>Ange en princip för omstart

Hur du anger en princip för omstart beror på hur du skapar dina behållar instanser, till exempel med Azure CLI, Azure PowerShell-cmdlets eller i Azure Portal. I Azure CLI anger `--restart-policy` du parametern när du anropar [AZ container Create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Exempel på Kör till slut för ande

Om du vill se principen starta om i praktiken skapar du en behållar instans från Microsoft [ACI-WORDCOUNT-][aci-wordcount-image] avbildningen och anger `OnFailure` principen för omstart. Den här exempel behållaren kör ett Python-skript som, som standard, analyserar texten i Shakespeare- [Hamlet](http://shakespeare.mit.edu/hamlet/full.html), skriver de 10 vanligaste orden till stdout och sedan avslutar.

Kör exempel behållaren med följande [AZ container Create][az-container-create] -kommando:

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances startar containern och stoppar den när appen (eller skriptet i det här fallet) avslutas. När Azure Container Instances stoppar en behållare vars restart-princip är `Never` eller `OnFailure` , är behållarens status inställd på **avslutad**. Du kan kontrol lera statusen för en behållare med kommandot [AZ container show][az-container-show] :

```azurecli-interactive
az container show \
    --resource-group myResourceGroup \
    --name mycontainer \
    --query containers[0].instanceView.currentState.state
```

Exempel på utdata:

```bash
"Terminated"
```

När exempelcontainerns status blir *Avslutad* kan du se utdata för uppgiften i containerloggarna. Kör kommandot [AZ container logs][az-container-logs] för att Visa skriptets utdata:

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

Utdata:

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

I det här exemplet visas utdata som skriptet skickat till STDOUT. Dina uppgifter i behållare kan dock i stället skriva sina utdata till beständig lagring för senare hämtning. Till exempel till en [Azure-filresurs](./container-instances-volume-azure-files.md).

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, t. ex. batchbearbetning av en stor data uppsättning med flera behållare, kan dra nytta av anpassade [miljövariabler](container-instances-environment-variables.md) eller [kommando rader](container-instances-start-command.md) vid körning.

Mer information om hur du behåller utdata från dina behållare som körs för slut för ande finns i [montera en Azure-filresurs med Azure Container instances](./container-instances-volume-azure-files.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
