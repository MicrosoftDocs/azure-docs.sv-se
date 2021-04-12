---
title: Datasäkerhet i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver hur data hanteras och skyddas i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 0c21c916d152188031212b47704dfb41d3f64a82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596275"
---
# <a name="azure-security-center-data-security"></a>Azure Security Center data säkerhet

För att hjälpa kunder att förhindra, upptäcka och reagera på hot, Azure Security Center samla in och bearbeta säkerhetsrelaterade data, inklusive konfigurations information, metadata, händelse loggar och mycket annat. Microsoft följer strikta riktlinjer för efterlevnad och säkerhet – från kodning till driften av en tjänst.

Den här artikeln förklarar hur data hanteras och skyddas i Security Center.

## <a name="data-sources"></a>Datakällor
Security Center analyserar data från följande källor för att ge insyn i säkerhets läget, identifiera sårbarheter och rekommendera åtgärder och identifiera aktiva hot:

- **Azure-tjänster**: använder information om konfigurationen av Azure-tjänster som du har distribuerat genom att kommunicera med tjänstens resurs leverantör.
- **Nätverks trafik**: använder exempel på metadata från Microsofts infrastruktur, till exempel käll-/mål-IP/port, paket storlek och nätverks protokoll.
- **Partner lösningar**: använder säkerhets aviseringar från integrerade partner lösningar, till exempel brand väggar och lösningar mot skadlig kod.
- **Dina datorer**: använder konfigurations information och information om säkerhets händelser, till exempel händelser och gransknings loggar i Windows, samt syslog-meddelanden från dina datorer.


## <a name="data-protection"></a>Dataskydd

### <a name="data-segregation"></a>Dataavgränsning
Data lagras logiskt åtskilda på varje komponent i tjänsten. Alla data taggas efter organisation. Den här taggningen finns kvar i informationens hela livscykel och används på varje lager i tjänsten.

### <a name="data-access"></a>Dataåtkomst
För att kunna erbjuda säkerhets rekommendationer och undersöka möjliga säkerhetshot kan Microsofts personal komma åt information som samlas in eller analyseras av Azure-tjänster, inklusive process skapande händelser och andra artefakter, som oavsiktligt kan ta med kund information eller personliga data från dina datorer. 

Vi följer [Microsoft Online Services-tillägget för data skydd](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880), som anger att Microsoft inte kommer att använda kunddata eller härleda information från det för reklam eller liknande kommersiella ändamål. Vi använder bara kunddata i den mån det är nödvändigt för att tillhandahålla Azure-tjänsterna, samt för därtill relaterade ändamål. Du äger alla rättigheter till dina kunddata.

### <a name="data-use"></a>Data användning
Microsoft använder mönster och hot information som visas över flera klienter för att förbättra våra förebyggande-och identifierings funktioner. Vi gör detta i enlighet med de sekretess åtaganden som beskrivs i vår [sekretess policy](https://privacy.microsoft.com/privacystatement).

## <a name="manage-data-collection-from-machines"></a>Hantera data insamling från datorer
När du väljer att aktivera Security Center i Azure är datainsamling aktiverat för var och en av dina Azure-prenumerationer. Du kan också aktivera data insamling för dina prenumerationer i Security Center. När data insamling är aktiverat Security Center etablerar Log Analytics agenten på alla befintliga virtuella Azure-datorer som stöds och eventuella nya som skapas.

Log Analytics agent söker efter olika säkerhetsrelaterade konfigurationer och händelser i [ETW (Event tracing for Windows)](/windows/win32/etw/event-tracing-portal) (ETW) spår. Dessutom utlöser operativsystemet händelselogghändelser när datorn körs. Exempel på sådana data är: operativsystemets typ och version, operativsystemloggar (Windows-händelseloggar), processer som körs, datornamn, IP-adresser, inloggad användare och klient-ID. Log Analytics agent läser händelse logg poster och ETW-spårning och kopierar dem till dina arbets ytor för analys. Log Analytics agenten aktiverar också process skapande händelser och kommando rads granskning.

Om du inte använder Azure Defender kan du också inaktivera data insamling från virtuella datorer i säkerhets principen. Data insamling krävs för prenumerationer som skyddas av Azure Defender. Funktionerna för ögonblicksbilder av virtuella datordisker och artefaktinsamling är fortfarande aktiverade även om datainsamling har inaktiverats.

Du kan ange arbets ytan och regionen där data som samlas in från dina datorer lagras. Standardvärdet är att lagra data som samlas in från datorerna på den närmaste arbets ytan som visas i följande tabell:

| Region för virtuell dator                                      | Region för arbetsyta  |
|---------------------------------------------|----------------|
| USA, Brasilien, Sydafrika         | USA  |
| Kanada                                      | Kanada         |
| Europa (exklusive Storbritannien)           | Europa         |
| Storbritannien                              | Storbritannien |
| Asien (exklusive Indien, Japan, Korea, Kina) | Asien och stillahavsområdet   |
| Korea                                       | Asien och stillahavsområdet   |
| Indien                                       | Indien          |
| Japan                                       | Japan          |
| Kina                                       | Kina          |
| Australien                                   | Australien      |
|                                             |                |

> [!NOTE]
> **Azure Defender för lagring** lagrar artefakter i regionalt enligt platsen för den relaterade Azure-resursen. Läs mer i [Introduktion till Azure Defender för lagring](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Dataförbrukning

Kunder kan komma åt Security Center relaterade data från följande data strömmar:


| Stream                                                                                | Datatyper                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure aktivitets logg](../azure-monitor/essentials/activity-log.md)                       | Alla säkerhets aviseringar, godkända Security Center [just-in-Time-](security-center-just-in-time.md) begäranden och alla aviseringar som genereras av [adaptiva program kontroller](security-center-adaptive-application.md).|
| [Azure Monitor-loggar](../azure-monitor/data-platform.md)                      | Alla säkerhets aviseringar.                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | Säkerhets aviseringar, säkerhets rekommendationer, sårbarhets bedömnings resultat, säker Poäng information, status för kontroll av efterlevnad och mycket annat.                                                                       |
| [REST API för Azure Security Center](/rest/api/securitycenter/) | Säkerhets aviseringar, säkerhets rekommendationer med mera.                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur data hanteras och skyddas i Azure Security Center. 

Mer information om Azure Security Center finns i [Vad är Azure Security Center?](security-center-introduction.md)