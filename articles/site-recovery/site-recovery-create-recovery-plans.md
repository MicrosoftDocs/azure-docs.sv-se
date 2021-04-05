---
title: Skapa/anpassa återställnings planer i Azure Site Recovery
description: Lär dig hur du skapar och anpassar återställnings planer för haveri beredskap med hjälp av Azure Site Recovery-tjänsten.
ms.topic: how-to
ms.date: 01/23/2020
ms.openlocfilehash: 0dcde98e8dcaef12896c18c25429f0ba7b1b27d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009729"
---
# <a name="create-and-customize-recovery-plans"></a>Skapa och anpassa återställningsplaner

Den här artikeln beskriver hur du skapar och anpassar en återställnings plan för redundans i [Azure Site Recovery](site-recovery-overview.md). Innan du börjar kan du [läsa mer](recovery-plan-overview.md) om återställnings planer.

## <a name="create-a-recovery-plan"></a>Skapa en återställningsplan

1. I Recovery Services-valvet väljer du **återställnings planer (Site Recovery)**  >  **+ återställnings plan**.
2. I **skapa återställnings plan** anger du ett namn för planen.
3. Välj en källa och ett mål baserat på datorerna i planen och välj **Resource Manager** för distributions modellen. Käll platsen måste ha datorer som är aktiverade för redundans och återställning. 

    **Redundans** | **Källa** | **Mål** 
   --- | --- | ---
   Azure till Azure | Välj Azure-region | Välj Azure-region
   VMware till Azure | Välj konfigurations Server | Välj Azure
   Fysiska datorer till Azure | Välj konfigurations Server | Välj Azure   
   Hyper-V till Azure | Välj namnet på Hyper-V-platsen | Välj Azure
   Hyper-V (hanteras av VMM) till Azure  | Välj VMM-Server | Välj Azure
  
    . Tänk på följande:
    - Du kan använda en återställnings plan för både redundans till Azure och återställning efter fel från Azure.
    - Käll platsen måste ha datorer som är aktiverade för redundans och återställning.
    - En återställnings plan kan innehålla datorer med samma källa och mål.
    - Du kan inkludera virtuella VMware-datorer och virtuella Hyper-V-datorer som hanteras av VMM i samma plan.
    - Virtuella VMware-datorer och fysiska servrar kan vara i samma plan.

4. I **Välj objekt virtuella datorer** väljer du de datorer (eller replikeringsgruppen) som du vill lägga till i planen. Klicka sedan på **OK**.
    - Datorer läggs till som standard grupp (grupp 1) i planen. Efter redundansväxlingen startar alla datorer i den här gruppen på samma tidpunkt.
    - Du kan bara välja datorer på käll-och mål platserna som du har angett. 
5. Skapa planen genom att klicka på **OK** .

## <a name="add-a-group-to-a-plan"></a>Lägg till en grupp i en plan

Du skapar ytterligare grupper och lägger till datorer i olika grupper så att du kan ange olika beteenden per grupp. Du kan till exempel ange när datorer i en grupp ska starta efter redundansväxlingen eller ange anpassade åtgärder per grupp.

1. I **återställnings planer** högerklickar du på planen > **Anpassa**. När du har skapat en plan finns alla datorer som du har lagt till i standard grupp 1.
2. Klicka på **+ grupp**. Som standard är en ny grupp numrerad i den ordning som den har lagts till. Du kan ha upp till sju grupper.
3. Välj den dator som du vill flytta till den nya gruppen, klicka på **Ändra grupp** och välj sedan den nya gruppen. Du kan också högerklicka på grupp namnet > **skyddat objekt** och lägga till datorer i gruppen. En dator eller replikeringsgrupp kan bara tillhöra en grupp i en återställnings plan.


## <a name="add-a-script-or-manual-action"></a>Lägg till ett skript eller en manuell åtgärd

Du kan anpassa en återställnings plan genom att lägga till ett skript eller en manuell åtgärd. Tänk på följande:

- Om du replikerar till Azure kan du integrera Azure Automation-runbooks i din återställnings plan. [Läs mer](site-recovery-runbook-automation.md).
- Om du replikerar virtuella Hyper-V-datorer som hanteras av System Center VMM kan du skapa ett skript på den lokala VMM-servern och inkludera det i återställnings planen.
- När du lägger till ett skript lägger till en ny uppsättning åtgärder för gruppen. En uppsättning för steg för grupp 1 skapas till exempel med namnet *grupp 1: för-steg*. Alla för steg visas i den här uppsättningen. Du kan bara lägga till ett skript på den primära platsen om du har distribuerat en VMM-Server.
- Om du lägger till en manuell åtgärd när återställnings planen körs, stoppas den vid den tidpunkt då du infogade den manuella åtgärden. En dialog ruta visas där du får ange att den manuella åtgärden har slutförts.
- Följ anvisningarna i [den här artikeln](hyper-v-vmm-recovery-script.md)om du vill skapa ett skript på VMM-servern.
- Skript kan tillämpas vid redundansväxling till den sekundära platsen och under återställning efter fel från den sekundära platsen till den primära platsen. Supporten beror på ditt scenario för replikering:
    
    **Scenario** | **Redundans** | **Återställ**
    --- | --- | --- 
    Azure till Azure  | Runbook | Runbook
    VMware till Azure | Runbook | NA 
    Hyper-V med VMM till Azure | Runbook | Skript
    Hyper-V-plats till Azure | Runbook | NA
    VMM till sekundär VMM | Skript | Skript

1. I återställnings planen klickar du på steget som åtgärden ska läggas till i och anger när åtgärden ska utföras:
    1. Om du vill att åtgärden ska utföras innan datorerna i gruppen startas efter redundansväxlingen väljer du **Lägg till före åtgärd**.
    1. Om du vill att åtgärden ska utföras efter att datorerna i gruppen har startats efter redundansväxlingen väljer du **Lägg till post-åtgärd**. Om du vill flytta åtgärdens position väljer du knapparna **Flytta upp** eller **Flytta ned** .
2. I **Infoga åtgärd** väljer du **skript** eller **manuell åtgärd**.
3. Gör så här om du vill lägga till en manuell åtgärd:
    1. Ange ett namn för åtgärden och skriv instruktions instruktioner. Den person som kör redundansväxlingen kommer att se dessa instruktioner.
    1. Ange om du vill lägga till den manuella åtgärden för alla typer av redundans (test, redundans, planerad redundansväxling (om det behövs)). Klicka sedan på **OK**.
4. Gör så här om du vill lägga till ett skript:
    1. Om du lägger till ett VMM-skript väljer du **redundans till VMM-skript** och anger den relativa sökvägen till resursen i **skript Sök väg** . Om resursen till exempel finns på \\ \<VMMServerName> \MSSCVMMLibrary\RPScripts anger du sökvägen: \RPScripts\RPScript.PS1.
    1. Om du lägger till en Azure Automation-körnings bok anger du **Azure Automation kontot** där runbooken finns och väljer lämpligt **Azure Runbook-skript**.
5. Kör ett redundanstest för återställnings planen för att kontrol lera att skriptet fungerar som förväntat.

## <a name="watch-a-video"></a>Se ett videoklipp

Titta på en video som visar hur du skapar en återställnings plan.


> [!VIDEO https://www.youtube.com/embed/1KUVdtvGqw8]

## <a name="next-steps"></a>Nästa steg

Läs mer om att [köra redundans](site-recovery-failover.md).  

    
