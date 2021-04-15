---
title: Hantera medgivande till program och utvärdera begäranden om medgivande i Azure Active Directory
description: Lär dig hur du hanterar begäranden om medgivande när användarmedgivande är inaktiverat eller begränsat och hur du utvärderar en begäran om administratörsmedgivande för hela klientorganisationen till ett program i Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 12/27/2019
ms.author: iangithinji
ms.reviewer: phsignor
ms.openlocfilehash: 3405181f9bace023950e583dfe1a334216bf0aa0
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373952"
---
# <a name="managing-consent-to-applications-and-evaluating-consent-requests"></a>Hantera medgivande till program och utvärdera begäranden om medgivande

Microsoft [rekommenderar att](../../security/fundamentals/steps-secure-identity.md#restrict-user-consent-operations) slutanvändarmedgivande inaktiveras för program. Detta centraliserar beslutsprocessen med organisationens säkerhets- och identitetsadministratörsteam.

När slutanvändares medgivande har inaktiverats eller begränsats finns det flera viktiga överväganden för att se till att din organisation förblir säker samtidigt som affärskritiska program kan användas. De här stegen är viktiga för att minimera påverkan på organisationens supportteam och IT-administratörer, samtidigt som du förhindrar användningen av ohanterade konton i program från tredje part.

## <a name="process-changes-and-education"></a>Bearbeta ändringar och utbildning

 1. Överväg att aktivera [arbetsflödet för administratörsmedgivande](configure-admin-consent-workflow.md) så att användarna kan begära administratörsgodkännande direkt från medgivandeskärmen.

 2. Se till att alla administratörer förstår [](../develop/application-consent-experience.md) [ramverket](../develop/consent-framework.md)för behörigheter och medgivande, hur frågan om medgivande fungerar och hur du utvärderar en begäran om administratörsmedgivande [för hela klientorganisationen.](#evaluating-a-request-for-tenant-wide-admin-consent)
 3. Granska organisationens befintliga processer för användare för att begära administratörsgodkännande för ett program och göra uppdateringar om det behövs. Om processer ändras:
    * Uppdatera relevant dokumentation, övervakning, automatisering och så vidare.
    * Kommunicera processändringar för alla berörda användare, utvecklare, supportteam och IT-administratörer.

## <a name="auditing-and-monitoring"></a>Granskning och övervakning

1. [Granska appar och bevilja behörigheter](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) i din organisation för att säkerställa att inga obehöriga eller misstänkta program tidigare har beviljats åtkomst till data.

2. Läs [Identifiera och åtgärda otillåtna medgivanden i Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) för ytterligare metodtips och skydd mot misstänkta program som begär OAuth-medgivande.

3. Om din organisation har rätt licens:

    * Använd ytterligare [granskningsfunktioner för OAuth-program i Microsoft Cloud App Security](/cloud-app-security/investigate-risky-oauth).
    * Använd [Azure Monitor arbetsböcker för att övervaka behörighets- och medgivanderelaterade](../reports-monitoring/howto-use-azure-monitor-workbooks.md) aktiviteter. Arbetsboken *Consent Insights* ger en vy över appar efter antal begäranden om misslyckat medgivande. Detta kan vara användbart för att prioritera program som administratörer kan granska och avgöra om de ska beviljas administratörsmedgivande.

### <a name="additional-considerations-for-reducing-friction"></a>Ytterligare överväganden för att minska friktionen

För att minimera påverkan på betrodda, affärskritiska program som redan används bör du överväga att proaktivt bevilja administratörsmedgivande till program som har ett stort antal beviljanden av användarmedgivande:

1. Gör en inventering av de appar som redan har lagts till i din organisation med hög användning, baserat på inloggningsloggar eller aktivitet för medgivandebe beviljande. Ett [PowerShell-skript](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) kan användas för att snabbt och enkelt identifiera program med ett stort antal användarmedgivanden.

2. Utvärdera de främsta programmen som ännu inte har beviljats administratörsmedgivande.

   > [!IMPORTANT]
   > Utvärdera noggrant ett program innan du beviljar administratörsmedgivande för hela klientorganisationen, även om många användare i organisationen redan har gett sitt medgivande själva.

3. För varje program som godkänns beviljar du administratörsmedgivande för hela klientorganisationen med någon av de metoder som beskrivs nedan.

4. Överväg att begränsa användaråtkomsten för [varje godkänt program.](configure-user-consent.md)

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Utvärdera en begäran om administratörsmedgivande för hela klientorganisationen

Att bevilja administratörsmedgivande för hela klientorganisationen är en känslig åtgärd.  Behörigheter beviljas för hela organisationens räkning och kan innehålla behörigheter för att försöka utföra åtgärder med hög behörighet. Till exempel rollhantering, fullständig åtkomst till alla postlådor eller alla webbplatser och fullständig användarpersonifiering.

Innan du beviljar administratörsmedgivande för hela klientorganisationen måste du se till att du litar på programmet och programutgivaren för den åtkomstnivå som du beviljar. Om du inte är säker på att du förstår vem som styr programmet och varför programmet begär behörighet ska *du inte bevilja medgivande.*

Följande lista innehåller några rekommendationer att tänka på när du utvärderar en begäran om att bevilja administratörsmedgivande.

* **Förstå [ramverket för behörigheter och medgivande](../develop/consent-framework.md) på Microsoft Identity-plattformen.**

* **Förstå skillnaden mellan [delegerade behörigheter och programbehörigheter.](../develop/v2-permissions-and-consent.md#permission-types)**

   Programbehörigheter gör att programmet kan komma åt data för hela organisationen, utan någon användarinteraktion. Delegerade behörigheter gör att programmet kan agera för en användares räkning som någon gång har loggat in i programmet.

* **Förstå de behörigheter som begärs.**

   De behörigheter som begärs av programmet visas i fråga [om medgivande.](../develop/application-consent-experience.md) Om du expanderar behörighetsrubriken visas behörighetens beskrivning. Beskrivningen för programbehörigheter slutar vanligtvis med "utan en inloggad användare". Beskrivningen för delegerade behörigheter slutar vanligtvis med "för den inloggade användarens räkning". Behörigheter för Microsoft Graph-API:et beskrivs [i Microsoft Graph behörighetsreferens](/graph/permissions-reference) – se dokumentationen för andra API:er för att förstå de behörigheter som de exponerar.

   Om du inte förstår att en behörighet begärs ska *du inte bevilja medgivande.*

* **Förstå vilket program som begär behörigheter och vem som publicerade programmet.**

   Var försiktig med skadliga program som försöker se ut som andra program.

   Om du är osäker på om ett program eller dess utgivare är legitimt *ska du inte bevilja medgivande.* Sök i stället efter ytterligare bekräftelse (till exempel direkt från programutgivaren).

* **Se till att de begärda behörigheterna överensstämmer med de funktioner som du förväntar dig av programmet.**

   Ett program som erbjuder SharePoint-webbplatshantering kan till exempel kräva delegerad åtkomst för att läsa alla webbplatssamlingar, men behöver inte nödvändigtvis fullständig åtkomst till alla postlådor eller fullständiga personifieringsbehörigheter i katalogen.

   Om du misstänker att programmet begär fler behörigheter än det behöver ska *du inte bevilja medgivande.* Kontakta programutgivaren om du vill ha mer information.

## <a name="granting-consent-as-an-administrator"></a>Bevilja medgivande som administratör

### <a name="granting-tenant-wide-admin-consent"></a>Bevilja administratörsmedgivande för hela klientorganisationen
Se [Bevilja](grant-admin-consent.md) administratörsmedgivande för hela klientorganisationen till ett program för stegvisa instruktioner för att bevilja administratörsmedgivande för hela klientorganisationen från Azure Portal, med hjälp av Azure AD PowerShell eller från själva medgivandeuppfråga.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Bevilja medgivande för en specifik användares räkning
I stället för att bevilja medgivande för hela organisationen kan en administratör också använda [api:et för Microsoft Graph](/graph/use-the-api) för att bevilja medgivande till delegerade behörigheter för en enskild användares räkning. Mer information finns i [Få åtkomst för en användares räkning.](/graph/auth-v2-user)

## <a name="limiting-user-access-to-applications"></a>Begränsa användaråtkomst till program
Användarnas åtkomst till program kan fortfarande begränsas även om administratörsmedgivande för hela klientorganisationen har beviljats. Mer information om hur du kräver användartilldelning till ett program finns i [metoder för att tilldela användare och grupper.](./assign-user-or-group-access-portal.md)

Mer en bredare översikt, inklusive hur du hanterar ytterligare komplexa scenarier, finns i [Använda Azure AD för programåtkomsthantering.](what-is-access-management.md)

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Inaktivera alla framtida åtgärder för användarmedgivande för alla program
Om du inaktiverar användarmedgivande för hela katalogen kan slutanvändare inte godkänna program. Administratörer kan fortfarande godkänna för användarens räkning. Mer information om programmedgivande och varför du kanske vill ge ditt medgivande finns i Förstå [användar- och administratörsmedgivande.](../develop/howto-convert-app-to-be-multi-tenant.md)

Följ dessa steg om du vill inaktivera alla framtida åtgärder för användarmedgivande i hela katalogen:
1.  Öppna [**Azure Portal**](https://portal.azure.com/) och logga in som **global administratör.**
2.  Öppna Azure Active Directory **genom** att klicka **på Alla** tjänster längst upp på den vänstra navigeringsmenyn.
3.  Skriv **"Azure Active Directory"** i filtersökrutan och välj **Azure Active Directory** objekt.
4.  Välj **Användare och grupper** i navigeringsmenyn.
5.  Välj **Användarinställningar**.
6.  Inaktivera alla framtida åtgärder för användarmedgivande genom att ange Inställningen Användare kan tillåta att appar får åtkomst till sina **data** växlar **till Nej** och klickar på **knappen** Spara.

## <a name="next-steps"></a>Nästa steg
* [Fem steg för att skydda din identitetsinfrastruktur](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Konfigurera arbetsflödet för administratörsmedgivande](configure-admin-consent-workflow.md)
* [Konfigurera hur slutanvändare godkänner program](configure-user-consent.md)
* [Behörigheter och medgivande i Microsofts identitetsplattform](../develop/v2-permissions-and-consent.md)
