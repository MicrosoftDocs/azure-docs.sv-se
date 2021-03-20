---
title: Introduktion till benchmark-värden för Azure-säkerhet
description: Introduktion till säkerhet benchmark
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369199"
---
# <a name="azure-security-benchmark-introduction"></a>Introduktion till Azure Security benchmark

Nya tjänster och funktioner lanseras dagligen i Azure, och utvecklare kan snabbt publicera nya moln program som bygger på dessa tjänster och angripare söker alltid nya sätt att utnyttja felkonfigurerade resurser. Molnet flyttas snabbt, utvecklare flyttar snabbt och angripare är alltid på språng. Hur kan du hålla reda på och se till att dina moln distributioner är säkra? Hur skiljer sig säkerhets praxis för moln system från lokala system? Hur övervakar du för konsekvens i många oberoende utvecklings team?

Microsoft har upptäckt att användning av *säkerhets mått för säkerhet* kan hjälpa dig att snabbt skydda moln distributioner. Benchmark-rekommendationer från moln tjänst leverantören ger dig en utgångs punkt för att välja vissa inställningar för säkerhets konfiguration i din miljö, så att du snabbt kan minska risken för din organisation.

Azures säkerhets benchmark innehåller en samling med högkvalitativa säkerhets rekommendationer som du kan använda för att skydda de tjänster som du använder i Azure:

- **Säkerhets kontroller**: de här rekommendationerna är allmänt tillgängliga i din Azure-klient och Azure-tjänster. Varje rekommendation identifierar en lista över intressenter som vanligt vis ingår i planering, godkännande eller implementering av benchmark. 
- **Tjänste bas linjer**: dessa gäller för enskilda Azure-tjänster för att tillhandahålla rekommendationer för den tjänstens säkerhets konfiguration.

## <a name="implement-the-azure-security-benchmark"></a>Implementera Azures säkerhets benchmark
- **Planera** implementeringen av din Azure-säkerhet genom att granska [dokumentationen](overview.md) för företags kontrollerna och de tjänstspecifika bas linjerna för att planera kontroll ramverket och hur det mappas till vägledning som CIS (kontroll v 7.1) och NIST (SP 800-53) Framework.
- **Övervaka** din efterlevnad med Azures säkerhets status för säkerhet (och andra kontroll uppsättningar) med hjälp av [instrument panelen](../../security-center/security-center-compliance-dashboard.md)för Azure Security Center övervakning av regelefterlevnad.
- **Upprätta guardrails** för att automatisera säkra konfigurationer och upprätthålla efterlevnaden av Azures säkerhets benchmark (och andra krav i din organisation) med Azure-ritningar och Azure policy.
 
Observera att Azure Security benchmark v2 är justerat med [Microsofts rekommenderade säkerhets metoder](/security/compass/microsoft-security-compass-introduction) (tidigare Azure Security kompass) så att Azures säkerhets-benchmark ger en enda sammanslagen vy över Microsofts Azure-säkerhetsrekommendationer.

## <a name="common-use-cases"></a>Vanliga användnings fall

Azures säkerhets benchmark används ofta för att åtgärda de vanliga utmaningarna för kunder eller tjänste partner som är:
- Nytt i Azure och söker efter rekommenderade säkerhets metoder för att säkerställa en säker distribution.
- Förbättra säkerhets position i befintliga Azure-distributioner för att prioritera de största riskerna och begränsningarna.
- Att godkänna Azure-tjänster för användning av teknik-och företags användning för att uppfylla specifika säkerhets rikt linjer.
- Uppfylla regel krav för kunder som är från statliga eller mycket reglerade branscher som ekonomi och sjukvård (eller tjänste leverantörer som behöver bygga system för dessa kunder). Dessa kunder måste se till att Azures konfiguration uppfyller de säkerhetsfunktioner som anges i ett bransch ramverk, till exempel CIS, NIST eller PCI. Med Azures säkerhets effektivitet får du en effektiv metod med de kontroller som redan är fördelade på dessa bransch mått.

## <a name="terminology"></a>Terminologi

Termerna "kontroll", "benchmark" och "bas linje" används ofta i Azures säkerhets benchmark-dokumentation och det är viktigt att förstå hur Azure använder dessa villkor.


| Period | Beskrivning | Exempel |
|--|--|--|
| Kontroll | En kontroll är en övergripande beskrivning av en funktion eller aktivitet som behöver åtgärdas och som inte är särskilt för en teknik eller implementering. | Data skydd är en av säkerhets kontrollerna. Den här kontrollen innehåller vissa åtgärder som måste åtgärdas för att säkerställa att data skyddas. |
| Benchmark | Ett riktmärke innehåller säkerhets rekommendationer för en speciell teknik, till exempel Azure. Rekommendationerna kategoriseras med den kontroll som de tillhör. | Azures säkerhets benchmark omfattar de säkerhets rekommendationer som är aktuella för Azure-plattformen |
| Baslinje | En bas linje är implementeringen av benchmark för enskilda Azure-tjänster. Varje organisation bestämmer benchmark-rekommendation och motsvarande konfigurationer krävs i Azure implementation-omfånget. | Contoso-företaget ser till att aktivera funktioner för Azure SQL-säkerhet genom att följa den konfiguration som rekommenderas i Azure SQL Security-bas linjen.

Vi välkomnar din feedback om Azures säkerhets benchmark! Vi rekommenderar att du anger kommentarer i feedback-avsnittet nedan. Om du föredrar att dela dina inloggade privat med Azures säkerhets benchmark-teamet, är du välkommen att fylla i formuläret på https://aka.ms/AzSecBenchmark
