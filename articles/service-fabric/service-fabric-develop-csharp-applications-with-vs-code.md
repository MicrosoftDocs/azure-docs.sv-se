---
title: Utveckla .NET Core-program med Visual Studio Code
description: Den här artikeln visar hur du skapar, distribuerar och felsöker .NET Core Service Fabric program med hjälp av Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: 5fbd523a38b3c4860316e45b8b7c03a17de19499
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92678332"
---
# <a name="develop-c-service-fabric-applications-with-visual-studio-code"></a>Utveckla C# Service Fabric-program med Visual Studio Code

[Service Fabric Reliable Services tillägget för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) gör det enkelt att bygga .net Core Service Fabric-program på Windows-, Linux-och MacOS-operativsystem.

Den här artikeln visar hur du skapar, distribuerar och felsöker ett .NET Core Service Fabric-program med Visual Studio Code.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har installerat VS Code, Service Fabric Reliable Services tillägget för VS Code och eventuella beroenden som krävs för utvecklings miljön. Läs mer i [komma igång](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Ladda ned exemplet
I den här artikeln används CounterService-programmet i [Service Fabric .net Core komma igång-exempel GitHub-lagringsplatsen](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started). 

Om du vill klona lagrings platsen till utvecklings datorn kör du följande kommando från ett terminalfönster (kommando fönster i Windows):

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started.git
```

## <a name="open-the-application-in-vs-code"></a>Öppna programmet i VS Code

### <a name="windows"></a>Windows
Högerklicka på VS Code-ikonen på Start-menyn och välj **Kör som administratör**. Om du vill koppla fel sökaren till dina tjänster måste du köra VS Code som administratör.

### <a name="linux"></a>Linux
Använd terminalen och navigera till sökvägen/service-fabric-dotnet-core-getting-started/Services/CounterService från katalogen som programmet klonades till lokalt.

Kör följande kommando för att öppna VS Code som en rot användare så att fel söknings programmet kan ansluta till dina tjänster.
```
sudo code . --user-data-dir='.'
```

Programmet bör nu visas i din VS Code-arbetsyta.

![Counter service-program på arbets ytan](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-application-in-workspace.png)

## <a name="build-the-application"></a>Skapa programmet
1. Tryck på (Ctrl + Shift + p) för att öppna **kommando-paletten** i vs Code.
2. Sök efter och välj kommandot **Service Fabric: build Application** . Build-utdata skickas till den integrerade terminalen.

   ![Kommandot build Application i VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuera programmet till det lokala klustret
När du har skapat programmet kan du distribuera det till det lokala klustret. 

1. Välj **kommandot Service Fabric: distribuera program (localhost)** från **kommando paletten**. Utdata från installations processen skickas till den integrerade terminalen.

   ![Distribuera program kommando i VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-deploy-application.png)

4. När distributionen är klar startar du en webbläsare och öppnar Service Fabric Explorer: http: \/ /localhost: 19080/Explorer. Du bör se att programmet körs. Det kan ta lite tid, så därför måste du vara patient. 

   ![Counter service-program i Service Fabric Explorer](./media/service-fabric-develop-csharp-applications-with-vs-code/sfx-verify-deploy.png)

4. När du har verifierat att programmet körs startar du en webbläsare och öppnar den här sidan: http: \/ /localhost: 31002. Det här är webb klient delen av programmet. Uppdatera sidan om du vill se räknarens aktuella värde när det ökar.

   ![Counter service-program i webbläsare](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-running.png)

## <a name="publish-the-application-to-an-azure-service-fabric-cluster"></a>Publicera programmet till ett Azure Service Fabric-kluster
Förutom att distribuera programmet till det lokala klustret kan du också publicera programmet till ett fjärran slutet Azure Service Fabric-kluster. 

1. Se till att du har skapat ditt program med hjälp av anvisningarna ovan. Uppdatera den genererade konfigurations filen `Cloud.json` med information om det fjärrkluster som du vill publicera till.

2. Välj **kommandot Service Fabric: publicera program** från **kommando paletten**. Utdata från installations processen skickas till den integrerade terminalen.

   ![Kommandot Publicera program i VS Code](./media/service-fabric-develop-csharp-applications-with-vs-code/sf-publish-application.png)

3. När distributionen är klar startar du en webbläsare och öppnar Service Fabric Explorer: `https:<clusterurl>:19080/Explorer` . Du bör se att programmet körs. Det kan ta lite tid, så därför måste du vara patient. 

## <a name="debug-the-application"></a>Felsök programmet
När program felsöks i VS Code måste programmet köras på ett lokalt kluster. Bryt punkter kan sedan läggas till i koden.

Utför följande steg för att ange en Bryt punkt och fel sökning:
1. Öppna */src/CounterServiceApplication/CounterService/CounterService.cs* -filen i Utforskaren och ange en Bryt punkt på rad 62 i `RunAsync` metoden.
3. Klicka på ikonen Felsök i **aktivitets fältet** för att öppna fel söknings vyn i vs Code. Klicka på kugg hjuls ikonen överst i vyn för fel sökning och välj **.net Core** på menyn List Rute miljö. launch.jsfilen öppnas. Du kan stänga den här filen. Nu bör du se konfigurations alternativ på menyn för fel söknings konfiguration bredvid knappen Kör (grön pil).

   ![Fel söknings ikon i VS Code-arbetsyta](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-icon-workspace.png)

2. Välj **.net Core Attach** från fel söknings konfigurations menyn.

   ![Skärm bild som visar .NET Core Attach valt i fel söknings konfigurations menyn.](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-start.png)

3. Öppna Service Fabric Explorer i en webbläsare: http: \/ /localhost: 19080/Explorer. Klicka på **program** och öka detalj nivån för att avgöra vilken primär nod som CounterService körs på. I bilden nedanför den primära noden för CounterService är Node 0.

   ![Primär nod för CounterService](./media/service-fabric-develop-csharp-applications-with-vs-code/counter-service-primary-node.png)

4. I VS-kod klickar du på ikonen kör (grön pil) bredvid **.net Core Attach** debug-konfigurationen. I dialog rutan process val väljer du den CounterService-process som körs på den primära nod som du identifierade i steg 4.

   ![Primär process](./media/service-fabric-develop-csharp-applications-with-vs-code/select-process.png)

5. Bryt punkten i filen *CounterService. cs* kommer snart att gå ut. Du kan sedan utforska värdena för de lokala variablerna. Använd fel söknings verktygsfältet överst i VS Code för att fortsätta körningen, steg för steg över rader, stega till metoder eller Stega ut ur den aktuella metoden. 

   ![Fel söknings värden](./media/service-fabric-develop-csharp-applications-with-vs-code/breakpoint-hit.png)

6. Avsluta felsökningssessionen genom att klicka på plugin-ikonen i verktygsfältet Felsök överst i VS Code.
   
   ![Koppla från fel söknings programmet](./media/service-fabric-develop-csharp-applications-with-vs-code/debug-bar-disconnect.png)
       
7. När du är klar med fel sökningen kan du använda kommandot **Service Fabric: ta bort program** för att ta bort CounterService-programmet från det lokala klustret. 

## <a name="next-steps"></a>Nästa steg

* Lär dig att [utveckla och felsöka Java Service Fabric program med vs Code](./service-fabric-develop-java-applications-with-vs-code.md).



