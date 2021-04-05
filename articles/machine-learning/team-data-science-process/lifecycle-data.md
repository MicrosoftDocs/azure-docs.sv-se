---
title: Data hämtning och förståelse för team data science process
description: Mål, uppgifter och slut produkter för data förvärv och förståelse av data kunskaps projekt
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fffb52e333bea1b2be11b127a9eab6656dc1d1f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94740336"
---
# <a name="data-acquisition-and-understanding-stage-of-the-team-data-science-process"></a>Data hämtning och förståelse av steg för team data vetenskaps processen

Den här artikeln beskriver de mål, uppgifter och slut produkter som är kopplade till data hämtningen och förståelse av steg för team data science-processen (TDSP). Den här processen ger en rekommenderad livs cykel som du kan använda för att strukturera data vetenskaps projekt. Livs cykeln beskriver de viktigaste faserna som projekt vanligt vis körs, ofta iterativt:

   1. **Förståelse för verksamheten**
   2. **Förvärv och förståelse av data**
   3. **Modellering**
   4. **Distribution**
   5. **Kundgodkännande**

Här är en visuell representation av TDSP-livs cykeln: 

![TDSP-livscykel](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Mål
* Skapa en ren data uppsättning med hög kvalitet vars relation till mål variablerna förstås. Leta upp data uppsättningen i lämplig analys miljö så att du är redo att modellera.
* Utveckla en lösnings arkitektur för data pipelinen som uppdaterar och uppdaterar data regelbundet.

## <a name="how-to-do-it"></a>Gör så här
Det finns tre huvudsakliga uppgifter i det här steget:

   * Mata in **data** i den analytiska mål miljön.
   * **Utforska data** för att avgöra om data kvaliteten är tillräcklig för att besvara frågan. 
   * **Konfigurera en datapipeline** för att skapa nya eller regelbundet uppdaterade data.

### <a name="ingest-the-data"></a>Mata in data
Konfigurera processen för att flytta data från käll platserna till de mål platser där du kör Analytics-åtgärder, till exempel utbildning och förutsägelser. För teknisk information och alternativ för att flytta data med olika Azure-datatjänster, se [Läs in data i lagrings miljöer för analys](ingest-data.md). 

### <a name="explore-the-data"></a>Utforska data
Innan du tränar dina modeller måste du utveckla en bra förståelse för dina data. Verkliga data uppsättningar är ofta brus, saknar värden eller har en värd för andra avvikelser. Du kan använda data Sammanfattning och visualisering för att granska kvaliteten på dina data och tillhandahålla den information som du behöver för att bearbeta data innan de är klara för modellering. Den här processen är ofta iterativ. Vägledning om hur du rensar data finns i [uppgifter för att förbereda data för förbättrad maskin inlärning](prepare-data.md).  

När du är nöjd med kvaliteten på de rensade data är nästa steg att bättre förstå de mönster som finns i data. Med den här data analysen kan du välja och utveckla en lämplig förutsägelse modell för målet. Leta efter bevis för hur väl anslutna data är till målet. Ta sedan reda på om det finns tillräckligt med data för att gå vidare med nästa modell steg. Den här processen är ofta iterativ. Du kan behöva hitta nya data källor med mer exakta eller mer relevanta data för att utöka den data uppsättning som ursprungligen identifierades i föregående steg. 

### <a name="set-up-a-data-pipeline"></a>Konfigurera en datapipeline
Förutom den första inmatningen och rensningen av data, behöver du vanligt vis skapa en process för att skapa nya data eller uppdatera informationen regelbundet som en del av en pågående inlärnings process. Poängen kan vara slutförd med en datapipeline eller ett arbets flöde. [Med artikeln flytta data från en SQL Server instans till Azure SQL Database med Azure Data Factory](move-sql-azure-adf.md) får du ett exempel på hur du konfigurerar en pipeline med [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

I det här steget utvecklar du en lösnings arkitektur för data pipelinen. Du utvecklar pipelinen parallellt med nästa steg i data vetenskaps projektet. Beroende på dina affärs behov och begränsningarna för dina befintliga system där den här lösningen integreras, kan pipelinen vara något av följande alternativ: 

   * Batch-baserad
   * Strömning eller real tid 
   * En hybrid 

## <a name="artifacts"></a>Artifacts
Följande är slut produkterna i det här skedet:

   * [Data kvalitets rapport](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/DataSummaryReport.md): den här rapporten innehåller data sammanfattningar, relationerna mellan varje attribut och mål, variabel rangordning med mera. 
   * **Lösnings arkitektur**: lösnings arkitekturen kan vara ett diagram eller en beskrivning av din datapipeline som du använder för att köra poängsättning eller förutsägelser på nya data när du har skapat en modell. Den innehåller också pipelinen för att träna om modellen baserat på nya data. Lagra dokumentet i [projekt](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Project) katalogen när du använder TDSP katalog struktur mal len.
   * **Kontroll punkts beslut**: innan du påbörjar fullständig teknik och modell utveckling kan du utvärdera om projektet för att avgöra om det förväntade värdet är tillräckligt för att fortsätta med det. Du kan till exempel vara redo att fortsätta, behöva samla in mer data eller överge projektet eftersom data inte finns för att besvara frågan.

## <a name="next-steps"></a>Nästa steg

Här följer länkar till varje steg i livs cykeln för TDSP:

   1. [Förståelse för verksamheten](lifecycle-business-understanding.md)
   2. [Förvärv och förståelse av data](lifecycle-data.md)
   3. [Modellering](lifecycle-modeling.md)
   4. [Distribution](lifecycle-deployment.md)
   5. [Kundgodkännande](lifecycle-acceptance.md)

Vi tillhandahåller fullständiga genom gångar som demonstrerar alla steg i processen för olika scenarier. [Exempel](walkthroughs.md) artikeln innehåller en lista över scenarier med länkar och miniatyr beskrivningar. Genom gången illustrerar hur du kombinerar moln, lokala verktyg och tjänster till ett arbets flöde eller en pipeline för att skapa ett intelligent program. 

Exempel på hur du kör steg i TDSPs som använder Azure Machine Learning Studio finns i [använda TDSP med Azure Machine Learning]().
