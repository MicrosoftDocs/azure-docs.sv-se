---
title: Översikt över Azures säkerhets benchmark v1
description: Översikt över Azure Security benchmark v1
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 39b6a5452c4bc0cba1e1153c266b83e87f6209ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90059417"
---
# <a name="overview-of-the-azure-security-benchmark-v1"></a>Översikt över Azures säkerhets benchmark (v1)

> [!NOTE]
> Det senaste Azure-säkerhetsprestandatestet finns [här](overview.md).

Azure Security Benchmark innehåller rekommendationer som hjälper dig att förbättra säkerheten för dina program och data i Azure.

Detta riktmärke fokuserar på molnbaserade kontroll områden. Dessa kontroller är konsekventa med välkända säkerhets mått, till exempel de som beskrivs av CIS-kontroller (Center for Internet Security) version 7,1.

Följande kontroller används i Azures säkerhets benchmark: 

- [Nätverkssäkerhet](security-control-network-security.md)
- [Loggning och övervakning](security-control-logging-monitoring.md)
- [Identitets- och åtkomstkontroll](security-control-identity-access-control.md)
- [Dataskydd](security-control-data-protection.md)
- [Sårbarhetshantering](security-control-vulnerability-management.md)
- [Inventerings- och tillgångshantering](security-control-inventory-asset-management.md)
- [Säker konfiguration](security-control-secure-configuration.md)
- [Skydd mot skadlig kod](security-control-malware-defense.md)
- [Data återställning](security-control-data-recovery.md)
- [Incidenthantering](security-control-incident-response.md)
- [Penetrationstester och Red Team-tester](security-control-penetration-tests-red-team-exercises.md)

Du kan också hämta [Azure Security-prestandatest v1 Excel-kalkylbladet](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets).

## <a name="azure-security-benchmark-recommendations"></a>Rekommendationer för Azure Security benchmark 

Varje rekommendation innehåller följande information: 

- **Azure-ID**: det ID för Azure-säkerhetsbenchmark som motsvarar rekommendationen. 
- **CIS-ID**: de CIS-benchmark-rekommendationer som motsvarar den här rekommendationen.  
- **Ansvar**: om kunden eller tjänst leverantören (eller båda) är (är) ansvarig för att implementera den här rekommendationen. Säkerhets ansvars områden delas i det offentliga molnet. Vissa säkerhets kontroller är bara tillgängliga för moln tjänst leverantören och därför ansvarar leverantören för att adressera dem. Detta är allmänna observationer – för vissa enskilda tjänster skiljer sig ansvaret från det som anges i Azures säkerhets benchmark. Skillnaderna beskrivs i grundläggande rekommendationer för den enskilda tjänsten. 
- **Information**: motiveringen till rekommendationen och länkar till vägledning om hur du implementerar den. Om rekommendationen stöds av Azure Security Center, visas även den informationen.

Vi välkomnar din detaljerade feedback och aktivt deltagande i Azures säkerhets benchmark-ansträngning. Om du vill ge Azures prestandatest för Azure-säkerhet kan du fylla i formuläret på [https://aka.ms/AzSecBenchmark](https://aka.ms/AzSecBenchmark) .

## <a name="next-steps"></a>Nästa steg

- Se den första säkerhets kontrollen: [nätverks säkerhet](security-control-network-security.md)
- Läs [introduktionen till Azure Security benchmark](introduction.md)
- Hämta [Excel-kalkylbladet för Azure Security benchmark v1](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/spreadsheets)
