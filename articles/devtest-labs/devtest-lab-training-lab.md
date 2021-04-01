---
title: Använda Azure DevTest Labs för utbildning | Microsoft Docs
description: Den här artikeln innehåller detaljerade steg som du kan följa för att ställa in ett labb för utbildning i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2de9550c62f04286a4f9ad42238bfefb9846477
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89294857"
---
# <a name="use-azure-devtest-labs-for-training"></a>Använda Azure DevTest Labs för utbildning
Azure DevTest Labs kan användas för att implementera många viktiga scenarier förutom utveckling/testning. Ett av dessa scenarier är att ställa in ett labb för utbildning. Med Azure DevTest Labs kan du skapa ett labb där du kan ange anpassade mallar som varje fordonsbaserad kan använda för att skapa identiska och isolerade miljöer för utbildning. Du kan använda principer för att säkerställa att inlärnings miljöer endast är tillgängliga för varje tågen när de behöver dem och innehåller tillräckligt med resurser – till exempel virtuella datorer – krävs för utbildningen. Slutligen kan du enkelt dela labbet med eleverna, som de har åtkomst till med ett klick.

![Använda DevTest Labs för utbildning](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs uppfyller följande krav som krävs för att utföra utbildning i en virtuell miljö: 

* Eleverna kan inte se virtuella datorer som skapats av andra eleverna
* Varje utbildnings dator bör vara identisk
* Eleverna kan snabbt etablera sina utbildnings miljöer
* Kontrol lera kostnaden genom att se till att eleverna inte kan hämta fler virtuella datorer än de behöver för utbildningen och Stäng virtuella datorer när de inte använder dem
* Dela utbildnings labbet enkelt med varje fordonsbaserad
* Återanvänd utbildnings labbet igen och återigen

I den här artikeln får du lära dig mer om olika Azure DevTest Labs funktioner som kan användas för att uppfylla de tidigare beskrivna utbildnings kraven och detaljerade steg som du kan följa för att ställa in ett labb för utbildning.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementera utbildning med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labb är start punkten i Azure DevTest Labs. När du har skapat ett labb kan du utföra uppgifter som till exempel lägga till användare (eleverna) i labbet, ange principer för att kontrol lera kostnader, definiera VM-avbildningar som kan skapas snabbt och mer.   
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb i Azure DevTest Labs](devtest-lab-create-lab.md) |Lär dig hur du skapar ett labb i Azure DevTest Labs i Azure Portal. |
2. **Skapa utbildningsbaserade virtuella datorer på bara några minuter med färdiga Marketplace-avbildningar och anpassade avbildningar** 
   
    Du kan välja färdiga avbildningar från en mängd olika avbildningar på Azure Marketplace och göra dem tillgängliga för eleverna i labbet. Om de färdiga avbildningarna inte uppfyller dina krav kan du skapa en anpassad avbildning genom att skapa en virtuell labb dator med en färdig avbildning från Azure Marketplace, installera all program vara som du behöver för utbildningen och spara den virtuella datorn som en anpassad avbildning i labbet. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera Azure Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) |Lär dig hur du kan tillåta Azure Marketplace-avbildningar; gör bara tillgängligt för val av de bilder du vill använda för träningen. |
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att förinstallera den program vara du behöver för utbildningen så att eleverna snabbt kan skapa en virtuell dator med hjälp av den anpassade avbildningen. |
3. **Skapa åter användnings bara mallar för utbildnings datorer** 
   
    En formel i Azure DevTest Labs är en lista över standard egenskaps värden som används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en avbildning, en VM-storlek (en kombination av processor och RAM) och ett virtuellt nätverk. Varje tågen kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Hantera DevTest Labs-formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att välja en avbildning, VM-storlek (kombination av processor och RAM) och ett virtuellt nätverk. |
4. **Kontrollera kostnader**
   
    Med Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas av en ombordpersonal i labbet. 
   
    Om du utför utbildning på flera dagar och vill stoppa alla virtuella datorer vid en viss tidpunkt och sedan automatiskt starta om dem på följande dag, kan du enkelt göra det genom att ställa in principer för automatisk avstängning och automatisk start i labbet. 
   
    När inlärningen är klar kan du ta bort alla virtuella datorer samtidigt genom att köra ett enda PowerShell-skript. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrol lera kostnaderna genom att ställa in principer i labbet. |
   | [Ta bort alla virtuella labb datorer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labb i en åtgärd när utbildningen är klar. |
5. **Dela labbet med varje ombordpersonal**
   
    Labb kan nås direkt med en länk som du delar med din eleverna. Din eleverna behöver inte ens ha ett Azure-konto, så länge de har en [Microsoft-konto](devtest-lab-faq.md#what-is-a-microsoft-account). Eleverna kan inte se virtuella datorer som skapats av andra eleverna.  
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Lägg till en ombordpersonal i ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använd Azure Portal för att lägga till eleverna i utbildnings labbet. |
   | [Lägga till eleverna i labbet med hjälp av ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera tillägg av eleverna i utbildnings labbet. |
   | [Hämta en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Lär dig hur ett labb kan nås direkt via en hyperlänk. |
6. **Återanvänd labbet igen och återigen** 
   
    Du kan automatisera labb skapande, inklusive anpassade inställningar, genom att skapa en Resource Manager-mall och använda den för att skapa identiska labb igen och igen. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb med en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
