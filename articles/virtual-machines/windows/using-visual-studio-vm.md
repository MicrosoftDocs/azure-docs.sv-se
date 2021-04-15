---
title: Använda Visual Studio på en virtuell Azure-dator
description: Använda Visual Studio på en virtuell Azure-dator.
author: andysterland
manager: andster
ms.service: virtual-machines
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/17/2020
ms.author: andster
keywords: Visualstudio
ms.openlocfilehash: f26bec64012e0b0909b7df5422c57ff2cb1c347e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478564"
---
# <a name="visual-studio-images-on-azure"></a>Visual Studio avbildningar i Azure
Att Visual Studio en förkonfigurerad virtuell Azure-dator (VM) är ett snabbt och enkelt sätt att gå från ingenting till en aktiv utvecklingsmiljö. Systemavbildningar med Visual Studio konfigurationer är tillgängliga i [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images%3Bmicrosoft%3Bwindows&page=1&subcategories=application-infrastructure).

Ny Azure-användare? [Skapa ett kostnadsfritt Azure-konto.](https://azure.microsoft.com/free)

> [!NOTE]
> Alla prenumerationer är inte berättigade att distribuera Windows 10 avbildningar. Mer information finns i [Använda Windows-klienten i Azure för dev/test-scenarier](./client-images.md)

## <a name="what-configurations-and-versions-are-available"></a>Vilka konfigurationer och versioner är tillgängliga?
Avbildningar för de senaste större versionerna, Visual Studio 2019, Visual Studio 2017 och Visual Studio 2015, finns i Azure Marketplace.  För varje utgiven huvudversion ser du den ursprungligen "utgivna till webben"-versionen (RTW) och de senaste uppdaterade versionerna.  Var och en av dessa versioner Visual Studio Enterprise och de Visual Studio Community versionerna.  Dessa avbildningar uppdateras minst varje månad för att inkludera de senaste Visual Studio- och Windows-uppdateringarna.  Namnen på bilderna förblir desamma, men beskrivningen för varje avbildning innehåller den installerade produktversionen och avbildningens datum "från och med".

| Versionsversion                                                                                                                                                | Utgåvor              | Produktversion   |
|:--------------------------------------------------------------------------------------------------------------------------------------------------------------:|:---------------------:|:-----------------:|
| [Visual Studio 2019: Senaste (version 16.8)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftvisualstudio.visualstudio2019latest?tab=Overview) | Enterprise, Community | Version 16.8.0    |
| Visual Studio 2019: RTW                         | Stora företag | Version 16.0.20    |
| Visual Studio 2017: Senaste (version 15.9)           | Enterprise, Community | Version 15.9.29   |
| Visual Studio 2017: RTW                             | Enterprise, Community | Version 15.0.28  |
| Visual Studio 2015: Senaste (uppdatering 3)               | Enterprise, Community | Version 14.0.25431.01 |

> [!NOTE]
> I enlighet med Microsofts serviceprincip har den ursprungligen utgivna versionen av Visual Studio 2015 upphört att gälla för underhåll. Visual Studio 2015 Update 3 är den enda återstående versionen som erbjuds för produktraden Visual Studio 2015.

Mer information finns i [Visual Studio Serviceprincip](https://www.visualstudio.com/productinfo/vs-servicing-vs).

## <a name="what-features-are-installed"></a>Vilka funktioner är installerade?
Varje bild innehåller den rekommenderade funktionsuppsättningen för den Visual Studio utgåvan. I allmänhet omfattar installationen:

* Alla tillgängliga arbetsbelastningar, inklusive de rekommenderade valfria komponenterna för varje arbetsbelastning. Mer information om de arbetsbelastningar, komponenter och SDK:er som ingår Visual Studio finns i [Visual Studio dokumentationen](https://docs.microsoft.com/visualstudio/install/workload-and-component-ids)
* .NET 4.6.2- och .NET 4.7-SDK:er, målpaket och Utvecklarverktyg
* Visuellt objekt F #
* GitHub-tillägg för Visual Studio
* LINQ to SQL Tools

Den kommandorad som används för att Visual Studio när du skapar avbildningarna är följande:

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

Om bilderna inte innehåller en funktion Visual Studio som du behöver kan du ge feedback via feedbackverktyget i det övre högra hörnet på sidan.

## <a name="what-size-vm-should-i-choose"></a>Vilken storlek ska jag välja för virtuell dator?
Azure erbjuder ett komplett utbud av storlekar för virtuella datorer. Eftersom Visual Studio är ett kraftfullt flertrådat program vill du ha en VM-storlek som innehåller minst två processorer och 7 GB minne. Vi rekommenderar följande VM-storlekar för Visual Studio avbildningar:

   * Standard_D2_v3
   * Standard_D2s_v3
   * Standard_D4_v3
   * Standard_D4s_v3
   * Standard_D2_v2
   * Standard_D2S_v2
   * Standard_D3_v2
    
Mer information om de senaste datorstorlekarna finns [i Storlekar för virtuella Windows-datorer i Azure.](../sizes.md)

Med Azure kan du balansera om ditt första val genom att ändra storlek på den virtuella datorn. Du kan antingen etablera en ny virtuell dator med en lämpligare storlek eller ändra storlek på den befintliga virtuella datorn till en annan underliggande maskinvara. Mer information finns i Ändra storlek [på en virtuell Windows-dator.](./resize-vm.md)

## <a name="after-the-vm-is-running-whats-next"></a>Vad händer nu när den virtuella datorn körs?
Visual Studio följer modellen "Bring Your Own License" i Azure. Precis som med en installation på upphovsrättsskyddad maskinvara är ett av de första stegen att licensiera Visual Studio installation. Om du vill Visual Studio du antingen:
- Logga in med en Microsoft-konto som är associerad med en Visual Studio prenumeration 
- Lås Visual Studio med produktnyckeln som kom med ditt första köp

Mer information finns i Logga [in på Visual Studio](/visualstudio/ide/signing-in-to-visual-studio) och Låsa upp [Visual Studio](/visualstudio/ide/how-to-unlock-visual-studio).

## <a name="how-do-i-save-the-development-vm-for-future-or-team-use"></a>Hur gör jag för att du den virtuella utvecklingsdatorn för framtida användning eller för teamanvändning?

Spektrumet av utvecklingsmiljöer är enormt och det finns verkliga kostnader för att bygga ut de mer komplexa miljöerna. Oavsett din miljökonfiguration kan du spara eller avbilda den konfigurerade virtuella datorn som en "basavbildning" för framtida användning eller för andra medlemmar i ditt team. När du startar en ny virtuell dator etablerar du den sedan från basavbildningen i stället för Azure Marketplace avbildningen.

En snabb sammanfattning: Använd verktyget Systemförberedelse (Sysprep) och stäng av den virtuella dator som körs och avbilda *sedan (bild 1)* den virtuella datorn som en avbildning via användargränssnittet i Azure Portal. Azure sparar filen `.vhd` som innehåller avbildningen i det lagringskonto du väljer. Den nya bilden visas sedan som en bildresurs i prenumerationens lista över resurser.

<img src="media/using-visual-studio-vm/capture-vm.png" alt="Capture an image through the Azure portal UI" style="border:3px solid Silver; display: block; margin: auto;"><center>*(Bild 1) Skapa en avbildning via Azure Portal användargränssnitt.*</center>

Mer information finns i Skapa [en hanterad avbildning av en generaliserad virtuell dator i Azure](./capture-image-resource.md).

> [!IMPORTANT]
> Glöm inte att använda Sysprep för att förbereda den virtuella datorn. Om du missar det steget kan Azure inte etablera en virtuell dator från avbildningen.

> [!NOTE]
> Du ådrar dig fortfarande vissa kostnader för lagring av avbildningarna, men den inkrementella kostnaden kan jämföras med omkostnaderna för att återskapa den virtuella datorn från grunden för varje teammedlem som behöver en. Det kostar till exempel några dollar att skapa och lagra en avbildning på 127 GB under en månad som kan återanvändas av hela teamet. Dessa kostnader jämförs dock med de timmar som varje anställd investerar för att bygga ut och validera en korrekt konfigurerad utvecklingsruta för enskild användning.

Dessutom kan dina utvecklingsuppgifter eller tekniker behöva mer skala, till exempel olika typer av utvecklingskonfigurationer och flera datorkonfigurationer. Du kan använda Azure DevTest Labs för att _skapa recept_ som automatiserar bygget av din "gyllene bild". Du kan också använda DevTest Labs för att hantera principer för teamets virtuella datorer som körs. [Att Azure DevTest Labs för utvecklare](../../devtest-labs/devtest-lab-developer-lab.md) är den bästa källan för mer information om DevTest Labs.

## <a name="next-steps"></a>Nästa steg
Nu när du känner till de förkonfigurerade Visual Studio är nästa steg att skapa en ny virtuell dator:

* [Skapa en virtuell dator via Azure Portal](quick-create-portal.md)
* [Virtuella Windows-datorer översikt](overview.md)
