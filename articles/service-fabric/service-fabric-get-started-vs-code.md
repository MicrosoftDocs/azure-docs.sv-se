---
title: Azure Service Fabric med VS Code-Komma igång
description: Den här artikeln är en översikt över hur du Service Fabric program med Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: 7d54b4b048632324a58708f893a4778a56137916
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876087"
---
# <a name="service-fabric-for-visual-studio-code"></a>Service Fabric för Visual Studio Code

Tillägget [Service Fabric Reliable Services VS Code innehåller](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) de verktyg som behövs för att skapa, bygga och felsöka Service Fabric på Windows-, Linux- och macOS-operativsystem.

Den här artikeln innehåller en översikt över kraven och konfigurationen av tillägget samt användningen av de olika kommandon som tillhandahålls av tillägget. 

> [!IMPORTANT]
> Service Fabric Java-program kan utvecklas på Windows-datorer, men kan endast distribueras till Azure Linux-kluster. Felsökning av Java-program stöds inte i Windows.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste vara installerade i alla miljöer.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [Service Fabric SDK](./service-fabric-get-started.md)
* Yeoman-generatorer – installera lämpliga generatorer för ditt program

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Följande krav måste vara installerade för Java-utveckling:

* [Java SDK](/azure/developer/java/fundamentals/java-jdk-long-term-support) (version 1.8)
* [Gradle](https://gradle.org/install/)
* [Felsökare för Java VS Code-tillägg](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) Krävs för att felsöka Java-tjänster. Felsökning av Java-tjänster stöds endast på Linux. Du kan installera antingen genom  att klicka på ikonen Tillägg i aktivitetsfältet i VS Code och söka efter tillägget eller från VS Code Marketplace.

Följande krav måste vara installerade för .NET Core/C#-utveckling:

* [.NET Core](https://dotnet.microsoft.com/download) (version 2.0.0 eller senare)
* [C# för Visual Studio Code (drivs av OmniSharp) VS Code-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) Krävs för att felsöka C#-tjänster. Du kan installera antingen genom  att klicka på ikonen Tillägg i Aktivitetsfältet i VS Code och söka efter tillägget eller från VS Code Marketplace.

## <a name="setup"></a>Konfiguration

1. Öppna VS Code.
2. Klicka på ikonen Tillägg i **Aktivitetsfältet** till vänster i VS Code. Sök efter "Service Fabric". Klicka **på Installera** för Service Fabric Reliable Services tillägget.

## <a name="commands"></a>Kommandon
Tillägget Service Fabric Reliable Services vs code innehåller många kommandon som hjälper utvecklare att skapa och distribuera Service Fabric projekt. Du kan anropa kommandon från **kommandopaletten** genom att trycka på , skriva in kommandonamnet i inmatningsfältet och välja `(Ctrl + Shift + p)` önskat kommando från promptlistan. 

* Service Fabric: Skapa program 
* Service Fabric: Publicera program 
* Service Fabric: Distribuera program 
* Service Fabric: Ta bort program  
* Service Fabric: Skapa program 
* Service Fabric: Rensa program 

### <a name="service-fabric-create-application"></a>Service Fabric: Skapa program

Kommandot **Service Fabric: Skapa program** skapar ett nytt Service Fabric på din aktuella arbetsyta. Beroende på vilka Yeoman-generatorer som är installerade på utvecklingsdatorn kan du skapa flera typer av Service Fabric-program, inklusive Java-, C#-, Container- och Gästprojekt. 

1.  Välj kommandot **Service Fabric: Skapa** program
2.  Välj typ för ditt nya Service Fabric program. 
3.  Ange namnet på det program som du vill skapa
3.  Välj den typ av tjänst som du vill lägga till i Service Fabric program. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Det nya Service Fabric-programmet visas i arbetsytan.
6.  Öppna den nya programmappen så att den blir rotmappen på arbetsytan. Du kan fortsätta att köra kommandon härifrån.

### <a name="service-fabric-add-service"></a>Service Fabric: Lägg till tjänst
Kommandot **Service Fabric: Lägg till tjänst** lägger till en ny tjänst i ett befintligt Service Fabric program. Programmet som tjänsten ska läggas till i måste vara arbetsytans rotkatalog. 

1.  Välj kommandot **Service Fabric: Lägg till** tjänst.
2.  Välj typ av ditt Service Fabric program. 
3.  Välj den typ av tjänst som du vill lägga till i Service Fabric program. 
4.  Följ anvisningarna för att namnge tjänsten. 
5.  Den nya tjänsten visas i projektkatalogen. 

### <a name="service-fabric-publish-application"></a>Service Fabric: Publicera program
Kommandot **Service Fabric: Publicera program** distribuerar ditt Service Fabric på ett fjärrkluster. Målklustret kan vara antingen ett säkert eller oskyddat kluster. Om parametrar inte anges i Cloud.jspå distribueras programmet till det lokala klustret.

1.  Första gången programmet skapas genereras en Cloud.jsfil i projektkatalogen.
2.  Ange värdena för det kluster som du vill ansluta till i Cloud.jspå filen.
3.  Välj kommandot **Service Fabric: Publicera** program.
4.  Visa målklustret med Service Fabric Explorer för att bekräfta att programmet har installerats. 

### <a name="service-fabric-deploy-application-localhost"></a>Service Fabric: Distribuera program (Localhost)
Kommandot **Service Fabric: Distribuera program** distribuerar ditt Service Fabric till ditt lokala kluster. Kontrollera att det lokala klustret körs innan du använder kommandot . 

1. Välj kommandot **Service Fabric: Distribuera** program
2. Visa det lokala klustret med Service Fabric Explorer (http: \/ /localhost:19080/Explorer) för att bekräfta att programmet har installerats. Det kan ta lite tid, så ha tålamod.
3. Du kan också använda **Service Fabric: Kommandot Publicera** program utan parametrar som angetts i Cloud.jsför att distribuera till ett lokalt kluster.

> [!NOTE]
> Distribution av Java-program till det lokala klustret stöds inte på Windows-datorer.

### <a name="service-fabric-remove-application"></a>Service Fabric: Ta bort program
Kommandot **Service Fabric: Ta** bort program tar Service Fabric ett program från klustret som det tidigare distribuerades till med vs code-tillägget. 

1.  Välj kommandot **Service Fabric: Ta bort** program.
2.  Visa klustret med Service Fabric Explorer för att bekräfta att programmet har tagits bort. Det kan ta lite tid, så ha tålamod.

### <a name="service-fabric-build-application"></a>Service Fabric: Skapa program
Kommandot **Service Fabric: Build Application (Skapa** program) kan skapa antingen Java eller C# Service Fabric program. 

1.  Kontrollera att du är i programmets rotmapp innan du kör det här kommandot. Kommandot identifierar programtypen (C# eller Java) och skapar programmet därefter.
2.  Välj kommandot **Service Fabric: Build Application** (Skapa program).
3.  Utdata från byggprocessen skrivs till den integrerade terminalen.

### <a name="service-fabric-clean-application"></a>Service Fabric: Rensa program
Kommandot **Service Fabric: Rensa program** tar bort alla JAR-filer och interna bibliotek som genererades av bygget. Endast giltigt för Java-program. 

1.  Kontrollera att du är i programmets rotmapp innan du kör det här kommandot. 
2.  Välj kommandot **Service Fabric: Rensa** program.
3.  Utdata från den rena processen skrivs till den integrerade terminalen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur [du utvecklar och felsöker C# Service Fabric-program med VS Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Lär dig hur [du utvecklar och felsöker Java Service Fabric-program med VS Code](./service-fabric-develop-java-applications-with-vs-code.md).