---
title: Översikt över Azure Data Lake Analytics
description: Med Data Lake Analytics kan du driva din verksamhet med insikter som du har fått via dina molndata oavsett skala.
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: overview
ms.date: 06/23/2017
ms.openlocfilehash: f2916b45c04aac3e36e8dfb82a6bb9b332f55286
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102449160"
---
# <a name="what-is-azure-data-lake-analytics"></a>Vad är Azure Data Lake Analytics?

Azure Data Lake Analytics är en tjänst på begäran som gör det enklare att analysera stordata. I stället för att distribuera, konfigurera och justera maskinvara, kan du skriva frågor genom vilka du kan omvandla dina data och extrahera värdefulla insikter. Med Analytics-tjänsten kan du hantera jobb av alla skalor direkt bara genom att ange hur mycket kraft du behöver. Du betalar endast för jobbet när tjänsten körs vilket gör den kostnadseffektiv. 

## <a name="azure-data-lake-analytics-recent-update-information"></a>Senaste uppdaterings information för Azure Data Lake Analytics

Azure Data Lake Analytics-tjänsten uppdateras på ett aperiodic-baserat för visst ändamål. Vi fortsätter att tillhandahålla support för den här tjänsten med komponent uppdatering, komponent beta för hands version och så vidare. 

- För senaste uppdatering allmän information, se [vad som är nytt i data Lake Analytics?](data-lake-analytics-whats-new.md).
- Information om varje uppdatering finns i [versions anteckningen Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).

## <a name="dynamic-scaling"></a>Dynamisk skalning
  
Data Lake Analytics etablerar resurser dynamiskt och du kan utföra analyser på terabyte eller även petabyte av data. Du betalar endast för bearbetningen du använt. När du ökar eller minskar storleken på data som lagras eller mängden beräkning som används behöver du inte skriva om kod. 

## <a name="develop-faster-debug-and-optimize-smarter-using-familiar-tools"></a>Utveckla snabbare, felsök och optimera smartare med hjälp av välbekanta verktyg
  
Data Lake Analytics djupintegreras med Visual Studio. Du kan använda välbekanta verktyg för att köra, felsöka och justera koden. Med visualiseringar av dina U-SQL-jobb kan du se hur koden körs i skala, så att du lätt kan identifiera prestandaflaskhalsar och optimera kostnader.

## <a name="u-sql-simple-and-familiar-powerful-and-extensible"></a>U-SQL: Enkelt och bekant, kraftfullt och utökningsbart
  
Data Lake Analytics innehåller U-SQL, ett frågespråk som utökar den bekanta, enkla, deklarativa kompetensen hos SQL med den expressiva kraften i C#. U-SQL-språket använder samma distribuerade körning som används av Microsofts interna exabyteskaliga Data Lake. SQL- och .NET-utvecklare kan nu bearbeta och analysera alla sina data med kunskaper som de redan har.

## <a name="integrates-seamlessly-with-your-it-investments"></a>Kan helt integreras med dina IT-investeringar
  
Data Lake Analytics använder dina befintliga IT-investeringar för identitet, hantering och säkerhet. Detta förenklar datastyrning och gör det enkelt att utöka dina aktuella program. Data Lake Analytics är integrerat med Active Directory, så att användare och behörigheter kan hanteras, och integrerad övervakning och granskning ingår.

## <a name="affordable-and-cost-effective"></a>Prisvärt och kostnadseffektivt

Data Lake Analytics är en kostnadseffektiv lösning för arbetsbelastningar av stordata som körs. Du betalar per projekt när data bearbetas. Det krävs ingen maskinvara, licenser eller tjänstspecifikt supportavtal. Systemet skalas automatiskt uppåt eller nedåt när jobbet startas och är klart, vilket innebär att du aldrig betalar för mer än vad du behöver. [Läs mer om att kontrollera kostnader och spara pengar](https://aka.ms/adlasavemoney).

## <a name="works-with-all-your-azure-data"></a>Fungerar med alla dina Azure-data
  
Data Lake Analytics fungerar med Azure Data Lake Storage för högsta prestanda, data flöde och parallellisering och fungerar med Azure Storage blobbar, Azure SQL Database, Azure Synapse Analytics.

## <a name="in-region-data-residency"></a>Placering för region data
  
Data Lake Analytics flyttar eller lagrar inte kund information från den region där den distribueras.


## <a name="next-steps"></a>Nästa steg

* Se Azure Data Lake Analytics senaste uppdateringen med [Nyheter i Azure Data Lake Analytics?](data-lake-analytics-whats-new.md)
* Kom igång med data Lake Analytics att använda [Azure Portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [CLI](data-lake-analytics-get-started-cli.md)
* Hantera Azure Data Lake Analytics med [Azure Portal](data-lake-analytics-manage-use-portal.md)  |  [Azure PowerShell](data-lake-analytics-manage-use-powershell.md)  |  [CLI](data-lake-analytics-manage-use-cli.md)  |  [Azure .NET SDK](data-lake-analytics-manage-use-dotnet-sdk.md)  |  [Node.js](data-lake-analytics-manage-use-nodejs.md)
* [Kontrollera kostnader och spara pengar med Data Lake Analytics](https://1drv.ms/f/s!AvdZLquGMt47h213Hg3rhl-Tym1c)
