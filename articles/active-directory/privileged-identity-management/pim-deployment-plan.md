---
title: Distribuera Privileged Identity Management (PIM) – Azure AD | Microsoft Docs
description: Beskriver hur du planerar distributionen av Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/27/2020
ms.author: curtand
ms.custom: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b1d18982a4f2a9ee8ba585af56a5e9ded7c1c62
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036834"
---
# <a name="deploy-azure-ad-privileged-identity-management-pim"></a>Distribuera Azure AD Privileged Identity Management (PIM)

Den här artikeln är en steg-för-steg-guide som beskriver hur du planerar distributionen av Privileged Identity Management (PIM) i din Azure Active Directory (Azure AD)-organisation. Du omtilldelar användare i privilegierade roller till mindre kraftfulla inbyggda eller anpassade roller där det är möjligt, och planera för just-in-Time-roll tilldelningar för dina mest privilegierade roller. I den här artikeln får vi rekommendationer för både distributions planering och implementering.

> [!TIP]
> I den här artikeln visas objekt som är markerade som:
>
> : heavy_check_mark: **Microsoft rekommenderar**
>
> Detta är allmänna rekommendationer och du bör endast implementera dem när de gäller för dina företags behov.

## <a name="licensing-requirements"></a>Licenskrav

Om du vill använda Privileged Identity Management måste katalogen ha någon av följande betalda eller utvärderings licenser. Mer information finns i [licens krav för att använda Privileged Identity Management](subscription-requirements.md).

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5
- Microsoft 365 Education A5
- Microsoft 365 Enterprise E5

## <a name="how-pim-works"></a>Så här fungerar PIM

Det här avsnittet innehåller en översikt över planeringen av relevanta delar av Privileged Identity Management processen. Mer information finns i [Vad är Azure AD Privileged Identity Management?](pim-configure.md)

1. Börja använda Privileged Identity Management så att användarna är berättigade till privilegierade roller.
1. När en berättigad användare behöver använda sin privilegierade roll, aktive ras rollen med hjälp av Privileged Identity Management.
1. Användaren kan ställas in i inställningar för att:

    - Använd Multi-Factor Authentication
    - Begär godkännande för aktivering
    - Ge en verksamhets orsak för aktivering

1. När användaren har aktiverat sin roll har de roll behörigheterna för en angiven varaktighet.
1. Administratörer kan visa en historik över alla Privileged Identity Management aktiviteter i gransknings loggen. De kan också skydda sina Azure AD-organisationer ytterligare och möta efterlevnaden med hjälp av Privileged Identity Management funktioner som åtkomst granskningar och aviseringar.

## <a name="roles-that-can-be-managed-by-pim"></a>Roller som kan hanteras av PIM

**Azure AD-roller** är alla i Azure Active Directory (till exempel global administratör, Exchange-administratör och säkerhets administratör). Du kan läsa mer om rollerna och deras funktioner i [Administratörs roll behörigheter i Azure Active Directory](../roles/permissions-reference.md). För hjälp med att bestämma vilka roller som ska tilldelas dina administratörer, se [minst privilegierade roller efter uppgift](../roles/delegate-by-task.md).

**Azure-roller** är roller som är länkade till en Azure-resurs, en resurs grupp, en prenumeration eller en hanterings grupp. Du kan använda PIM för att ge just-in-Time-åtkomst till inbyggda Azure-roller som ägare, användar åtkomst administratör och deltagare, samt även för [anpassade roller](../../role-based-access-control/custom-roles.md). Mer information om Azure-roller finns i [rollbaserad åtkomst kontroll i Azure](../../role-based-access-control/overview.md).

Mer information finns i [roller som du inte kan hantera i Privileged Identity Management](pim-roles.md).

## <a name="deployment-plan"></a>Distributionsplan

Innan du distribuerar Privileged Identity Management i din organisation, följer du anvisningarna och förstår begreppen i det här avsnittet för att hjälpa dig att skapa en plan som är anpassad till organisationens privilegierade identitets krav.

### <a name="identify-your-stakeholders"></a>Identifiera dina intressenter

I följande avsnitt får du hjälp att identifiera alla intressenter som ingår i projektet och som behöver logga ut, granska eller hålla dig informerad. Den innehåller separata tabeller för att distribuera PIM för Azure AD-roller och PIM för Azure-roller. Lägg till intressenter i följande tabell efter vad som är lämpligt för din organisation.

- SÅ = logga ut på det här projektet
- R = granska projektet och ange inmatade
- I = informeras om det här projektet

#### <a name="stakeholders-privileged-identity-management-for-azure-ad-roles"></a>Intressenter: Privileged Identity Management för Azure AD-roller

| Namn | Roll | Action |
| --- | --- | --- |
| Namn och e-postadress | **Identitets arkitekt eller Global Azure-administratör**<br/>En representant från identitets hanterings teamet som är ansvarig för att definiera hur den här ändringen ska justeras med infrastrukturen för kärn identitets hantering i din organisation. | SÅ/R/I |
| Namn och e-postadress | **Tjänst ägare/rads hanterare**<br/>En representant från IT-ägare till en tjänst eller en grupp av tjänster. De är viktiga för att fatta beslut och hjälpa till att distribuera Privileged Identity Management för sitt team. | SÅ/R/I |
| Namn och e-postadress | **Säkerhets ägare**<br/>En representant från säkerhets teamet som kan godkänna att planen uppfyller organisationens säkerhets krav. | SÅ/R |
| Namn och e-postadress | **IT support Manager/supportavdelningen**<br/>En representant från IT-supporten som kan ge feedback om supporten för den här ändringen från ett support perspektiv. | R/I |
| Namn och e-post för pilot användare | **Privilegierade roll användare**<br/>Gruppen med användare som Privileged Identity Management implementeras för. De måste känna till hur de aktiverar sina roller när Privileged Identity Management implementeras. | I |

#### <a name="stakeholders-privileged-identity-management-for-azure-roles"></a>Intressenter: Privileged Identity Management för Azure-roller

| Namn | Roll | Action |
| --- | --- | --- |
| Namn och e-postadress | **Prenumeration/resurs ägare**<br/>En representant från IT-ägarna till varje prenumeration eller resurs som du vill distribuera Privileged Identity Management för | SÅ/R/I |
| Namn och e-postadress | **Säkerhets ägare**<br/>En representant från säkerhets teamet som kan signera att planen uppfyller organisationens säkerhets krav. | SÅ/R |
| Namn och e-postadress | **IT support Manager/supportavdelningen**<br/>En representant från IT-supporten som kan ge feedback om supporten för den här ändringen från ett support perspektiv. | R/I |
| Namn och e-post för pilot användare | **Azure Role-användare**<br/>Gruppen med användare som Privileged Identity Management implementeras för. De måste känna till hur de aktiverar sina roller när Privileged Identity Management implementeras. | I |

### <a name="start-using-privileged-identity-management"></a>Börja använda Privileged Identity Management

Som en del av planerings processen bör du förbereda Privileged Identity Management genom att följa artikeln [börja använda Privileged Identity Management](pim-getting-started.md) . Privileged Identity Management ger dig till gång till vissa funktioner som har utformats för att hjälpa dig med din distribution.

Om målet är att distribuera Privileged Identity Management för Azure-resurser bör du följa våra [Azure-resurser för att hantera i Privileged Identity Management](pim-resource-roles-discover-resources.md) artikeln. Endast ägare av prenumerationer och hanterings grupper kan se till att dessa resurser hanteras av Privileged Identity Management. När den har hanterats är PIM-funktionen tillgänglig för ägare på alla nivåer, inklusive hanterings grupp, prenumeration, resurs grupp och resurs. Om du är en global administratör som försöker distribuera Privileged Identity Management för dina Azure-resurser kan du [öka åtkomsten för att hantera alla Azure-prenumerationer](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) för att ge dig åtkomst till alla Azure-resurser i katalogen för identifiering. Vi rekommenderar dock att du får godkännande från var och en av dina prenumerations ägare innan du hanterar resurserna med Privileged Identity Management.

### <a name="enforce-principle-of-least-privilege"></a>Tillämpa principen för minsta behörighet

Det är viktigt att se till att du har tillämpat principen om minsta behörighet i din organisation för både din Azure AD och dina Azure-roller.

#### <a name="plan-least-privilege-delegation"></a>Planera minsta behörighets delegering

För Azure AD-roller är det vanligt att organisationer tilldelar rollen global administratör till ett antal administratörer när de flesta administratörer bara behöver en eller två särskilda och mindre kraftfulla administratörs roller. Med ett stort antal globala administratörer eller andra roller med hög behörighet är det svårt att spåra dina privilegierade roll tilldelningar tillräckligt.

Följ dessa steg för att implementera principen om minsta behörighet för dina Azure AD-roller.

1. Förstå rollernas granularitet genom att läsa och förstå de tillgängliga [inbyggda Azure AD-rollerna](../roles/permissions-reference.md). Du och ditt team bör också referera till [Administratörs roller efter identitets uppgift i Azure AD](../roles/delegate-by-task.md), som förklarar den minst privilegierade rollen för särskilda uppgifter.

1. Lista med privilegierade roller i din organisation. Du kan använda Privileged Identity Management [identifiering och insikter (för hands version)](pim-security-wizard.md) för att minska exponeringen.

    ![Sidan identifiering och insikter (för hands version) för att minska exponeringen via privilegierade roller](./media/pim-deployment-plan/new-preview-page.png)

1. Ta reda på varför de behöver rollen för alla globala administratörer i din organisation. Ta sedan bort dem från den globala administratörs rollen och tilldela inbyggda roller eller anpassade roller med lägre behörighet i Azure Active Directory. Obs! Microsoft har för närvarande bara ca 10 administratörer med rollen global administratör. Läs mer om [hur Microsoft använder Privileged Identity Management](https://www.microsoft.com/itshowcase/Article/Content/887/Using-Azure-AD-Privileged-Identity-Management-for-elevated-access).

1. För alla andra Azure AD-roller granskar du listan över tilldelningar, identifierar administratörer som inte längre behöver rollen och tar bort dem från deras tilldelningar.

Om du vill automatisera de två sista stegen kan du använda åtkomst granskningar i Privileged Identity Management. Genom att följa stegen i [starta en åtkomst granskning för Azure AD-roller i Privileged Identity Management](pim-how-to-start-security-review.md)kan du konfigurera en åtkomst granskning för varje Azure AD-roll som har en eller flera medlemmar.

![Skapa ett åtkomst gransknings fönster för Azure AD-roller](./media/pim-deployment-plan/create-access-review.png)

Ställ in granskarna till **medlemmar (Self)**. Alla användare i rollen får ett e-postmeddelande som ber dem att bekräfta att de behöver åtkomst. Aktivera även **Kräv orsak för godkännande** i de avancerade inställningarna så att användarna måste ange varför de behöver rollen. Utifrån den här informationen kan du ta bort användare från onödiga roller eller delegera dem till mer detaljerade administratörs roller.

Åtkomst granskningar förlitar sig på e-postmeddelanden för att meddela personer att granska deras åtkomst till rollerna. Om du har privilegierade konton som inte har e-post länkade måste du fylla i fältet sekundär e-post för dessa konton. Mer information finns i [proxyaddresses-attribut i Azure AD](https://support.microsoft.com/help/3190357/how-the-proxyaddresses-attribute-is-populated-in-azure-ad).

#### <a name="plan-azure-resource-role-delegation"></a>Planera delegering av Azure-resurs roll

För Azure-prenumerationer och-resurser kan du konfigurera en liknande åtkomst gransknings process för att granska rollerna i varje prenumeration eller resurs. Målet med den här processen är att minimera ägar-och användar åtkomst administratörs tilldelningar kopplade till varje prenumeration eller resurs och ta bort onödiga tilldelningar. Organisationer delegerar ofta sådana uppgifter till ägaren av varje prenumeration eller resurs, eftersom de har en bättre förståelse för de olika rollerna (särskilt anpassade roller).

Om du använder den globala administratörs rollen för att försöka distribuera PIM för Azure-roller i din organisation kan du [öka åtkomsten för att hantera alla Azure-prenumerationer](../../role-based-access-control/elevate-access-global-admin.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json) för att få åtkomst till varje prenumeration. Du kan sedan hitta varje prenumerations ägare och arbeta med dem för att ta bort onödiga tilldelningar och minimera ägar roll tilldelningen.

Användare med ägar rollen för en Azure-prenumeration kan också använda [åtkomst granskningar för Azure-resurser](pim-resource-roles-start-access-review.md) för att granska och ta bort onödiga roll tilldelningar som liknar processen som beskrivits tidigare för Azure AD-roller.

### <a name="decide-which-role-assignments-should-be-protected-by-privileged-identity-management"></a>Bestäm vilka roll tilldelningar som ska skyddas av Privileged Identity Management

När du har rensat privilegierade roll tilldelningar i din organisation måste du bestämma vilka roller som ska skyddas med Privileged Identity Management.

Om en roll skyddas av Privileged Identity Management, måste berättigade användare som är tilldelade till den utökas till att använda privilegier som beviljats av rollen. Upphöjnings processen kan också innehålla godkännande, använda Multi-Factor Authentication och ange en orsak till varför de aktive ras. Privileged Identity Management kan också spåra utökade privilegier via meddelanden och gransknings händelse loggar för Privileged Identity Management och Azure AD.

Det kan vara svårt att välja vilka roller som ska skyddas med Privileged Identity Management och de är olika för varje organisation. Det här avsnittet innehåller vår bästa praxis för Azure AD och Azure-roller.

#### <a name="azure-ad-roles"></a>Azure AD-roller

Det är viktigt att prioritera att skydda Azure AD-roller som har mest behörighet. Baserat på användnings mönster bland alla Privileged Identity Management-kunder är de viktigaste 10 Azure AD-rollerna som hanteras av Privileged Identity Management:

1. Global administratör
1. Säkerhetsadministratör
1. Användaradministratör
1. Exchange-administratör
1. SharePoint-administratör
1. Intune-administratör
1. Säkerhetsläsare
1. Tjänstadministratör
1. Faktureringsadministratör
1. Skype for Business-administratör

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du hanterar alla dina globala administratörer och säkerhets administratörer med Privileged Identity Management som ett första steg eftersom de är de användare som kan göra de mest skadliga när de komprometteras.

Det är viktigt att tänka på vilka data och behörigheter som är mest känsliga för din organisation. Vissa organisationer kan till exempel vilja skydda sin Power BI administratörs roll eller deras team administratörs roll med Privileged Identity Management eftersom de kan komma åt data och ändra kärn arbets flöden.

Om det finns roller med tilldelade gäst användare är de utsatta för angrepp.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du hanterar alla roller med gäst användare som använder Privileged Identity Management för att minska risken som är kopplad till komprometterade gäst användar konton.

Reader-roller som katalog läsaren, meddelande Center läsaren och säkerhets läsaren betraktas ibland som mindre viktiga än andra roller eftersom de inte har Skriv behörighet. Vi har dock vissa kunder som också skyddar dessa roller eftersom angripare med åtkomst till dessa konton kan läsa känsliga data, till exempel personliga data. Ta hänsyn till den här risken när du bestämmer om du vill att läsarens roller i din organisation ska hanteras med hjälp av Privileged Identity Management.

#### <a name="azure-roles"></a>Azure-roller

När du bestämmer vilka roll tilldelningar som ska hanteras med Privileged Identity Management för Azure-resurser måste du först identifiera de prenumerationer/resurser som är mest viktiga för din organisation. Exempel på sådana prenumerationer/resurser är:

- Resurser som är värdar för de mest känsliga data
- Resurser som grundar sig på kund riktade program är beroende av

Om du är global administratör och har problem med att bestämma vilka prenumerationer och resurser som är viktigast bör du kontakta Prenumerationens ägare i din organisation för att samla in en lista över resurser som hanteras av varje prenumeration. Sedan arbetar du med prenumerations ägarna för att gruppera resurserna baserat på allvarlighets grad, då de komprometteras (låg, medium, hög). Prioritera hantering av resurser med Privileged Identity Management baserat på den här allvarlighets graden.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du arbetar med prenumerationer/resurs ägare av kritiska tjänster för att skapa Privileged Identity Management-arbetsflöde för alla roller i känsliga prenumerationer/resurser.

Privileged Identity Management för Azure-resurser stöder tids gränser för tjänst konton. Du bör behandla tjänst konton exakt på samma sätt som du skulle behandla ett vanligt användar konto.

För prenumerationer/resurser som inte är lika kritiska behöver du inte konfigurera Privileged Identity Management för alla roller. Du bör dock fortfarande skydda rollen ägare och användar åtkomst administratör med Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du hanterar ägar roller och administratörs roller för användar åtkomst för alla prenumerationer/resurser med hjälp av Privileged Identity Management.

### <a name="decide-whether-to-use-a-group-to-assign-roles"></a>Bestäm om du vill använda en grupp för att tilldela roller

Om du vill tilldela en roll till en grupp i stället för till enskilda användare är ett strategiskt beslut. När du planerar bör du överväga att tilldela en roll till en grupp för att hantera roll tilldelningar när:

- Många användare tilldelas en roll
- Du vill delegera tilldelningen av rollen

#### <a name="many-users-are-assigned-to-a-role"></a>Många användare tilldelas en roll

Hålla reda på vem som är tilldelad en roll och hantera tilldelningar baserat på när de behöver det kan ta tid när som helst. Om du vill tilldela en grupp till en roll måste du först [skapa en grupp](../roles/groups-create-eligible.md) för tilldelnings bara roller och sedan tilldela gruppen som giltig för en roll. Den här åtgärden delar alla i gruppen på samma aktiverings process som enskilda användare som är berättigade att kunna utökas till rollen. Grupp medlemmar aktiverar sina tilldelningar till gruppen individuellt med hjälp av processen för Privileged Identity Management aktiverings förfrågan och godkännande. Gruppen är inte aktive rad, bara användarens grupp medlemskap.

#### <a name="you-want-to-delegate-assigning-the-role"></a>Du vill delegera tilldelningen av rollen

En grupp ägare kan hantera medlemskap för en grupp. För Azure AD Role-tilldelnings bara grupper kan endast privilegierade roll administratörer, global administratör och grupp ägare hantera grupp medlemskap. Genom att lägga till nya medlemmar i gruppen får medlemmen åtkomst till de roller som gruppen tilldelas om tilldelningen är giltig eller aktiv. Använd grupp ägare för att delegera hanteringen av grupp medlemskap för en tilldelad roll för att minska den behörighets bredd som krävs. Mer information om hur du tilldelar en ägare till en grupp när du skapar gruppen finns i [skapa en roll tilldelnings bara grupp i Azure AD](../roles/groups-create-eligible.md).

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du ger Azure AD-roll tilldelnings bara grupper under hantering genom att Privileged Identity Management. När en grupp som kan tilldelas roller hanteras av PIM kallas den för en privilegie rad åtkomst grupp. Använd PIM för att kräva att grupp ägare aktiverar sina ägares roll tilldelningar innan de kan hantera grupp medlemskap. Mer information om att lägga till grupper under PIM-hantering finns i ge [privilegierade åtkomst grupper (för hands version) till Privileged Identity Management](groups-discover-groups.md).

### <a name="decide-which-role-assignments-should-be-permanent-or-eligible"></a>Bestäm vilka roll tilldelningar som ska vara permanenta eller berättigade

När du har bestämt listan över roller som ska hanteras av Privileged Identity Management måste du bestämma vilka användare som ska få den kvalificerade rollen och den permanent aktiva rollen. Permanent aktiva roller är de normala roller som tilldelas via Azure Active Directory och Azure-resurser, medan berättigade roller bara kan tilldelas i Privileged Identity Management.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du har noll aktiva tilldelningar för både Azure AD-roller och andra Azure-roller än de rekommenderade två återställnings [kontona med hårt glas](../roles/security-emergency-access.md), vilket bör ha den permanenta globala administratörs rollen.

Även om vi rekommenderar ständig administratör är det ibland svårt för organisationer att uppnå detta direkt. Följande är saker att tänka på när du fattar det här beslutet:

- Frekvens av höjning – om användaren bara behöver den privilegierade tilldelningen en gång får de inte ha den permanenta tilldelningen. Å andra sidan, om användaren behöver rollen för sitt dagliga jobb och använder Privileged Identity Management avsevärt minskar deras produktivitet, kan de övervägas för den permanenta rollen.
- Fall som är specifikt för din organisation – om personen som har fått den berättigade rollen kommer från ett avlägsen team eller en chef med hög rangordning till den punkt som kommunicerar och framtvingar upphöjnings processen är svår, kan de övervägas för den permanenta rollen.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du konfigurerar återkommande åtkomst granskningar för användare med permanenta roll tilldelningar (om du har några). Läs mer om återkommande åtkomst granskning i den sista delen av den här distributions planen

### <a name="draft-your-privileged-identity-management-settings"></a>Utkast till dina Privileged Identity Management inställningar

Innan du implementerar din Privileged Identity Management-lösning är det bra att skissa dina Privileged Identity Management inställningar för varje privilegie rad roll som din organisation använder. Det här avsnittet innehåller några exempel på Privileged Identity Management inställningar för vissa roller (de är endast för referens och kan vara olika för din organisation). Var och en av dessa inställningar beskrivs i detalj med Microsofts rekommendationer efter tabellerna.

#### <a name="privileged-identity-management-settings-for-azure-ad-roles"></a>Privileged Identity Management inställningar för Azure AD-roller

| Roll | Krav på MFA | Meddelande | Incident biljett | Kräv godkännande | Godkännare | Varaktighet för aktivering | Permanent administratör |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Global administratör | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andra globala administratörer | 1 timme | Konton för nöd åtkomst |
| Exchange-administratör | :heavy_check_mark: | :heavy_check_mark: | röntgen | röntgen | Inget | 2 timme | Inget |
| Support administratör | röntgen | röntgen | :heavy_check_mark: | röntgen | Inget | 8 timmar | Inget |

#### <a name="privileged-identity-management-settings-for-azure-roles"></a>Privileged Identity Management inställningar för Azure-roller

| Roll | Krav på MFA | Meddelande | Kräv godkännande | Godkännare | Varaktighet för aktivering | Aktiv administratör | Aktiv utgång | Giltig förfallo datum |
| --- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Ägare till kritiska prenumerationer | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Andra ägare till prenumerationen | 1 timme | Inget | saknas | 3 månad |
| Administratör för användar åtkomst för mindre kritiska prenumerationer | :heavy_check_mark: | :heavy_check_mark: | röntgen | Inget | 1 timme | Inget | saknas | 3 månad |
| Virtuell datordeltagare | röntgen | :heavy_check_mark: | röntgen | Inget | 3 timmar | Inget | saknas | 6 månad |

I följande tabell beskrivs var och en av inställningarna.

| Inställning | Beskrivning |
| --- | --- |
| Roll | Namnet på den roll som du definierar inställningarna för. |
| Krav på MFA | Om den kvalificerade användaren behöver utföra MFA innan rollen aktive ras.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att du tillämpar MFA för alla administratörs roller, särskilt om rollerna har gäst användare. |
| Meddelande | Om detta är inställt på Sant får du ett e-postmeddelande när en berättigad användare aktiverar rollen.<br/><br/>**Obs:** Vissa organisationer har ingen e-postadress knutna till sina administratörs konton, för att få dessa e-postaviseringar ska du ange en alternativ e-postadress så att administratörer får e-postmeddelandena. |
| Incident biljett | Om den berättigade användaren behöver registrera ett incident biljett nummer när de aktiverar sin roll. Med den här inställningen kan en organisation identifiera varje aktivering med ett internt incident nummer för att minimera oönskade aktiveringar.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att man drar nytta av incident nummer för att koppla Privileged Identity Management till ditt interna system. Den här metoden kan vara användbar för god kännare som behöver kontext för aktiveringen. |
| Kräv godkännande | Om den kvalificerade användaren behöver få godkännande för att aktivera rollen.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att du ställer in godkännande för roller med mest behörighet. Baserat på användnings mönster för alla Privileged Identity Management-kunder, global administratör, användar administratör, Exchange-administratör, säkerhets administratör och lösen ords administratör är de vanligaste rollerna med godkännande inställningar. |
| Godkännare | Om godkännande krävs för att aktivera den berättigade rollen, listar du de personer som ska godkänna begäran. Som standard ställer Privileged Identity Management god kännaren till alla användare som är privilegierade roll administratörer oavsett om de är permanenta eller berättigade.<br/><br/>**Obs:** Om en användare både är berättigad till en Azure AD-roll och en god kännare av rollen, kan de inte godkänna sig själva.<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att du väljer god kännare som användare som är mest kunniga om rollen och dess frekventa användare i stället för en global administratör. |
| Varaktighet för aktivering | Hur lång tid en användare aktive ras i rollen innan den upphör att gälla. |
| Permanent administratör | Lista över användare som kommer att vara permanent administratör för rollen (måste aldrig aktivera).<br/><br/> : heavy_check_mark: **Microsoft rekommenderar** att du har ingen ständig administratör för alla roller förutom globala administratörer. Läs mer om dem i som ska vara berättigade och som bör vara aktiva permanent i den här planen. |
| Aktiv administratör | För Azure-resurser är Active Administrator en lista över användare som aldrig kommer att behöva aktivera för att använda rollen. Den här listan kallas inte permanent administratör som i Azure AD-roller eftersom du kan ange en förfallo tid för när användaren ska förlora den här rollen. |
| Aktiv utgång | Aktiva roll tilldelningar för Azure-roller upphör att gälla efter den konfigurerade varaktigheten. Du kan välja mellan 15 dagar, 1 månad, 3 månad, 6 månad, 1 år eller permanent aktiv. |
| Giltig förfallo datum | Kvalificerade roll tilldelningar för Azure roles upphör att gälla efter denna varaktighet. Du kan välja mellan 15 dagar, 1 månad, 3 månad, 6 månad, 1 år eller permanent berättigad. |

## <a name="implementation-plan"></a>Implementerings plan

Grunden för lämplig planering är grunden för att du ska kunna distribuera ett program med Azure Active Directory.  Den ger intelligent säkerhet och integrering som fören klar onboarding samtidigt som du minskar tiden för lyckade distributioner.  Den här kombinationen säkerställer att ditt program är integrerat med lätthet samtidigt som du minimerar tiden för dina slutanvändare.

### <a name="identify-test-users"></a>Identifiera test användare

Använd det här avsnittet för att identifiera en uppsättning användare och eller grupper av användare för att verifiera implementeringen. Baserat på de inställningar som du valde i avsnittet planering, identifierar du de användare som du vill testa för varje roll.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du gör tjänst ägarna till varje Azure AD-roll som test användare så att de kan bekanta sig med processen och bli en intern rådgivare för distributionen.

I den här tabellen identifierar du de test användare som ska kontrol lera att inställningarna för rollerna fungerar.

| Rollnamn | Testa användare |
| --- | --- |
| &lt;Rollnamn&gt; | &lt;Användare kan testa rollen&gt; |
| &lt;Rollnamn&gt; | &lt;Användare kan testa rollen&gt; |

### <a name="test-implementation"></a>Testa implementering

Nu när du har identifierat test användare kan du använda det här steget för att konfigurera Privileged Identity Management för dina test användare. Om din organisation vill införliva Privileged Identity Management-arbetsflöde i ditt egna interna program i stället för att använda Privileged Identity Management i Azure Portal, stöds även alla åtgärder i Privileged Identity Management via vårt [Graph API](/graph/api/resources/privilegedidentitymanagement-root).

#### <a name="configure-privileged-identity-management-for-azure-ad-roles"></a>Konfigurera Privileged Identity Management för Azure AD-roller

1. [Konfigurera roll inställningarna för Azure AD](pim-how-to-change-default-settings.md) baserat på vad du har planerat.

1. Gå till **Azure AD-roller**, Välj **roller** och välj sedan den roll som du konfigurerade.

1. För gruppen av test användare, om de redan är permanent administratör, kan du göra dem tillgängliga genom att söka efter dem och konvertera dem från permanenta till kvalificerade genom att välja de tre punkterna på raden. Om de inte har roll tilldelningarna ännu kan du [göra en ny berättigad tilldelning](pim-how-to-add-role-to-user.md#make-a-user-eligible-for-a-role).

1. Upprepa steg 1-3 för alla roller som du vill testa.

1. När du har konfigurerat test användarna bör du skicka länken för hur du [aktiverar deras Azure AD-roll](pim-how-to-activate-role.md).

#### <a name="configure-privileged-identity-management-for-azure-roles"></a>Konfigurera Privileged Identity Management för Azure-roller

1. [Konfigurera Azures resurs roll inställningar](pim-resource-roles-configure-role-settings.md) för en roll i en prenumeration eller resurs som du vill testa.

1. Gå till **Azure-resurser** för den prenumerationen och välj **roller** och välj den roll som du har konfigurerat.

1. För gruppen av test användare, om de redan är en aktiv administratör, kan du göra dem tillgängliga genom att söka efter dem och [Uppdatera roll tilldelningen](pim-resource-roles-assign-roles.md#update-or-remove-an-existing-role-assignment). Om de inte har rollen än kan du [tilldela en ny roll](pim-resource-roles-assign-roles.md#assign-a-role).

1. Upprepa steg 1-3 för alla roller som du vill testa.

1. När du har konfigurerat test användarna bör du skicka länken för hur du [aktiverar Azures resurs roll](pim-resource-roles-activate-your-roles.md).

Du bör använda det här steget för att kontrol lera om all konfiguration som du har konfigurerat för rollerna fungerar som den ska. Använd följande tabell för att dokumentera dina tester. Du bör också använda det här steget för att optimera kommunikationen med berörda användare.

| Roll | Förväntat beteende under aktiveringen | Faktiska resultat |
| --- | --- | --- |
| Global administratör | (1) Kräv MFA<br/>(2) Kräv godkännande<br/>(3) god kännare får meddelande och kan godkänna<br/>(4) rollen upphör att gälla efter den förinställda tiden |  |
| Prenumerationens ägare *X* | (1) Kräv MFA<br/>(2) den kvalificerade tilldelningen upphör att gälla efter den konfigurerade tids perioden |  |

### <a name="communicate-privileged-identity-management-to-affected-stakeholders"></a>Kommunicera Privileged Identity Management till berörda intressenter

Distribution av Privileged Identity Management kommer att införa ytterligare steg för användare av privilegierade roller. Även om Privileged Identity Management avsevärt minskar säkerhets problem som är kopplade till privilegierade identiteter, måste ändringen kommuniceras effektivt före distributionen av hela organisationen. Beroende på antalet påverkade administratörer väljer organisationer ofta att skapa ett internt dokument, en video eller ett e-postmeddelande om ändringen. Här ingår ofta bland dessa kommunikationer:

- Vad är PIM
- Vad är fördelarna med organisationen?
- Som kommer att påverkas
- När kommer PIM att distribueras
- Vilka ytterligare steg krävs för att användarna ska kunna aktivera sin roll
    - Du bör skicka länkar till vår dokumentation:
    - [Aktivera Azure AD-roller](pim-how-to-activate-role.md)
    - [Aktivera Azure-roller](pim-resource-roles-activate-your-roles.md)
- Kontakt information eller supportavdelningen för eventuella problem som är associerade med PIM

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du ställer in tid hos supportavdelningen/support-teamet för att gå igenom Privileged Identity Management arbets flödet (om din organisation har ett internt IT-support team). Förse dem med lämpliga dokument och din kontakt information.

### <a name="move-to-production"></a>Flytta till produktion

När testningen är slutförd och genomförd flyttar du Privileged Identity Management till produktion genom att upprepa alla steg i test faserna för alla användare av varje roll som du har definierat i Privileged Identity Management konfigurationen. För Privileged Identity Management för Azure AD-roller, testar organisationer ofta Privileged Identity Management för globala administratörer innan de testar och distribuerar Privileged Identity Management för andra roller. Under tiden för Azure-resurser, testar organisationer vanligt vis och distribuerar Privileged Identity Management en Azure-prenumeration i taget.

### <a name="if-a-rollback-is-needed"></a>Om en återställning behövs

Om Privileged Identity Management inte fungerade som det behövs i produktions miljön, kan följande återställnings steg hjälpa dig att återgå till ett känt fungerande tillstånd innan du konfigurerar Privileged Identity Management:

#### <a name="azure-ad-roles"></a>Azure AD-roller

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Öppna **Azure AD Privileged Identity Management**.
1. Välj **Azure AD-roller** och välj sedan **roller**.
1. För varje roll som du har konfigurerat väljer du ellipsen (**...**) för alla användare med en berättigad tilldelning.
1. Välj alternativet **gör permanent** för att göra roll tilldelningen permanent.

#### <a name="azure-roles"></a>Azure-roller

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Öppna **Azure AD Privileged Identity Management**.
1. Välj **Azure-resurser** och välj sedan en prenumeration eller resurs som du vill återställa.
1. Välj **roller**.
1. För varje roll som du har konfigurerat väljer du ellipsen (**...**) för alla användare med en berättigad tilldelning.
1. Välj alternativet **gör permanent** för att göra roll tilldelningen permanent.

## <a name="next-steps-after-deploying"></a>Nästa steg efter distribution

Att distribuera Privileged Identity Management i produktion är ett stort steg framåt när det gäller att skydda din organisations privilegierade identiteter. Med distributionen av Privileged Identity Management finns ytterligare Privileged Identity Management funktioner som du bör använda för säkerhet och efterlevnad.

### <a name="use-privileged-identity-management-alerts-to-safeguard-your-privileged-access"></a>Använd Privileged Identity Management aviseringar för att skydda privilegie rad åtkomst

Mer information om hur du använder Privileged Identity Management inbyggda aviserings funktioner för att skydda din organisation finns i [säkerhets aviseringar](pim-how-to-configure-security-alerts.md#security-alerts). Dessa aviseringar är: administratörer som inte använder privilegierade roller, roller som tilldelas utanför Privileged Identity Management, roller aktive ras för ofta och mer. För att skydda din organisation fullständigt bör du regelbundet gå igenom listan över aviseringar och åtgärda problemen. Du kan visa och åtgärda aviseringar på följande sätt:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Öppna **Azure AD Privileged Identity Management**.
1. Välj **Azure AD-roller** och välj sedan **aviseringar**.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du hanterar alla aviseringar som är markerade med hög allvarlighets grad omedelbart. För aviseringar med medelhög och låg allvarlighets grad bör du hålla dig informerad och göra ändringar om du anser att det är ett säkerhetshot.

Om någon av de enskilda aviseringarna inte är användbara eller inte gäller för din organisation, kan du alltid stänga aviseringen på sidan aviseringar. Du kan alltid återställa den här avsändningen senare på sidan för Azure AD-inställningar.

### <a name="set-up-recurring-access-reviews-to-regularly-audit-your-organizations-privileged-identities"></a>Konfigurera återkommande åtkomst granskningar för att regelbundet granska din organisations privilegierade identiteter

Åtkomst granskningar är det bästa sättet att be användare som har tilldelats privilegierade roller eller vissa granskare oavsett om varje användare behöver den privilegierade identiteten. Åtkomst granskningar är bra om du vill minska angrepps ytan och vara kompatibel. Mer information om hur du startar en åtkomst granskning finns i åtkomst granskningar för [Azure AD-roller](pim-how-to-start-security-review.md) och [åtkomst granskningar för Azure-roller](pim-resource-roles-start-access-review.md). För vissa organisationer krävs regelbunden åtkomst granskning för att vara kompatibel med lagar och föreskrifter, medan för andra, åtkomst granskning är det bästa sättet att genomdriva huvud kontot för minst behörighet i hela organisationen.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du konfigurerar kvartals Visa åtkomst granskningar för alla dina Azure AD-och Azure-roller.

I de flesta fall är granskaren för Azure AD-roller användarna själva medan granskaren för Azure-roller är Prenumerationens ägare, som rollen finns i. Det är dock ofta fallet om företag har privilegierade konton som inte är länkade till någon speciell persons e-postadress. I dessa fall får ingen läsning och granskning av åtkomsten.

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du lägger till en sekundär e-postadress för alla konton med privilegierade roll tilldelningar som inte är länkade till en regelbundet kontrollerad e-postadress

### <a name="get-the-most-out-of-your-audit-log-to-improve-security-and-compliance"></a>Få ut mesta möjliga av gransknings loggen för att förbättra säkerheten och efterlevnaden

Gransknings loggen är den plats där du kan hålla dig uppdaterad och vara kompatibel med reglerna. Privileged Identity Management lagrar för närvarande en 30-dagars historik över alla organisationens historik i sin Gransknings logg, inklusive:

- Aktivering/inaktive ring av berättigade roller
- Roll tilldelnings aktiviteter i och utanför Privileged Identity Management
- Ändringar i roll inställningar
- Begär/Godkänn/neka aktiviteter för roll aktivering med godkännande inställningar
- Uppdatera till aviseringar

Du kan komma åt gransknings loggarna om du är global administratör eller administratör för privilegierade roller. Mer information finns i [gransknings historik för Azure AD-roller](pim-how-to-use-audit-log.md) och [gransknings historik för Azure-roller](azure-pim-resource-rbac.md).

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du har minst en administratör läst igenom alla gransknings händelser varje vecka och exportera gransknings händelserna varje månad.

Om du automatiskt vill lagra gransknings händelser under en längre tids period, synkroniseras Privileged Identity Managementens Gransknings logg automatiskt i [Azure AD audit-loggarna](../reports-monitoring/concept-audit-logs.md).

> [!TIP]
> : heavy_check_mark: **Microsoft rekommenderar** att du konfigurerar [Azure logg övervakning](../reports-monitoring/concept-activity-logs-azure-monitor.md) för att arkivera gransknings händelser i ett Azure Storage-konto för att öka säkerheten och efterlevnaden.
