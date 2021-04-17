---
title: Azure Monitor för SAP-lösningar översikt och arkitektur| Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om Azure Monitor för SAP-lösningar
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 45085c910974402a968075a66087a04fb30e8bd9
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576211"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor för SAP-lösningar (förhandsversion)

## <a name="overview"></a>Översikt

Azure Monitor för SAP-lösningar är en Azure-inbyggd övervakningsprodukt för kunder som kör sina SAP-landskap i Azure. Produkten fungerar med både [SAP på Azure Virtual Machines](./hana-get-started.md) [och SAP på Azure stora instanser](./hana-overview-architecture.md).
Med Azure Monitor för SAP-lösningar kan kunder samla in telemetridata från Azure-infrastruktur och databaser på en central plats och visuellt korrelera telemetridata för snabbare felsökning.

Azure Monitor för SAP-lösningar erbjuds via Azure Marketplace. Det ger en enkel, intuitiv installationsupplevelse och tar bara några klick för att distribuera resursen för Azure Monitor för SAP-lösningar (kallas **sap monitor resource).**

Kunder kan övervaka olika komponenter i ett SAP-landskap som Azure Virtual Machines, kluster med hög tillgänglighet, SAP HANA-databas, SAP NetWeaver och så vidare genom att lägga till motsvarande **provider** för komponenten.

Infrastruktur som stöds:

- Virtuell Azure-dator
- Stor Azure-instans

Databaser som stöds:
- SAP HANA-databas
- Microsoft SQL Server

Azure Monitor för SAP-lösningar använder kraften hos [befintliga Azure Monitor](../../../azure-monitor/overview.md) funktioner som Log Analytics och [Arbetsböcker](../../../azure-monitor/visualize/workbooks-overview.md) för att tillhandahålla fler övervakningsfunktioner. Kunder kan [](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) skapa anpassade visualiseringar genom att redigera standardarbetsböcker [](../../../azure-monitor/logs/log-analytics-tutorial.md) som tillhandahålls [](../../../azure-monitor/alerts/tutorial-response.md) av Azure Monitor för SAP-lösningar, skriva anpassade frågor och skapa anpassade aviseringar med hjälp av Azure Log Analytics-arbetsytan, dra nytta av flexibel kvarhållningsperiod och ansluta övervakningsdata till biljettsystemet. [](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Vilka data samlas Azure Monitor SAP-lösningar in?

Datainsamling i Azure Monitor för SAP-lösningar beror på vilka leverantörer som har konfigurerats av kunder. Under den allmänt tillgängliga förhandsversionen samlas följande data in.

Telemetri för pacemakerkluster med hög tillgänglighet:
- Enhetsstatus för nod, resurs och SBD
- Platsbegränsningar för pacemaker
- Kvorumröster och ringstatus
- [Övrigt](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA telemetri:
- Processor-, minnes-, disk- och nätverksanvändning
- HANA-systemreplikering (HSR)
- HANA-säkerhetskopiering
- HANA-värdstatus
- Indexserver- och namnserverroller

Microsoft SQL Server-telemetri:
- CPU, minne, diskanvändning
- Värdnamn, SQL-instansnamn, SAP System-ID
- Batch-begäranden, kompileringar och förväntad sidlivslängd över tid
- De 10 dyraste SQL-uttrycken över tid
- De 12 största tabellerna i SAP-systemet
- Problem som registrerats i SQL Server felloggen
- Blockera processer och SQL-väntestatistik över tid

Telemetri för operativsystem (Linux) 
- CPU-användning, antal för förlopp, processer som körs och blockeras. 
- Minnesanvändning och distribution mellan utnyttjad, cachelagrad, buffrad. 
- Växlingsanvändning, växling och växlingshastighet. 
- Filsystemsanvändning, antal byte som läses och skrivs per blockenhet. 
- Läs-/skrivfördröjning per blockenhet. 
- Löpande I/O-antal, Beständigt minne, byte för läsning/skrivning. 
- Nätverkspaket in/ut, nätverksbyte in/ut 

SAP NetWeaver-telemetri:

- TILLGÄNGLIGHET FÖR SAP-system och programserver, inklusive tillgänglighet för instansprocess för Dispatcher, ICM, Gateway, Meddelandeserver, Enqueue Server, IGS Watchdog
- Statistik och trender för användning av arbetsprocess
- Statistik och trender för ifyllningslås
- Statistik och trender för köanvändning

## <a name="data-sharing-with-microsoft"></a>Datadelning med Microsoft

Azure Monitor för SAP-lösningar samlar in systemmetadata för att ge bättre support för våra SAP på Azure kunder. Ingen PII/EUII samlas in.
Kunder kan aktivera datadelning med Microsoft när de skapar Azure Monitor för SAP-lösningar resurs genom att *välja* Dela i listrutan.
Vi rekommenderar starkt att kunder aktiverar delning av data eftersom det ger Microsofts support- och ingenjörsteam mer information om kundmiljön och ger bättre support till våra verksamhetskritiska SAP på Azure kunder.

## <a name="architecture-overview"></a>Översikt över arkitekturen

På en hög nivå förklarar följande diagram hur Azure Monitor för SAP-lösningar samlar in telemetri från SAP HANA databasen. Arkitekturen är agnostisk för huruvida SAP HANA distribueras på Azure Virtual Machines eller stora Azure-instanser.

![Azure Monitor för SAP-lösningsarkitektur](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

Huvudkomponenterna i arkitekturen är:
- Azure Portal – startpunkten för kunder. Kunder kan navigera till marknadsplatsen inom Azure Portal identifiera Azure Monitor för SAP-lösningar
- Azure Monitor för SAP-lösningar resurs – en landningsplats där kunderna kan visa övervakning av telemetri
- Hanterad resursgrupp – distribueras automatiskt som en del Azure Monitor för SAP-lösningar resursdistributionen. De resurser som distribueras i den hanterade resursgruppen hjälper till med insamling av telemetri. Viktiga resurser som distribueras och deras syfte är:
   - Virtuell Azure-dator: Kallas även för virtuell *insamlardator.* Det här är en Standard_B2ms virtuell dator. Huvudsyftet med den här virtuella datorn är att vara värd för *övervakningsnyttolasten*. Övervakningsnyttolast syftar på logiken för att samla in telemetri från källsystemen och överföra insamlade data till övervakningsramverket. I diagrammet ovan innehåller övervakningsnyttolasten logiken för att ansluta till SAP HANA via SQL-port.
   - [Azure Key Vault:](../../../key-vault/general/basic-concepts.md)Den här resursen distribueras för att på ett säkert sätt lagra SAP HANA databasautentiseringsuppgifter och för att lagra information om [providers](./azure-monitor-providers.md).
   - Log Analytics-arbetsyta: målet där telemetridata finns.
      - Visualiseringen bygger på telemetri i Log Analytics med hjälp av [Azure-arbetsböcker.](../../../azure-monitor/visualize/workbooks-overview.md) Kunder kan anpassa visualiseringen. Kunder kan också fästa sina arbetsböcker eller specifika visualiseringar i Arbetsböcker på Azure-instrumentpanelen för automatisk återinrefering med den lägsta kornigheten på 30 minuter.
      - Kunder kan använda sin befintliga arbetsyta i samma prenumeration som SAP-övervakningsresursen genom att välja det här alternativet vid tidpunkten för distributionen.
      - Kunder kan använda Kusto-frågespråket (KQL) för att [köra frågor mot](../../../azure-monitor/logs/log-query-overview.md) råtabellerna i Log Analytics-arbetsytan. Titta på *Anpassade loggar.*

> [!Note]
> Kunderna ansvarar för att korrigera och underhålla den virtuella datorn, distribuerad i den hanterade resursgruppen.

> [!Tip]
> Kunder kan välja att använda en befintlig Log Analytics-arbetsyta för telemetriinsamling, om den distribueras inom samma Azure-prenumeration som resursen för Azure Monitor för SAP-lösningar.

### <a name="architecture-highlights"></a>Arkitekturhöjdpunkter

Här är några viktiga punkter i arkitekturen:
 - Flera instanser – Kunder kan skapa övervakare för flera instanser av en viss komponenttyp (till exempel HANA DB, **HA-kluster,** Microsoft SQL Server, SAP NetWeaver) över flera SAP SID i ett VNET med en enda resurs för Azure Monitor för SAP-lösningar.
 - **Flera leverantörer –** Arkitekturdiagrammet ovan visar SAP HANA providern som exempel. På samma sätt kan kunder konfigurera fler leverantörer för motsvarande komponenter (till exempel HANA DB, HA-kluster, Microsoft SQL Server, SAP NetWeaver) för att samla in data från dessa komponenter.
 - **Öppen källkod** – Källkoden för Azure Monitor för SAP-lösningar finns i [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions). Kunder kan referera till providerkoden och lära sig mer om produkten, bidra eller dela feedback.
 - **Utökningsbart frågeramverk** – SQL-frågor för att samla in telemetridata skrivs i [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json). Fler SQL-frågor för att samla in mer telemetridata kan enkelt läggas till. Kunder kan begära specifika telemetridata som ska läggas till i Azure Monitor för SAP-lösningar genom att lämna feedback via länken i slutet av det här dokumentet eller kontakta kontoteamet.

## <a name="pricing"></a>Priser
Azure Monitor för SAP-lösningar är en kostnadsfri produkt (ingen licensavgift). Kunderna ansvarar för att betala kostnaden för de underliggande komponenterna i den hanterade resursgruppen.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om leverantörer och skapa din Azure Monitor för SAP-lösningar resurs.
 - Läs mer om [providers](./azure-monitor-providers.md)
 - [Distribuera Azure Monitor för SAP-lösningar med Azure PowerShell](azure-monitor-sap-quickstart-powershell.md)
 - Har du frågor om Azure Monitor för SAP-lösningar? Läs avsnittet [vanliga frågor och](./azure-monitor-faq.md) svar
