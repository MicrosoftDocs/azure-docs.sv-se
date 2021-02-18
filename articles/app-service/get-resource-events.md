---
title: Hämta resurs händelser i Azure App Service
description: Lär dig hur du hämtar resurs händelser via aktivitets loggar och Event Grid i App Service-appen.
ms.topic: article
ms.date: 04/24/2020
ms.author: msangapu
ms.openlocfilehash: c20028a4f84dae9d292cf855a1e164bd69864909
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100574037"
---
# <a name="get-resource-events-in-azure-app-service"></a>Hämta resurs händelser i Azure App Service

Azure App Service innehåller inbyggda verktyg för att övervaka resursernas status och hälsa. Resurs händelser hjälper dig att förstå eventuella ändringar som har gjorts i dina underliggande webb program resurser och vidta åtgärder vid behov. Exempel på händelser är: skalning av instanser, uppdateringar av program inställningar, omstart av webbappen och många fler. I den här artikeln får du lära dig hur du visar [Azures aktivitets loggar](../azure-monitor/essentials/activity-log.md#view-the-activity-log) och aktiverar [Event Grid](../event-grid/index.yml) för att övervaka resurs händelser som är relaterade till din app service webbapp.

> [!NOTE]
> App Service-integrering med Event Grid är i för **hands version**. [Visa meddelandet för mer information.](https://aka.ms/app-service-event-grid-announcement)
>

## <a name="view-azure-activity-logs"></a>Visa Azures aktivitets loggar
Azure aktivitets loggar innehåller resurs händelser som har avsänts av åtgärder som vidtagits på resurserna i din prenumeration. Både användar åtgärderna i Azure Portal-och Azure Resource Manager-mallarna bidrar till de händelser som registrerats av aktivitets loggen. 

Azure aktivitets loggar för App Service information som:
- vilka åtgärder utfördes på resurserna (t. ex.: App Service planer)
- Vem startade åtgärden
- När åtgärden utfördes
- åtgärdens status
- egenskaps värden som hjälper dig att undersöka åtgärden

### <a name="what-can-you-do-with-azure-activity-logs"></a>Vad kan du göra med Azure aktivitets loggar?

Azure aktivitets loggar kan frågas med hjälp av Azure Portal, PowerShell, REST API eller CLI. Du kan skicka loggarna till ett lagrings konto, Händelsehubben och Log Analytics. Du kan också analysera dem i Power BI eller skapa aviseringar för att hålla uppdaterad om resurs händelser.

[Visa och hämta Azure aktivitets logg händelser.](../azure-monitor/essentials/activity-log.md#view-the-activity-log)

## <a name="ship-activity-logs-to-event-grid"></a>Skicka aktivitets loggar till Event Grid

Medan aktivitets loggarna är användarspecifika är det en ny [Event Grid](../event-grid/index.yml) -integration med App Service (för hands version) som loggar både användar åtgärder och automatiserade händelser. Med Event Grid kan du konfigurera en hanterare för att reagera på dessa händelser. Använd exempelvis Event Grid för att direkt utlösa att en funktion utan server kör bildanalys varje gång ett nytt foto läggs till i en bloblagringscontainer.

Du kan även använda Event Grid med Logic Apps för att bearbeta data var som helst, utan att skriva någon kod. Event Grid kopplar samman datakällor och händelsehanterare. Använd exempelvis Event Grid för att direkt utlösa att en funktion utan server kör bildanalys varje gång ett nytt foto läggs till i en bloblagringscontainer.

[Visa egenskaper och schema för Azure App Service händelser.](../event-grid/event-schema-app-service.md)

## <a name="next-steps"></a><a name="nextsteps"></a> Nästa steg
* [Fråga efter loggar med Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Övervaka Azure App Service](web-sites-monitor.md)
* [Felsöka Azure App Service i Visual Studio](troubleshoot-dotnet-visual-studio.md)
* [Analysera app-loggar i HDInsight](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)