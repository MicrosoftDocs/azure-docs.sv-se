---
title: Skala upp din Azure DevTest Labs-infrastruktur
description: Den här artikeln innehåller rikt linjer för att skala upp din Azure DevTest Labs-infrastruktur.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 50bf08678a12a1a0499abd08c52a264d03f4a401
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85478798"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Skala upp din Azure DevTest Labs-infrastruktur
Innan du implementerar DevTest Labs i företags skala finns det flera viktiga besluts punkter. Att förstå dessa besluts punkter på en hög nivå hjälper en organisation med design beslut i framtiden. Dessa punkter bör dock inte innehålla en organisation från att starta ett koncept bevis. De tre översta områdena för inledande planering av skala är:

- Nätverk och säkerhet
- Prenumerations sto pol Ogin
- Roller och ansvarsområden

## <a name="networking-and-security"></a>Nätverk och säkerhet
Nätverk och säkerhet är hörn för alla organisationer. En företagsomfattande distribution kräver en mycket djupare analys, men det finns ett minskat antal krav för att kunna utföra ett koncept bevis. Några viktiga områden i fokus är:

- **Azure-prenumeration** – om du vill distribuera DevTest Labs måste du ha åtkomst till en Azure-prenumeration med rätt behörighet för att skapa resurser. Det finns ett antal sätt att få åtkomst till Azure-prenumerationer, inklusive en Enterprise-avtal och betala per användning. Mer information om hur du får åtkomst till en Azure-prenumeration finns i [licensiera Azure för företaget](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Åtkomst till lokala resurser** – vissa organisationer kräver att deras resurser i DevTest Labs har åtkomst till lokala resurser. Det krävs en säker anslutning från din lokala miljö till Azure. Därför är det viktigt att du konfigurerar/konfigurerar antingen en VPN-eller ExpressRoute-anslutning innan du börjar. Mer information finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
- **Ytterligare säkerhets krav** – andra säkerhets krav som dator principer, åtkomst till offentliga IP-adresser, anslutning till Internet är scenarier som kan behöva granskas innan du implementerar ett koncept bevis. 

## <a name="subscription-topology"></a>Prenumerations sto pol Ogin
Prenumerations sto pol Ogin är ett kritiskt design övervägande när du distribuerar DevTest Labs till företaget. Men det är inte nödvändigt att stärka alla beslut förrän ett koncept bevis har slutförts. När du utvärderar antalet prenumerationer som krävs för en företags implementering finns det två extrema: 

- En prenumeration för hela organisationen
- Prenumeration per användare

Nu ska vi Markera de olika teknikernas tekniker.

### <a name="one-subscription"></a>En prenumeration
Ofta är metoden för en prenumeration inte hanterbar i ett stort företag. Att begränsa antalet prenumerationer ger dock följande fördelar:

- **Prognosticering** av kostnader för företag.  Budgetering blir mycket enklare i en enda prenumeration eftersom alla resurser finns i en enda pool. Den här metoden gör det enklare att fatta ett enklare beslut när man ska vidta åtgärder för kostnads kontroll vid en bestämd tidpunkt i en fakturerings period.
- **Hantering** av virtuella datorer, artefakter, formler, nätverks konfiguration, behörigheter, principer osv. är enklare eftersom alla uppdateringar endast krävs i en prenumeration i stället för att göra uppdateringar över flera prenumerationer.
- **Nätverks** ansträngningen är avsevärt förenklad i en enda prenumeration för företag där lokal anslutning är ett krav. Det krävs ytterligare prenumerationer för att ansluta virtuella nätverk över prenumerationer (hubb-eker-modell) med ytterligare prenumerationer, vilket kräver ytterligare konfiguration, hantering, IP-adressutrymme osv.
- **Grupp samarbete** är enklare när alla arbetar i samma prenumeration – till exempel är det enklare att omtilldela en virtuell dator till en medarbetare, dela team resurser osv.

### <a name="subscription-per-user"></a>Prenumeration per användare
En separat prenumeration per användare ger lika möjligheter till det alternativa spektrumet. Fördelarna med att ha många prenumerationer är:

- **Azures skalnings kvoter** kommer inte att hindra antagandet. Anta till exempel att Azure tillåter 200 lagrings konton per prenumeration. Det finns operativa kvoter för de flesta tjänster i Azure (många kan anpassas, vissa kan inte). I den här modellen av en prenumeration per användare är det mycket osannolikt att de flesta kvoter har uppnåtts. Mer information om aktuella Azure Scaling-kvoter finns i [prenumerations-och tjänst begränsningar, kvoter och begränsningar i Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Åter **betalningar** till grupper eller enskilda utvecklare blir mycket enklare för organisationer att redovisa sina kostnader med hjälp av sin nuvarande modell.
- **Ägarskap &s behörigheter** för DevTest Labs-miljöerna är enkla. Du ger utvecklarna åtkomst till prenumerations nivå och de är 100% ansvariga för allt, inklusive nätverks konfigurationen, labb principer och hantering av virtuella datorer.

I företaget kan det finnas tillräckligt med begränsningar för extrem i spektrumet. Därför kan du behöva konfigurera prenumerationer på ett sätt som hamnar i mitten av dessa extrema. Som bästa praxis bör målet för en organisation vara att använda det minimala antalet prenumerationer som det är möjligt att tänka på när det gäller tvingande funktioner som ökar det totala antalet prenumerationer. För att kunna upprepas, är prenumerations miljön kritisk för en företags distribution av DevTest Labs, men bör inte fördröja ett koncept bevis. Det finns ytterligare information i [styrnings](devtest-lab-guidance-governance-policy-compliance.md) artikeln om hur du bestämmer prenumerations-och labb precision i organisationen.

## <a name="roles-and-responsibilities"></a>Roller och ansvarsområden
Ett koncept bevis för DevTest Labs har tre primära roller med definierade ansvar – prenumerations ägare, DevTest Labs-ägare, DevTest Labs-användare och eventuellt en deltagare.

- **Prenumerations ägare** – prenumerations ägaren har behörighet att administrera en Azure-prenumeration, inklusive att tilldela användare, hantera principer, skapa & hantera nätverkstopologi, begära kvot ökningar osv. Mer information finns i [den här artikeln](../role-based-access-control/rbac-and-directory-admin-roles.md).
- **DevTest Labs-ägare** – DevTest Labs-ägaren har fullständig administrativ åtkomst till labbet. Den här personen ansvarar för att lägga till/ta bort användare, hantera kostnads inställningar, allmänna labb inställningar och andra VM/artefaktbaserade uppgifter. En labb ägare har också alla rättigheter till en DevTest Labs-användare.
- **DevTest Labs-användare** – DevTest Labs-användaren kan skapa och använda de virtuella datorerna i labbet. Dessa personer har vissa minimala administrativa funktioner på virtuella datorer som de skapar (starta/stoppa/ta bort/konfigurera sina virtuella datorer). Användarna kan inte hantera virtuella datorer för andra användare.

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: samordna [implementeringen av Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)