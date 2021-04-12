---
title: Skapa en Azure båg-datakontrollant i Azure Portal
description: Skapa en Azure båg-datakontrollant i Azure Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 04/07/2021
ms.topic: how-to
ms.openlocfilehash: 12d0997e677bcca423f32951e99a6202855104ad
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107030874"
---
# <a name="create-an-azure-arc-data-controller-in-the-azure-portal"></a>Skapa en Azure båg-datakontrollant i Azure Portal

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="introduction"></a>Introduktion

Du kan använda Azure Portal för att skapa en Azure Arc-datakontrollant.

Många av de olika skapande upplevelserna för Azure Arc startar i Azure Portal även om resursen som ska skapas eller hanteras ligger utanför Azure-infrastrukturen. Mönstret för användar upplevelse i dessa fall, särskilt när det inte finns någon direkt anslutning mellan Azure och din miljö, är att använda Azure Portal för att generera ett skript som sedan kan laddas ned och köras i din miljö för att upprätta en säker anslutning tillbaka till Azure. Azure Arc-aktiverade servrar följer till exempel det här mönstret för att [skapa Arc-aktiverade servrar](../servers/onboard-portal.md).

När du använder det indirekta anslutnings läget i Azure Arc-aktiverade data tjänster kan du använda Azure Portal för att skapa en antecknings bok som sedan kan hämtas och köras i Azure Data Studio mot ditt Kubernetes-kluster. 

När du använder direkt anslutnings läge kan du etablera dataenheten direkt från Azure Portal. Du kan läsa mer om [anslutnings lägen](connectivity.md).

## <a name="use-the-azure-portal-to-create-an-azure-arc-data-controller"></a>Använd Azure Portal för att skapa en Azure Arc-datakontrollant

Följ stegen nedan för att skapa en Azure Arc-dataenhet med hjälp av Azure Portal och Azure Data Studio.

1. Börja med att logga in på [Azure Portal Marketplace](https://ms.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/home/searchQuery/azure%20arc%20data%20controller).  Sök resultaten för Marketplace kommer att filtreras för att visa dig "Azure Arc data Controller".
1. Om det första steget inte har angett Sök villkoren. Ange i Sök resultaten och klicka på Azure Arc data Controller.
1. Välj panelen för Azure Data Controller från Marketplace.
1. Klicka på knappen **skapa** .
1. Välj läget för indirekt anslutning. Läs mer om [anslutnings lägen och krav](./connectivity.md). 
1. Granska kraven för att skapa en Azure Arc-datakontrollant och installera eventuella nödvändiga program som saknas, till exempel Azure Data Studio och kubectl.
1. Klicka på knappen **Nästa: information om data kontrollant** .
1. Välj en prenumeration, resurs grupp och Azure-plats precis som du skulle göra för alla andra resurser som du skulle skapa i Azure Portal. I det här fallet kommer den Azure-plats som du väljer att lagra metadata om resursen.  Själva resursen kommer att skapas på den infrastruktur du väljer. Den behöver inte vara med i Azure-infrastrukturen.
1. Ange ett namn för data kontrollen.

1. Välj en distributions konfigurations profil.
1. Klicka på knappen **Öppna i Azure Studio** .
1. På nästa skärm visas en sammanfattning av dina val och en antecknings bok som genereras.  Du kan hämta antecknings boken genom att klicka på knappen **Hämta antecknings bok för etablering** .

   > [!IMPORTANT]
   > På Azure Red Hat OpenShift eller Red Hat OpenShift container Platform måste du tillämpa begränsningen för säkerhets kontext innan du skapar data kontrollen. Följ anvisningarna i [tillämpa en säkerhets kontext begränsning för Azure Arc-aktiverade data tjänster i OpenShift](how-to-apply-security-context-constraint.md).

1. Öppna den bärbara datorn i Azure Data Studio och klicka på knappen **Kör alla** överst.
1. Följ anvisningarna och anvisningarna i antecknings boken för att slutföra skapandet av datakontrollanten.

## <a name="monitoring-the-creation-status"></a>Övervaka skapande status

Det tar några minuter att slutföra skapandet av kontrollanten. Du kan övervaka förloppet i ett annat terminalfönster med följande kommandon:

> [!NOTE]
>  Exempel kommandona nedan förutsätter att du har skapat en datakontrollant och ett Kubernetes-namnområde med namnet "Arc".  Om du har använt ett annat namn på namn område/data enhet kan du ersätta "Arc" med ditt namn.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Du kan också kontrol lera skapande status för alla specifika Pod genom att köra ett kommando som nedan.  Detta är särskilt användbart vid fel sökning av problem.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Fel sökning av problem med skapande

Om du stöter på problem med att skapa kan du läsa [fel söknings guiden](troubleshoot-guide.md).