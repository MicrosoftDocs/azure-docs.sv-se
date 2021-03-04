---
title: Förstå säkerhets principer, initiativ och rekommendationer i Azure Security Center
description: Lär dig mer om säkerhets principer, initiativ och rekommendationer i Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/28/2021
ms.openlocfilehash: a5aae4013067ba37334e3e85a9e7ef882efd1dd2
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107933"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Vad är säkerhets principer, initiativ och rekommendationer?

Security Center tillämpar säkerhets initiativ till dina prenumerationer. Dessa initiativ innehåller en eller flera säkerhets principer. Var och en av dessa principer resulterar i en säkerhets rekommendation för att förbättra din säkerhets position. På den här sidan beskrivs var och en av dessa idéer i detalj.


## <a name="what-is-a-security-policy"></a>Vad är en säkerhets princip?

En Azure policy-definition, som skapats i Azure Policy, är en regel om vissa säkerhets villkor som du vill kontrol lera. Inbyggda definitioner omfattar saker som att styra vilken typ av resurser som kan distribueras eller tillämpa taggar på alla resurser. Du kan också skapa egna anpassade princip definitioner.

Om du vill implementera dessa princip definitioner (oavsett om de är inbyggda eller anpassade) måste du tilldela dem. Du kan tilldela de här principerna via Azure-portalen, PowerShell eller Azure CLI.


## <a name="what-is-a-security-initiative"></a>Vad är ett säkerhets initiativ?

Ett Azure-initiativ är en samling av Azure policy-definitioner, eller regler, som grupperas tillsammans med ett specifikt mål eller ett specifikt syfte. Azure-initiativ fören klar hanteringen av dina principer genom att gruppera en uppsättning principer tillsammans, som ett enda objekt.

Ett säkerhets initiativ definierar den önskade konfigurationen av dina arbets belastningar och hjälper dig att se till att du följer säkerhets kraven för ditt företag eller din regulator.

Precis som säkerhets principer skapas även Security Center initiativ i Azure Policy. Du kan använda [Azure policy](../governance/policy/overview.md) för att hantera principer, skapa initiativ och tilldela initiativ till flera prenumerationer eller för hela hanterings grupper.

Standard initiativet som tilldelas automatiskt till varje prenumeration i Azure Security Center är Azures säkerhets benchmark. Detta riktmärke är Microsofts-skapade, Azure-/regionsspecifika uppsättning rikt linjer för säkerhets-och efterlevnads metod tips baserade på vanliga ramverk för efterlevnad. Detta respekterade riktmärken bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) med fokus på Cloud-inriktad säkerhet. Läs mer om [Azure Security Benchmark](../security/benchmarks/introduction.md).

Security Center erbjuder följande alternativ för att arbeta med säkerhets initiativ och principer:

- **Visa och redigera det inbyggda standard initiativet** – när du aktiverar Security Center tilldelas initiativet med namnet "Azure Security benchmark" automatiskt till alla Security Center registrerade prenumerationer. Du kan anpassa det här initiativet genom att aktivera eller inaktivera enskilda principer i den. Se listan över [inbyggda säkerhets principer](./policy-reference.md) för att förstå alternativen som är tillgängliga direkt.

- **Lägg till egna anpassade initiativ** – om du vill anpassa de säkerhets initiativ som tillämpas på din prenumeration kan du göra det i Security Center. Du får sedan rekommendationer om datorerna inte följer de principer som du skapar. Anvisningar om hur du skapar och tilldelar anpassade principer finns i [använda anpassade säkerhets initiativ och principer](custom-security-policies.md).

- **Lägg till regler för regelefterlevnad som initiativ** – Security Center rikt panelen för kontroll av efterlevnad visar status för alla utvärderingar i din miljö i samband med en viss standard eller regel (till exempel Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020). Mer information finns i [förbättra din regelefterlevnad](security-center-compliance-dashboard.md).

## <a name="what-is-a-security-recommendation"></a>Vad är en säkerhets rekommendation?

Azure Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen. En rekommendation är resultatet av att utvärdera resurserna mot de relevanta principerna och identifiera resurser som inte uppfyller dina definierade krav.

Azure Security Center gör säkerhets rekommendationerna baserade på dina valda initiativ. 

Rekommendationer är åtgärder som du kan vidta för att skydda och skärp dina resurser. Varje rekommendation innehåller följande information:

- En kort beskrivning av problemet
- De åtgärder som vidtas för att genomföra rekommendationen
- De berörda resurserna

I praktiken fungerar det som detta:

1. Azure Security Benchmark är ett ***initiativ***
1. Den innehåller en ***princip*** för att kräva att alla Azure Storage konton ska begränsa nätverks åtkomsten, så att deras attack yta minskar. Den här principen kallas för "lagrings konton bör begränsa nätverks åtkomst med hjälp av regler för virtuella nätverk" och kan inaktive ras eller aktive ras från Azure Policy
1. Om Azure Security Center hittar ett Azure Storage konto på någon av dina skyddade prenumerationer, utvärderas dessa konton för att se om de skyddas med regler för virtuella nätverk. Om de inte gör det visas en ***rekommendation*** för att åtgärda situationen och skärp säkerheten för dessa resurser. 

Ett initiativ (1) innehåller därför principer (2) som genererar rekommendationer vid behov (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Visa relationen mellan en rekommendation och en princip

Som nämnts ovan är Security Center som skapats i rekommendationerna baserade på Azures säkerhets benchmark. Nästan alla rekommendationer har en underliggande princip som härleds från ett krav i benchmark.

När du granskar informationen om en rekommendation är det ofta bra att kunna se den underliggande principen. För varje rekommendation som stöds av en princip använder du länken **Visa princip definition** från rekommendations informations sidan för att gå direkt till Azure policy posten för den aktuella principen:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Länk till Azure Policy sida för den speciella principen som stöder en rekommendation":::

Använd den här länken för att Visa princip definitionen och granska utvärderings logiken. 

Om du granskar listan över rekommendationer i [referens guiden för säkerhets rekommendationer](recommendations-reference.md)kan du också se länkar till princip definitions sidorna:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Åtkomst till Azure Policy sidan för en speciell princip direkt från referens sidan för Azure Security Center rekommendationer":::


## <a name="next-steps"></a>Nästa steg

På den här sidan beskrivs de grundläggande begreppen och relationerna mellan principer, initiativ och rekommendationer på en hög nivå. Relaterad information finns i:

- [Skapa anpassade initiativ](custom-security-policies.md)
- [Inaktivera säkerhets principer för att inaktivera rekommendationer](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Lär dig hur du redigerar en säkerhets princip i Azure Policy](../governance/policy/tutorials/create-and-manage.md)