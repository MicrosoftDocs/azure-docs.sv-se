---
title: Utveckla och distribuera WebJobs med Visual Studio
description: Lär dig hur du utvecklar Azure WebJobs i Visual Studio och distribuerar dem till Azure App Service, inklusive hur du skapar en schemalagd aktivitet.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: de10903be86b52b3415b57a53be81e7fd1661f63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89226037"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Utveckla och distribuera WebJobs med Visual Studio

Den här artikeln förklarar hur du använder Visual Studio för att distribuera ett konsol program projekt till en webbapp i [Azure App Service](overview.md) som ett [Azure-webbjobb](https://go.microsoft.com/fwlink/?LinkId=390226). Information om hur du distribuerar WebJobs med hjälp av [Azure Portal](https://portal.azure.com)finns i [köra bakgrunds aktiviteter med webjobs i Azure App Service](webjobs-create.md).

Du kan välja att utveckla ett webb jobb som körs antingen som en [.net Core-app](#webjobs-as-net-core-console-apps) eller en [.NET Framework app](#webjobs-as-net-framework-console-apps). Med version 3. x av [Azure WEBJOBS SDK](webjobs-sdk-how-to.md) kan du utveckla WebJobs som körs som antingen .net Core-appar eller .NET Framework appar, medan version 2. x bara stöder .NET Framework. Hur du distribuerar ett WebJobs-projekt skiljer sig åt för .NET Core-projekt än för .NET Framework projekt.

Du kan publicera flera WebJobs i en enda webbapp, förutsatt att varje webbjobb i en webbapp har ett unikt namn.

## <a name="webjobs-as-net-core-console-apps"></a>WebJobs som .NET Core Console-appar

Med version 3. x av Azure WebJobs SDK kan du skapa och publicera webbjobb som .NET Core Console-appar. Stegvisa instruktioner för att skapa och publicera en .NET Core-konsol-app till Azure som ett webb jobb finns i [komma igång med Azure WEBJOBS SDK för händelse driven bakgrunds bearbetning](webjobs-sdk-get-started.md).

> [!NOTE]
> Det går inte att länka .NET Core-webbjobb till webb projekt. Om du behöver distribuera ditt webbjobb med en webbapp, [skapar du dina WebJobs som en .NET Framework-konsol program](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Distribuera till Azure App Service

Publicering av ett .NET Core-webbjobb till Azure App Service från Visual Studio använder samma verktyg som publicering av en ASP.NET Core-app.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>Webbjobb som .NET Framework-konsol program  

Om du använder Visual Studio för att distribuera ett WebJobs-aktiverat .NET Framework-konsolprogram, kopieras runtime-filer till lämplig mapp i webbappen (*App_Data/Jobs/Continuous* för kontinuerliga webbjobb och *App_Data/Jobs/triggered* för schemalagda webb jobb på begäran).

Visual Studio lägger till följande objekt i ett WebJobs-aktiverat projekt:

* [Microsoft. Web. WebJobs. Publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-paketet.
* En [webjob-publish-settings.jspå](#publishsettings) en fil som innehåller distributions-och Scheduler-inställningar. 

![Diagram som visar vad som har lagts till i en konsol app för att aktivera distribution som ett webb jobb](./media/webjobs-dotnet-deploy-vs/convert.png)

Du kan lägga till dessa objekt i ett befintligt konsol program eller använda en mall för att skapa ett nytt webbjobb-aktiverat-konsol program. 

Distribuera ett projekt som ett webb jobb eller länka det till ett webb projekt så att det automatiskt distribueras när du distribuerar webb projektet. För att länka projekt innehåller Visual Studio namnet på det WebJobs-aktiverade projektet i en [webjobs-list.jspå](#webjobslist) filen i webbprojektet.

![Diagram över projekt för webb jobb som länkar till webb projekt](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Förutsättningar

Installera Visual Studio 2017 eller Visual Studio 2019 med [arbets belastningen Azure Development](/visualstudio/install/install-visual-studio#step-4---choose-workloads).

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> Aktivera distribution av WebJobs för ett befintligt konsol program projekt

Du kan välja mellan två alternativ:

* [Aktivera automatisk distribution med ett webb projekt](#convertlink).

  Konfigurera ett befintligt konsol program projekt så att det automatiskt distribueras som ett webb jobb när du distribuerar ett webb projekt. Använd det här alternativet om du vill köra ditt webb jobb i samma webbapp som du kör det relaterade webb programmet i.

* [Aktivera distribution utan ett webb projekt](#convertnolink).

  Konfigurera ett befintligt konsol program projekt som ska distribueras som ett webb jobb separat, utan en länk till ett webb projekt. Använd det här alternativet om du vill köra ett webbjobb i en webbapp med sig själv, utan att något webb program körs i webbappen. Du kanske vill göra detta för att skala dina webb jobbs resurser oberoende av dina webb programs resurser.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Aktivera automatisk distribution av WebJobs med ett webb projekt

1. Högerklicka på webb projektet i **Solution Explorer** och välj sedan **Lägg till**  >  **befintligt projekt som Azure-webbjobb**.
   
    ![Befintligt projekt som Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Dialog rutan [Lägg till Azure-webbjobb](#configure) visas.
2. I list rutan **projekt namn** väljer du det konsol program som ska läggas till som ett webb jobb.
   
    ![Välja projekt i dialog rutan Lägg till Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Slutför dialog rutan [Lägg till Azure-webbjobb](#configure) och välj sedan **OK**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Aktivera distribution av WebJobs utan ett webb projekt
1. Högerklicka på projektet för konsol programmet i **Solution Explorer** och välj sedan **Publicera som Azure-webbjobb**. 
   
    ![Publicera som Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Dialog rutan [Lägg till Azure-webbjobb](#configure) visas med projektet markerat i rutan **projekt namn** .
2. Slutför dialog rutan [Lägg till Azure-webbjobb](#configure) och välj sedan **OK**.
   
   Guiden **Publicera webbplats** visas. Om du inte vill publicera omedelbart stänger du guiden. De inställningar du har angett sparas för när du vill [distribuera projektet](#deploy).

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Skapa ett nytt WebJobs-aktiverat projekt
Om du vill skapa ett nytt WebJobs-aktiverat projekt använder du projekt mal len för konsol programmet och aktiverar distribution av WebJobs enligt beskrivningen i [föregående avsnitt](#convert). Som ett alternativ kan du använda mallen WebJobs New-Project:

* [Använd mallen WebJobs New-Project för ett oberoende webbjobb](#createnolink)
  
    Skapa ett projekt och konfigurera det för att distribuera det som ett webb jobb, utan länk till ett webb projekt. Använd det här alternativet om du vill köra ett webbjobb i en webbapp med sig själv, utan att något webb program körs i webbappen. Du kanske vill göra detta för att skala dina webb jobbs resurser oberoende av dina webb programs resurser.
* [Använd mallen WebJobs New-Project för ett webb jobb som är länkat till ett webb projekt](#createlink)
  
    Skapa ett projekt som är konfigurerat för att distribuera automatiskt som ett webb jobb när du distribuerar ett webb projekt i samma lösning. Använd det här alternativet om du vill köra ditt webb jobb i samma webbapp som du kör det relaterade webb programmet i.

> [!NOTE]
> Med mallen WebJobs New-Project installeras automatiskt NuGet-paket och innehåller kod i *program. cs* för [WebJobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). Om du inte vill använda WebJobs SDK tar du bort eller ändrar `host.RunAndBlock` instruktionen i *program. cs*.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> Använd mallen WebJobs New-Project för ett oberoende webbjobb
1. Välj **Arkiv**  >  **nytt**  >  **projekt**. I dialog rutan **Crete ett nytt projekt** söker du efter och väljer **Azure-webbjobb (.NET Framework)** för C#.
   
2. Följ föregående anvisningar om du vill [göra ett projekt i en oberoende WebJobs](#convertnolink)-projekt.

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> Använd mallen WebJobs New-Project för ett webb jobb som är länkat till ett webb projekt
1. Högerklicka på webb projektet i **Solution Explorer** och välj sedan **Lägg till**  >  **nytt Azure-webbjobb-projekt**.
   
    ![Nytt projekt meny alternativ för Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Dialog rutan [Lägg till Azure-webbjobb](#configure) visas.
2. Slutför dialog rutan [Lägg till Azure-webbjobb](#configure) och välj sedan **OK**.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a>webjob-publish-settings.jspå fil
När du konfigurerar en konsol app för distribution av WebJobs, installerar Visual Studio [Microsoft. Web. WebJobs. publicera](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-paket och lagrar schemaläggnings information i en *webjob-publish-settings.jspå* filen i mappen Project *Properties* i projektet WebJobs. Här är ett exempel på filen:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

Du kan redigera den här filen direkt och Visual Studio innehåller IntelliSense. Fil schemat lagras på [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) och kan visas där.  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a>webjobs-list.jspå fil
När du länkar ett WebJobs-aktiverat projekt till ett webb projekt, lagrar Visual Studio namnet på WebJobs-projektet i en *webjobs-list.jspå* filen i webbprojektets *egenskaps* -mapp. Listan kan innehålla flera jobb projekt, som du ser i följande exempel:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
  "WebJobs": [
    {
      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
    },
    {
      "filePath": "../WebJob1/WebJob1.csproj"
    }
  ]
}
```

Du kan redigera den här filen direkt i Visual Studio med IntelliSense. Fil schemat lagras på [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) .

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Distribuera ett WebJobs-projekt
Ett WebJobs-projekt som du har länkat till ett webb projekt distribueras automatiskt med webbprojektet. Information om distribution av webb projekt finns i **instruktions guider**  >  **distribuera appen** i det vänstra navigerings fältet.

Om du vill distribuera ett WebJobs-projekt själva högerklickar du på projektet i **Solution Explorer** och väljer **Publicera som Azure**-webbjobb. 

![Publicera som Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/paw.png)

Samma **publicerings webb** guide som används för webb projekt visas för ett oberoende webb jobb, men med färre inställningar som kan ändras.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Dialog rutan Lägg till Azure-webbjobb
I dialog rutan **Lägg till Azure-webbjobb** kan du ange webb jobbets namn och inställningen körnings läge för ditt webb jobb. 

![Dialog rutan Lägg till Azure-webbjobb](./media/webjobs-dotnet-deploy-vs/aaw2.png)

Några av fälten i den här dialog rutan motsvarar fält i dialog rutan **Lägg till webbjobb** i Azure Portal. Mer information finns i [köra bakgrunds aktiviteter med WebJobs i Azure App Service](webjobs-create.md).

Distributions information för webbjobb:

* Information om distribution av kommando raden finns i [Aktivera kommando rads-eller kontinuerlig leverans av Azure WebJobs](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).

* Om du distribuerar ett webb jobb och sedan bestämmer dig för att ändra typ av webb jobb och omdistribuera, tar du bort *webjobs-publish-settings.jspå* filen. Det gör att Visual Studio visar publicerings alternativen igen, så att du kan ändra typ av webb jobb.

* Om du distribuerar ett webb jobb och senare ändrar körnings läget från kontinuerlig till icke-kontinuerlig eller vice versa, skapar Visual Studio ett nytt webbjobb i Azure när du distribuerar om. Om du ändrar andra schema inställningar, men låter körnings läget vara samma eller växla mellan schemalagda och på begäran, uppdaterar Visual Studio det befintliga jobbet i stället för att skapa ett nytt.

## <a name="webjob-types"></a>Webb jobb typer

Typen av ett webbjobb kan antingen *utlösas* eller vara *kontinuerlig*:

- Utlöst (standard): ett utlöst webb jobb startar baserat på en bindnings händelse, enligt ett [schema](#scheduling-a-triggered-webjob)eller när du utlöser det manuellt (på begäran). Den körs på en enda instans som webbappen körs på.

- Kontinuerlig: ett [kontinuerligt](#continuous-execution) webbjobb startar omedelbart när webb jobbet skapas. Den körs på alla skalbara instanser av webb program som standard, men kan konfigureras att köras som en enskild instans via *Inställningar. job*.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>Schemalägga ett utlöst webbjobb

När du publicerar en-konsol app till Azure anger Visual Studio vilken typ av webb jobb som ska **aktive** ras som standard och lägger till en ny *Settings. job* -fil i projektet. För utlösta webb jobb typer kan du använda den här filen för att ange ett körnings schema för ditt webb jobb.

Använd filen *Settings. job* för att ange ett körnings schema för ditt webb jobb. Följande exempel körs varje timme från och med 9 till 5 PM:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Den här filen finns i roten i mappen WebJobs med ditt webbjobbs skript, till exempel `wwwroot\app_data\jobs\triggered\{job name}` eller `wwwroot\app_data\jobs\continuous\{job name}` . När du distribuerar ett webb jobb från Visual Studio markerar du *inställningarna.* egenskaper för jobb fil i Visual Studio som **Kopiera om nyare**.

Om du [skapar ett webb jobb från Azure Portal](webjobs-create.md)skapas *Inställningar. job* -filen åt dig.

#### <a name="cron-expressions"></a>CRON-uttryck

WebJobs använder samma CRON-uttryck för schemaläggning som timer-utlösaren i Azure Functions. Mer information om CRON-support finns i [timer-utlösare för Azure Functions](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>inställningar. jobb referens

Följande inställningar stöds av WebJobs:

| **Inställning** | **Typ**  | **Beskrivning** |
| ----------- | --------- | --------------- |
| `is_in_place` | Alla | Tillåter att webb jobbet körs på plats utan att först kopieras till en tillfällig mapp. Mer information finns i [arbets katalogen för webb jobbet](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Kontinuerlig igenkänning | Kör bara webb jobbet på en enskild instans vid utskalning. Mer information finns i [Ange ett kontinuerligt jobb som singleton](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton). |
| `schedule` | Utlöses | Kör webb jobbet på ett CRON-baserat schema. Mer information finns i [NCRONTAB-uttryck](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Alla | Tillåter kontroll av avstängnings beteendet. Mer information finns i en [korrekt avstängning](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

### <a name="continuous-execution"></a>Kontinuerlig körning

Om du aktiverar **Always on** i Azure kan du använda Visual Studio för att ändra att webb jobbet körs kontinuerligt:

1. [Publicera projektet till Azure](#deploy-to-azure-app-service)om du inte redan gjort det.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**.

1. På fliken **publicera** väljer du **Redigera**. 

1. I dialog rutan **profil inställningar** väljer du **kontinuerlig** för **jobb typ** och väljer sedan **Spara**.

    ![Dialog rutan publicera inställningar för ett webb jobb](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. Välj **publicera** på fliken **publicera** för att publicera webbjobbet igen med de uppdaterade inställningarna.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om WebJobs SDK](webjobs-sdk-how-to.md)