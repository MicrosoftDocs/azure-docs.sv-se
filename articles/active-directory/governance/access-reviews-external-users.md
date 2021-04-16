---
title: Använd Azure AD Identity Governance för att granska och ta bort externa användare som inte längre har resursåtkomst
description: Använda åtkomstgranskningar för att utöka eller ta bort åtkomst från medlemmar i partnerorganisationer
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 09/06/2020
ms.author: ajburnle
ms.openlocfilehash: c976562224d4a0caca8921e46d8f8566800027ee
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532227"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Använd Azure Active Directory (Azure AD) Identity Governance för att granska och ta bort externa användare som inte längre har resursåtkomst

I den här artikeln beskrivs funktioner och metoder som gör att du kan hitta och välja externa identiteter så att du kan granska dem och ta bort dem från Azure AD om de inte längre behövs. Molnet gör det enklare än någonsin att samarbeta med interna eller externa användare. Genom att börja använda Office 365 börjar organisationer se den ökande mängden externa identiteter (inklusive gäster) när användarna arbetar tillsammans med data, dokument eller digitala arbetsytor som Teams. Organisationer behöver balansera, möjliggöra samarbete och uppfylla säkerhets- och styrningskrav. En del av dessa åtgärder bör omfatta utvärdering och rensning av externa användare, som bjudits in för samarbete med din klientorganisation, som kommer från partnerorganisationer och ta bort dem från Azure AD när de inte längre behövs.

>[!NOTE]
>En giltig Azure AD Premium P2-, Enterprise Mobility + Security E5-betal- eller utvärderingslicens krävs för att använda Azure AD-åtkomstgranskningar. Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Varför ska du granska användare från externa organisationer i din klientorganisation?

I de flesta organisationer initierar slutanvändarna processen med att bjuda in affärspartner och leverantörer för samarbete. Behovet av att samarbeta gör att organisationer kan ge resursägare och slutanvändare ett sätt att utvärdera och utvärdera externa användare regelbundet. Ofta planeras och redovisas processen för att registrera nya samarbetspartners, men med många samarbeten som inte har ett tydligt slutdatum är det inte alltid uppenbart när en användare inte längre behöver åtkomst. Identitetslivscykelhanteringen gör det också möjligt för företag att hålla Azure AD rent och ta bort användare som inte längre behöver åtkomst till organisationens resurser. Genom att endast ha relevanta identitetsreferenser för partner och leverantörer i katalogen kan du minska risken för dina anställda genom att oavsiktligt välja och bevilja åtkomst till externa användare som borde ha tagits bort. Det här dokumentet går igenom flera alternativ, från rekommenderade proaktiva förslag till reaktiva och rensningsaktiviteter för att styra externa identiteter.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Använda berättigandehantering för att bevilja och återkalla åtkomst

Berättigandehanteringsfunktioner möjliggör [den automatiserade livscykeln för externa identiteter](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) med åtkomst till resurser. Genom att upprätta processer och procedurer för att hantera åtkomst via berättigandehantering och publicera resurser via åtkomstpaket blir det mycket mindre komplicerat att hålla reda på extern användaråtkomst till resurser. När du hanterar åtkomst via [åtkomstpaket](entitlement-management-overview.md) för berättigandehantering i Azure AD kan din organisation centralt definiera och hantera åtkomst för dina användare samt användare från partnerorganisationer. Berättigandehantering använder godkännanden och tilldelningar av åtkomstpaket för att spåra var externa användare har begärt och tilldelats åtkomst. Om en extern användare förlorar alla sina tilldelningar kan berättigandehanteringen ta bort dessa externa användare automatiskt från klientorganisationen. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Hitta gäster som inte bjudits in via berättigandehantering

När anställda har behörighet att samarbeta med externa användare kan de bjuda in val av antal användare utanför organisationen. Att söka efter och gruppera externa partner i företagsjusterade dynamiska grupper och granska dem kanske inte är möjligt, eftersom det kan finnas för många olika enskilda företag att granska, eller det inte finns någon ägare eller sponsring för organisationen. Microsoft tillhandahåller ett PowerShell-exempelskript som kan hjälpa dig att analysera användningen av externa identiteter i en klientorganisation. Skriptet räknar upp externa identiteter och kategoriserar dem. Skriptet kan hjälpa dig att identifiera och rensa externa identiteter som kanske inte längre behövs. Som en del av skriptets utdata stöder skriptexempel automatiserat skapande av säkerhetsgrupper som innehåller identifierade externa partner utan grupp – för ytterligare analys och användning med Azure AD-åtkomstgranskningar.
Skriptet finns på [GitHub.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse) När skriptet har körts klart genererar det en HTML-utdatafil som beskriver externa identiteter som:

- Har inte längre något gruppmedlemskap i klientorganisationen
- Ha en tilldelning för en privilegierad roll i klientorganisationen
- Ha en tilldelning till ett program i klientorganisationen

Utdata innehåller även de enskilda domänerna för var och en av dessa externa identiteter. 

>[!NOTE]
>Skriptet som refereras ovan är ett exempelskript som söker efter gruppmedlemskap, rolltilldelningar och programtilldelningar i Azure AD. Det kan finnas andra tilldelningar i program som externa användare har fått utanför Azure AD, till exempel SharePoint (direkt medlemskapstilldelning) eller Azure RBAC eller Azure DevOps.

## <a name="review-resources-used-by-external-identities"></a>Granska resurser som används av externa identiteter

Om du har externa identiteter som använder resurser som Teams eller andra program som ännu inte styrs av berättigandehantering, kanske du även vill granska åtkomsten till dessa resurser regelbundet. Med Azure [AD-åtkomstgranskningar](create-access-review.md) kan du granska åtkomsten till externa identiteter genom att antingen låta resursägaren, de externa identiteterna själva eller någon annan delegerad person som du litar på attestera om fortsatt åtkomst krävs. Åtkomstgranskningar riktar in sig på en resurs och skapar en granskningsaktivitet som är begränsad till antingen Alla som endast har åtkomst till resursen eller Gästanvändare. Granskaren ser sedan den resulterande listan över användare som de behöver granska – antingen alla användare, inklusive anställda i din organisation eller endast externa identiteter.

![använda en grupp för att granska åtkomst](media/access-reviews-external-users/group-members.png)

Genom att upprätta en resursägaredriven granskningskultur kan du styra åtkomsten för externa identiteter. Resursägare, som ansvarar för åtkomst, tillgänglighet och säkerhet för den information som de äger, är i de flesta fall den bästa målgruppen för att fatta beslut om åtkomst till sina resurser och är närmare de användare som har åtkomst till dem än den centrala IT-avdelningen eller en sponsring som hanterar många externa användare.

## <a name="create-access-reviews-for-external-identities"></a>Skapa åtkomstgranskningar för externa identiteter

Användare som inte längre har åtkomst till resurser i din klientorganisation kan tas bort om de inte längre arbetar med din organisation. Innan du blockerar och tar bort dessa externa identiteter kanske du vill kontakta dessa externa användare och se till att du inte har förbisett ett projekt eller en stående åtkomst som de fortfarande behöver. När du skapar en grupp som innehåller alla externa identiteter som medlemmar som du hittade inte har åtkomst till några resurser i din klientorganisation kan du använda Åtkomstgranskningar för att få alla externa identiteter attesterade till om de fortfarande behöver eller har åtkomst, eller om de fortfarande behöver åtkomst i framtiden. Som en del av granskningen kan granskaren  i Åtkomstgranskningar använda funktionen Kräv orsak för godkännande för att kräva att externa användare anger en motivering för fortsatt åtkomst, där du kan lära dig var och hur de fortfarande behöver åtkomst i din klientorganisation. Du kan också aktivera  inställningen Funktionen Ytterligare innehåll för granskarens e-post så att användarna vet att de kommer att förlora åtkomst om de inte svarar och, om de fortfarande behöver åtkomst, en motivering krävs. Om du vill låta åtkomstgranskningar inaktivera och ta bort externa identiteter kan du använda alternativet Inaktivera och ta bort om de inte svarar eller anger en giltig orsak till fortsatt åtkomst, enligt beskrivningen i nästa avsnitt. 

![begränsa granskningsomfånget till endast gästanvändare](media/access-reviews-external-users/guest-users-only.png)

När granskningen är klar visar **sidan Resultat** en översikt över svaret som ges av varje extern identitet. Du kan välja att tillämpa resultat automatiskt och låta Åtkomstgranskningar inaktivera och ta bort dem. Du kan också titta igenom svaren och bestämma om du vill ta bort en användares åtkomst eller uppföljning med dem och få ytterligare information innan du fattar ett beslut. Om vissa användare fortfarande har åtkomst till resurser som du inte har granskat ännu kan du använda granskningen som en del av din identifiering och utöka din nästa gransknings- och atterstationscykel.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Inaktivera och ta bort externa identiteter med Azure AD-åtkomstgranskningar

Förutom alternativet att ta bort oönskade externa identiteter från resurser som grupper eller program kan Azure AD-åtkomstgranskningar blockera externa identiteter från att logga in på din klientorganisation och ta bort de externa identiteterna från din klientorganisation efter 30 dagar. När du har valt Blockera användare från att logga in i **30** dagar och sedan tar bort användaren från klientorganisationen förblir granskningen i "tillämpningstillstånd" i 30 dagar. Under den här perioden kan inte inställningar, resultat, granskare eller granskningsloggar under den aktuella granskningen visas eller konfigureras. 

![inställningar för slutförande](media/access-reviews-external-users/upon-completion-settings.png)

När du skapar en ny åtkomstgranskning går du  till avsnittet "Inställningar för slutförande" och för Åtgärd som ska gälla för nekade användare kan du definiera Blockera användare från att logga **in i 30** dagar och sedan ta bort användaren från klientorganisationen .

Med den här inställningen kan du identifiera, blockera och ta bort externa identiteter från Din Azure AD-klientorganisation. Externa identiteter som granskas och nekas fortsatt åtkomst av granskaren kommer att blockeras och tas bort, oavsett resursåtkomst eller gruppmedlemskap. Den här inställningen används bäst som ett sista steg när du har verifierat att de externa användare som granskas inte längre har resursåtkomst och kan tas bort från din klient eller om du vill se till att de tas bort, oavsett deras stående åtkomst. Funktionen "Inaktivera och ta bort" blockerar den externa användaren först och tar bort möjligheten att logga in på din klient och komma åt resurser. Resursåtkomst återkallas inte i det här skedet, och om du vill initiera om den externa användaren kan deras möjlighet att logga in konfigureras om. Vid ingen ytterligare åtgärd tas en blockerad extern identitet bort från katalogen efter 30 dagar, vilket tar bort kontot och deras åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Åtkomstgranskningar – Graph API](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true)
- [Berättigandehantering – Graph API](/graph/api/resources/entitlementmanagement-root)
