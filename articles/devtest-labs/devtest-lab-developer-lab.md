---
title: Använda Azure DevTest Labs för utvecklare | Microsoft Docs
description: Lär dig mer om Azure DevTest Labs funktioner som kan användas för att uppfylla kraven för utvecklare och detaljerade steg som du kan följa för att ställa in ett labb.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4f195f843e30ad1a56bad7d5b9ca9e73888783b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89296251"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Använd Azure DevTest Labs för utvecklare
Azure DevTest Labs kan användas för att implementera många viktiga scenarier, men ett av de primära involverar användning av DevTest Labs som värd för utvecklingsdatorer för utvecklare. I det här scenariot har DevTest Labs följande fördelar:

- Utvecklare kan snabbt etablera sina utvecklings maskiner på begäran.
- Utvecklare kan enkelt anpassa sina utvecklingsdatorer när så behövs.
- Administratörer kan kontrol lera kostnaderna genom att se till att:
  - Utvecklare kan inte få fler virtuella datorer än de behöver för utveckling.
  - Virtuella datorer stängs av när de inte används. 

![Använda DevTest Labs för utbildning](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

I den här artikeln får du lära dig mer om olika Azure DevTest Labs funktioner som kan användas för att uppfylla kraven för utvecklare och detaljerade steg som du kan följa för att konfigurera ett labb.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementera utvecklings miljöer med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labb är start punkten i Azure DevTest Labs. När du har skapat ett labb kan du utföra uppgifter som att lägga till användare (utvecklare) i labbet, ange principer för att kontrol lera kostnader, definiera avbildningar av virtuella datorer som kan skapas snabbt och mer.  
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb i Azure DevTest Labs](devtest-lab-create-lab.md) |Lär dig hur du skapar ett labb i Azure DevTest Labs i Azure Portal. |
2. **Skapa virtuella datorer på några minuter med färdiga Marketplace-avbildningar och anpassade avbildningar** 
   
    Du kan välja färdiga avbildningar från en mängd olika avbildningar på Azure Marketplace och göra dem tillgängliga i labbet. Om de färdiga avbildningarna inte uppfyller dina krav kan du skapa en anpassad avbildning genom att skapa en virtuell labb dator med en färdig avbildning från Azure Marketplace, installera all program vara som du behöver och spara den virtuella datorn som en anpassad avbildning i labbet.

    Om du kommer att använda anpassade avbildningar kan du överväga att använda en avbildnings fabrik för att skapa och distribuera dina avbildningar. En avbildnings fabrik är en konfigurations-som-kod-lösning som regelbundet skapar och distribuerar dina konfigurerade avbildningar automatiskt. Detta sparar den tid som krävs för att konfigurera systemet manuellt när en virtuell dator har skapats med bas operativ systemet.
  
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera Azure Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) |Lär dig hur du kan tillåta Azure Marketplace-avbildningar, vilket gör det möjligt att välja enbart de avbildningar som du vill använda för utvecklarna.|
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att förinstallera den program vara du behöver så att utvecklare snabbt kan skapa en virtuell dator med hjälp av den anpassade avbildningen.|
   | [Lär dig mer om image Factory](./devtest-lab-faq.md#blog-post) |Titta på en video som beskriver hur du konfigurerar och använder en avbildnings fabrik.|

3. **Skapa åter användnings bara mallar för Developer Machines** 
   
    En formel i Azure DevTest Labs är en lista över standard egenskaps värden som används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en avbildning, en VM-storlek (en kombination av processor och RAM) och ett virtuellt nätverk. Varje utvecklare kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Hantera DevTest Labs-formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att välja en avbildning, VM-storlek (kombination av processor och RAM) och ett virtuellt nätverk.|

4. **Skapa artefakter för att aktivera flexibel VM-anpassning**

   Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har tillhandahållits. Artefakter kan vara:

   - Verktyg som du vill installera på den virtuella datorn, till exempel agenter, Fiddler och Visual Studio.
   - Åtgärder som du vill köra på den virtuella datorn, till exempel kloning av en lagrings platsen.
   - Program som du vill testa.

   Många artefakter finns redan i rutan. Du kan skapa dina egna anpassade artefakter om du vill ha mer anpassning för dina specifika behov.

   Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Skapa anpassade artefakter för den virtuella DevTest Labs-datorn](devtest-lab-artifact-author.md) |Skapa dina egna anpassade artefakter för de virtuella datorerna i labbet.|
   | [Lägg till en git-lagringsplats för att lagra anpassade artefakter och Azure Resource Manager mallar för användning i Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Lär dig hur du lagrar dina anpassade artefakter i din egen privata git-lagrings platsen.|

5. **Kontrollera kostnader**
   
    Med Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas av en utvecklare i labbet. 
   
    Om Developer-teamet har ett angivet arbets schema och du vill stoppa alla virtuella datorer vid en viss tidpunkt, och sedan automatiskt starta om dem på följande dag, kan du enkelt göra det genom att ange principer för automatisk avstängning och automatisk start i labbet. 
   
    Slutligen kan du ta bort alla virtuella datorer samtidigt genom att köra ett enda PowerShell-skript när app-utveckling är klar. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrol lera kostnaderna genom att ställa in principer i labbet. |
   | [Ta bort alla virtuella labb datorer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labb i en åtgärd när utvecklingen är klar.|

1. **Lägga till ett virtuellt nätverk till en virtuell dator** 
   
    DevTest Labs skapar ett nytt virtuellt nätverk (VNET) när ett labb skapas. Om du har konfigurerat ditt eget VNET – till exempel genom att använda ExpressRoute eller plats-till-plats-VPN – kan du lägga till det virtuella nätverket i dina labbs inställningar för virtuella nätverk så att det blir tillgängligt när du skapar virtuella datorer.

    Dessutom finns det en tillgänglig Azure Active Directory domän kopplings artefakt som ansluter en virtuell dator till en domän när den virtuella datorn skapas. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md) |Lär dig hur du konfigurerar ett virtuellt nätverk i Azure DevTest Labs att använda Azure Portal.|

6. **Dela labbet med varje utvecklare**
   
    Labb kan nås direkt med hjälp av en länk som du delar med dina utvecklare. De behöver inte ens ha ett Azure-konto, så länge de har en [Microsoft-konto](devtest-lab-faq.md#what-is-a-microsoft-account). Utvecklare kan inte se virtuella datorer som skapats av andra utvecklare.  
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Lägg till en utvecklare i ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använd Azure Portal för att lägga till utvecklare i labbet.|
   | [Lägga till utvecklare i labbet med hjälp av ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera att lägga till utvecklare i labbet. |
   | [Hämta en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Lär dig hur utvecklare kan komma åt ett labb via en hyperlänk direkt.|

7. **Automatisera labb skapande för fler team** 
   
    Du kan automatisera labb skapande, inklusive anpassade inställningar, genom att skapa en Resource Manager-mall och använda den för att skapa identiska labb igen och igen. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb med en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
