---
title: Översikt över Microsoft Azure StorSimple Data Manager | Microsoft Docs
description: Lär dig mer om StorSimple Data Manager lösning och hur du kan använda den här tjänsten för att skriva program som använder StorSimple-data och andra Azure-tjänster.
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/21/2018
ms.author: alkohli
ms.openlocfilehash: d683f49cadb384ef59d3bae819156733691813cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88183504"
---
# <a name="storsimple-data-manager-solution-overview"></a>Översikt över StorSimple Data Manager lösning

## <a name="overview"></a>Översikt

Microsoft Azure StorSimple använder moln lagring som en förlängning av den lokala lösningen och data skiktas automatiskt i den lokala lagringen och i molnet. Data lagras i molnet i ett deduplicerat och komprimerat format för högsta möjliga effektivitet och lägre kostnader. När data lagras i StorSimple-format är det inte lätt att använda andra moln program som du kanske vill använda.

Med StorSimple Data Manager kan du sömlöst komma åt och använda StorSimple-format data i molnet. Det gör detta genom att transformera StorSimple-formatet till interna blobbar och filer, som du kan använda med andra tjänster som Azure Media Services, Azure HDInsights och Azure Machine Learning.

Den här artikeln innehåller en översikt över den StorSimple Data Manager lösningen. Det förklarar också hur du kan använda den här tjänsten för att skriva program som använder StorSimple-data och andra Azure-tjänster i molnet.

## <a name="how-it-works"></a>Hur fungerar det?

Tjänsten StorSimple Data Manager identifierar StorSimple-data i molnet från en lokal enhet med StorSimple 8000-serien. StorSimple-data i molnet är deduplicerad, komprimerat StorSimple-format. Tjänsten Data Manager innehåller API: er för att extrahera StorSimple-format data och omvandla dem till andra format, till exempel Azure-blobbar och Azure Files. Dessa omvandlade data används sedan lätt av Azure HDInsight och Azure Media Services. Datatransformeringen gör det möjligt för dessa tjänster att arbeta med transformerade StorSimple-data från StorSimple 8000-serien på en lokal enhet. Det här flödet illustreras i följande diagram.

![Diagram på hög nivå](./media/storsimple-data-manager-overview/storsimple-data-manager-overview2.png)


## <a name="data-manager-use-cases"></a>Data Manager användnings fall

Du kan använda Data Manager med Azure Functions, Azure Automation och Azure Data Factory för att köra arbets flöden på dina data när de kommer in i StorSimple. Du kanske vill bearbeta ditt medie innehåll som du lagrar på StorSimple med Azure Media Services, eller köra en Machine Learning algoritm på dessa data eller hämta ett Hadoop-kluster för att analysera de data som du lagrar på StorSimple. Med den stora mängden av tjänster som är tillgängliga i Azure tillsammans med data på StorSimple kan du låsa upp kraften i dina data.


## <a name="region-availability"></a>Regional tillgänglighet

StorSimple Data Manager finns i följande 7 regioner:

 - Sydostasien
 - East US
 - USA, västra
 - USA, västra 2
 - USA, västra centrala
 - Europa, norra
 - Europa, västra

StorSimple Data Manager kan dock användas för att transformera data i följande regioner. 

![Regioner som är tillgängliga för data](./media/storsimple-data-manager-overview/data-manager-job-definition-different-regions-m.png)

Den här mängden är större eftersom resurs distributionen i någon av de ovanstående regionerna kan ta upp Transformations processen i regionerna nedan. Så länge dina data finns i någon av de 19 regionerna kan du omvandla dina data med hjälp av den här tjänsten.


## <a name="choosing-a-region"></a>Välja en region

Vi rekommenderar att du:
 - Ditt käll lagrings konto (det som är associerat med din StorSimple-enhet) och mål lagrings kontot (där du vill att data i det interna formatet) ska finnas i samma Azure-region.
 - Du tar upp din Data Manager och jobb definition i den region som innehåller StorSimple lagrings konto. Om detta inte är möjligt, så ta upp Data Manager i den närmaste Azure-regionen och skapa sedan jobb definitionen i samma region som ditt StorSimple-lagrings konto. 

    Om ditt StorSimple lagrings konto inte finns i de 26 regioner som stöder skapande av jobb definition, rekommenderar vi att du inte kör StorSimple Data Manager när du ser långa svars tider och potentiella utgående avgifter.
    
Microsoft strävar efter att se till att Azure-tjänster alltid är tillgängliga i alla regioner. Oplanerade drift avbrott kan dock inträffa under korta perioder i en viss region. I sådana fall kan du hämta en Data Manager och en jobb definition i en region som inte påverkas av avbrottet och köra Transformations jobbet. Du kan stöta på viss ytterligare svars tid i ett sådant scenario, men det kan vara din återställnings strategi i sällsynta fall av ett regionalt avbrott.

## <a name="security-considerations"></a>Säkerhetsöverväganden

StorSimple Data Manager behöver krypterings nyckeln för tjänst data som ska transformeras från StorSimple-format till ursprungligt format. Krypterings nyckeln för tjänst data genereras när den första enheten registreras med StorSimple-tjänsten. Mer information om den här nyckeln finns på [StorSimple Security](storsimple-8000-security.md).

Krypterings nyckeln för tjänst data som angavs som indata lagras i ett nyckel valv som skapas när du skapar en Data Manager. Valvet finns i samma Azure-region som din StorSimple Data Manager. Den här nyckeln tas bort när du tar bort din Data Manager-tjänst.

Den här nyckeln används av beräknings resurserna för att utföra omvandlingen. Dessa beräknings resurser finns i samma Azure-region som jobb definitionen. Den här regionen får inte vara samma som den region där du har Data Manager.

Om din Data Manager region skiljer sig från ditt jobb definitions område är det viktigt att du förstår vilka data/metadata som finns i var och en av dessa regioner. Följande diagram illustrerar effekterna av att ha olika regioner för Data Manager och jobb definition.

![Tjänst-och jobb definition i olika regioner](./media/storsimple-data-manager-overview/data-manager-job-different-regions.png)

## <a name="managing-personal-information"></a>Hantera personlig information

StorSimple Data Manager samlar eller visar inte någon personlig information. Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="known-limitations"></a>Kända begränsningar

Tjänsten har för närvarande följande begränsningar:
- StorSimple Data Manager fungerar för närvarande inte med volymer som är BitLocker-krypterade. Du kommer att se jobb felen om du försöker köra tjänsten med en krypterad enhet.
- Vissa metadata för filer (inklusive ACL: er) kommer inte att behållas i transformerade data.
- Den här tjänsten fungerar bara med NTFS-volymer.
- Fil Sök vägens längd måste vara kortare än 256 tecken. det går inte att utföra jobbet.

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager användar gränssnitt för att transformera data](storsimple-data-manager-ui.md).
