---
title: Använda .NET för Apache Spark
description: Lär dig mer om hur du använder .NET och Apache Spark för batchbearbetning, strömning i realtid, maskininlärning och skriv ad hoc-frågor i Azure Synapse Analytics notebook-datorer.
author: luisquintanilla
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: luquinta
ms.reviewer: jrasnick
ms.openlocfilehash: 8d045c1ec96bb7b31a710a28e30e3d428922b65e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378558"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Använda .NET för Apache Spark med Azure Synapse Analytics

[.NET for Apache Spark](https://dot.net/spark) tillhandahåller kostnadsfritt .NET-stöd [med](https://github.com/dotnet/spark)öppen källkod och plattformsoberoende för Spark. 

Den tillhandahåller .NET-bindningar för Spark, vilket gör att du kan komma åt Spark-API:er via C# och F#. Med .NET för Apache Spark kan du också skriva och köra användardefinierade funktioner för Spark som skrivits i .NET. Med .NET-API:erna för Spark kan du komma åt alla aspekter av Spark DataFrames som hjälper dig att analysera dina data, inklusive Spark SQL, Delta Lake och Structured Streaming.

Du kan analysera data med .NET för att Apache Spark spark batch-jobbdefinitioner eller med interaktiva Azure Synapse Analytics notebook-datorer. I den här artikeln får du lära dig hur du använder .NET för Apache Spark med Azure Synapse båda teknikerna.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Skicka batchjobb med Spark-jobbdefinitionen

Gå till självstudien för att lära dig hur du använder Azure Synapse Analytics för [att skapa Apache Spark jobbdefinitioner för Synapse Spark-pooler.](apache-spark-job-definitions.md) Om du inte har paketerat din app för att skicka Azure Synapse gör du följande.

1. Kör följande kommandon för att publicera din app. Ersätt *mySparkApp med* sökvägen till din app.
   
   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.18.04-x64
   ```

2. Zippa innehållet i `publish.zip` publiceringsmappen, till exempel som skapades som ett resultat av steg 1. Alla sammansättningar ska finnas i det första lagret i ZIP-filen och det ska inte finnas något mellanliggande mapplager. Det innebär att när du packa upp `publish.zip` extraheras alla sammansättningar till din aktuella arbetskatalog.

    **I Windows:**

    Använd ett extraheringsprogram, till exempel [7-Zip](https://www.7-zip.org/) eller [WinZip,](https://www.winzip.com/)för att extrahera filen till bin-katalogen med alla publicerade binärfiler.

    **I Linux:**

    Öppna ett bash-gränssnitt och cd i bin-katalogen med alla publicerade binärfiler och kör följande kommando.

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET för Apache Spark i Azure Synapse Analytics notebook-datorer 

Notebooks är ett bra alternativ för prototyper av .NET för Apache Spark pipelines och scenarier. Du kan börja arbeta med, förstå, filtrera, visa och visualisera dina data snabbt och effektivt. 

Datatekniker, dataforskare, affärsanalytiker och maskininlärningstekniker kan samarbeta i ett delat, interaktivt dokument. Du ser omedelbara resultat från datagranskning och kan visualisera dina data i samma notebook-dator.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>Så här använder du .NET för Apache Spark notebook-datorer

När du skapar en ny notebook-dator väljer du en språkkärna som du vill uttrycka din affärslogik med. Kernel-stöd är tillgängligt för flera språk, inklusive C#.

Om du vill använda .NET för Apache Spark i din Azure Synapse Analytics-notebook-fil väljer du **.NET Spark (C#)** som kernel och kopplar anteckningsboken till en befintlig serverlös Apache Spark pool.

.NET Spark-notebook-datorn baseras på [de interaktiva .NET-upplevelserna](https://github.com/dotnet/interactive) och ger interaktiva C#-upplevelser med möjlighet att använda .NET för Spark utan problem med Spark-sessionsvariabeln redan `spark` fördefinierad.

### <a name="install-nuget-packages-in-notebooks"></a>Installera NuGet-paket i notebook-datorer

Du kan installera valfria NuGet-paket i din notebook-fil med hjälp av `#r nuget` det magiska kommandot före namnet på NuGet-paketet. Följande diagram visar ett exempel:

![Skärmbild som visar hur du använder #r för att installera en Spark .NET-notebook NuGet-paket](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

Mer information om hur du arbetar med NuGet-paket i notebook-fil finns i [den interaktiva .NET-dokumentationen.](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md)

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET för Apache Spark C#-kernelfunktioner

Följande funktioner är tillgängliga när du använder .NET för Apache Spark i den Azure Synapse Analytics notebook-datorn:

* Deklarativ HTML: Generera utdata från cellerna med HTML-syntax, till exempel rubriker, punktlistor och till och med bilder.
* Enkla C#-instruktioner (till exempel tilldelningar, utskrift till konsolen, utlösa undantag och så vidare).
* C#-kodblock med flera rad (till exempel if-instruktioner, foreach-loopar, klassdefinitioner och så vidare).
* Åtkomst till C#-standardbiblioteket (till exempel System, LINQ, Enumerables och så vidare).
* Stöd för språkfunktioner i C# 8.0.
* `spark` som en fördefinierad variabel för att ge dig åtkomst till din Apache Spark session.
* Stöd för att [definiera .NET-användardefinierade funktioner som kan köras inom Apache Spark](/dotnet/spark/how-to-guides/udf-guide). Vi rekommenderar att du skriver och anropar [användar-UDF:er](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue) i .NET för Apache Spark interaktiva miljöer för att lära dig hur du använder användarupplevelser i .NET för Apache Spark interaktiva upplevelser.
* Stöd för att visualisera utdata från dina Spark-jobb med hjälp av olika diagram (till exempel linje, stapel eller histogram) och layouter (till exempel enkla, överlagrade och så vidare) med hjälp av `XPlot.Plotly` biblioteket.
* Möjlighet att inkludera NuGet-paket i din C#-notebook-fil.

## <a name="next-steps"></a>Nästa steg

* [.NET för Apache Spark dokumentation](/dotnet/spark/)
* [.NET för interaktiva Apache Spark interaktiva guider](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET Interactive](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
