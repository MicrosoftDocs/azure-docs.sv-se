---
title: Starta om principen för uppgifter som körs en gång
description: Lär dig hur du använder Azure Container Instances för att köra uppgifter som körs för att slutföras, till exempel i jobb för att skapa, testa eller återge avbildningar.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 3bce208e3663ecfcebe520be92de3ac4443c0c8f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771163"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>Köra uppgifter i containrar med principer för omstart

Eftersom det går snabbt att distribuera containrar i Azure Container Instances är det en bra plattform för att köra engångsuppgifter som att skapa, testa och återge avbildningar i en containerinstans.

Med en konfigurerbar omstartsprincip kan du ange att dina containrar ska stoppas när deras processer har slutförts. Eftersom du faktureras per sekund för containerinstanser debiteras du endast för de beräkningsresurser som används när containern kör dina uppgifter.

Exemplen som visas i den här artikeln använder Azure CLI. Du måste ha Azure CLI version 2.0.21 eller senare installerat [lokalt,][azure-cli-install]eller använda CLI i [Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="container-restart-policy"></a>Omstartsprincip för container

När du skapar en [containergrupp](container-instances-container-groups.md) i Azure Container Instances kan du ange en av tre principinställningar för omstart.

| Omstartsprincip   | Beskrivning |
| ---------------- | :---------- |
| `Always` | Containrar i containergruppen startas alltid om. Det här är **standardvärdet** som används om du inte anger någon omstartsprincip när du skapar containern. |
| `Never` | Containrar i containergruppen startas aldrig om. Containers körs högst en gång. |
| `OnFailure` | Containrar i containergruppen startas bara om när processen som körs i containern inte slutförs utan fel (när den avslutas med en annan slutkod än noll). Containrar körs minst en gång. |

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="specify-a-restart-policy"></a>Ange en omstartsprincip

Hur du anger en omstartsprincip beror på hur du skapar dina containerinstanser, till exempel med Azure CLI, Azure PowerShell-cmdlets eller i Azure Portal. I Azure CLI anger du parametern `--restart-policy` när du anropar [az container create][az-container-create].

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>Exempel på körning till slutförande

Om du vill se omstartsprincipen i praktiken skapar du en containerinstans från Microsoft [aci-wordcount-avbildningen][aci-wordcount-image] och anger `OnFailure` omstartsprincipen. Den här exempelcontainern kör ett Python-skript som som standard analyserar texten i Script's [Script,](http://shakespeare.mit.edu/hamlet/full.html)skriver de 10 vanligaste orden till STDOUT och avslutas sedan.

Kör exempelcontainern med följande [az container create-kommando:][az-container-create]

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances startar containern och stoppar den när appen (eller skriptet i det här fallet) avslutas. När Azure Container Instances stoppar en container vars omstartsprincip `Never` är eller , anges `OnFailure` containerns status till **Avslutad.** Du kan kontrollera statusen för en container med [kommandot az container show:][az-container-show]

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

När exempelcontainerns status blir *Avslutad* kan du se utdata för uppgiften i containerloggarna. Kör kommandot [az container logs][az-container-logs] för att visa skriptets utdata:

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

Det här exemplet visar de utdata som skriptet skickade till STDOUT. Dina containeruppgifter kan dock i stället skriva sina utdata till beständig lagring för senare hämtning. Till exempel till en [Azure-filresurs](./container-instances-volume-azure-files.md).

## <a name="next-steps"></a>Nästa steg

Uppgiftsbaserade scenarier, till exempel batchbearbetning av en stor datamängd med flera containrar, kan dra nytta av anpassade miljövariabler eller [kommandorader](container-instances-start-command.md) vid körning. [](container-instances-environment-variables.md)

Mer information om hur du sparar utdata från containrar som körs tills de är klara finns i [Montera en Azure-filresurs med Azure Container Instances](./container-instances-volume-azure-files.md).

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[azure-cli-install]: /cli/azure/install-azure-cli
