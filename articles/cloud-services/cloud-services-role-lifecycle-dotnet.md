---
title: Hantera livs cykel händelser för moln tjänst (klassisk) | Microsoft Docs
description: Lär dig hur du använder livs cykel metoder för en moln tjänst roll i .NET, inklusive RoleEntryPoint, som innehåller metoder för att svara på livs cykel händelser.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b5aa4bd061647f63ebcc70109f0ba21b39e814cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741340"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Anpassa livs cykeln för en webb-eller arbets roll i .NET

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

När du skapar en arbets roll utökar du klassen [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) som tillhandahåller metoder för att åsidosätta att du kan svara på livs cykel händelser. För webb roller är den här klassen valfri, så du måste använda den för att svara på livs cykel händelser.

## <a name="extend-the-roleentrypoint-class"></a>Utöka RoleEntryPoint-klassen
Klassen [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) innehåller metoder som anropas av Azure när den **startas**, **körs** eller **stoppar** en webb-eller arbets roll. Du kan välja att åsidosätta dessa metoder för att hantera roll initiering, sekvenser av roll avstängning eller körnings tråd för rollen. 

När du utökar **RoleEntryPoint** bör du vara medveten om följande beteenden för metoderna:

* Metoden [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) returnerar ett booleskt värde, så det är möjligt att returnera **false** från den här metoden.
  
   Om din kod returnerar **false** avbryts roll processen plötsligt, utan att du behöver köra någon avstängnings sekvens som du har på plats. I allmänhet bör du undvika att returnera **falskt** från metoden **OnStart** .
* Undantag som inte har fångats i en överlagring av en **RoleEntryPoint** -Metod behandlas som ett ohanterat undantag.
  
   Om ett undantag inträffar inom en av livs cykel metoderna kommer Azure att höja [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) -händelsen och sedan avslutas processen. När rollen har kopplats från kommer den att startas om av Azure. När ett ohanterat undantag uppstår, aktive ras inte [stopp](/previous-versions/azure/reference/ee758136(v=azure.100)) händelsen och **OnStop** -metoden anropas inte.

Om din roll inte startar eller om du förbrukar mellan att initiera, upptagen och stoppa tillstånd, kan din kod leda till ett ohanterat undantag i en av livs cykel händelserna varje gång rollen startas om. I det här fallet använder du [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) -händelsen för att fastställa orsaken till undantaget och hanterar det på lämpligt sätt. Din roll kan också returneras från [körnings](/previous-versions/azure/reference/ee772746(v=azure.100)) metoden, vilket gör att rollen startas om. Mer information om distributions tillstånd finns i [vanliga problem som orsakar att roller återanvänds](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Om du använder Azure- **verktygen för Microsoft Visual Studio** för att utveckla ditt program, utökar rollens projekt mallar automatiskt **klassen RoleEntryPoint** åt dig, i filerna *webrole. cs* och *WorkerRole. cs* .
> 
> 

## <a name="onstart-method"></a>OnStart-metod
Metoden **OnStart** anropas när roll instansen tas online av Azure. Medan OnStart-koden körs markeras roll instansen som **upptagen** och ingen extern trafik dirigeras till den av belastningsutjämnaren. Du kan åsidosätta den här metoden för att utföra initierings arbete, till exempel att implementera händelse hanterare och starta [Azure-diagnostik](cloud-services-how-to-monitor.md).

Om **OnStart** returnerar **True** initieras instansen och Azure anropar metoden **RoleEntryPoint. Run** . Om **OnStart** returnerar **false** avslutas rollen omedelbart utan att några planerade avstängnings sekvenser körs.

I följande kod exempel visas hur du åsidosätter metoden **OnStart** . Den här metoden konfigurerar och startar en diagnostisk övervakare när roll instansen startar och konfigurerar en överföring av loggnings data till ett lagrings konto:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>OnStop-metod
**OnStop** -metoden anropas när en roll instans har tagits offline av Azure och innan processen avslutas. Du kan åsidosätta den här metoden för att anropa kod som krävs för att din roll instans ska stängas av.

> [!IMPORTANT]
> Kod som körs i **OnStop** -metoden har en begränsad tid att avslutas när den anropas av andra orsaker än en användarinitierad avstängning. När den här tiden har gått ut avslutas processen, så du måste se till att koden i **OnStop** -metoden kan köras snabbt eller tolereras som inte körs till slut för ande. Metoden **OnStop** anropas efter att **stopp** händelsen har Aktiver ATS.
> 
> 

## <a name="run-method"></a>Kör metod
Du kan åsidosätta **körnings** metoden för att implementera en tids krävande tråd för roll instansen.

Att åsidosätta **körnings** metoden krävs inte. standard implementeringen startar en tråd som alltid är i vilo läge. Om du åsidosätter **körnings** metoden ska koden blockeras på obestämd tid. Om **körnings** metoden returnerar, återvinns rollen automatiskt. med andra ord höjer Azure **stopp** händelsen och anropar metoden **OnStop** så att dina avstängnings sekvenser kan köras innan rollen kopplas från.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementera ASP.NET livs cykel metoder för en webb roll
Du kan använda ASP.NET livs cykel metoder, förutom de som tillhandahålls av klassen **RoleEntryPoint** , för att hantera initierings-och avstängnings sekvenser för en webbroll. Detta kan vara användbart för kompatibilitet om du använder en port för ett befintligt ASP.NET-program i Azure. ASP.NET livs cykel metoder kallas inifrån **RoleEntryPoint** -metoderna. **\_ Start** metoden för programmet anropas när metoden **RoleEntryPoint. OnStart** har slutförts. **Program \_ slut** metoden anropas innan metoden **RoleEntryPoint. OnStop** anropas.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapar ett moln tjänst paket](cloud-services-model-and-package.md).




