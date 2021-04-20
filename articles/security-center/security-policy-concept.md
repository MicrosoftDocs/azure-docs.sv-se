---
title: Förstå säkerhetsprinciper, initiativ och rekommendationer i Azure Security Center
description: Lär dig mer om säkerhetsprinciper, initiativ och rekommendationer i Azure Security Center.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4eea2611997732a263e9e824bc150b45ed145ecd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738980"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>Vad är säkerhetsprinciper, initiativ och rekommendationer?

Security Center tillämpar säkerhetsinitiativ på dina prenumerationer. Dessa initiativ innehåller en eller flera säkerhetsprinciper. Var och en av dessa principer resulterar i en säkerhetsrekommendation för att förbättra din säkerhetsstatus. Den här sidan förklarar var och en av dessa idéer i detalj.


## <a name="what-is-a-security-policy"></a>Vad är en säkerhetsprincip?

En Azure-principdefinition som skapas i Azure Policy är en regel om specifika säkerhetsvillkor som du vill kontrollera. Inbyggda definitioner omfattar saker som att styra vilken typ av resurser som kan distribueras eller framtvinga användningen av taggar på alla resurser. Du kan också skapa egna anpassade principdefinitioner.

Om du vill implementera dessa principdefinitioner (oavsett om de är inbyggda eller anpassade) måste du tilldela dem. Du kan tilldela de här principerna via Azure-portalen, PowerShell eller Azure CLI.

Det finns olika typer av principer i Azure Policy. Security Center använder huvudsakligen granskningsprinciper som kontrollerar specifika villkor och konfigurationer och sedan rapporterar om efterlevnad. Det finns även "Framtvinga"-principer som kan användas för att tillämpa säkra inställningar.

## <a name="what-is-a-security-initiative"></a>Vad är ett säkerhetsinitiativ?

Ett Azure-initiativ är en samling azure-principdefinitioner, eller regler, som grupperas tillsammans mot ett specifikt mål eller syfte. Azure-initiativ förenklar hanteringen av dina principer genom att gruppera en uppsättning principer logiskt som ett enda objekt.

Ett säkerhetsinitiativ definierar önskad konfiguration av dina arbetsbelastningar och hjälper till att säkerställa att du uppfyller säkerhetskraven för ditt företag eller dina myndigheter.

Precis som säkerhetsprinciper Security Center initiativ också i Azure Policy. Du kan använda [Azure Policy](../governance/policy/overview.md) för att hantera dina principer, skapa initiativ och tilldela initiativ till flera prenumerationer eller för hela hanteringsgrupper.

Standardinitiativ som automatiskt tilldelas till varje prenumeration i Azure Security Center är Azure Security Benchmark. Det här benchmark-måttet är Microsofts skribenterade, Azure-specifika uppsättning riktlinjer för bästa praxis för säkerhet och efterlevnad baserat på vanliga ramverk för efterlevnad. Det här allmänt respekterade benchmark-måttet bygger på kontrollerna från [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) och National Institute of Standards and Technology [(NIST)](https://www.nist.gov/) med fokus på molncentrerad säkerhet. Läs mer om [Azure Security Benchmark](https://docs.microsoft.com/security/benchmark/azure/introduction).

Security Center erbjuder följande alternativ för att arbeta med säkerhetsinitiativ och principer:

- **Visa och redigera det inbyggda** standardinitiativ – När du aktiverar Security Center tilldelas initiativet "Azure Security Benchmark" automatiskt till alla Security Center registrerade prenumerationer. Om du vill anpassa det här initiativet kan du aktivera eller inaktivera enskilda principer i det genom att redigera en princips parametrar. Se listan över [inbyggda säkerhetsprinciper för](./policy-reference.md) att förstå de tillgängliga alternativen.

- **Lägg till egna anpassade initiativ** – Om du vill anpassa de säkerhetsinitiativ som tillämpas på din prenumeration kan du göra det inom Security Center. Du får sedan rekommendationer om dina datorer inte följer de principer som du skapar. Anvisningar om hur du skapar och tilldelar anpassade principer finns i [Använda anpassade säkerhetsinitiativ och principer.](custom-security-policies.md)

- Lägg **till standarder för** regelefterlevnad som initiativ – Security Center:s instrumentpanel för regelefterlevnad visar status för alla utvärderingar i din miljö i samband med en viss standard eller regel (till exempel Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Mer information finns i Förbättra [regelefterlevnaden.](security-center-compliance-dashboard.md)

## <a name="what-is-a-security-recommendation"></a>Vad är en säkerhetsrekommendation?

Med hjälp av principerna Security Center regelbundet kompatibilitetsstatusen för dina resurser för att identifiera potentiella säkerhetsfelkonfigurationer och svagheter. Du får sedan rekommendationer om hur du kan åtgärda problemen. Rekommendationerna är resultatet av att utvärdera dina resurser mot relevanta principer och identifiera resurser som inte uppfyller dina definierade krav.

Security Center sina säkerhetsrekommendationer baserat på dina valda initiativ. När en princip från ditt initiativ jämförs med dina resurser och hittar en eller flera som inte är kompatibla visas den som en rekommendation i Security Center.

Rekommendationer är åtgärder som du kan vidta för att skydda och härda dina resurser. Varje rekommendation ger dig följande information:

- En kort beskrivning av problemet
- De reparationssteg som ska genomföras för att implementera rekommendationen
- De berörda resurserna

I praktiken fungerar det så här:

1. Azure Security Benchmark är ett ***initiativ***
1. Den innehåller en ***princip som*** kräver att alla Azure Storage-konton begränsar nätverksåtkomsten så att attackytan minskar. Den här principen kallas "Lagringskonton bör begränsa nätverksåtkomst med hjälp av regler för virtuellt nätverk" och kan inaktiveras eller aktiveras från Azure Policy
1. Om Azure Security Center hittar ett Azure Storage-konto på någon av dina skyddade prenumerationer utvärderar den dessa konton för att se om de är skyddade med regler för virtuellt nätverk. Om de inte är det visas en ***rekommendation för*** att åtgärda den situationen och förbättra säkerheten för dessa resurser. 

Därför innehåller ett initiativ (1) principer (2) som genererar rekommendationer när det är lämpligt (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Visa relationen mellan en rekommendation och en princip

Som nämnts ovan Security Center de inbyggda rekommendationerna baserat på Azure Security Benchmark. Nästan alla rekommendationer har en underliggande princip som härleds från ett krav i benchmark.

När du granskar information om en rekommendation är det ofta bra att kunna se den underliggande principen. För varje rekommendation som stöds  av en princip använder du länken Visa principdefinition från sidan med rekommendationsinformation för att gå direkt till Azure Policy posten för den relevanta principen:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Länk till Azure Policy för den specifika princip som stöder en rekommendation":::

Använd den här länken för att visa principdefinitionen och granska utvärderingslogiken. 

Om du granskar listan över rekommendationer i referensguiden för säkerhetsrekommendationer visas även länkar till principdefinitionssidorna: [](recommendations-reference.md)

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Åtkomst till Azure Policy för en specifik princip direkt från referenssidan Azure Security Center rekommendationer":::


## <a name="next-steps"></a>Nästa steg

På den här sidan förklaras grundläggande begrepp och relationer mellan principer, initiativ och rekommendationer på hög nivå. Relaterad information finns i:

- [Skapa anpassade initiativ](custom-security-policies.md)
- [Inaktivera säkerhetsprinciper för att inaktivera rekommendationer](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Lär dig hur du redigerar en säkerhetsprincip i Azure Policy](../governance/policy/tutorials/create-and-manage.md)