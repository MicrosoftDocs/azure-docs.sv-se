---
title: Använda Visual Studio på en virtuell Azure-dator
description: Använda Visual Studio på en virtuell Azure-dator.
author: cathysull
manager: cathys
ms.service: virtual-machines
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: cathys
keywords: VisualStudio
ms.openlocfilehash: a6acf27743f17941e14adc3f6deea903f4f5ca87
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562086"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio-avbildningar i Azure
Att använda Visual Studio i en förkonfigurerad virtuell dator i Azure är ett snabbt och enkelt sätt att gå från ingenting till en utvecklings miljö som är igång. System avbildningar med olika Visual Studio-konfigurationer är tillgängliga på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure).

Ny Azure-användare? [Skapa ett kostnads fritt Azure-konto](https://azure.microsoft.com/free).

> [!NOTE]
> Alla prenumerationer är inte berättigade att distribuera Windows 10-avbildningar. Mer information finns i [använda Windows-klienten i Azure för utveckling/testnings scenarier](./client-images.md)

## <a name="what-configurations-and-versions-are-available"></a>Vilka konfigurationer och versioner är tillgängliga?
Avbildningar för de senaste huvud versionerna, Visual Studio 2019, Visual Studio 2017 och Visual Studio 2015 finns på Azure Marketplace.  För varje släppt större version ser du den ursprungligen "lanserad till webb"-versionen (RTW) och de senaste uppdaterade versionerna.  Var och en av dessa versioner har Visual Studio Enterprise-och community-versionerna av Visual Studio.  De här avbildningarna uppdateras minst varje månad för att inkludera de senaste versionerna av Visual Studio och Windows.  Även om namnen på bilderna är desamma, innehåller varje avbildnings Beskrivning den installerade produkt versionen och bildens datum.

| Version                                                                                                                                                | Utgåvor              | Produktversion   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: senaste (version 16,8)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enterprise, community | Version 16.8.0    |
| Visual Studio 2019: RTW                         | Stora företag | Version 16.0.20    |
| Visual Studio 2017: senaste (version 15,9)           | Enterprise, community | Version 15.9.29   |
| Visual Studio 2017: RTW                             | Enterprise, community | Version 15.0.28  |
| Visual Studio 2015: senaste (uppdatering 3)               | Enterprise, community | Version 14.0.25431.01 |

> [!NOTE]
> I enlighet med Microsofts Service policy har den ursprungligen publicerade (RTW) versionen av Visual Studio 2015 gått ut för service. Visual Studio 2015 uppdatering 3 är den enda återstående versionen som erbjuds för produkt raden för Visual Studio 2015.

Mer information finns i hanterings [principen för Visual Studio](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Vilka funktioner är installerade?
Varje bild innehåller den rekommenderade funktions uppsättningen för Visual Studio-versionen. I allmänhet innehåller installationen:

* Alla tillgängliga arbets belastningar, inklusive varje arbets belastnings rekommenderade valfria komponenter
* .NET 4.6.2-och .NET 4,7-SDK: er, mål paket och Utvecklarverktyg
* Visual F #
* GitHub-tillägg för Visual Studio
* LINQ to SQL verktyg

Den kommando rad som används för att installera Visual Studio när avbildningarna skapas är följande:

```
    vs_enterprise.exe --allWorkloads --includeRecommended --passive ^
       add Microsoft.Net.Component.4.7.SDK ^
       add Microsoft.Net.Component.4.7.TargetingPack ^ 
       add Microsoft.Net.Component.4.6.2.SDK ^
       add Microsoft.Net.Component.4.6.2.TargetingPack ^
       add Microsoft.Net.ComponentGroup.4.7.DeveloperTools ^
       add Microsoft.VisualStudio.Component.FSharp ^
       add Component.GitHub.VisualStudio ^
       add Microsoft.VisualStudio.Component.LinqToSql
```

Om avbildningarna inte innehåller en Visual Studio-funktion som du behöver, lämna feedback via feedback-verktyget i det övre högra hörnet på sidan.

## <a name="what-size-vm-should-i-choose"></a>Vilken storlek ska jag välja för den virtuella datorn?
Azure erbjuder ett stort utbud av virtuella dator storlekar. Eftersom Visual Studio är ett kraftfullt program med flera trådar, vill du ha en VM-storlek som innehåller minst två processorer och 7 GB minne. Vi rekommenderar följande VM-storlekar för Visual Studio-avbildningar:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Mer information om de senaste dator storlekarna finns i [storlekar för virtuella Windows-datorer i Azure](../sizes.md).

Med Azure kan du balansera om ditt ursprungliga val genom att ändra storlek på den virtuella datorn. Du kan antingen etablera en ny virtuell dator med en lämplig storlek eller ändra storlek på den befintliga virtuella datorn till annan underliggande maskin vara. Mer information finns i [ändra storlek på en virtuell Windows-dator](./resize-vm.md).

## <a name="after-the-vm-is-running-whats-next"></a>Vad händer när den virtuella datorn körs?
Visual Studio följer modellen "ta med din egen licens" i Azure. Som vid en installation av tillverkarspecifik maskin vara är ett av de första stegen att licensiera din Visual Studio-installation. För att låsa upp Visual Studio, antingen:
- Logga in med ett Microsoft-konto som är associerat med en Visual Studio-prenumeration 
- Lås upp Visual Studio med produkt nyckeln som medföljde ditt första köp

Mer information finns i [Logga in i Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) och [så här låser du upp Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hur gör jag för att vill du spara den virtuella utvecklings datorn för framtida bruk eller team?

Spektrumet av utvecklings miljöer är enorma och det finns en verklig kostnad för att bygga ut mer komplexa miljöer. Oavsett din miljös konfiguration kan du spara eller avbilda en konfigurerad virtuell dator som en "bas avbildning" för framtida användning eller för andra medlemmar i din grupp. När du startar en ny virtuell dator kan du sedan etablera den från bas avbildningen i stället för Azure Marketplace-avbildningen.

En snabb sammanfattning: Använd system förberedelse verktyget (Sysprep) och Stäng av den virtuella datorn som körs och avbilda sedan *(bild 1)* den virtuella datorn som en bild via användar gränssnittet i Azure Portal. Azure sparar `.vhd` filen som innehåller avbildningen i det lagrings konto som du väljer. Den nya avbildningen visas sedan som en avbildnings resurs i prenumerationens lista över resurser.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Bild 1) Avbilda en avbildning via Azure Portal gränssnittet.*</center>

Mer information finns i [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](./capture-image-resource.md).

> [!IMPORTANT]
> Glöm inte att använda Sysprep för att förbereda den virtuella datorn. Om du saknar det steget kan Azure inte etablera en virtuell dator från avbildningen.

> [!NOTE]
> Du får fortfarande lite kostnad för lagring av avbildningarna, men den ökade kostnaden kan vara obetydlig jämfört med kostnaderna för att återskapa den virtuella datorn från grunden för varje grupp medlem som behöver en. Till exempel kostar det några dollar att skapa och lagra en 127 GB-avbildning för en månad som kan återanvändas av hela teamet. Dessa kostnader är dock obetydliga jämfört med timmar som varje medarbetare investerar för att bygga ut och validera en korrekt konfigurerad dev box för deras enskilda användning.

Dessutom kan utvecklings uppgifterna eller teknikerna behöva större skalning, t. ex. sorters utvecklings konfigurationer och flera datorkonfigurationer. Du kan använda Azure DevTest Labs för att skapa _recept_ som automatiserar skapandet av "gyllene bilder". Du kan också använda DevTest Labs för att hantera principer för gruppens virtuella datorer som körs. Att [använda Azure DevTest Labs för utvecklare](../../devtest-labs/devtest-lab-developer-lab.md) är den bästa källan för mer information om DevTest Labs.

## <a name="next-steps"></a>Nästa steg
Nu när du vet om de förkonfigurerade Visual Studio-avbildningarna är nästa steg att skapa en ny virtuell dator:

* [Skapa en virtuell dator via Azure Portal](quick-create-portal.md)
* [Översikt över Virtuella Windows-datorer](overview.md)