---
title: Återställning efter fel vid haveri beredskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller en översikt över olika typer av återställnings fel och varningar som ska övervägas och som inte kan återgå till lokalt under haveri beredskap med Azure Site Recovery-tjänsten.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "84691092"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Återställning efter fel för virtuella VMware-datorer efter haveriberedskap till Azure

När du har redundansväxlats till Azure som en del av din haveri beredskaps process kan du växla tillbaka till din lokala plats. Det finns två olika typer av återställning efter fel som kan uppstå i Azure Site Recovery: 

- Växla tillbaka till den ursprungliga platsen 
- Växla tillbaka till en alternativ plats

Om du har växlat över en virtuell VMware-dator kan du växla tillbaka till samma lokala lokala virtuella dator om den fortfarande finns. I det här scenariot replikeras bara ändringarna tillbaka. Det här scenariot kallas för den **ursprungliga plats återställningen**. Om den lokala virtuella datorn inte finns är scenariot en **alternativ plats återställning**.

> [!NOTE]
> Du kan bara växla tillbaka till den ursprungliga vCenter-servern och konfigurations servern. Du kan inte distribuera en ny konfigurations Server och återställa den med den. Du kan inte heller lägga till en ny vCenter till den befintliga konfigurations servern och återställning efter fel till nya vCenter.

## <a name="original-location-recovery-olr"></a>Återställning av ursprunglig plats (OLR)
Om du väljer att växla tillbaka till den ursprungliga virtuella datorn måste följande villkor uppfyllas:

* Om den virtuella datorn hanteras av en vCenter-Server borde huvud målets ESX-värd ha åtkomst till den virtuella datorns data lager.
* Om den virtuella datorn finns på en ESX-värd men inte hanteras av vCenter måste den virtuella datorns hård disk finnas i ett data lager som huvud målets värd har åtkomst till.
* Om den virtuella datorn finns på en ESX-värd och inte använder vCenter, bör du slutföra identifieringen av ESX-värden på huvud mål servern innan du återaktiverar skyddet. Detta gäller även om du inte återställer fysiska servrar.
* Du kan växla tillbaka till en virtuell storage area network (virtuellt SAN) eller en disk som baseras på mappning av rå enheter (RDM) om diskarna redan finns och är anslutna till den lokala virtuella datorn.

> [!IMPORTANT]
> Det är viktigt att aktivera disk. Enableuuid är = TRUE, så att Azure Site Recoverys tjänsten kan identifiera den ursprungliga VMDK på den virtuella datorn som de väntande ändringarna kommer att skrivas under återställning efter fel. Om det här värdet inte anges till TRUE försöker tjänsten identifiera motsvarande lokala VMDK på bästa möjliga sätt. Om rätt VMDK inte hittas skapas en extra disk och data skrivs till den.

## <a name="alternate-location-recovery-alr"></a>Återställning av alternativ plats (återställning till)
Om den lokala virtuella datorn inte finns innan du återskapar den virtuella datorn, kallas scenariot en alternativ plats återställning. Arbets flödet för att skydda på nytt skapar den lokala virtuella datorn igen. Detta leder också till en fullständig data hämtning.

* När du växlar tillbaka till en annan plats återställs den virtuella datorn till samma ESX-värd som huvud mål servern har distribuerats till. Data lagret som används för att skapa disken är samma data lager som valdes när den virtuella datorn skulle skyddas.
* Du kan bara växla tillbaka till ett fil system för virtuell dator (VMFS) eller virtuellt San data lager. Om du har en RDM-åtgärd fungerar inte skydd och återställning efter fel.
* Skydd innebär en stor inledande data överföring som följs av ändringarna. Den här processen finns eftersom den virtuella datorn inte finns på plats. Fullständiga data måste replikeras tillbaka. Den här skyddet tar också längre tid än en ursprunglig plats återställning.
* Det går inte att växla tillbaka till RDM-baserade diskar. Det går bara att skapa nya virtuella dator diskar (VMDK: er) i ett VMFS/virtuellt San-datalager.

> [!NOTE]
> En fysisk dator, vid växling till Azure, kan inte återställas bara som en virtuell VMware-dator. Detta följer samma arbets flöde som den alternativa plats återställningen. Se till att du identifierar minst en huvud mål server och de ESX/ESXi-värdar som du behöver för att återställa.

## <a name="next-steps"></a>Nästa steg

Följ stegen för att utföra [återställnings åtgärden](vmware-azure-failback.md).

