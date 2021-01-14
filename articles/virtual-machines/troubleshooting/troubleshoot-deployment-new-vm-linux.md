---
title: Felsöka distribution av virtuella Linux-datorer | Microsoft Docs
description: Felsök distributions problem i Resource Manager när du skapar en ny virtuell Linux-dator i Azure
services: virtual-machines-linux, azure-resource-manager
documentationcenter: ''
author: DavidCBerry13
manager: gwallace
editor: ''
tags: top-support-issue, azure-resource-manager
ms.assetid: 906a9c89-6866-496b-b4a4-f07fb39f990c
ms.service: virtual-machines-linux
ms.workload: na
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 09/09/2016
ms.author: daberry
ms.openlocfilehash: d94f7389ce96c2e3bda35413cbcc7b1e8a992683
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197548"
---
# <a name="troubleshoot-resource-manager-deployment-issues-with-creating-a-new-linux-virtual-machine-in-azure"></a>Felsök distributions problem i Resource Manager med att skapa en ny Linux-virtuell dator i Azure
[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-opening](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-opening-include.md)]

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="top-issues"></a>Främsta problem
[!INCLUDE [support-disclaimer](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

Information om andra problem med VM-distribution samt frågor finns i [Felsöka problem med distributionen av virtuella Linux-datorer i Azure](troubleshoot-deploy-vm-linux.md).

## <a name="collect-activity-logs"></a>Samla in aktivitets loggar
För att starta fel sökningen samlar du in aktivitets loggarna för att identifiera det fel som är associerat med problemet. Följande länkar innehåller detaljerad information om processen som ska följas.

[Visa distributionsåtgärder](../../azure-resource-manager/templates/deployment-history.md)

[Visa aktivitets loggar för att hantera Azure-resurser](../../azure-resource-manager/management/view-activity-logs.md)

[!INCLUDE [virtual-machines-troubleshoot-deployment-new-vm-issue1](../../../includes/virtual-machines-troubleshoot-deployment-new-vm-issue1-include.md)]

[!INCLUDE [virtual-machines-linux-troubleshoot-deployment-new-vm-table](../../../includes/virtual-machines-linux-troubleshoot-deployment-new-vm-table.md)]

**Y:** Om operativ systemet är generaliserat i Linux och det laddas upp och/eller registreras med den generaliserade inställningen, kommer det inte att finnas några fel. På samma sätt, om operativ systemet är specialiserat på Linux och laddas upp och/eller samlas in med den särskilda inställningen, kommer det inte att finnas några fel.

**Överförings fel:**

**N <sup>1</sup>:** Om operativ systemet är i Linux-generaliserat och det har laddats upp som specialiserat, får du ett timeout-fel för etablering eftersom den virtuella datorn fastnar i etablerings fasen.

**N <sup>2</sup>:** Om operativ systemet är specialiserat på Linux och det laddas upp som generaliserat, får du ett fel meddelande om etableringen eftersom den nya virtuella datorn körs med det ursprungliga dator namnet, användar namnet och lösen ordet.

**Lösning:**

För att lösa båda dessa fel kan du överföra den ursprungliga virtuella hård disken, som är tillgänglig lokalt, med samma inställning som för operativ systemet (generaliserad/specialiserad). Kom ihåg att köra avetableringen först om du vill överföra som generaliserad.

**Infångnings fel:**

**N <sup>3</sup>:** Om operativ systemet är generaliserat för Linux och det har fångats som specialiserat får du ett timeout-fel för etableringen eftersom den ursprungliga virtuella datorn inte kan användas eftersom den är markerad som generaliserad.

**N <sup>4</sup>:** Om operativ systemet är specialiserat på Linux och det registreras som generaliserat får du ett fel meddelande om etableringen, eftersom den nya virtuella datorn körs med det ursprungliga dator namnet, användar namnet och lösen ordet. Dessutom går det inte att använda den ursprungliga virtuella datorn eftersom den är markerad som specialiserad.

**Lösning:**

Du kan lösa båda dessa fel genom att ta bort den aktuella avbildningen från portalen och [avbilda om den från de aktuella virtuella hård diskarna](../linux/capture-image.md) med samma inställning som för operativ systemet (generaliserat/specialiserat).

## <a name="issue-custom-gallery-marketplace-image-allocation-failure"></a>Problem: anpassad/Galleri/Marketplace-avbildning; allokeringsfel
Det här felet uppstår i situationer när den nya VM-begäran fästs i ett kluster som antingen inte stöder den virtuella dator storleken eller som inte har tillräckligt med ledigt utrymme för att hantera begäran.

**Orsak 1:** Klustret har inte stöd för den begärda virtuella dator storleken.

**Lösning 1:**

* Gör om begäran med en mindre VM-storlek.
* Om det inte går att ändra storleken på den begärda virtuella datorn:
  * Stoppa alla virtuella datorer i tillgänglighets uppsättningen.
    Klicka på **resurs grupper**  >  *resurs gruppen*  >  **resurser**  >  *din tillgänglighets uppsättning*  >  **Virtual Machines**  >  *den virtuella datorn*  >  **stoppas**.
  * När alla virtuella datorer har stoppats skapar du den nya virtuella datorn i önskad storlek.
  * Starta den nya virtuella datorn först och välj sedan var och en av de stoppade virtuella datorerna och klicka på **Starta**.

**Orsak 2:** Det finns inga lediga resurser för klustret.

**Lösning 2:**

* Gör om begäran vid ett senare tillfälle.
* Om den nya virtuella datorn kan ingå i en annan tillgänglighets uppsättning
  * Skapa en ny virtuell dator i en annan tillgänglighets uppsättning (i samma region).
  * Lägg till den nya virtuella datorn i samma virtuella nätverk.

## <a name="next-steps"></a>Nästa steg
Om du får problem när du startar en virtuell Linux-dator som har stoppats eller ändrar storlek på en befintlig virtuell Linux-dator i Azure läser du [Felsöka distributions problem med Resource Manager med att starta om eller ändra storlek på en befintlig virtuell Linux-dator i Azure](./troubleshoot-deploy-vm-linux.md?toc=/azure/virtual-machines/linux/toc.json).
