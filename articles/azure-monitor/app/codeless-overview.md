---
title: Övervaka dina appar utan kod ändringar – Auto-Instrumentation för Azure Monitor Application Insights | Microsoft Docs
description: Översikt över Auto-Instrumentation för Azure Monitor Application Insights-programkodad hantering av program prestanda
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: efa951ce5a15460e3eacfd4c7abecfac17106b4e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880516"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Vad är Auto-Instrumentation eller kod fast kopplings Azure Monitor Application Insights?

Automatisk instrumentering eller kod lös koppling gör att du kan aktivera program övervakning med Application Insights utan att ändra koden.  

Application Insights är integrerat med olika resurs leverantörer och fungerar i olika miljöer. Allt du behöver göra är att aktivera och i vissa fall Konfigurera agenten, som samlar in telemetrina från kartongen. I ingen tid ser du Mät värden, data och beroenden i din Application Insights resurs, vilket gör att du kan hitta källan till eventuella problem innan de uppstår och analysera rotor saken med en transaktion från slut punkt till slut punkt.

## <a name="supported-environments-languages-and-resource-providers"></a>Miljöer, språk och resurs leverantörer som stöds

När vi lägger till fler och fler integreringar blir matrisen för automatiska instrument funktioner komplexa. I tabellen nedan visas det aktuella läget för frågan, i mån av stöd för olika resurs leverantörer, språk och miljöer.

|Miljö/resurs-Provider          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service i Windows           | GA, OnBD *       | GA, Anmäl dig      | Privat för hands version | Privat för hands version | Stöds inte   |
|Azure App Service på Linux             | Ej tillämpligt             | Stöds inte   | Privat för hands version | Offentlig för hands version  | Stöds inte   |
|Azure App Service på AKS               | Ej tillämpligt             | I design       | I design       | I design       | Stöds inte   |
|Azure Functions-Basic                | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions Windows-beroenden | Stöds inte   | Stöds inte   | Offentlig för hands version  | Stöds inte   | Stöds inte   |
|Azure Kubernetes Service               | Ej tillämpligt             | I design       | Via agent   | I design       | Stöds inte   |
|Azure VM-fönster                      | Offentlig för hands version  | Stöds inte   | Stöds inte   | Stöds inte   | Stöds inte   |
|Lokala virtuella dator fönster                | GA, Anmäl dig      | Stöds inte   | Via agent   | Stöds inte   | Stöds inte   |
|Fristående agent – valfritt kuvert.            | Stöds inte   | Stöds inte   | Allmän tillgänglighet (GA)              | Stöds inte   | Stöds inte   |

* OnBD är kort för på som standard – Application Insights aktive ras automatiskt när du har distribuerat din app i miljöer som stöds. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

[Program övervakning på Azure App Service](./azure-web-apps.md?tabs=net) är tillgängligt för .NET-program och är aktiverat som standard, kan .net Core aktive ras med ett klick och Java och Node.js finns i privat förhands granskning.

### <a name="linux"></a>Linux 

Övervakning av Java-och Node.js-program i App Service finns i offentlig för hands version och kan aktive ras i Azure Portal, som är tillgängligt i alla regioner.

## <a name="azure-functions"></a>Azure Functions

Den grundläggande övervakningen av Azure Functions är aktive rad som standard för att samla in logg, prestanda, fel data och HTTP-begäranden. För Java-program kan du aktivera bättre övervakning med distribuerad spårning och hämta transaktions information från slut punkt till slut punkt. Den här funktionen för Java är i offentlig för hands version och du kan [Aktivera den i Azure Portal](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Kodbaserade instrumentering av Azure Kubernetes-tjänsten är för närvarande tillgänglig för Java-program via den [fristående agenten](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Virtuella Azure Windows-datorer och skalnings uppsättningar för virtuella datorer

[Automatisk instrumentering för virtuella Azure-datorer och skalnings uppsättningar för virtuella datorer](./azure-vm-vmss-apps.md) är tillgängligt för .NET-program 

## <a name="on-premises-servers"></a>Lokala servrar
Du kan enkelt aktivera övervakning för dina [lokala Windows-servrar för .NET-program](./status-monitor-v2-overview.md) och för [Java-appar](./java-in-process-agent.md).

## <a name="other-environments"></a>Andra miljöer
Den mångsidiga, fristående Java-agenten fungerar i alla miljöer, men du behöver inte göra några instrument för din kod. [Följ guiden](./java-in-process-agent.md) för att aktivera Application Insights och läsa om de fantastiska funktionerna i Java-agenten. Agenten finns i en offentlig för hands version och är tillgänglig i alla regioner. 

## <a name="next-steps"></a>Nästa steg

* [Översikt över Application Insights](./app-insights-overview.md)
* [Program karta](./app-map.md)
* [Prestanda övervakning från slut punkt till slut punkt](../learn/tutorial-performance.md)

