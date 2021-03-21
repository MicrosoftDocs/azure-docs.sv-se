---
title: Azure Monitor terminologi-uppdateringar | Microsoft Docs
description: Beskriver de senaste terminologi ändringar som gjorts i Azure Monitoring Services.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: fea85a85db1e555adea63f5b65b09a9285d714da
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031981"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Azure Monitor namngivnings-och terminologi ändringar
Betydande ändringar har gjorts i Azure Monitor nyligen, med olika tjänster som sammanställs för att förenkla övervakningen av Azure-kunder. I den här artikeln beskrivs nya namn-och terminologi ändringar i Azure Monitor-dokumentationen.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>Oktober 2019 – diagnostisk logg till resurs loggen
"Diagnostikloggar" ändrades till "resurs loggar" för att bättre matcha vad som faktiskt samlas in. Termen "diagnostikinställningar" förblir desamma.  

## <a name="february-2019---log-analytics-terminology"></a>Februari 2019-Log Analytics terminologi
Efter konsolideringen av olika tjänster under Azure Monitor tar vi nästa steg genom att ändra terminologin i vår dokumentation för att bättre beskriva Azure Monitor tjänsten och dess olika komponenter. 

### <a name="log-analytics"></a>Log Analytics
Azure Monitor loggdata lagras fortfarande i en Log Analytics arbets yta och samlas in och analyseras av samma Log Analytics tjänst, men vi ändrar termen _Log Analytics_ på många platser till _Azure Monitor loggar_. Den här termen bättre återspeglar dess roll i Azure Monitor och ger bättre konsekvens med [Mät värden i Azure Monitor](essentials/data-platform-metrics.md).

Term _logg analys_ gäller nu främst för sidan i Azure Portal som används för att skriva och köra frågor och analysera loggdata. Det är den funktionella motsvarigheten till [Metrics Explorer](essentials/metrics-charts.md), som är sidan i Azure Portal som används för att analysera mått data.

### <a name="log-analytics-workspaces"></a>Log Analytics-arbetsytor
[Arbets ytor](logs/manage-access.md) som innehåller loggdata i Azure Monitor kallas fortfarande för Log Analytics arbets ytor. **Log Analytics** -menyn i Azure Portal har bytt namn till **Log Analytics arbets ytor** och är där du [skapar nya arbets ytor](logs/quick-create-workspace.md) och konfigurerar data källor. Analysera dina loggar och andra övervaknings data i **Azure Monitor** och konfigurera arbets ytan i **Log Analytics arbets ytor**.

### <a name="management-solutions"></a>Hanteringslösningar
[Hanterings lösningar](insights/solutions.md) har bytt namn till _övervaknings lösningar_, vilket bättre beskriver deras funktioner.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Augusti 2018 – konsolidering av övervaknings tjänster i Azure Monitor
Log Analytics och Application Insights har konsoliderats till Azure Monitor för att tillhandahålla en enda integrerad upplevelse för övervakning av Azure-resurser och hybrid miljöer. Inga funktioner har tagits bort från dessa tjänster och användare kan utföra samma scenarier som de alltid har slutfört utan att några funktioner går förlorade eller komprometteras.

Dokumentationen för var och en av dessa tjänster har samlats in i en enda uppsättning innehåll för Azure Monitor. Detta hjälper läsaren att hitta allt innehåll för ett visst övervaknings scenario på en enda plats i stället för att referera till flera uppsättningar innehåll. När den konsoliderade tjänsten utvecklas kommer innehållet att bli mer integrerat.

Andra funktioner som ansågs vara en del av Log Analytics, till exempel agenter och vyer, har också flyttats som funktioner i Azure Monitor. Deras funktioner har inte ändrats förutom potentiella förbättringar av deras erfarenhet i Azure Portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>April 2018 – pensionering av Operations Management Suite-varumärke
Operations Management Suite (OMS) var ett sammanslag av följande Azure-hanterings tjänster i licens syfte:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Nya priser har introducerats för dessa tjänster](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)och OMS-databuntarna är inte längre tillgängliga för nya kunder. Ingen av de tjänster som var en del av OMS har ändrats, förutom konsolideringen i Azure Monitor som beskrivs ovan. 




## <a name="next-steps"></a>Nästa steg

- Läs en [Översikt över Azure Monitor](overview.md) som beskriver de olika komponenterna och funktionerna.
- Lär dig mer om [över gången av OMS-portalen](./logs/oms-portal-transition.md).
