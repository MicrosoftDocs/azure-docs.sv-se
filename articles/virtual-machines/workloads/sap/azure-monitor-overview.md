---
title: Översikt och arkitektur för SAP-lösningar för Azure Monitor | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om Azure Monitor för SAP-lösningar
author: rdeltcheva
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.reviewer: cynthn
ms.openlocfilehash: 411a95154c9ca36595dff4472e9ab8e1ae8a767e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571376"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor för SAP-lösningar (för hands version)

## <a name="overview"></a>Översikt

Azure Monitor for SAP-lösningar är en Azure-inbyggd övervaknings produkt för kunder, som kör sina SAP-landskap på Azure. Produkten fungerar med både [SAP på azure Virtual Machines](./hana-get-started.md) och [SAP på Azures stora instanser](./hana-overview-architecture.md).
Med Azure Monitor för SAP-lösningar kan kunder samla in telemetridata från Azure-infrastrukturen och databaser på en central plats och visuellt korrelera telemetridata för snabbare fel sökning.

Azure Monitor för SAP-lösningar erbjuds via Azure Marketplace. Det ger en enkel, intuitiv installations upplevelse och tar bara några klick för att distribuera resursen för Azure Monitor för SAP-lösningar (kallas för **SAP Monitor-resurs**).

Kunder kan övervaka olika komponenter i ett SAP-landskap, till exempel Azure Virtual Machines, kluster med hög tillgänglighet, SAP HANA-databas och så vidare genom att lägga till motsvarande **Provider** för den komponenten.

Infrastruktur som stöds:

- Virtuell Azure-dator
- Stor Azure-instans

Databaser som stöds:
- SAP HANA-databas
- Microsoft SQL Server

Azure Monitor for SAP-lösningar använder kraften i befintliga [Azure Monitor](../../../azure-monitor/overview.md) funktioner som Log Analytics och [arbets böcker](../../../azure-monitor/visualize/workbooks-overview.md) för att ge fler övervaknings funktioner. Kunder kan skapa [anpassade visualiseringar](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) genom att redigera standard arbets böckerna som tillhandahålls av Azure Monitor för SAP-lösningar, skriva [anpassade frågor](../../../azure-monitor/logs/log-analytics-tutorial.md) och skapa [anpassade aviseringar](../../../azure-monitor/alerts/tutorial-response.md) med hjälp av Azure Log Analytics-arbetsytan, dra nytta av den [flexibla kvarhållningsperioden](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) och ansluta övervaknings data med sitt biljett system.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Vilka data Azure Monitor för att samla in SAP-lösningar?

Data insamling i Azure Monitor för SAP-lösningar är beroende av leverantörerna som har kon figurer ATS av kunder. Under den offentliga för hands versionen samlas följande data in.

Pacemaker för högpresterande kluster med hög tillgänglighet:
- Status för nod-, resurs-och SBD-enhet
- Begränsningar för pacemaker-platser
- Röster och ring status för kvorum
- [Övrigt](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetri:
- PROCESSOR, minne, disk och nätverks användning
- HANA-systemreplikering (HSR)
- HANA-säkerhetskopiering
- HANA-värd status
- Index server-och namn server roller

Microsoft SQL Server-telemetri:
- PROCESSOR, minne, disk användning
- Värdnamn, SQL-instansnamn, SAP-system-ID
- Batch-begäranden, kompileringar och sid livs längd förväntad över tid
- De 10 mest dyra SQL-uttrycken över tid
- Topp 12 största tabell i SAP-systemet
- Problem som registrerats i SQL Server fel loggen
- Blockera processer och SQL-wait-statistik över tid

Telemetri för operativ system (Linux) 
- PROCESSOR användning, antal förgreningar, processer som körs och blockeras. 
- Minnes användning och distribution som används, cachelagras, buffras. 
- Växlings användning, växlings grad och växlings takt. 
- Utnyttjande av fil system, antal lästa byte som har lästs och skrivits per block enhet. 
- Läs-/skriv latens per block enhet. 
- Löpande I/O-antal, beständiga minnes läsning/skrivning byte. 
- Nätverks paket in/ut, nätverks-byte in/ut 

## <a name="data-sharing-with-microsoft"></a>Data delning med Microsoft

Azure Monitor for SAP-lösningar samlar in systemmetadata för att ge bättre stöd för våra SAP på Azure-kunder. Ingen PII/EUII samlas in.
Kunder kan aktivera data delning med Microsoft när de skapar Azure Monitor för SAP-lösnings resurser genom att välja *dela* i list rutan.
Vi rekommenderar starkt att kunderna aktiverar data delning, eftersom det ger Microsoft support och teknik team mer information om kund miljön och ger bättre support till vår verksamhets kritiska SAP på Azure-kunder.

## <a name="architecture-overview"></a>Översikt över arkitekturen

På hög nivå förklarar följande diagram hur Azure Monitor för SAP-lösningar samlar in telemetri från SAP HANA-databasen. Arkitekturen är oberoende till om SAP HANA distribueras på Azure Virtual Machines-eller Azures stora instanser.

![Arkitektur för SAP-lösningar Azure Monitor](./media/azure-monitor-sap/azure-monitor-architecture.png)

Huvud komponenterna i arkitekturen är:
- Azure Portal – start punkten för kunderna. Kunder kan navigera till Marketplace i Azure Portal och identifiera Azure Monitor för SAP-lösningar
- Azure Monitor för SAP Solutions-resurs – en landnings plats där kunder kan visa övervakning av telemetri
- Hanterad resurs grupp – distribueras automatiskt som en del Azure Monitor av resurs distributionen för SAP-lösningar. Resurserna som distribueras i hjälpen för hanterad resurs grupp i insamlingen av telemetri. Viktiga resurser som distribueras och deras syfte är:
   - Virtuell Azure-dator: kallas även för *insamlad virtuell* dator. Detta är en Standard_B2ms virtuell dator. Huvud syftet med den här virtuella datorn är att vara värd för *övervaknings nytto lasten*. Övervaknings nytto Last syftar på logiken för insamling av telemetri från käll systemen och överföring av insamlade data till övervaknings ramverket. I diagrammet ovan innehåller övervaknings nytto lasten logiken för att ansluta till SAP HANA Database över SQL-porten.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): den här resursen distribueras för att lagra autentiseringsuppgifter på SAP HANA-databasen på ett säkert sätt och för att lagra information om [leverantörer](./azure-monitor-providers.md).
   - Log Analytics arbets yta: målet där telemetri-data finns.
      - Visualisering bygger på telemetri i Log Analytics att använda Azure- [arbetsböcker](../../../azure-monitor/visualize/workbooks-overview.md). Kunder kan anpassa visualiseringar. Kunder kan också fästa sina arbets böcker eller en speciell visualisering i arbets böcker till Azure-instrumentpanelen för uppdaterings möjligheter med den lägsta precisionen på 30 minuter.
      - Kunder kan använda sina befintliga arbets ytor i samma prenumeration som SAP Monitor-resursen genom att välja det här alternativet vid tidpunkten för distributionen.
      - Kunder kan använda KQL (Kusto Query Language) för att köra [frågor](../../../azure-monitor/logs/log-query-overview.md) mot obearbetade tabeller i Log Analytics arbets ytan. Titta på *anpassade loggar*.

> [!Note]
> Kunderna ansvarar för att korrigera och underhålla den virtuella datorn, som distribueras i den hanterade resurs gruppen.

> [!Tip]
> Kunderna kan välja att använda en befintlig Log Analytics arbets yta för telemetri-samling, om den distribueras inom samma Azure-prenumeration som resursen för Azure Monitor för SAP-lösningar.

### <a name="architecture-highlights"></a>Arkitektur höjd punkter

Följande är de viktigaste höjd punkterna i arkitekturen:
 - **Flera** instanser – kunder kan skapa Övervakare för flera instanser av en viss komponent typ (till exempel Hana DB, ha-kluster, Microsoft SQL Server) över flera SAP-sid i ett VNet med en enda resurs med Azure Monitor för SAP-lösningar.
 - **Multi-Provider** – diagrammet ovan visar SAP HANA leverantören som ett exempel. På samma sätt kan kunder konfigurera fler leverantörer för motsvarande komponenter (till exempel HANA DB, HA-kluster, Microsoft SQL Server) för att samla in data från dessa komponenter.
 - **Öppen källkod** – käll koden för Azure Monitor för SAP-lösningar finns i [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Kunder kan referera till leverantörs koden och lära sig mer om produkten, bidra eller dela feedback.
 - **Extensible Query Framework** -SQL-frågor för att samla in telemetridata skrivs i [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Det går enkelt att lägga till fler SQL-frågor för att samla in mer telemetridata. Kunder kan begära särskilda telemetridata som ska läggas till Azure Monitor för SAP-lösningar genom att lämna feedback via länken i slutet av det här dokumentet eller kontakta sitt konto team.

## <a name="pricing"></a>Prissättning
Azure Monitor för SAP-lösningar är en kostnads fri produkt (ingen licens avgift). Kunderna ansvarar för att betala kostnaden för de underliggande komponenterna i den hanterade resurs gruppen.

## <a name="next-steps"></a>Nästa steg

Lär dig om leverantörer och skapa din första Azure Monitor för SAP-lösnings resurser.
 - Läs mer om [leverantörer](./azure-monitor-providers.md)
 - [Distribuera Azure Monitor för SAP-lösningar med Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Har du frågor om Azure Monitor för SAP-lösningar? Läs avsnittet med [vanliga frågor och svar](./azure-monitor-faq.md)
