---
title: Använd .NET för Apache Spark
description: Lär dig mer om att använda .NET och Apache Spark för batchbearbetning, real tids strömning, maskin inlärning och att skriva ad hoc-frågor i Azure Synapse Analytics-anteckningsböcker.
author: luisquintanilla
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 05/01/2020
ms.author: luquinta
ms.reviewer: jrasnick
ms.openlocfilehash: 895d766cc1e70e6a001904770d6f3ef12b6945cd
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018669"
---
# <a name="use-net-for-apache-spark-with-azure-synapse-analytics"></a>Använda .NET för Apache Spark med Azure Synapse Analytics

[.Net för Apache Spark](https://dot.net/spark) tillhandahåller kostnads fri, [öppen källkod](https://github.com/dotnet/spark)och plattforms oberoende .net-support för Spark. 

Den tillhandahåller .NET-bindningar för Spark, som gör att du kan komma åt Spark-API: er via C# och F #. Med .NET för Apache Spark kan du också skriva och köra användardefinierade funktioner för Spark som skrivits i .NET. Med .NET-API: erna för Spark kan du få åtkomst till alla aspekter av Spark-DataFrames som hjälper dig att analysera dina data, inklusive Spark SQL, delta Lake och strukturerad strömning.

Du kan analysera data med .NET för Apache Spark genom Spark-batchjobb eller med interaktiva Azure Synapse Analytics-anteckningsböcker. I den här artikeln får du lära dig hur du använder .NET för Apache Spark med Azure Synapse med hjälp av båda metoderna.

## <a name="submit-batch-jobs-using-the-spark-job-definition"></a>Skicka batch-jobb med hjälp av Spark-jobbets definition

Besök själv studie kursen och lär dig hur du använder Azure Synapse Analytics för att [skapa Apache Spark jobb definitioner för Synapse Spark-pooler](apache-spark-job-definitions.md). Om du inte har paketerat appen för att skicka till Azure-Synapse utför du följande steg.

1. Kör följande kommandon för att publicera din app. Se till att ersätta *mySparkApp* med sökvägen till din app.

   **I Windows:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r win-x64
   ```
   
   **I Linux:**

   ```dotnetcli
   cd mySparkApp
   dotnet publish -c Release -f netcoreapp3.1 -r ubuntu.16.04-x64
   ```

2. Zip-innehållet i mappen Publish, `publish.zip` till exempel, som skapades på grund av steg 1. Alla sammansättningar ska finnas i det första lagret i ZIP-filen och det bör inte finnas något mellanliggande mappobjekt. Det innebär att när du packar `publish.zip` upp extraheras alla sammansättningar i den aktuella arbets katalogen.

    **I Windows:**

    Använd ett extraherings program som [7-zip](https://www.7-zip.org/) eller [WinZip](https://www.winzip.com/)för att extrahera filen till bin-katalogen med alla publicerade binärfiler.

    **I Linux:**

    Öppna en bash shell och CD i katalogen bin med alla publicerade binärfiler och kör följande kommando.

    ```bash
    zip -r publish.zip
    ```

## <a name="net-for-apache-spark-in-azure-synapse-analytics-notebooks"></a>.NET för Apache Spark i Azure Synapse Analytics-anteckningsböcker 

Bärbara datorer är ett bra alternativ för prototypering av .NET för Apache Spark pipelines och scenarier. Du kan börja arbeta med, förstå, filtrera, Visa och visualisera dina data snabbt och effektivt. 

Data tekniker, data forskare, affärsanalytiker och maskin inlärnings tekniker kan samar beta över ett delat, interaktivt dokument. Du ser omedelbara resultat från data utforskning och kan visualisera dina data i samma antecknings bok.

### <a name="how-to-use-net-for-apache-spark-notebooks"></a>Använda .NET för Apache Spark antecknings böcker

När du skapar en ny antecknings bok väljer du en språk kärna som du vill uttrycka affärs logiken för. Kernel-support är tillgängligt för flera språk, inklusive C#.

Om du vill använda .NET för Apache Spark i din Azure Synapse Analytics-anteckningsbok väljer du **.net Spark (C#)** som kernel och ansluter antecknings boken till en befintlig server lös Apache Spark-pool.

.NET Spark-anteckningsboken baseras på de [interaktiva](https://github.com/dotnet/interactive) funktionerna i .net och ger interaktiva C#-upplevelser möjlighet att använda .net för Spark med en spark-sessionsvariabel som `spark` redan är fördefinierad.

### <a name="install-nuget-packages-in-notebooks"></a>Installera NuGet-paket i antecknings böcker

Du kan installera NuGet-paket som du väljer i din bärbara dator genom att använda `#r nuget` kommandot Magic före namnet på NuGet-paketet. Följande diagram visar ett exempel:

![Skärm bild som visar hur du använder #r för att installera ett spark .NET Notebook NuGet-paket](./media/apache-spark-development-using-notebooks/synapse-spark-dotnet-notebook-nuget.png)

Mer information om hur du arbetar med NuGet-paket i antecknings böcker finns i den [interaktiva dokumentationen för .net](https://github.com/dotnet/interactive/blob/main/docs/nuget-overview.md).

### <a name="net-for-apache-spark-c-kernel-features"></a>.NET för Apache Spark C# kernel-funktioner

Följande funktioner är tillgängliga när du använder .NET för Apache Spark i antecknings boken för Azure Synapse Analytics:

* Deklarativ HTML: generera utdata från dina celler med HTML-syntax, till exempel sidhuvuden, punkt listor och till och med visning av bilder.
* Enkla C#-uttryck (till exempel tilldelningar, utskrift till konsol, Utlös ande undantag och så vidare).
* Kod block i flera rader i C# (till exempel IF-instruktioner, förgrunds slingor, klass definitioner och så vidare).
* Åtkomst till standard biblioteket i C# (till exempel system, LINQ, Enumerables och så vidare).
* Stöd för språk funktionerna i C# 8,0.
* `spark` som en fördefinierad variabel för att ge dig åtkomst till din Apache Spark-session.
* Stöd för [att definiera .net-användardefinierade funktioner som kan köras i Apache Spark](/dotnet/spark/how-to-guides/udf-guide). Vi rekommenderar att du [skriver och anropar UDF: er i .net för Apache Spark interaktiva miljöer](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue) för att lära dig hur du använder UDF: er i .net för Apache Spark interaktiva upplevelser.
* Stöd för visualisering av utdata från Spark-jobb med olika diagram (till exempel linje, stapel eller histogram) och layouter (till exempel en enskild, överanvändning osv.) med hjälp av `XPlot.Plotly` biblioteket.
* Möjlighet att inkludera NuGet-paket i din C#-anteckningsbok.

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om .NET för Apache Spark](/dotnet/spark/)
* [.NET för Apache Spark interaktiva guider](/dotnet/spark/how-to-guides/dotnet-interactive-udf-issue)
* [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics/)
* [.NET-interaktiv](https://devblogs.microsoft.com/dotnet/creating-interactive-net-documentation/)
