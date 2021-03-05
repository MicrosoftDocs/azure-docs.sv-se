---
title: Använd Azure AD Identity Governance för att granska och ta bort externa användare som inte längre har resurs åtkomst
description: Använd åtkomst granskningar för att utöka borttagning av åtkomst från medlemmar i partner organisationer
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
ms.openlocfilehash: fe68ec498d17ec20778c8f34fc6ffa1f0964c44e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176968"
---
# <a name="use-azure-active-directory-azure-ad-identity-governance-to-review-and-remove-external-users-who-no-longer-have-resource-access"></a>Använd Azure Active Directory (Azure AD) identitets styrning för att granska och ta bort externa användare som inte längre har resurs åtkomst

I den här artikeln beskrivs funktioner och metoder som gör att du kan hitta och välja externa identiteter så att du kan granska dem och ta bort dem från Azure AD om de inte längre behövs. Molnet gör det enklare än någonsin att samar beta med interna eller externa användare. Med Office 365 börjar organisationer att se spridningen av externa identiteter (inklusive gäster), eftersom användarna arbetar tillsammans med data, dokument eller digitala arbets ytor, till exempel team. Organisationer behöver balansera, aktivera samarbete och uppfylla säkerhets-och styrnings krav. En del av dessa ansträngningar bör omfatta utvärdering och rensning av externa användare, som bjudits in för samarbete till din klient organisation, som kommer från partner organisationer och tar bort dem från din Azure AD när de inte längre behövs.

>[!NOTE]
>En giltig Azure AD Premium P2, Enterprise Mobility + Security E5 betald eller utvärderings licens krävs för att använda åtkomst granskningar för Azure AD. Mer information finns i [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

## <a name="why-review-users-from-external-organizations-in-your-tenant"></a>Varför ska du granska användare från externa organisationer i din klient organisation?

I de flesta organisationer initierar slutanvändare processen med att bjuda in affärs partner och leverantörer för samarbete. Behovet av att samar beta om enhets organisationer för att ge resurs ägare och slutanvändare möjlighet att utvärdera och intyga externa användare regelbundet. Processen för att registrera nya samarbets partner planeras och redovisas, men med många samarbeten som inte har ett tydligt slutdatum är det inte alltid uppenbart när en användare inte längre behöver åtkomst. Dessutom kan du använda identitets hanterings enheter för att hålla Azure AD Clean och ta bort användare som inte längre behöver åtkomst till organisationens resurser. Att bara behålla relevanta identitets referenser för partner och leverantörer i katalogen bidrar till att minska risken för dina anställda, oavsiktligt välja och bevilja åtkomst till externa användare som bör ha tagits bort. Det här dokumentet vägleder dig genom flera alternativ som sträcker sig från rekommenderade proaktiva förslag på att återaktivera och rensa aktiviteter för att styra externa identiteter.

## <a name="use-entitlement-management-to-grant-and-revoke-access"></a>Använd rättighets hantering för att bevilja och återkalla åtkomst

Funktionerna för rättighets hantering möjliggör [automatiserad livs cykel för externa identiteter](entitlement-management-external-users.md#manage-the-lifecycle-of-external-users) med åtkomst till resurser. Genom att skapa processer och procedurer för att hantera åtkomst via rättighets hantering och publicera resurser via åtkomst paket, blir det mycket mindre svårt att lösa problem med att spåra externa användares åtkomst till resurser. När du hanterar åtkomst via [åtkomst paket för rättighets hantering](entitlement-management-overview.md) i Azure AD kan din organisation centralt definiera och hantera åtkomst för dina användare, samt användare från partner organisationer. Hantering av rättigheter använder godkännanden och tilldelningar av åtkomst paket för att spåra var externa användare har begärt och tilldelats åtkomst. Om en extern användare förlorar alla sina tilldelningar kan hantering av rättigheter ta bort dessa externa användare automatiskt från klienten. 

## <a name="find-guests-not-invited-through-entitlement-management"></a>Hitta gäster som inte har bjudits in via rättighets hantering

När anställda har behörighet att samar beta med externa användare kan de bjuda in flera användare utanför organisationen. Det kanske inte är möjligt att söka efter och gruppera externa partner i företags justerade dynamiska grupper och granska dem, eftersom det kan finnas för många olika företag att granska, eller om det inte finns någon ägare eller sponsor för organisationen. Microsoft tillhandahåller ett exempel på PowerShell-skript som kan hjälpa dig att analysera användningen av externa identiteter i en klient. Skriptet räknar upp externa identiteter och kategoriserar dem. Skriptet kan hjälpa dig att identifiera och rensa externa identiteter som kanske inte längre krävs. Som en del av skriptets utdata stöder skript exemplet automatisk skapande av säkerhets grupper som innehåller de identifierade grupper som är mindre externa partners – för ytterligare analys och användning med Azure AD Access-granskningar.
Skriptet finns på [GitHub](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). När skriptet har körts genererar det en HTML-utdatafil som visar externa identiteter som:

- Har inte längre några grupp medlemskap i klient organisationen
- Ha en tilldelning för en privilegie rad roll i klienten
- Ha en tilldelning till ett program i klienten

Utdata inkluderar även de enskilda domänerna för var och en av dessa externa identiteter. 

>[!NOTE]
>Skriptet som refereras ovan är ett exempel skript som söker efter grupp medlemskap, roll tilldelningar och program tilldelningar i Azure AD. Det kan finnas andra tilldelningar i program som externa användare har tagit emot utanför Azure AD, till exempel SharePoint (tilldelning av direkt medlemskap) eller Azure RBAC eller Azure-DevOps.

## <a name="review-resources-used-by-external-identities"></a>Granska resurser som används av externa identiteter

Om du har externa identiteter som använder resurser, t. ex. team eller andra program som ännu inte har regler ATS av rättighets hantering, kanske du vill granska åtkomsten till dessa resurser regelbundet. [Åtkomst granskningar](create-access-review.md) i Azure AD ger dig möjlighet att granska externa identiteter åtkomst genom att antingen låta resurs ägaren, externa identiteter eller någon annan delegerad person som du litar på bestyrka om det krävs fortsatt åtkomst. Åtkomst granskningar riktar in sig på en resurs och skapar en gransknings aktivitet som är begränsad till antingen alla som har åtkomst till resursen eller gäst användare. Granskaren ser sedan den resulterande listan med användare som de behöver granska – antingen alla användare, inklusive anställda i din organisation eller externa identiteter.

![använda en grupp för att granska åtkomst](media/access-reviews-external-users/group-members.png)

Att upprätta en resurs ägar driven gransknings kultur hjälper till att styra åtkomsten för externa identiteter. Resurs ägare, konto för åtkomst, tillgänglighet och säkerhet för den information som de äger, är i de flesta fall den bästa mål gruppen att få beslut om åtkomst till sina resurser och är närmare de användare som har åtkomst till dem än centrala eller en sponsor som hanterar många externa platser.

## <a name="create-access-reviews-for-external-identities"></a>Skapa åtkomst granskningar för externa identiteter

Användare som inte längre har åtkomst till resurser i din klient organisation kan tas bort om de inte längre arbetar med din organisation. Innan du blockerar och tar bort dessa externa identiteter kanske du vill nå ut till dessa externa användare och se till att du inte har övertagit ett projekt eller en ständig åtkomst som de fortfarande behöver. När du skapar en grupp som innehåller alla externa identiteter som medlemmar som du hittat har ingen åtkomst till några resurser i din klient organisation kan du använda åtkomst granskningar för att alla externa objekt ska bekräftas oavsett om de fortfarande behöver eller har åtkomst – eller kommer fortfarande att ha åtkomst i framtiden. Som en del av granskningen kan gransknings Skaparen i åtkomst granskningar använda funktionen **Kräv orsak till godkännande** för att kräva att externa användare ger en motivering för fortsatt åtkomst, genom vilken du kan lära dig var och hur de fortfarande behöver åtkomst i din klient. Du kan också aktivera funktionen för att ställa in **ytterligare innehåll för e-post i granskare** , så att användarna vet att de kommer att förlora åtkomsten om de inte svarar och, om de fortfarande behöver åtkomst, en motivering krävs. Om du vill gå vidare och låta åtkomst granskarna **inaktivera och ta bort** externa identiteter, om de inte svarar eller ger en giltig orsak för fortsatt åtkomst, kan du använda alternativet Inaktivera och ta bort, enligt beskrivningen i nästa avsnitt.

![begränsa omfånget för granskningen till gäst användare](media/access-reviews-external-users/guest-users-only.png)

När granskningen är klar visas en översikt över svaret som anges av varje extern identitet i **resultat** sidan. Du kan välja att tillämpa resultatet automatiskt och låta åtkomst granskarna inaktivera och ta bort dem. Du kan också titta igenom svaren och bestämma om du vill ta bort en användares åtkomst eller följa upp dem och få ytterligare information innan du fattar ett beslut. Om vissa användare fortfarande har åtkomst till resurser som du ännu inte har granskat, kan du använda granskningen som en del av identifieringen och utöka nästa gransknings-och attesterings cykel.

## <a name="disable-and-delete-external-identities-with-azure-ad-access-reviews"></a>Inaktivera och ta bort externa identiteter med åtkomst granskningar för Azure AD

Förutom alternativet att ta bort oönskade externa identiteter från resurser som grupper eller program, kan åtkomst granskningar i Azure AD blockera externa identiteter från att logga in till klienten och ta bort externa identiteter från din klient organisation efter 30 dagar. När du **har valt blockera användare från att logga in i 30 dagar tar du bort användaren från klienten**, granskningen förblir i läget "tillämpar" i 30 dagar. Under den här perioden går det inte att visa eller konfigurera inställningar, resultat, granskare eller gransknings loggar under den aktuella granskningen. 

![vid slut för ande inställningar](media/access-reviews-external-users/upon-completion-settings.png)

När du skapar en ny åtkomst granskning, i avsnittet "vid slut för ande inställningar", för **åtgärd som ska gälla för nekade användare** kan du definiera **Blockera användare från inloggning i 30 dagar och sedan ta bort användare från klienten**.

Med den här inställningen kan du identifiera, blockera och ta bort externa identiteter från din Azure AD-klient. Externa identiteter som granskas och nekas fortsatt åtkomst av granskaren kommer att blockeras och tas bort, oavsett resurs åtkomst eller grupp medlemskap. Den här inställningen används bäst som ett sista steg när du har verifierat att de externa användarna i granskning inte längre har resurs åtkomst och kan tas bort från din klient organisation eller om du vill se till att de tas bort, oavsett deras ständiga åtkomst. Funktionen "inaktivera och ta bort" blockerar den externa användaren först, vilket gör att du kan logga in på din klient organisation och få åtkomst till resurser. Resurs åtkomsten återkallas inte i det här steget, och om du vill skapa en instans av den externa användaren kan du konfigurera om möjligheten att logga in. Vid inga ytterligare åtgärder tas en blockerad extern identitet bort från katalogen efter 30 dagar, vilket tar bort kontot och deras åtkomst.

## <a name="next-steps"></a>Nästa steg

- [Åtkomstgranskningar – Graph API](/graph/api/resources/accessreviews-root?view=graph-rest-beta)
- [Berättigandehantering – Graph API](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)
