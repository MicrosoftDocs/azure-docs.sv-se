---
title: Skapa en avbildnings fabrik i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln visar hur du konfigurerar en anpassad avbildnings fabrik med hjälp av exempel skript som är tillgängliga i git-lagringsplatsen (Azure DevTest Labs).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "85482079"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Skapa en anpassad avbildnings fabrik i Azure DevTest Labs
Den här artikeln visar hur du konfigurerar en anpassad avbildnings fabrik med hjälp av exempel skript som är tillgängliga i [git-lagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>Vad är en image Factory?
En avbildnings fabrik är en konfigurations-som-kod-lösning som skapar och distribuerar bilder automatiskt regelbundet med alla önskade konfigurationer. Avbildningarna i avbildnings fabriken är alltid uppdaterade och det löpande underhållet är nästan noll när hela processen automatiseras. Eftersom alla konfigurationer som krävs redan finns i avbildningen sparar den tiden från att manuellt konfigurera systemet när en virtuell dator har skapats med bas operativ systemet.

Den viktigaste acceleratorn för att få en utvecklare skriv bord till ett klart tillstånd i DevTest Labs använder anpassade avbildningar. Nack delen med anpassade bilder är att det finns något extra att underhålla i labbet. Till exempel gäller utvärderings versioner av produkter som går ut över tid (eller) nyligen utgivna säkerhets uppdateringar som inte tillämpas, vilket tvingar oss att uppdatera den anpassade avbildningen med jämna mellanrum. Med en avbildnings fabrik har du en definition av bilden som marker ATS i käll kods kontrollen och har en automatiserad process för att skapa anpassade avbildningar baserat på definitionen.

Lösningen gör det möjligt att skapa virtuella datorer från anpassade avbildningar samtidigt som du eliminerar ytterligare löpande underhålls kostnader. Med den här lösningen kan du automatiskt skapa anpassade avbildningar, distribuera dem till andra DevTest Labs och dra tillbaka de gamla avbildningarna. I följande videoklipp får du lära dig om avbildnings fabriken och hur den implementeras med DevTest Labs.  Alla Azure PowerShell-skript är tillgängliga och finns här:  [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Övergripande överblick över lösningen
Lösningen gör det möjligt att skapa virtuella datorer från anpassade avbildningar samtidigt som du eliminerar ytterligare löpande underhålls kostnader. Med den här lösningen kan du automatiskt skapa anpassade avbildningar och distribuera dem till andra DevTest Labs. Du använder Azure DevOps (tidigare Visual Studio Team Services) som Orchestration-motor för automatisering av alla åtgärder i DevTest-labben.

![Övergripande överblick över lösningen](./media/create-image-factory/high-level-view-of-solution.png)

Det finns ett [VSTS-tillägg för DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) som gör att du kan utföra dessa enskilda steg:

- Skapa anpassad bild
- Skapa en virtuell dator
- Ta bort virtuell dator
- Skapa miljö
- Ta bort miljö
- Fyll i miljö

Att använda DevTest Labs-tillägget är ett enkelt sätt att komma igång med att automatiskt skapa anpassade avbildningar i DevTest Labs.

Det finns en alternativ implementering med PowerShell-skript för ett mer komplext scenario. Med hjälp av PowerShell kan du helt automatisera en avbildnings fabrik baserat på DevTest Labs som kan användas i verktygskedjan för kontinuerlig integrering och kontinuerlig leverans (CI/CD). Principerna som följer i denna alternativa lösning är:

- Vanliga uppdateringar kräver inga ändringar i avbildnings fabriken. (du kan till exempel lägga till en ny typ av anpassad avbildning, automatiskt ta bort gamla bilder, lägga till en ny "slut punkt" DevTest Labs för att ta emot anpassade avbildningar och så vidare.)
- Vanliga ändringar backas upp av käll kods kontroll (infrastruktur som kod)
- DevTest Labs som tar emot anpassade avbildningar kanske inte är i samma Azure-prenumeration (labb span-prenumerationer)
- PowerShell-skript måste återanvändas så att vi kan skapa ytterligare fabriker efter behov

## <a name="next-steps"></a>Nästa steg
Gå vidare till nästa artikel i det här avsnittet: [köra en avbildnings fabrik från Azure DevOps](image-factory-set-up-devops-lab.md)
