---
title: Stoppa eller starta containergrupp manuellt
description: Lär dig hur du stoppar eller startar en containergrupp manuellt i Azure Container Instances.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 43498a7d31f0cb78751d2f318aede8523b7b9345
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787007"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Stoppa eller starta containrar manuellt i Azure Container Instances

Inställningen [för omstartsprincipen](container-instances-restart-policy.md) för en containergrupp avgör hur containerinstanser startar eller stoppas som standard. Du kan åsidosätta standardinställningen genom att manuellt stoppa eller starta en containergrupp.

[!INCLUDE [container-instances-restart-ip](../../includes/container-instances-restart-ip.md)]

## <a name="stop"></a>Stoppa

Stoppa en containergrupp som körs manuellt, till exempel genom att använda [kommandot az container stop][az-container-stop] eller Azure Portal. För vissa containerarbetsbelastningar kanske du vill stoppa en långvarig containergrupp efter en definierad period för att spara på kostnaderna. 

*När en containergrupp går in i tillståndet Stoppad avslutas den och återanvänder alla containrar i gruppen. Det bevarar inte containertillståndet.*

När containrarna återanvänds [frisallokeras](container-instances-container-groups.md#resource-allocation) resurserna och faktureringen stoppas för containergruppen.

Stoppåtgärden har ingen effekt om containergruppen redan avslutats (är i tillståndet Lyckades eller Misslyckades). Till exempel avslutas en containergrupp med run-once-containeruppgifter som har körts i tillståndet Lyckades. Försök att stoppa gruppen i det tillståndet ändrar inte tillståndet. 

## <a name="start"></a>Start

När en containergrupp stoppas – antingen på grund av att containrarna avslutades på egen hand eller om du har stoppat gruppen manuellt – kan du starta containrarna. Du kan till exempel använda [kommandot az container start][az-container-start] eller Azure Portal starta containrarna i gruppen manuellt. Om containeravbildningen för en container uppdateras hämtas en ny avbildning. 

När du startar en containergrupp börjar en ny distribution med samma containerkonfiguration. Den här åtgärden kan hjälpa dig att snabbt återanvända en känd containergruppskonfiguration som fungerar som förväntat. Du behöver inte skapa en ny containergrupp för att köra samma arbetsbelastning.

Alla containrar i en containergrupp startas med den här åtgärden. Du kan inte starta en specifik container i gruppen.

När du startar eller startar om en containergrupp manuellt körs containergruppen enligt den konfigurerade omstartsprincipen.
  
## <a name="restart"></a>Starta om

Du kan starta om en containergrupp medan den körs, till exempel med kommandot [az container restart.][az-container-restart] Den här åtgärden startar om alla containrar i containergruppen. Om containeravbildningen för en container uppdateras hämtas en ny avbildning. 

Att starta om en containergrupp är användbart när du vill felsöka ett distributionsproblem. Om en tillfällig resursbegränsning till exempel förhindrar att dina containrar körs korrekt kan du lösa problemet genom att starta om gruppen.

Alla containrar i en containergrupp startas om med den här åtgärden. Du kan inte starta om en specifik container i gruppen.

När du har startat om en containergrupp manuellt körs containergruppen enligt den konfigurerade omstartsprincipen.

## <a name="next-steps"></a>Nästa steg

Läs mer om [principinställningar för omstart](container-instances-restart-policy.md) i Azure Container Instances.

Förutom att manuellt stoppa och starta en containergrupp med den befintliga konfigurationen kan du [uppdatera inställningarna för](container-instances-update.md) en containergrupp som körs.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container#az_container_restart
[az-container-start]: /cli/azure/container#az_container_start
[az-container-stop]: /cli/azure/container#az_container_stop
