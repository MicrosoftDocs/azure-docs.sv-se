---
title: Felsöka flyttnings fel
description: Felsök att flytta resurser till en ny resurs grupp eller prenumeration.
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 41b1e2435caf9874f3582a3394664c7b7f5a8d29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90054170"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>Felsöka flytt av Azure-resurser till en ny resursgrupp eller prenumeration

Den här artikeln innehåller förslag på hur du kan lösa problem när du flyttar resurser.

## <a name="upgrade-a-subscription"></a>Uppgradera en prenumeration

Om du faktiskt vill uppgradera din Azure-prenumeration (t. ex. genom att byta från kostnads fritt till betala per användning) måste du konvertera din prenumeration.

* Om du vill uppgradera en kostnads fri utvärderings version läser du [uppgradera din kostnads fria utvärderings version eller Microsoft Imagine Azure-prenumeration för att betala per](../../cost-management-billing/manage/upgrade-azure-subscription.md)användning.
* Om du vill ändra ett konto där du betalar per användning kan du läsa mer i [ändra din Azure-prenumeration med betala per användning till ett annat erbjudande](../../cost-management-billing/manage/switch-azure-offer.md).

Om du inte kan konvertera prenumerationen [skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Välj **prenumerations hantering** för typ av problem.

## <a name="service-limitations"></a>Tjänst begränsningar

Vissa tjänster kräver ytterligare överväganden när du flyttar resurser. Om du flyttar följande tjänster, se till att du har kontroll över vägledning och begränsningar.

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [Klassisk distributionsmodell](./move-limitations/classic-model-move-limitations.md)
* [Nätverk](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>Stora begär Anden

När det är möjligt flyttas stora steg till separata flytt åtgärder. Resource Manager returnerar omedelbart ett fel när det finns fler än 800 resurser i en enda åtgärd. Men att flytta mindre än 800 resurser kan också Miss lyckas genom timeout.

## <a name="resource-not-in-succeeded-state"></a>Resursen har inte statusen slutförd

När du får ett fel meddelande som anger att en resurs inte kan flyttas eftersom den inte är i ett framgångs tillstånd, kan det hända att den faktiskt är en beroende resurs som blockerar flytten. Felkoden är vanligt vis **MoveCannotProceedWithResourcesNotInSucceededState**.

Om käll-eller mål resurs gruppen innehåller ett virtuellt nätverk, kontrol leras tillståndet för alla beroende resurser för det virtuella nätverket under flytten. Kontrollen inkluderar de resurser som är direkt och indirekt beroende av det virtuella nätverket. Om någon av dessa resurser är i ett felaktigt tillstånd blockeras flyttningen. Om till exempel en virtuell dator som använder det virtuella nätverket har misslyckats blockeras flyttningen. Flyttningen blockeras även om den virtuella datorn inte är en av de resurser som flyttas och inte finns i någon av resurs grupperna för flytten.

När du får det här felet har du två alternativ. Flytta dina resurser till en resurs grupp som inte har ett virtuellt nätverk eller [kontakta supporten](../../azure-portal/supportability/how-to-create-azure-support-request.md).

## <a name="next-steps"></a>Nästa steg

För kommandon för att flytta resurser, se [Flytta resurser till ny resurs grupp eller prenumeration](move-resource-group-and-subscription.md).
