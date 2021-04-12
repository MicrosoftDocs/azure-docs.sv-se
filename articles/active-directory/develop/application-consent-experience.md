---
title: Azure AD-appens medgivande upplevelser
titleSuffix: Microsoft identity platform
description: Lär dig mer om godkännande upplevelser i Azure AD för att se hur du kan använda den när du hanterar och utvecklar program i Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: ryanwi
ms.reviewer: jesakowi, asteen
ms.openlocfilehash: c570fc9f30d69f13546353cf6edab4122ae35142
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105466"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Förstå medgivande i Azure AD-program

Lär dig mer om användar upplevelsen för program medgivande i Azure Active Directory (Azure AD). Så att du kan hantera program för din organisation och/eller utveckla program med en mer sömlös godkännande upplevelse.

## <a name="consent-and-permissions"></a>Medgivande och behörigheter

Samtycke är en användare som ger behörighet till ett program för att få åtkomst till skyddade resurser för deras räkning. En administratör eller användare kan be om medgivande för att tillåta åtkomst till deras organisation/individuella data.

Den faktiska användar upplevelsen för godkännandet kan variera beroende på principer som anges på användarens klient organisation, användarens auktoritets område (eller roll) och vilken typ av [behörighet](v2-permissions-and-consent.md) som begärs av klient programmet. Det innebär att program utvecklare och klient administratörer har viss kontroll över medgivande upplevelsen. Administratörer har flexibiliteten att ställa in och inaktivera principer på en klient eller app för att kontrol lera medgivande upplevelsen i sina klienter. Programutvecklare kan diktera vilka typer av behörigheter som begärs och om de vill ge användarna möjlighet att använda flödet för användarens medgivande eller det administrativa godkännande flödet.

- **Flöde för användar godkännande** är när en programutvecklare dirigerar användare till behörighets slut punkten med avsikt att registrera medgivande för enbart den aktuella användaren.
- **Flöde för administratörs medgivande** är när en programutvecklare dirigerar användare till en slut punkt för administratörs medgivande med avsikt att registrera medgivande för hela klienten. För att säkerställa att det administrativa godkännande flödet fungerar korrekt måste programutvecklare lista alla behörigheter i `RequiredResourceAccess` egenskapen i program manifestet. Mer information finns i [program manifestet](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>Bygg stenar av medgivande frågan

Medgivande frågan är utformad för att se till att användarna har tillräckligt med information för att avgöra om de litar på klient programmet för att få åtkomst till skyddade resurser för deras räkning. Genom att förstå Bygg stenarna kan användare som beviljar sitt medgivande fatta mer välgrundade beslut och hjälpa utvecklare att bygga bättre användar upplevelser.

Följande diagram och tabell innehåller information om bygg blocken i medgivande frågan.

:::image type="content" source="./media/application-consent-experience/consent_prompt.png" alt-text="Bygg stenar av medgivande frågan":::

| # | Komponent | Syfte |
| ----- | ----- | ----- |
| 1 | Användar identifierare | Den här identifieraren representerar användaren som klient programmet begär för att få åtkomst till skyddade resurser åt. |
| 2 | Titel | Rubriken ändras baserat på om användarna ska gå igenom flödet för användare eller administrativt godkännande. I användarens godkännande flöde blir rubriken "behörighet begärs", medan rubriken i det administrativa godkännande flödet har en rad "acceptera för din organisation". |
| 3 | Applogotyp | Den här bilden bör hjälpa användarna att ha en visuell ikon för om den här appen är den app som de är avsedd att komma åt. Den här avbildningen tillhandahålls av programutvecklare och ägarskapet för den här avbildningen är inte verifierad. |
| 4 | Appnamn | Det här värdet bör informera användarna om vilka program som begär åtkomst till sina data. Obs! det här namnet tillhandahålls av utvecklarna och ägarskapet för det här namnet på appen är inte verifierat. |
| 5 | Utgivardomän | Det här värdet bör ge användare en domän som de kan utvärdera för pålitlighet. Den här domänen tillhandahålls av utvecklare och ägarskapet för den här utgivarens domän är verifierad. |
| 6 | Utgivare verifierades | Det blå "verifierade"-märket innebär att appens utgivare har verifierat sin identitet med hjälp av ett Microsoft Partner Network konto och har slutfört verifierings processen.|
| 7 | Utgivarinformation  | Visar om programmet har publicerats av Microsoft eller din organisation. |
| 8 | Behörigheter | Den här listan innehåller de behörigheter som begärs av klient programmet. Användare bör alltid utvärdera de typer av behörigheter som begärs för att förstå vilka data som klient programmet kommer att ha behörighet att komma åt för deras räkning om de accepterar. Som programutvecklare är det bäst att begära åtkomst till behörigheterna med minsta behörighet. |
| 9 | Beskrivning av behörighet | Det här värdet tillhandahålls av tjänsten som visar behörigheterna. Om du vill se behörighets beskrivningarna måste du växla v-tecknet bredvid behörigheten. |
| 10| Program villkor | Dessa villkor innehåller länkar till tillämpnings programmets villkor och sekretess policy. Utgivaren ansvarar för att disponera sina regler i deras användnings villkor. Dessutom är utgivaren ansvarig för att stänga av hur de använder och dela användar data i sin sekretess policy. Om utgivaren inte tillhandahåller länkar till dessa värden för program med flera klienter, kommer det att finnas en varning om medgivande. |
| 11 | https://myapps.microsoft.com | Detta är den länk där användarna kan granska och ta bort program som inte kommer från Microsoft och som för närvarande har åtkomst till sina data. |
| 12 | Rapportera den här | Den här länken används för att rapportera en misstänkt app om du inte litar på appen, om du tror att appen personifierar en annan app, om du tror att appen kommer att missbruka dina data eller av någon annan orsak. |

## <a name="app-requires-a-permission-within-the-users-scope-of-authority"></a>Appen kräver en behörighet inom användarens auktoritets område

Ett gemensamt godkännande scenario är att användaren får åtkomst till en app som kräver en behörighets uppsättning som ligger inom användarens auktoritets omfång. Användaren dirigeras till flödet för användar godkännande.

Administratörer ser en ytterligare kontroll i den traditionella medgivande frågan som gör det möjligt för dem att ge sitt uppdrag åt hela klienten. Kontrollen kommer att försättas till av som standard, så endast när administratörer uttryckligen markerar rutan kommer att beviljas för hela klient organisationens räkning. Från och med idag visas den här kryss rutan bara för den globala administratörs rollen, så moln administratören och app-administratören kommer inte att se den här kryss rutan.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Medgivande-prompt för scenario 1a":::

Användarna kommer att se den traditionella medgivande frågan.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1b.png" alt-text="Skärm bild som visar den traditionella medgivande frågan.":::

## <a name="app-requires-a-permission-outside-of-the-users-scope-of-authority"></a>Appen kräver en behörighet utanför användarens auktoritets område

Ett annat vanligt godkännande scenario är att användaren får åtkomst till en app som kräver minst en behörighet som ligger utanför användarens auktoritets område.

Administratörer ser en ytterligare kontroll i den traditionella medgivande frågan som gör det möjligt för dem att ge sitt uppdrag åt hela klienten.

:::image type="content" source="./media/application-consent-experience/consent_prompt_1a.png" alt-text="Medgivande-prompt för scenario 1a":::

Användare som inte är administratörer kommer att blockeras från att bevilja programmet, och de uppmanas att be dennes administratör om åtkomst till appen.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Skärm bild av medgivande meddelandet som uppmanar användaren att be en administratör om åtkomst till appen.":::

## <a name="user-is-directed-to-the-admin-consent-flow"></a>Användaren dirigeras till det administrativa godkännande flödet

Ett annat vanligt scenario är när användaren navigerar till eller dirigeras till det administrativa godkännande flödet.

Administratörs användare ser frågan om administrativt medgivande. Rubriken och behörighets beskrivningarna som har ändrats i den här prompten. ändringarna markerar det faktum att den här varningen ger appen åtkomst till begärda data för hela klient organisationens räkning.

:::image type="content" source="./media/application-consent-experience/consent_prompt_3a.png" alt-text="Medgivande-prompt för scenario 3a":::

Användare som inte är administratörer kommer att blockeras från att bevilja programmet, och de uppmanas att be dennes administratör om åtkomst till appen.

:::image type="content" source="./media/application-consent-experience/consent_prompt_2b.png" alt-text="Skärm bild av medgivande meddelandet som uppmanar användaren att be en administratör om åtkomst till appen.":::

## <a name="next-steps"></a>Nästa steg

- Få en stegvis översikt över [hur Azure AD medgivande Framework implementerar medgivande](./quickstart-register-app.md).
- Mer djupgående får du lära dig [hur ett program med flera klienter kan använda medgivande ramverket](./howto-convert-app-to-be-multi-tenant.md) för att implementera användar-och administratörs medgivande, stöd för mer avancerade program mönster på flera nivåer.
- Lär dig [hur du konfigurerar appens utgivare domän](howto-configure-publisher-domain.md).