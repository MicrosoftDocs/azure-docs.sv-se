---
title: Vad är åtkomstgranskningar? - Azure Active Directory | Microsoft Docs
description: Med hjälp av Azure Active Directory åtkomst granskningar kan du kontrol lera grupp medlemskap och program åtkomst för att möta styrning, riskhantering och regelefterlevnad i din organisation.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 10/29/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: 22bfef17f68b2e83e4f7462d8e9af6fb8aacc284
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246748"
---
# <a name="what-are-azure-ad-access-reviews"></a>Vad är Azure AD-åtkomstgranskningar?

Azure Active Directory (Azure AD) åtkomst granskningar gör det möjligt för organisationer att effektivt hantera grupp medlemskap, åtkomst till företags program och roll tilldelningar. Användarens åtkomst kan granskas regelbundet för att se till att endast rätt personer har fortsatt åtkomst.

Här är en video som ger en snabb översikt över åtkomst granskningar:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Varför är åtkomst granskningar viktiga?

Med Azure AD kan du samar beta med användare i din organisation och med externa användare. Användare kan ansluta till grupper, bjuda in gäster, ansluta till molnappar och arbeta via fjärr anslutning från sina arbets-eller personliga enheter. Bekvämligheten med att använda självbetjäning har lett till ett behov av bättre åtkomst hanterings funktioner.

- Hur ser du till att de har den åtkomst som nya anställda behöver för att vara produktiv?
- Hur ser du till att deras gamla åtkomst tas bort när personer flyttar team eller lämnar företaget?
- Hög åtkomst behörighet kan leda till kompromisser.
- För hög åtkomst rättighet kan också leda till gransknings resultat när de visar att det saknas kontroll över åtkomsten.
- Du måste proaktivt kommunicera med resurs ägare för att se till att de regelbundet granskar vem som har åtkomst till sina resurser.

## <a name="when-should-you-use-access-reviews"></a>När bör du använda åtkomst granskningar?

- **För många användare i privilegierade roller:** Det är en bra idé att kontrol lera hur många användare som har administrativ åtkomst, hur många av dem som är globala administratörer och om det finns inbjudna gäster eller partners som inte har tagits bort efter att ha tilldelats en administrativ uppgift. Du kan omcertifiera roll tilldelnings användare i [Azure AD-roller](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) som globala administratörer eller [Azure-resurser](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) , t. ex. administratör för användar åtkomst i [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) .
- **När Automation inte är möjligt:** Du kan skapa regler för dynamiskt medlemskap i säkerhets grupper eller Microsoft 365 grupper, men vad händer om HR-data inte finns i Azure AD eller om användarna fortfarande behöver åtkomst efter att ha lämnat gruppen för att träna utbyte av dem? Du kan sedan skapa en granskning av den gruppen för att säkerställa att de som fortfarande behöver åtkomst har fortsatt åtkomst.
- **När en grupp används för ett nytt syfte:** Om du har en grupp som ska synkroniseras med Azure AD, eller om du planerar att aktivera programmet Salesforce för alla i försäljnings grupp gruppen, är det praktiskt att be grupp ägaren att granska grupp medlemskapet innan gruppen används i ett annat risk innehåll.
- **Affärs kritisk data åtkomst:** för vissa resurser kan det vara nödvändigt att be personer utanför IT att regelbundet logga ut och ge en motivering för varför de behöver åtkomst till gransknings syfte.
- **För att underhålla undantags listan för en princip:** I en idealisk värld följer alla användare åtkomst principerna för att skydda åtkomsten till organisationens resurser. Ibland finns det dock affärs fall där du behöver göra undantag. Som IT-administratör kan du hantera den här uppgiften, undvika överblick av princip undantag och tillhandahålla granskare med bevis på att dessa undantag granskas regelbundet.
- **Be grupp ägare att bekräfta att de fortfarande behöver gäster i sina grupper:** Åtkomst till anställda kan automatiseras med en del lokal identitets-och åtkomst hantering (IAM), men inte inbjudna gäster. Om en grupp ger gäster åtkomst till företagets känsliga innehåll, är det grupp ägarens ansvar att bekräfta att gästerna fortfarande har till gång till ett legitimt verksamhets behov.
- **Låt granskningar upprepas regelbundet:** Du kan ställa in återkommande åtkomst granskningar av användare i angivna frekvenser, till exempel varje vecka, varje kvartal eller varje kvartal, och granskarna meddelas i början av varje granskning. Granskare kan godkänna eller neka åtkomst med ett vänligt gränssnitt och med hjälp av smarta rekommendationer.

>[!NOTE]
>Om du är redo att testa åtkomst granskningar tar du en titt på [skapa en åtkomst granskning av grupper eller program](create-access-review.md)

## <a name="where-do-you-create-reviews"></a>Var skapar du granskningar?

Beroende på vad du vill granska kommer du att skapa din åtkomst granskning i Azure AD åtkomst granskningar, Azure AD Enterprise-appar (i för hands versionen) eller Azure AD PIM.

| Åtkomst rättigheter för användare | Granskare kan vara | Granskning skapad i | Granskare upplevelse |
| --- | --- | --- | --- |
| Medlemmar i säkerhets grupp</br>Office-gruppmedlemmar | Angivna granskare</br>Grupp ägare</br>Själv granskning | Azure AD-åtkomstgranskningar</br>Azure AD-grupper | Åtkomstpanel |
| Tilldelad till en ansluten app | Angivna granskare</br>Själv granskning | Azure AD-åtkomstgranskningar</br>Azure AD Enterprise-appar (för hands version) | Åtkomstpanel |
| Azure AD-roll | Angivna granskare</br>Själv granskning | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |
| Resurs roll för Azure | Angivna granskare</br>Själv granskning | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure Portal |

## <a name="license-requirements"></a>Licenskrav

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Hur många licenser måste du ha?

Katalogen kräver minst så många Azure AD Premium P2-licenser som antalet anställda som utför följande aktiviteter:

-   Medlems användare som har tilldelats som granskare
-   Medlems användare som utför en själv granskning
-   Medlems användare som grupp ägare som utför en åtkomst granskning
-   Medlems användare som program ägare som utför en åtkomst granskning

För gäst användare beror licensierings behovet på den licensierings modell som du använder. Följande gäst användares aktiviteter betraktas dock som Azure AD Premium P2-användning:

-   Gäst användare som tilldelats som granskare
-   Gäst användare som utför en själv granskning
-   Gäst användare som grupp ägare som utför en åtkomst granskning
-   Gäst användare som program ägare som utför en åtkomst granskning


Azure AD Premium P2-licenser krävs **inte** för användare med rollerna global administratör eller användar administratör som konfigurerar åtkomst granskningar, konfigurerar inställningar eller tillämpar besluten från granskningarna.

Åtkomst till Azure AD-gästen baseras på en MAU-fakturerings modell (månatlig aktiva användare) som ersätter fakturerings modellen för 1:5-förhållandet. Mer information finns i [priser för externa Azure AD-identiteter](../external-identities/external-identities-pricing.md).

Mer information om licenser finns i [tilldela eller ta bort licenser med hjälp av Azure Active Directory portalen](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Exempel på licens scenarier

Här följer några exempel på licens scenarier som hjälper dig att fastställa antalet licenser som du måste ha.

| Scenario | Beräkning | Antal licenser |
| --- | --- | --- |
| En administratör skapar en åtkomst granskning av grupp A med 75 användare och 1 grupp ägare och tilldelar grupp ägaren som granskaren. | 1 licens för grupp ägaren som granskare | 1 |
| En administratör skapar en åtkomst granskning av grupp B med 500 användare och tre grupp ägare och tilldelar de tre grupp ägarna som granskare. | 3 licenser för varje grupp ägare som granskare | 3 |
| En administratör skapar en åtkomst granskning av grupp B med 500 användare. Gör det till en själv granskning. | 500 licenser för varje användare som självgranskare | 500 |
| En administratör skapar en åtkomst granskning av grupp C med 50 medlems användare och 25 gäst användare. Gör det till en själv granskning. | 50 licenser för varje användare som egen granskare. * | 50 |
| En administratör skapar en åtkomst granskning av grupp D med 6 medlems användare och 108 gäst användare. Gör det till en själv granskning. | 6 licenser för varje användare som självgranskare. Gäst användare faktureras baserat på en månatlig aktiv användare (MAU). Inga ytterligare licenser krävs. *  | 6 |

\* Priser för externa Azure AD-identiteter (gäst användare) baseras på månatliga aktiva användare (MAU), vilket är antalet unika användare med autentiserings aktivitet inom en kalender månad. Den här modellen ersätter förhållandet mellan 1:5 och fakturerings modell, som tillåter upp till fem gäst användare för varje Azure AD Premium licens i din klient organisation. När klienten är länkad till en prenumeration och du använder externa identiteter för att samar beta med gäst användare debiteras du automatiskt med hjälp av den MAU fakturerings modellen. Mer information finns i fakturerings modell för externa Azure AD-identiteter.

## <a name="next-steps"></a>Nästa steg

- [Skapa en åtkomst granskning av grupper eller program](create-access-review.md)
- [Skapa en åtkomstgranskning av användarna i en administrativ roll i Azure AD](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Granska åtkomst till grupper eller program](perform-access-review.md)
- [Slutför en åtkomst granskning av grupper eller program](complete-access-review.md)
