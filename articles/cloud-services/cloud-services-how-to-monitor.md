---
title: Övervaka en Azure-moln tjänst (klassisk) | Microsoft Docs
description: Beskriver vad som övervakar en Azure-molnbaserad tjänst och vilka alternativ som finns.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 60f320f86860cca482cdf25c7d93f84dae8c4e5f
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743380"
---
# <a name="introduction-to-cloud-service-classic-monitoring"></a>Introduktion till moln tjänst (klassisk) övervakning

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Du kan övervaka viktiga prestanda mått för alla moln tjänster. Varje moln tjänst roll samlar in minimala data: CPU-användning, nätverks användning och disk användning. Om `Microsoft.Azure.Diagnostics` tillägget används för en roll i moln tjänsten kan den rollen samla in ytterligare data punkter. Den här artikeln innehåller en introduktion till Azure-diagnostik för Cloud Services.

Med grundläggande övervakning samplas prestanda räknar data från roll instanser och samlas in med 3 minuters intervall. Dessa grundläggande övervaknings data lagras inte i ditt lagrings konto och har ingen ytterligare kostnad kopplad till sig.

Med avancerad övervakning samplas ytterligare mått och samlas in i intervall om 5 minuter, 1 timme och 12 timmar. De aggregerade data lagras i ett lagrings konto i tabeller och rensas efter 10 dagar. Det använda lagrings kontot har kon figurer ATS av rollen. Du kan använda olika lagrings konton för olika roller. Detta konfigureras med en anslutnings sträng i [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) -och [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) -filerna.


## <a name="basic-monitoring"></a>Grundläggande övervakning

Som anges i introduktionen samlar en moln tjänst automatiskt in grundläggande övervaknings data från den virtuella värd datorn. Dessa data omfattar processor procent, nätverk in/ut och läsning/skrivning till disk. Insamlade övervaknings data visas automatiskt på sidan Översikt och mått i moln tjänsten i Azure Portal. 

Grundläggande övervakning kräver inte något lagrings konto. 

![grundläggande övervaknings paneler i Cloud service](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Avancerad övervakning

Avancerad övervakning innebär att använda **Azure-diagnostik** tillägget (och eventuellt Application Insights SDK) för den roll som du vill övervaka. Tillägget för diagnostik använder en konfigurations fil (per roll) med namnet **Diagnostics. wadcfgx** för att konfigurera de diagnostik mått som övervakas. Azure Diagnostic-tillägget samlar in och lagrar data i ett Azure Storage-konto. Dessa inställningar konfigureras i **. wadcfgx**-, [. csdef](cloud-services-model-and-package.md#servicedefinitioncsdef)-och [. cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) -filerna. Det innebär att det finns en extra kostnad som är kopplad till avancerad övervakning.

När varje roll skapas lägger Visual Studio till Azure-diagnostik-tillägget till den. Det här diagnostikprogrammet-tillägget kan samla in följande typer av information:

* Anpassade prestanda räknare
* Program loggar
* Windows-händelseloggar
* .NET-händelse källa
* IIS-loggar
* Manifestbaserat ETW
* Kraschdumpar
* Kund fel loggar

> [!IMPORTANT]
> Även om alla dessa data sammanställs i lagrings kontot, ger portalen **inget** inbyggt sätt att skapa diagram över data. Vi rekommenderar starkt att du integrerar en annan tjänst, t. ex. Application Insights, i ditt program.

## <a name="setup-diagnostics-extension"></a>Konfigurera diagnostik-tillägg

Börja med att [skapa ett](../storage/common/storage-account-create.md) **klassiskt** lagrings konto om du inte har det. Kontrol lera att lagrings kontot har skapats med den **klassiska distributions modellen** angiven.

Gå sedan till **lagrings konto resursen (klassisk)** . Välj **Inställningar**  >  **åtkomst nycklar** och kopiera värdet för **primär anslutnings sträng** . Du behöver det här värdet för moln tjänsten. 

Det finns två konfigurationsfiler du måste ändra för att avancerad diagnostik ska vara aktive rad, **service definition. csdef** och **ServiceConfiguration. cscfg**.

### <a name="servicedefinitioncsdef"></a>Service definition. csdef

I filen **service definition. csdef** lägger du till en ny inställning `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` som heter för varje roll som använder avancerad diagnostik. Visual Studio lägger till det här värdet i filen när du skapar ett nytt projekt. Om det saknas kan du lägga till det nu. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Detta definierar en ny inställning som måste läggas till i varje **ServiceConfiguration. cscfg** -fil. 

Förmodligen har du två **. cscfg** -filer, en med namnet **ServiceConfiguration. Cloud. cscfg** för att distribuera till Azure och en namngiven **ServiceConfiguration. local. cscfg** som används för lokala distributioner i den emulerade miljön. Öppna och ändra varje **. cscfg** -fil. Lägg till en inställning med namnet `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` . Ange värdet till den **primära anslutnings strängen** för det klassiska lagrings kontot. Använd om du vill använda den lokala lagrings platsen på din utvecklings dator `UseDevelopmentStorage=true` .

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Använd Application Insights

När du publicerar moln tjänsten från Visual Studio får du möjlighet att skicka diagnostikdata till Application Insights. Du kan skapa Application Insights Azure-resursen vid den tiden eller skicka data till en befintlig Azure-resurs. Din moln tjänst kan övervakas av Application Insights för tillgänglighet, prestanda, haverier och användning. Du kan lägga till anpassade diagram i Application Insights så att du kan se de data som är mest viktiga. Roll instans data kan samlas in med hjälp av Application Insights SDK i moln tjänst projektet. Mer information om hur du integrerar Application Insights finns [Application Insights med Cloud Services](../azure-monitor/app/cloudservices.md).

Observera att du kan använda Application Insights för att visa prestanda räknarna (och de andra inställningarna) som du har angett via Windows Azure-diagnostik-tillägget, men du får bara en rikare upplevelse genom att integrera Application Insights SDK i din arbets plats och dina webb roller.


## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om Application Insights med Cloud Services](../azure-monitor/app/cloudservices.md)
- [Konfigurera prestanda räknare](diagnostics-performance-counters.md)




