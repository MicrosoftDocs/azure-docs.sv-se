---
title: Använd Azure DevTest Labs för VM-och PaaS-testmiljöer | Microsoft Docs
description: Lär dig hur du använder Azure DevTest Labs för virtuella PaaS-och test miljö scenarier.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 978071aa160098612a5effebfb30d5d58b777df7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89299838"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Använd Azure DevTest Labs för VM-och PaaS-testmiljöer

Du kan använda Azure DevTest Labs för att implementera många viktiga scenarier, men ett av de primära involverar användning av DevTest Labs som värd för datorer för testare. 

I det här scenariot har DevTest Labs följande fördelar:

- Testarna kan testa den senaste versionen av sitt program genom att snabbt etablera Windows- och Linux-miljöer med återanvändningsbara mallar och artefakter.
- Testarna kan skala upp sin belastningstestning genom att etablera flera testagenter.
- Administratörer kan kontrol lera kostnaderna genom att se till att:
  - Testare kan inte hämta fler virtuella datorer än de behöver.
  - Virtuella datorer stängs av när de inte används.

![Använda DevTest Labs för utbildning](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

I den här artikeln får du lära dig mer om olika Azure DevTest Labs funktioner som används för att uppfylla test krav och detaljerade steg som du följer för att konfigurera ett labb.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementera test miljöer med Azure DevTest Labs
1. **Skapa labbet** 
   
    Labb är start punkten i Azure DevTest Labs. När du har skapat ett labb kan du utföra uppgifter som att lägga till användare (testare) i labbet, ange principer för att kontrol lera kostnader, definiera avbildningar av virtuella datorer som kan skapas snabbt och mer.  
   
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
   | [Konfigurera Azure Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) |Lär dig hur du kan tillåta Azure Marketplace-avbildningar, vilket gör det möjligt att välja enbart de avbildningar som du vill använda för testare.|
   | [Skapa en anpassad avbildning](devtest-lab-create-template.md) |Skapa en anpassad avbildning genom att förinstallera den program vara du behöver så att testare snabbt kan skapa en virtuell dator med hjälp av den anpassade avbildningen.|
   | [Lär dig mer om image Factory](./devtest-lab-faq.md#blog-post) |Titta på en video som beskriver hur du konfigurerar och använder en avbildnings fabrik.|

3. **Skapa åter användnings bara mallar för test datorer** 
   
    En formel i Azure DevTest Labs är en lista över standard egenskaps värden som används för att skapa en virtuell dator. Du kan skapa en formel i labbet genom att välja en avbildning, en VM-storlek (en kombination av processor och RAM) och ett virtuellt nätverk. Varje testare kan se formeln i labbet och använda den för att skapa en virtuell dator. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Hantera DevTest Labs-formler för att skapa virtuella datorer](devtest-lab-manage-formulas.md) |Lär dig hur du kan skapa en formel genom att välja en avbildning, VM-storlek (kombination av processor och RAM) och ett virtuellt nätverk.|

3. **Skapa test miljöer för flera virtuella datorer** 
   
    Du kan använda Azure Resource Manager mallar för att definiera infrastrukturen och konfigurationen av din Azure-lösning och flera gånger distribuera flera virtuella test datorer i ett konsekvent tillstånd.

    Azure PaaS-resurser kan tillhandahållas i en miljö från en Resource Manager-mall och visas i kostnads uppföljning. Automatisk avstängning av den virtuella datorn gäller dock inte för PaaS-resurser.

    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Skapa miljöer med flera virtuella datorer och PaaS-resurser med Azure Resource Manager-mallar](devtest-lab-create-environment-from-arm.md) |Lär dig hur du kan distribuera flera virtuella datorer i ett konsekvent tillstånd för din test miljö.|

4. **Skapa artefakter för att aktivera flexibel VM-anpassning**

   Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har tillhandahållits. Artefakter kan vara:

   - Verktyg som du vill installera på den virtuella datorn, till exempel agenter, Fiddler och Visual Studio.
   - Åtgärder som du vill köra på den virtuella datorn, till exempel kloning av en lagrings platsen.
   - Program som du vill testa.

   Många artefakter finns redan i rutan. Men om du vill ha mer anpassning för dina specifika behov kan du skapa dina egna anpassade artefakter.

   Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Skapa anpassade artefakter för den virtuella DevTest Labs-datorn](devtest-lab-artifact-author.md) |Skapa dina egna anpassade artefakter för de virtuella datorerna i labbet.|
   | [Lägg till en git-lagringsplats för att lagra anpassade artefakter och Azure Resource Manager mallar för användning i Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Lär dig hur du lagrar dina anpassade artefakter i din egen privata git-lagrings platsen.|

5. **Kontrollera kostnader**
   
    Med Azure DevTest Labs kan du ange en princip i labbet för att ange det maximala antalet virtuella datorer som kan skapas av en testare i labbet. 
   
    Om ditt test team har ett angivet arbets schema och du vill stoppa alla virtuella datorer vid en viss tid på dagen och sedan automatiskt starta om dem på följande dag, kan du enkelt göra det genom att ställa in principer för automatisk avstängning och automatisk start i labbet. 
   
    Slutligen kan du ta bort alla virtuella datorer samtidigt genom att köra ett enda PowerShell-skript när app-utveckling är klar. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Definiera labbprinciper](devtest-lab-set-lab-policy.md) |Kontrol lera kostnaderna genom att ställa in principer i labbet. |
   | [Ta bort alla virtuella labb datorer med ett PowerShell-skript](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Ta bort alla labb i en åtgärd när testningen är klar.|

1. **Lägga till ett virtuellt nätverk i ett labb** 
   
    DevTest Labs skapar ett nytt virtuellt nätverk (VNET) när ett labb skapas. Om du har konfigurerat ditt eget VNET – till exempel genom att använda ExpressRoute eller plats-till-plats-VPN – kan du lägga till det virtuella nätverket i dina labbs inställningar för virtuella nätverk så att det blir tillgängligt när du skapar virtuella datorer.

    Dessutom finns det en tillgänglig Azure Active Directory domän kopplings artefakt som ansluter en virtuell dator till en domän när den virtuella datorn skapas. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Konfigurera ett virtuellt nätverk i Azure DevTest Labs](devtest-lab-configure-vnet.md) |Lär dig hur du konfigurerar ett virtuellt nätverk i Azure DevTest Labs att använda Azure Portal.|

6. **Dela labbet med varje testare**
   
    Labb kan nås direkt med en länk som du delar med dina tester. De behöver inte ens ha ett Azure-konto, så länge de har en [Microsoft-konto](devtest-lab-faq.md#what-is-a-microsoft-account). Testare kan inte se virtuella datorer som skapats av andra tester.  
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Lägg till en testare i ett labb i Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Använd Azure Portal för att lägga till tester i labbet.|
   | [Lägga till tester i labbet med hjälp av ett PowerShell-skript](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Använd PowerShell för att automatisera testning av tester i labbet. |
   | [Hämta en länk till labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Lär dig hur testare kan få direkt åtkomst till ett labb via en hyperlänk.|

7. **Automatisera labb skapande för fler team** 
   
    Du kan automatisera labb skapande, inklusive anpassade inställningar, genom att skapa en Resource Manager-mall och använda den för att skapa identiska labb igen och igen. 
   
    Läs mer genom att klicka på länkarna i följande tabell:
   
   | Uppgift | Detta får du får lära dig |
   | --- | --- |
   | [Skapa ett labb med en Resource Manager-mall](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Skapa labb i Azure DevTest Labs med Resource Manager-mallar. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
