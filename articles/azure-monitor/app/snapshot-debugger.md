---
title: Azure Application insikter Snapshot Debugger för .NET-appar
description: Fel söknings ögonblicks bilder samlas in automatiskt när undantag genereras i produktions-.NET-appar
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 10/23/2019
author: cweining
ms.author: cweining
ms.reviewer: cweining
ms.openlocfilehash: bd196e60fed8a18064bfa59e343e51a878a11237
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217405"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Ögonblicksbilder för felsökning av undantag i .NET-appar
När ett undantag inträffar kan du automatiskt samla in en fel söknings ögonblicks bild från Live-webbappen. I ögonblicks bilden visas statusen för käll koden och variablerna vid det tillfälle då undantaget uppstod. Snapshot Debugger i [Azure Application Insights](./app-insights-overview.md) övervakar undantags telemetri från din webbapp. Den samlar in ögonblicks bilder i de övergivna undantagen så att du har den information du behöver för att diagnostisera problem i produktionen. Ta med [Snapshot Collector NuGet-paketet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) i ditt program och konfigurera sedan samlings parametrar i [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Ögonblicks bilder visas på [undantag](./asp-net-exceptions.md) i Application Insights portalen.

Du kan visa de här ögonblicksbilderna i portalen, se anropsstacken och inspektera variablerna på varje nivå av stacken. För att få en mer kraftfull fel söknings upplevelse med källkod öppnar du ögonblicks bilder med Visual Studio 2019 Enterprise. I Visual Studio kan du också [ställa in Snappoints så att interaktivt tar ögonblicks bilder](/visualstudio/debugger/debug-live-azure-applications) utan att vänta på ett undantag.

Fel söknings ögonblicks bilder lagras i 15 dagar. Den här bevarande principen anges per tillämpning. Om du behöver öka det här värdet kan du begära en ökning genom att öppna ett support ärende i Azure Portal.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Aktivera Application Insights Snapshot Debugger för ditt program
Ögonblicks bild samling är tillgänglig för:
* .NET Framework-och ASP.NET-program som kör .NET Framework 4,5 eller senare.
* .NET Core 2,0 och ASP.NET Core 2,0-program som körs på Windows.

Följande miljöer stöds:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure-funktion](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som kör OS-familjen 4 eller senare
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som körs på Windows Server 2012 R2 eller senare
* [Azure-Virtual Machines och skalnings uppsättningar för virtuella datorer som](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) kör Windows Server 2012 R2 eller senare
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) som kör Windows Server 2012 R2 eller senare eller Windows 8,1 eller senare

> [!NOTE]
> Klient program (till exempel WPF, Windows Forms eller UWP) stöds inte.

Om du har aktiverat Snapshot Debugger men inte ser ögonblicks bilder, se vår [fel söknings guide](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Bevilja behörigheter

Åtkomst till ögonblicks bilder skyddas av rollbaserad åtkomst kontroll i Azure (Azure RBAC). För att kunna se en ögonblicksbild måste en prenumerationsägare först lägga till dig i den roll som krävs.

> [!NOTE]
> Ägare och deltagare har inte den här rollen automatiskt. Om de vill visa ögonblicks bilder måste de lägga till sig själva i rollen.

Prenumerations ägare bör tilldela `Application Insights Snapshot Debugger` rollen till användare som ska inspektera ögonblicks bilder. Den här rollen kan tilldelas enskilda användare eller grupper efter prenumerations ägare för mål Application Insights resurs eller dess resurs grupp eller prenumeration.

1. Navigera till Application Insights resursen i Azure Portal.
1. Klicka på **Åtkomstkontroll (IAM)**.
1. Klicka på knappen **+ Lägg till roll tilldelning** .
1. Välj **Application Insights Snapshot debugger** i list rutan **roller** .
1. Sök efter och ange ett namn för den användare som ska läggas till.
1. Klicka på knappen **Spara** för att lägga till användaren i rollen.


> [!IMPORTANT]
> Ögonblicks bilder kan potentiellt innehålla personlig och annan känslig information i variabel-och parameter värden.

## <a name="view-snapshots-in-the-portal"></a>Visa ögonblicks bilder i portalen

När ett undantag har inträffat i ditt program och en ögonblicks bild har skapats bör du ha ögonblicks bilder att visa. Det kan ta 5 till 10 minuter från ett undantag som inträffar till en ögonblicks bild som är klar och kan visas från portalen. Om du vill visa ögonblicks bilder väljer du knappen **åtgärder** i rutan **fel** när du visar fliken **åtgärder** , eller så klickar du på knappen **undantag** när du visar fliken **undantag** :

![Sidan problem](./media/snapshot-debugger/failures-page.png)

Välj en åtgärd eller ett undantag i den högra rutan för att öppna fönstret **transaktions information från slut punkt till slut punkt** och välj sedan undantags händelsen. Om det finns en ögonblicks bild för det här undantaget visas en knapp för att **Öppna fel söknings ögonblicks bilder** i den högra rutan med information om [undantaget](./asp-net-exceptions.md).

![Knappen Öppna fel söknings ögonblicks bild vid undantag](./media/snapshot-debugger/e2e-transaction-page.png)

I vyn fel sökning Snapshot visas en anrops stack och ett variabel fönster. När du väljer bild rutor i anrops stacken i anrops stack fönstret kan du Visa lokala variabler och parametrar för det funktions anropet i fönstret variabler.

![Visa fel söknings ögonblicks bild i portalen](./media/snapshot-debugger/open-snapshot-portal.png)

Ögonblicks bilder kan innehålla känslig information och kan som standard inte visas. Om du vill visa ögonblicks bilder måste du ha `Application Insights Snapshot Debugger` rollen tilldelad till dig.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Visa ögonblicks bilder i Visual Studio 2017 Enterprise eller senare
1. Klicka på knappen **Hämta ögonblicks bild** för att ladda ned en `.diagsession` fil som kan öppnas av Visual Studio Enterprise.

2. `.diagsession`Du måste ha installerat Snapshot debugger Visual Studio-komponenten för att kunna öppna filen. Snapshot Debugger-komponenten är en obligatorisk komponent i ASP.net-arbetsbelastningen i Visual Studio och kan väljas från listan med enskilda komponenter i Visual Studio-installationsprogrammet. Om du använder en version av Visual Studio innan du börjar med Visual Studio 2017 version 15,5 måste du installera tillägget från [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. När du har öppnat ögonblicks bild filen visas sidan Minidump fel sökning i Visual Studio. Klicka på **Felsök hanterad kod** för att starta fel sökning av ögonblicks bilden. Ögonblicks bilden öppnas till den kodrad där undantaget utlöstes, så att du kan felsöka processens aktuella tillstånd.

    ![Visa fel söknings ögonblicks bild i Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Den hämtade ögonblicks bilden innehåller alla symboler som hittades på din webb program Server. Dessa symboler krävs för att associera ögonblicks bild data med käll koden. För App Service appar, se till att aktivera symbol distribution när du publicerar dina webb program.

## <a name="how-snapshots-work"></a>Hur ögonblicks bilder fungerar

Snapshot Collector implementeras som en [Application Insights Telemetry-processor](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). När ditt program körs läggs Snapshot Collector telemetri-processorn till i programmets telemetri-pipeline.
Varje gången ditt program anropar [TrackException](./asp-net-exceptions.md#exceptions)beräknar Snapshot Collector ett problem-ID från den typ av undantag som genereras och metoden Throw.
Varje gången ditt program anropar TrackException ökas en räknare för rätt problem-ID. När räknaren når `ThresholdForSnapshotting` värdet läggs problem-ID: t till i en samlings plan.

Snapshot Collector övervakar också undantag när de utlöses genom att prenumerera på händelsen [AppDomain. currentDomain. FirstChanceException](/dotnet/api/system.appdomain.firstchanceexception) . När händelsen utlöses beräknas problem-ID: t för undantaget och jämförs med problem-ID: n i samlings planen.
Om det finns en matchning, skapas en ögonblicks bild av processen som körs. Ögonblicks bilden tilldelas en unik identifierare och undantaget stämplas med den identifieraren. När FirstChanceException-hanteraren returnerar bearbetas det genererade undantaget som normalt. Slutligen når undantaget metoden TrackException igen där det, tillsammans med ögonblicks bilds-ID, rapporteras till Application Insights.

Huvud processen fortsätter att köra och betjäna trafik till användare med litet avbrott. Under tiden överförs ögonblicks bilden till överförings processen för ögonblicks bilder. Överföring av ögonblicks bilder skapar en MiniDump och laddar upp den till Application Insights tillsammans med eventuella relevanta symbol-filer (. pdb).

> [!TIP]
> - En ögonblicks bild av processen är en inaktive rad kloning av processen som körs.
> - Det tar cirka 10 till 20 millisekunder att skapa ögonblicks bilden.
> - Standardvärdet för `ThresholdForSnapshotting` är 1. Detta är också det lägsta värdet. Därför måste appen utlösa samma undantag **två gånger** innan en ögonblicks bild skapas.
> - Ange `IsEnabledInDeveloperMode` som sant om du vill skapa ögonblicks bilder under fel sökning i Visual Studio.
> - Hastigheten för skapande av ögonblicks bilder begränsas av `SnapshotsPerTenMinutesLimit` inställningen. Som standard är gränsen en ögonblicks bild var tionde minut.
> - Högst 50 ögonblicks bilder per dag kan laddas upp.

## <a name="limitations"></a>Begränsningar

Standardvärdet för kvarhållning av data är 15 dagar. För varje Application Insights instans tillåts ett maximalt antal 50 ögonblicks bilder per dag.

### <a name="publish-symbols"></a>Publicera symboler
Snapshot Debugger kräver symbolvarianter på produktions servern för att avkoda variabler och för att ge en fel söknings upplevelse i Visual Studio.
Version 15,2 (eller senare) av Visual Studio 2017 publicerar symboler för versions hantering som standard när den publiceras till App Service. I tidigare versioner måste du lägga till följande rad i publicerings profil `.pubxml` filen så att symboler publiceras i versions läge:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

För Azure Compute och andra typer ser du till att symbol-filerna finns i samma mapp i huvud programmet. dll (vanligt vis `wwwroot/bin` ) eller är tillgängliga på den aktuella sökvägen.

> [!NOTE]
> Mer information om de olika symbol alternativen som är tillgängliga finns i [Visual Studio-dokumentationen](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). För bästa resultat rekommenderar vi att du använder "fullständig", "portabel" eller "inbäddad".

### <a name="optimized-builds"></a>Optimerade versioner
I vissa fall kan inte lokala variabler visas i versions versioner på grund av optimeringar som tillämpas av JIT-kompilatorn.
Men i Azure App Services kan Snapshot Collector deoptimera de metoder som ingår i samlings planen.

> [!TIP]
> Installera det Application Insights webbplats tillägget i App Service för att få stöd för deoptimering.

## <a name="next-steps"></a>Nästa steg
Aktivera Application Insights Snapshot Debugger för ditt program:

* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure-funktion](snapshot-debugger-function-app.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-tjänster](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines och skalnings uppsättningar för virtuella datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Lokala virtuella eller fysiska datorer](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Utöver Application Insights Snapshot Debugger:
 
* [Ange snappoints i din kod](/visualstudio/debugger/debug-live-azure-applications) för att hämta ögonblicks bilder utan att vänta på ett undantag.
* [Diagnostisera undantag i dina webbappar](./asp-net-exceptions.md) förklarar hur du gör fler undantag synliga för Application Insights.
* [Smart identifiering](./proactive-diagnostics.md) identifierar automatiskt prestanda avvikelser.