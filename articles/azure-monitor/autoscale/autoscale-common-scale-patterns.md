---
title: Översikt över vanliga autoskalningsmönster
description: Lär dig några av de vanligaste mönstren för att skala din resurs automatiskt i Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: adbfecd05bfd4330e7c7c972cfdb9fe7021eec31
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623891"
---
# <a name="overview-of-common-autoscale-patterns"></a>Översikt över vanliga autoskalningsmönster
I den här artikeln beskrivs några av de vanliga mönstren för att skala din resurs i Azure.

Azure Monitor autoskalning gäller endast för [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/)-, [Cloud Services](https://azure.microsoft.com/services/cloud-services/)-, [App Service-Web Apps-](https://azure.microsoft.com/services/app-service/web/)och [API Management-tjänster](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Gör det möjligt att komma igång

Den här artikeln förutsätter att du är van vid automatisk skalning. Du kan [sätta igång här för att skala din resurs][1]. Här följer några av de vanliga skalnings mönstren.

## <a name="scale-based-on-cpu"></a>Skala baserat på CPU

Du har en webbapp (/VMSS/cloud service Role) och

- Du vill skala ut/skala i baserat på CPU.
- Dessutom vill du se till att det finns ett minsta antal instanser.
- Du vill också se till att du anger en maximal gräns för antalet instanser som du kan skala till.

![Skala baserat på CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Skala annorlunda på vardagar vs helger

Du har en webbapp (/VMSS/cloud service Role) och

- Du vill ha 3 instanser som standard (på vardagar)
- Du förväntar dig inte trafik på helger och därför vill du skala ned till 1 instans på helger.

![Skala annorlunda på vardagar vs helger][3]

## <a name="scale-differently-during-holidays"></a>Skala annorlunda under helger

Du har en webbapp (/VMSS/cloud service Role) och

- Du vill skala upp/ned baserat på CPU-användning som standard
- Under semester säsongen (eller specifika dagar som är viktiga för din verksamhet) vill du dock åsidosätta standardvärdena och få mer kapacitet i ditt förfogande.

![Skala annorlunda på helger][4]

## <a name="scale-based-on-custom-metric"></a>Skala baserat på anpassat mått

Du har en webb klient del och en API-nivå som kommunicerar med Server delen.

- Du vill skala API-nivån baserat på anpassade händelser på klient sidan (exempel: du vill skala din arbets process baserat på antalet objekt i shopping vagnen)

![Skala baserat på anpassat mått][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png
