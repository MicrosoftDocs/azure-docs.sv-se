---
title: Åtgärder för kontroll plan och data plan
description: Beskriver skillnaden mellan kontroll planet och data Plans åtgärder. Kontroll Plans åtgärder hanteras av Azure Resource Manager. Data Plans åtgärder hanteras av en tjänst.
ms.topic: conceptual
ms.date: 09/10/2020
ms.openlocfilehash: 76304c81a1af1eef87d12cfd4130867851a61d28
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544102"
---
# <a name="azure-control-plane-and-data-plane"></a>Azure kontroll plan och data plan

Azure-åtgärder kan delas in i två kategorier – kontroll planet och data planet. I den här artikeln beskrivs skillnaderna mellan dessa två typer av åtgärder.

Du använder kontroll planet för att hantera resurser i din prenumeration. Du använder data planet för att använda funktioner som exponeras av din instans av en resurs typ.

Exempel:

* Du skapar en virtuell dator via kontroll planet. När den virtuella datorn har skapats kan du interagera med den via data Plans åtgärder, till exempel Remote Desktop Protocol (RDP).

* Du skapar ett lagrings konto via kontroll planet. Du kan använda data planet för att läsa och skriva data i lagrings kontot.

* Du skapar en Cosmos-databas via kontroll planet. Om du vill fråga efter data i Cosmos-databasen använder du data planet.

## <a name="control-plane"></a>Kontrollplan

Alla begär Anden om kontroll Plans åtgärder skickas till Azure Resource Manager URL. URL: en varierar i Azure-miljön.

* För Azure Global är URL: en `https://management.azure.com` .
* För Azure Government är URL: en `https://management.usgovcloudapi.net/` .
* För Azure Germany är URL: en `https://management.microsoftazure.de/` .
* För Microsoft Azure Kina är URL: en `https://management.chinacloudapi.cn` .

Information om vilka åtgärder som använder Azure Resource Manager URL finns i [Azure-REST API](/rest/api/azure/). Till exempel är åtgärden [skapa eller uppdatera](/rest/api/mysql/databases/createorupdate) för MySQL en kontroll Plans åtgärd eftersom URL: en för begäran är:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}/databases/{databaseName}?api-version=2017-12-01
```

Azure Resource Manager hanterar alla kontroll Plans begär Anden. Den tillämpar automatiskt de Azure-funktioner som du har implementerat för att hantera dina resurser, till exempel:

* [Azure RBAC (rollbaserad åtkomstkontroll)](../../role-based-access-control/overview.md)
* [Azure Policy](../../governance/policy/overview.md)
* [Hanterings lås](lock-resources.md)
* [Aktivitetsloggar](view-activity-logs.md)

Efter autentiseringen av begäran skickar Azure Resource Manager den till resurs leverantören, som Slutför åtgärden.

Kontroll planet innehåller två scenarier för hantering av förfrågningar – "grönt fält" och "brun fält". Grönt fält refererar till nya resurser. Brun-fältet refererar till befintliga resurser. När du distribuerar resurser kan Azure Resource Manager tolka när nya resurser ska skapas och när befintliga resurser ska uppdateras. Du behöver inte oroa dig för att identiska resurser kommer att skapas.

## <a name="data-plane"></a>Dataplanet

Begär Anden om data Plans åtgärder skickas till en slut punkt som är speciell för din instans. Till exempel är åtgärden [identifiera språk](/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-language-detection) i Cognitive Services en data Plans åtgärd eftersom URL: en för begäran är:

```http
POST {Endpoint}/text/analytics/v2.0/languages
```

Data Plans åtgärder är inte begränsade till REST API. De kan kräva ytterligare autentiseringsuppgifter, till exempel Logga in på en virtuell dator eller databas server.

Funktioner som upprätthåller hantering och styrning kanske inte tillämpas på data Plans åtgärder. Du måste fundera över vilka olika sätt användarna interagerar med dina lösningar. Ett lås som hindrar användare från att ta bort en databas hindrar till exempel inte användare från att ta bort data via frågor.

Du kan använda vissa principer för att styra data Plans åtgärder. Mer information finns [i resurs leverantörs lägen (för hands version) i Azure policy](../../governance/policy/concepts/definition-structure.md#resource-provider-modes).

## <a name="next-steps"></a>Nästa steg

* En översikt över Azure Resource Manager finns [Azure Resource Manager?](overview.md)

* Mer information om effekten av princip definitioner på nya resurser och befintliga resurser finns i [utvärdera effekten av en ny Azure policy-definition](../../governance/policy/concepts/evaluate-impact.md).
