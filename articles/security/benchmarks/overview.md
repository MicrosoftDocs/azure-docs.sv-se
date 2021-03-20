---
title: Översikt över Azures säkerhets benchmark v2
description: Översikt över Azure Security benchmark v2
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3bc97a932e622838315f717cc7b036a559787805
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369165"
---
# <a name="overview-of-the-azure-security-benchmark-v2"></a>Översikt över Azure Security benchmark (v2)

ASB (Azure Security benchmark) tillhandahåller beprövade metod tips och rekommendationer för att förbättra säkerheten för arbets belastningar, data och tjänster på Azure.

Detta riktmärke är en del av en uppsättning holistiska säkerhets guider som också omfattar:

- **Ramverk för moln införande** – rikt linjer för säkerhet, inklusive [strategi](/azure/cloud-adoption-framework/strategy/define-security-strategy), [roller och ansvars områden](/azure/cloud-adoption-framework/organize/cloud-security), [Azures bästa säkerhets metod tips](/azure/cloud-adoption-framework/get-started/security#step-1-establish-essential-security-practices)och [referens implementering](/azure/cloud-adoption-framework/ready/enterprise-scale/).
- **Azure Well-Architected Framework** – rikt linjer för [att skydda dina arbets belastningar](/assessments/?mode=pre-assessment&session=local) på Azure.
- **Microsofts säkerhets** rekommendationer – [rekommendationer](/security/compass/microsoft-security-compass-introduction) med exempel på Azure.

 Azures säkerhets riktmärke fokuserar på molnbaserade kontroll områden. Dessa kontroller är konsekventa med välkända säkerhets krav, till exempel de som beskrivs av CIS-kontroller (Center for Internet Security), version 7,1 och National Institute of Standards and Technology (NIST) SP 800-53.
Följande kontroller ingår i Azures säkerhets benchmark:

| ASB kontroll domäner | Beskrivning 
|--|--|
| [Nätverks &nbsp; säkerhet &nbsp; (NS)](security-controls-v2-network-security.md) | Nätverks säkerhet täcker kontroller för att skydda och skydda Azure-nätverk, inklusive skydd av virtuella nätverk, upprättande av privata anslutningar, förebygga och begränsa externa attacker och säkra DNS. |
| [Identitets &nbsp; hantering &nbsp; (im)](security-controls-v2-identity-management.md) | Identitets hantering täcker kontroller för att upprätta en säker identitets-och åtkomst kontroll med hjälp av Azure Active Directory, inklusive användning av enkel inloggning, starka autentiseringar, hanterade identiteter (och tjänst principer) för program, villkorlig åtkomst och övervakning av konto avvikelser. |
| [Privilegie rad &nbsp; åtkomst &nbsp; (PA)](security-controls-v2-privileged-access.md) | Privilegie rad åtkomst täcker kontroller för att skydda privilegie rad åtkomst till din Azure-klient och dina resurser, inklusive ett antal kontroller för att skydda din administrativa modell, administrativa konton och arbets stationer med privilegie rad åtkomst mot avsiktliga och oavsiktliga risker. |
| [Data &nbsp; skydd &nbsp; (DP)](security-controls-v2-data-protection.md) | Data skydd täcker kontroll av data skydd i vila, vid överföring och via behöriga åtkomst metoder, inklusive identifiering, klassificering, skydd och övervakning av känsliga data till gångar med hjälp av åtkomst kontroll, kryptering och loggning i Azure. |
| [Till gångs &nbsp; hantering &nbsp; (am)](security-controls-v2-asset-management.md) | Till gångs hantering omfattar kontroller för att säkerställa ökad säkerhet och styrning över Azure-resurser, inklusive rekommendationer om behörigheter för säkerhets personal, säkerhets åtkomst till till gångs inventering och hantering av godkännanden för tjänster och resurser (inventering, spårning och rätt). |
| [Loggning &nbsp; och &nbsp; hot &nbsp; identifiering (lt)](security-controls-v2-logging-threat-detection.md) | Loggning och hot identifiering omfattar kontroller för att identifiera hot på Azure och aktivera, samla in och lagra gransknings loggar för Azure-tjänster, inklusive att aktivera identifiering, undersökning och reparations processer med kontroller för att generera högkvalitativa varningar med inbyggd hot identifiering i Azure-tjänster. den omfattar också insamling av loggar med Azure Monitor, centraliserad säkerhets analys med Azure Sentinel, tidssynkronisering och logg kvarhållning. |
| [Incident &nbsp; svar &nbsp; (IR)](security-controls-v2-incident-response.md) | Incident svar omfattar kontroller i incident svars livs cykel-förberedelse, identifiering och analys, inne slutnings-och efter incident aktiviteter, inklusive användning av Azure-tjänster som Azure Security Center och Sentinel för att automatisera incident svars processen. |
| [Position &nbsp; och &nbsp; sårbarhets &nbsp; hantering &nbsp; (PV)](security-controls-v2-posture-vulnerability-management.md) | Position och sårbarhets hantering fokuserar på kontroller för att utvärdera och förbättra Azure Security-position, inklusive sårbarhets sökning, inträngande testning och reparation, samt säkerhets konfigurations spårning, rapportering och korrigering i Azure-resurser. |
| [Slut punkts &nbsp; säkerhet &nbsp; (er)](security-controls-v2-endpoint-security.md) | Slut punkts säkerhet täcker kontroller i slut punkts identifiering och svar, inklusive användning av slut punkts identifiering och-svar (EDR) och skydd mot skadlig kod för slut punkter i Azure-miljöer. |
| [Säkerhets kopiering &nbsp; och &nbsp; återställning &nbsp; (br)](security-controls-v2-backup-recovery.md) | Säkerhets kopiering och återställning täcker kontroller för att säkerställa att säkerhets kopiering av data och konfigurationer på olika tjänst nivåer utförs, verifieras och skyddas. |
| [Styrning &nbsp; och &nbsp; strategi &nbsp; (GS)](security-controls-v2-governance-strategy.md) | Styrning och strategi ger vägledning för att säkerställa en konsekvent säkerhets strategi och dokumenterad styrnings metod för att hjälpa och upprätthålla säkerhets garantier, inklusive att upprätta roller och ansvar för de olika moln säkerhets funktionerna, en enhetlig teknisk strategi och stöd för principer och standarder. |

## <a name="azure-security-benchmark-recommendations"></a>Rekommendationer för Azure Security benchmark

Varje rekommendation innehåller följande information:

- **Azure-ID**: det ID för Azure-säkerhetsbenchmark som motsvarar rekommendationen.
- **CIS-kontroller v 7.1-ID**: CIS-kontroller v 7.1-kontroll (s) som motsvarar den här rekommendationen.
- **NIST sp 800-53 R4-ID (s)**: NIST SP 800-53 R4 (måttlig) som motsvarar den här rekommendationen.
- **Information**: motiveringen till rekommendationen och länkar till vägledning om hur du implementerar den. Om rekommendationen stöds av Azure Security Center, visas även den informationen.
- **Ansvar**: om kunden, tjänst leverantören eller båda är ansvariga för att implementera den här rekommendationen. Säkerhets ansvars områden delas i det offentliga molnet. Vissa säkerhets kontroller är bara tillgängliga för moln tjänst leverantören och därför ansvarar leverantören för att adressera dem. Detta är allmänna observationer – för vissa enskilda tjänster skiljer sig ansvaret från vad som anges i Azures säkerhets benchmark. Skillnaderna beskrivs i grundläggande rekommendationer för den enskilda tjänsten.
- **Kund säkerhets intressenter**: [säkerhetsfunktionerna](/azure/cloud-adoption-framework/organize/cloud-security#security-functions) hos kund organisationen som kan vara konto bara, ansvariga eller konsulteras för respektive kontroll. Det kan skilja sig från organisation till organisation beroende på företagets säkerhets organisations struktur och de roller och ansvars områden som du har konfigurerat för Azure-säkerhet.

> [!NOTE]
> Kontroll mappningarna mellan ASB och bransch mått (t. ex. NIST och CIS) visar bara att en viss Azure-funktion kan användas för att helt eller delvis adressera ett kontroll krav som definierats i NIST eller CIS. Du bör vara medveten om att sådan implementering inte nödvändigt vis översätter den fullständiga efterlevnaden av motsvarande kontroll i CIS eller NIST.

Vi välkomnar din detaljerade feedback och aktivt deltagande i Azures säkerhets benchmark-ansträngning. Om du vill ge Azures informations benchmark-grupp direkt inmatat fyller du i formuläret på https://aka.ms/AzSecBenchmark

## <a name="download"></a>Ladda ned

Du kan ladda ned Azures säkerhets benchmark i [kalkyl blads format](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Security%20Benchmark).

## <a name="next-steps"></a>Nästa steg 
- Se den första säkerhets kontrollen: [nätverks säkerhet](security-control-network-security.md)
- Läs [introduktionen till Azure Security benchmark](introduction.md)
- Lär dig [grunderna i Azures säkerhet](../fundamentals/index.yml)