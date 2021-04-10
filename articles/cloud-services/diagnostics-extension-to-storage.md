---
title: Lagra och visa diagnostikdata i Azure Storage
description: Lär dig hur du samlar in Azure Diagnostics-data i ett Azure Storage-konto så att du kan visa det med ett av flera tillgängliga verktyg.
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 4962d5f048cf41eca50a77a0dedad3cef48ac1f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98740082"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Lagra och visa diagnostikdata i Azure Storage

> [!IMPORTANT]
> [Azure Cloud Services (utökad support)](../cloud-services-extended-support/overview.md) är en ny Azure Resource Manager baserad distributions modell för Azure Cloud Services-produkten.Med den här ändringen har Azure Cloud Services som körs på Azure Service Manager-baserade distributions modellen bytt namn som Cloud Services (klassisk) och alla nya distributioner bör använda [Cloud Services (utökad support)](../cloud-services-extended-support/overview.md).

Diagnostikdata lagras inte permanent om du inte överför den till Microsoft Azure Storage-emulator eller Azure Storage. En gång i lagret kan visas med ett av flera tillgängliga verktyg.

## <a name="specify-a-storage-account"></a>Ange ett lagrings konto
Du anger det lagrings konto som du vill använda i filen ServiceConfiguration. cscfg. Konto informationen definieras som en anslutnings sträng i en konfigurations inställning. I följande exempel visas standard anslutnings strängen som skapats för ett nytt moln tjänst projekt i Visual Studio:

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

Du kan ändra den här anslutnings strängen för att tillhandahålla konto information för ett Azure Storage-konto.

Beroende på vilken typ av diagnostikdata som samlas in använder Azure-diagnostik antingen Blob Service eller Table service. I följande tabell visas de data källor som är beständiga och deras format.

| Datakälla | Lagrings format |
| --- | --- |
| Azure-loggar |Tabell |
| IIS 7,0-loggar |Blob |
| Azure Diagnostics-infrastrukturloggar |Tabell |
| Spårnings loggar för misslyckade begär Anden |Blob |
| Händelse loggar i Windows |Tabell |
| Prestandaräknare |Tabell |
| Kraschdumpar |Blob |
| Anpassa felloggar |Blob |

## <a name="transfer-diagnostic-data"></a>Överför diagnostikdata
För SDK 2,5 och senare kan begäran om att överföra diagnostikdata ske via konfigurations filen. Du kan överföra diagnostikdata enligt schemalagda intervall som anges i konfigurationen.

För SDK 2,4 och tidigare kan du begära att överföra diagnostikdata via konfigurations filen och program mässigt. Med programmerings metoden kan du också utföra överföringar på begäran.

> [!IMPORTANT]
> När du överför diagnostikdata till ett Azure Storage-konto debiteras du kostnader för de lagrings resurser som dina diagnostikdata använder.
> 
> 

## <a name="store-diagnostic-data"></a>Lagra diagnostikdata
Loggdata lagras antingen i BLOB-eller Table Storage med följande namn:

**Tabeller**

* **WadLogsTable** – loggar som skrivits i kod med spårnings lyssnaren.
* **WADDiagnosticInfrastructureLogsTable** – diagnostisk övervakning och konfigurations ändringar.
* **WADDirectoriesTable** – kataloger som diagnostiken övervakar.  Detta inkluderar IIS-loggar, IIS misslyckade begär Anden och anpassade kataloger.  Platsen för blobb logg filen anges i fältet container och namnet på blobben är i fältet RelativePath.  Fältet AbsolutePath anger platsen och namnet på filen som den fanns på den virtuella Azure-datorn.
* **WADPerformanceCountersTable** – prestanda räknare.
* **WADWindowsEventLogsTable** – händelse loggar i Windows.

**Blobar**

* **wad-Control-container** – (endast för SDK 2,4 och tidigare) innehåller de XML-konfigurationsfiler som styr Azure Diagnostics.
* **wad-IIS-failedreqlogfiles** – innehåller information från IIS misslyckade begär Anden om misslyckade förfrågningar.
* **wad-IIS-LogFiles** – innehåller information om IIS-loggar.
* **anpassad** – en anpassad behållare baserat på konfigurations kataloger som övervakas av diagnostiken.  Namnet på den här BLOB-behållaren anges i WADDirectoriesTable.

## <a name="tools-to-view-diagnostic-data"></a>Verktyg för att Visa diagnostikdata
Det finns flera verktyg för att visa data när de har överförts till lagring. Exempel:

* Server Explorer i Visual Studio – om du har installerat Azure-verktygen för Microsoft Visual Studio kan du använda noden Azure Storage i Server Explorer för att visa skrivskyddade blob-och tabell data från dina Azure Storage-konton. Du kan visa data från ditt lokala Storage emulator-konto och även från lagrings konton som du har skapat för Azure. Mer information finns i avsnittet [om att bläddra och hantera lagrings resurser med Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage).
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) är en fristående app som gör det enkelt att arbeta med Azure Storage data på Windows, OSX och Linux.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) innehåller Azure-diagnostik Manager som gör att du kan visa, ladda ned och hantera de diagnostikdata som samlas in av programmen som körs på Azure.

## <a name="next-steps"></a>Nästa steg
[Spåra flödet i ett Cloud Services program med Azure-diagnostik](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


