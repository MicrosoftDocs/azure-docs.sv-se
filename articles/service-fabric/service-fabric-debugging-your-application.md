---
title: Felsöka ditt program i Visual Studio
description: Förbättra tillförlitligheten och prestandan hos dina tjänster genom att utveckla och felsöka dem i Visual Studio på ett lokalt utvecklings kluster.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 0b7d08d610c883240abedc66c55abba64a74c8e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576323"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Felsöka ditt Service Fabric-program med Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Sol förmörkelse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Felsöka ett lokalt Service Fabric program
Du kan spara tid och pengar genom att distribuera och felsöka ditt Azure Service Fabric-program i ett lokalt dator utvecklings kluster. Visual Studio 2019 eller 2015 kan distribuera programmet till det lokala klustret och automatiskt ansluta fel söknings programmet till alla instanser av programmet. Visual Studio måste köras som administratör för att du ska kunna ansluta fel söknings programmet.

1. Starta ett lokalt utvecklings kluster genom att följa stegen i Konfigurera [din Service Fabric utvecklings miljö](service-fabric-get-started.md).
2. Tryck på **F5** eller klicka på **Felsök**  >  **Starta fel sökning**.
   
    ![Skärm bild som visar fel söknings menyn.][startdebugging]
3. Ange Bryt punkter i koden och gå igenom programmet genom att klicka på kommandon på **fel söknings** menyn.
   
   > [!NOTE]
   > Visual Studio bifogar alla instanser av ditt program. Medan du går igenom kod kan Bryt punkter uppnås av flera processer som resulterar i samtidiga sessioner. Försök att inaktivera Bryt punkterna när de har nåtts genom att varje Bryt punkt villkorligt på tråd-ID: t eller med hjälp av diagnostiska händelser.
   > 
   > 
4. Fönstret **Diagnostic Events** öppnas automatiskt så att du kan visa diagnostiska händelser i real tid.
   
    ![Visa diagnostiska händelser i real tid][diagnosticevents]
5. Du kan också öppna fönstret **diagnostiska händelser** i Cloud Explorer.  Under **Service Fabric** högerklickar du på en nod och väljer **Visa strömmande spår**.
   
    ![Öppna fönstret diagnostiska händelser][viewdiagnosticevents]
   
    Om du vill filtrera spårningarna till en specifik tjänst eller ett program aktiverar du strömmande spårningar för den specifika tjänsten eller programmet.
6. Diagnostiska händelser kan visas i den automatiskt genererade **ServiceEventSource. cs** -filen och anropas från program koden.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Fönstret **Diagnostic Events** stöder filtrering, paus och inspektion av händelser i real tid.  Filtret är en enkel Strängs ökning av händelse meddelandet, inklusive dess innehåll.
   
    ![Filtrera, pausa och återuppta eller inspektera händelser i real tid][diagnosticeventsactions]
8. Fel söknings tjänster fungerar som fel sökning av andra program. Du väljer normalt Bryt punkter via Visual Studio för enkel fel sökning. Även om pålitliga samlingar replikeras över flera noder implementerar de fortfarande IEnumerable. Den här implementeringen innebär att du kan använda vyn resultat i Visual Studio när du felsöker för att se vad du har lagrat i. Det gör du genom att ange en Bryt punkt var som helst i koden.
   
    ![Starta fel sökning av ett program][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Köra ett skript som en del av fel sökning
I vissa fall kan du behöva köra ett skript som en del av att starta en felsökningssession (t. ex. När du inte använder standard tjänster).

I Visual Studio kan du lägga till en fil med namnet **Start-Service.ps1** i mappen **scripts** i Service Fabric program projekt (. sfproj). Det här skriptet startas när programmet har skapats i det lokala klustret.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Felsöka ett program för fjärran sluten Service Fabric
Om dina Service Fabric-program körs på ett Service Fabric-kluster i Azure kan du fjärrfelsöka dessa program direkt från Visual Studio.

> [!NOTE]
> Funktionen kräver [Service Fabric SDK 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) och [Azure sdk för .NET 2,9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> Fjärrfelsökning är avsedd för utvecklings-och test scenarier och ska inte användas i produktions miljöer, på grund av påverkan på program som körs.

1. Navigera till ditt kluster i **Cloud Explorer**. Högerklicka och välj **Aktivera fel sökning**
   
    ![Aktivera fjärrfelsökning][enableremotedebugging]
   
    Den här åtgärden startar processen att aktivera fjärrfelsöknings tillägget på klusternoderna och nödvändiga nätverkskonfigurationer.
2. Högerklicka på noden kluster i **Cloud Explorer** och välj **koppla fel sökare**
   
    ![Bifoga fel sökare][attachdebugger]
3. I dialog rutan **bifoga till process** väljer du den process som du vill felsöka och klickar på **Anslut**
   
    ![Välj process][chooseprocess]
   
    Namnet på den process som du vill koppla till, motsvarar namnet på ditt tjänste projekts sammansättnings namn.
   
    Fel söknings programmet kommer att kopplas till alla noder som kör processen.
   
   * Om du felsöker en tillstånds lös tjänst är alla instanser av tjänsten på alla noder en del av felsökningssessionen.
   * Om du felsöker en tillstånds känslig tjänst är det bara den primära repliken av alla partitioner som är aktiv och därför fångas av fel söknings programmet. Om den primära repliken flyttas under felsökningssessionen, kommer bearbetningen av repliken fortfarande att vara en del av fel söknings sessionen.
   * Om du bara vill fånga relevanta partitioner eller instanser av en angiven tjänst kan du använda villkorliga Bryt punkter för att bara bryta en specifik partition eller instans.
     
     ![Villkorlig Bryt punkt][conditionalbreakpoint]
     
     > [!NOTE]
     > För närvarande stöder vi inte fel sökning av ett Service Fabric kluster med flera instanser av samma körbara tjänst namn.
     > 
     > 
4. När du har slutfört fel sökningen av programmet kan du inaktivera tillägget för fjärrfelsökning genom att högerklicka på klustret i **Cloud Explorer** och välja **inaktivera fel sökning**
   
    ![Inaktivera fjärrfelsökning][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Strömma spårningar från en fjärrklusternod
Du kan också strömma spårningar direkt från en klusternod till Visual Studio. Med den här funktionen kan du strömma ETW-spårnings händelser som skapas på en Service Fabric klusternod.

> [!NOTE]
> Den här funktionen kräver [Service Fabric SDK 2,0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) och [Azure sdk för .NET 2,9](https://azure.microsoft.com/downloads/).
> Den här funktionen stöder endast kluster som körs i Azure.
> 
> 

<!-- -->
> [!WARNING]
> Strömnings spårningar är avsedda för utvecklings-och test scenarier och ska inte användas i produktions miljöer, på grund av påverkan på program som körs.
> I ett produktions scenario bör du förlita dig på att vidarebefordra händelser med Azure-diagnostik.

1. Navigera till ditt kluster i **Cloud Explorer**. Högerklicka och välj **Aktivera strömmande spår**
   
    ![Aktivera fjärrstyrda direkt uppspelnings spårningar][enablestreamingtraces]
   
    Den här åtgärden startar processen att aktivera tillägget för strömnings spårning på klusternoderna, samt nödvändiga nätverkskonfigurationer.
2. Expandera elementet **Nodes** i **Cloud Explorer**, högerklicka på den nod som du vill strömma spårning från och välj **Visa strömmande spårningar**
   
    ![Visa fjärrstyrda direkt uppspelnings spårningar][viewremotestreamingtraces]
   
    Upprepa steg 2 för så många noder som du vill visa spårningar från. Varje nod i data strömmen visas i ett dedikerat fönster.
   
    Du kan nu se de spårningar som har utsänts av Service Fabric och dina tjänster. Om du vill filtrera händelserna så att de endast visar ett särskilt program, skriver du bara in namnet på programmet i filtret.
   
    ![Visa strömmande spår][viewingstreamingtraces]
3. När du är färdig med att strömma spårningar från klustret kan du inaktivera spårning av fjärrdirektuppspelninger genom att högerklicka på klustret i **Cloud Explorer** och välja **inaktivera strömmande spår**
   
    ![Inaktivera fjärrstyrda direkt uppspelnings spårningar][disablestreamingtraces]

## <a name="next-steps"></a>Nästa steg
* [Testa en Service Fabric-tjänst](service-fabric-testability-overview.md).
* [Hantera dina Service Fabric-program i Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
