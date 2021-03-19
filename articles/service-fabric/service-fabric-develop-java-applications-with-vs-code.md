---
title: Utveckla Java-program med Visual Studio Code
description: Den här artikeln visar hur du skapar, distribuerar och felsöker Java-Service Fabric program med hjälp av Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.custom: devx-track-java
ms.author: pepogors
ms.openlocfilehash: cc65deb924a9f3367c2ea1d7c71544743ccf2697
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87327369"
---
# <a name="develop-java-service-fabric-applications-with-visual-studio-code"></a>Utveckla Java Service Fabric-program med Visual Studio Code

Med [tillägget Service Fabric Reliable Services för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) är det enkelt att bygga Java Service Fabric-program på Windows-, Linux-och MacOS-operativsystem.

Den här artikeln visar hur du skapar, distribuerar och felsöker ett Java-Service Fabric program med hjälp av Visual Studio Code.

> [!IMPORTANT]
> Service Fabric Java-program kan utvecklas på Windows-datorer, men kan bara distribueras till Azure Linux-kluster. Fel sökning av Java-program stöds inte i Windows.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du redan har installerat VS Code, Service Fabric Reliable Services tillägget för VS Code och eventuella beroenden som krävs för utvecklings miljön. Läs mer i [komma igång](./service-fabric-get-started-vs-code.md#prerequisites).

## <a name="download-the-sample"></a>Ladda ned exemplet
I den här artikeln används röstnings programmet i [exemplet Service Fabric Java-GitHub för snabb starts lagring](https://github.com/Azure-Samples/service-fabric-java-quickstart). 

Om du vill klona lagrings platsen till utvecklings datorn kör du följande kommando från ett terminalfönster (kommando fönster i Windows):

```sh
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="open-the-application-in-vs-code"></a>Öppna programmet i VS Code

Öppna VS Code.  Klicka på Explorer-ikonen i **aktivitets fältet** och klicka på **Öppna mapp**, eller klicka på  **Arkiv-> Öppna mapp**. Navigera till *./Service-Fabric-Java-QuickStart/Voting* -katalogen i den mapp där du klonade lagrings platsen och klicka sedan på **OK**. Arbets ytan bör innehålla samma filer som visas i skärm bilden nedan.

![Java röstnings program på arbets ytan](./media/service-fabric-develop-java-applications-with-vs-code/java-voting-application.png)

## <a name="build-the-application"></a>Skapa programmet

1. Tryck på (Ctrl + Shift + p) för att öppna **kommando-paletten** i vs Code.
2. Sök efter och välj kommandot **Service Fabric: build Application** . Build-utdata skickas till den integrerade terminalen.

   ![Kommandot build Application i VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-build-application.png)

## <a name="deploy-the-application-to-the-local-cluster"></a>Distribuera programmet till det lokala klustret
När du har skapat programmet kan du distribuera det till det lokala klustret. 

> [!IMPORTANT]
> Distribution av Java-program till det lokala klustret stöds inte på Windows-datorer.

1. Välj **kommandot Service Fabric: distribuera program (localhost)** från **kommando paletten**. Utdata från installations processen skickas till den integrerade terminalen.

   ![Distribuera program kommando i VS Code](./media/service-fabric-develop-java-applications-with-vs-code/sf-deploy-application.png)

4. När distributionen är klar startar du en webbläsare och öppnar Service Fabric Explorer: `http://localhost:19080/Explorer` . Du bör se att programmet körs. Det kan ta lite tid, så därför måste du vara patient. 

   ![Röstnings program i Service Fabric Explorer](./media/service-fabric-develop-java-applications-with-vs-code/sfx-localhost-java.png)

4. När du har kontrollerat att programmet körs startar du en webbläsare och öppnar den här sidan: `http://localhost:8080` . Det här är webb klient delen av programmet. Du kan lägga till objekt och klicka på dem för att rösta.

   ![Röstnings program i webbläsare](./media/service-fabric-develop-java-applications-with-vs-code/voting-sample-in-browser.png)

5. Om du vill ta bort programmet från klustret väljer du kommandot **Service Fabric: ta bort program** från **kommando paletten**. Utdata från avinstallations processen skickas till den integrerade terminalen. Du kan använda Service Fabric Explorer för att kontrol lera att programmet har tagits bort från det lokala klustret.

## <a name="debug-the-application"></a>Felsök programmet
När program felsöks i VS Code måste programmet köras på ett lokalt kluster. Bryt punkter kan sedan läggas till i koden.

> [!IMPORTANT]
> Fel sökning av Java-program stöds inte på Windows-datorer.

Slutför följande steg för att förbereda VotingDataService och röstnings programmet för fel sökning:

1. Uppdatera filen *röstning/VotingApplication/VotingDataServicePkg/Code/entryPoint. sh* .
Kommentera ut kommandot på rad 6 (Använd ' # ') och Lägg till följande kommando längst ned i filen:

   ```
   java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingDataService.jar
   ```

2. Uppdatera *röstnings-VotingApplication/ApplicationManifest.xml-* filen. Ange attributen **MinReplicaSetSize** och **TargetReplicaSetSize** till "1" i elementet **StatefulService** :
   
   ```xml
         <StatefulService MinReplicaSetSize="1" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="1">
   ```

3. Klicka på ikonen Felsök i **aktivitets fältet** för att öppna fel söknings vyn i vs Code. Klicka på kugg hjuls ikonen överst i vyn för fel sökning och välj **Java** på menyn List Rute miljö. launch.jsfilen öppnas. 

   ![Fel söknings ikon i VS Code-arbetsyta](./media/service-fabric-develop-java-applications-with-vs-code/debug-icon-workspace.png)

3. I launch.jspå fil anger du Portvärdet i konfigurationen med namnet **debug (Attach)** till **8001**. Spara filen.

   ![Felsök konfiguration för launch.jspå](./media/service-fabric-develop-java-applications-with-vs-code/launch-json-java.png)

4. Distribuera programmet till det lokala klustret med hjälp av kommandot **Service Fabric: distribuera program (localhost)** . Kontrol lera att programmet körs i Service Fabric Explorer. Ditt program är nu klart att felsöka.

Utför följande steg för att ange en Bryt punkt:

1. Öppna filen */Voting/VotingDataService/src/statefulservice/VotingDataService.java* i Utforskaren. Ange en Bryt punkt på den första kodraden i `try` blocket i `addItem` metoden (rad 80).
   
   ![Ange Bryt punkten i röstnings data tjänsten](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-set.png)

   > [!IMPORTANT]
   > Se till att du ställer in Bryt punkter på körbara rader kod. Till exempel Bryt punkter som anges för metod deklarationer,- `try` instruktioner eller- `catch` instruktioner kommer att missas av fel söknings programmet.
2. Starta fel sökningen genom att klicka på fel söknings ikonen i **aktivitets fältet**, Välj **fel söknings konfiguration (bifoga)** på Felsök-menyn och klicka på knappen Kör (grön pil).

   ![Felsök (bifoga) konfiguration](./media/service-fabric-develop-java-applications-with-vs-code/debug-attach-java.png)

3. Via en webbläsare går du till `http://localhost:8080`. Skriv ett nytt objekt i text rutan och klicka på **+ Lägg till**. Din Bryt punkt ska vara påträffad. Du kan använda fel söknings verktygsfältet längst upp i VS Code för att fortsätta körningen, steg för steg över rader, stega till metoder eller Stega ut ur den aktuella metoden. 
   
   ![Besöks Bryt punkt](./media/service-fabric-develop-java-applications-with-vs-code/breakpoint-hit.png)
       
4. Avsluta felsökningssessionen genom att klicka på plugin-ikonen i verktygsfältet Felsök överst i VS Code.
   
   ![Koppla från fel söknings programmet](./media/service-fabric-develop-java-applications-with-vs-code/debug-bar-disconnect.png)
       
5. När du är klar med fel sökningen kan du använda kommandot **Service Fabric: ta bort program** för att ta bort röstnings programmet från det lokala klustret. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [utvecklar och felsöker C# Service Fabric program med vs Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
