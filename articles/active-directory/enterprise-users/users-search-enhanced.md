---
title: Förbättringar av användar hantering (för hands version) – Azure Active Directory | Microsoft Docs
description: Beskriver hur Azure Active Directory aktiverar användar sökning, filtrering och mer information om dina användare.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5038bde01a6b183a25a47f3b4e206c1ce80e6b6d
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127846"
---
# <a name="user-management-enhancements-preview-in-azure-active-directory"></a>Förbättringar av användar hantering (för hands version) i Azure Active Directory

Den här artikeln beskriver hur du använder för hands versionen av användar hanterings förbättringar i Azure Active Directory (Azure AD)-portalen. Sidorna **alla användare** och **borttagna användare** har uppdaterats för att ge mer information och göra det enklare att hitta användare. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Ändringarna i förhands granskningen är:

- Mer synliga användar egenskaper, inklusive objekt-ID, status för katalog synkronisering, skapande typ och identitets utfärdare
- Med Sök kan du nu söka efter en under sträng och kombinera sökning efter namn, e-post och objekt-ID: n
- Utökad filtrering efter användar typ (medlem, gäst, ingen), status för katalog, skapande typ, företags namn och domän namn
- Nya sorterings funktioner för egenskaper som namn och User Principal Name
- Ett nytt totalt antal användare som uppdaterar med sökningar eller filter

> [!NOTE]
> Den här för hands versionen är för närvarande inte tillgänglig för Azure AD B2C klienter.

## <a name="find-the-preview"></a>Hitta förhands granskningen

Förhands granskningen är aktive rad som standard, så att du kan använda den direkt. Du kan kolla de senaste funktionerna och förbättringarna genom att välja för **hands versions funktioner** på sidan **alla användare** . Alla sidor som har uppdaterats som en del av den här för hands versionen kommer att visa en för hands version. Om du har problem kan du växla tillbaka till den äldre upplevelsen:

1. Logga in på [Azure AD administrations Center](https://aad.portal.azure.com) och välj **användare**.
1. På sidan **användare – alla användare** väljer du banderollen högst upp på sidan.
1. I fönstret **förhands gransknings funktioner** aktiverar du **förbättrad användar hantering** .

   ![Hur och var du aktiverar och inaktiverar utökad användar hantering](./media/users-search-enhanced/enable-preview.png)

Vi uppskattar din feedback så att vi kan förbättra vår upplevelse.

## <a name="more-user-properties"></a>Fler användar egenskaper

Vi har gjort några ändringar i de kolumner som är tillgängliga på sidorna **alla användare** och **borttagna användare** . Förutom befintliga kolumner som vi tillhandahåller för att hantera din lista med användare har vi lagt till några fler kolumner.

### <a name="all-users-page"></a>Sidan alla användare

Följande är de användar egenskaper som visas på sidan **alla användare** :

- Namn: användarens visnings namn.
- Användarens huvud namn: användarens User Principal Name (UPN).
- Användar typ: medlem, gäst, ingen.
- Skapande tid: datum och tid då användaren skapades.
- Befattning: användarens jobb titel.
- Avdelning: den avdelning som användaren arbetar i.
- Katalogen har synkroniserats: anger om användaren har synkroniserats från en lokal katalog.
- Identitets utfärdare: utfärdare av den identitet som används för att logga in på ett användar konto.
- Objekt-ID: användarens objekt-ID.
- Typ av skapande: anger hur användar kontot skapades.
- Företags namn: företags namnet som användaren är associerad med.
- Status för inbjudan: status för inbjudan till en gäst användare.
- Mail: e-postmeddelandet för användaren.
- Senaste inloggning: datumet då användaren senast loggade in. Den här egenskapen är endast synlig för användare med behörighet att läsa gransknings loggar (Reporting_ApplicationAuditLogs_Read)

![nya användar egenskaper som visas på sidorna alla användare och borttagna användare](./media/users-search-enhanced/user-properties.png)

### <a name="deleted-users-page"></a>Sidan borttagna användare

Sidan **borttagna användare** innehåller alla kolumner som är tillgängliga på sidan **alla användare** och några ytterligare kolumner, nämligen:

- Borttagnings datum: datumet då användaren först togs bort från organisationen (användaren är återställas).
- Permanent borttagnings datum: det datum då processen för att permanent ta bort användaren från organisationen börjar automatiskt att gälla.
- Ursprunglig User Principal Name: det ursprungliga UPN för användaren innan objekt-ID: t lades till som ett prefix till deras borttagna UPN.

> [!NOTE]
> Borttagnings datum visas i UTC-tid (Coordinated Universal Time).

Vissa kolumner visas som standard. Om du vill lägga till andra kolumner väljer du **kolumner** på sidan, markerar de kolumn namn som du vill lägga till och väljer **OK** för att spara dina inställningar.

### <a name="identity-issuers"></a>Identitets utfärdare

Välj en post i kolumnen **identitets utfärdare** för en användare om du vill visa mer information om utfärdaren, inklusive inloggnings typ och tilldelat ID för utfärdaren. Posterna i kolumnen **identitets utfärdare** kan vara flera värden. Om det finns flera utfärdare av användarens identitet, visas ordet Multiple i kolumnen **identitets utfärdare** på sidorna **alla användare** och **borttagna användare** , och i informations fönstret visas alla utfärdare.

> [!NOTE]
> **Käll** kolumnen ersätts av flera kolumner, inklusive **skapande typ**, **katalog som synkroniseras** och **identitets utfärdare** för mer detaljerad filtrering.

## <a name="user-list-search"></a>Sök efter användar lista

När du anger en Sök sträng använder Sök funktionen nu "börjar med" och under Strängs sökning för att matcha namn, e-post eller objekt-ID: n i en enskild sökning. Du kan ange något av dessa attribut i sökrutan och sökningen ser automatiskt ut i alla dessa egenskaper för att returnera matchande resultat. Under Strängs sökningen utförs endast på hela ord. Du kan utföra samma sökning på sidorna **alla användare** och **borttagna användare** .

## <a name="user-list-filtering"></a>Filtrering av användar lista

Filtrerings funktionerna har förbättrats för att ge fler filtrerings alternativ för sidorna **alla användare** och **borttagna användare** . Du kan nu filtrera efter flera egenskaper samtidigt och kan filtrera efter fler egenskaper.

### <a name="filtering-all-users-list"></a>Filtrera listan alla användare

Följande är de filter bara egenskaperna på sidan **alla användare** :

- Användar typ: medlem, gäst, ingen
- Katalogens synkroniserings status: Ja, nej
- Typ av skapande: inbjudan, e-postverifierat, lokalt konto
- Skapelse tid: senaste 7, 14, 30, 90, 360 eller >360 dagar sedan
- Befattning: Ange en befattning
- Avdelning: Ange ett avdelnings namn
- Grupp: Sök efter en grupp
- Status för inbjudan – väntar på godkännande, accepterad
- Domän namn: Ange ett domän namn
- Företags namn: Ange ett företags namn
- Administrativ enhet: Välj det här alternativet om du vill begränsa omfånget för de användare som du visar till en enda administrativ enhet. Mer information finns i för [hands version av administrativ enhets hantering](../roles/administrative-units.md).

### <a name="filtering-deleted-users-list"></a>Filtrera listan med borttagna användare

Sidan för **borttagna användare** har ytterligare filter som inte finns på sidan **alla användare** . Följande är de filter bara egenskaperna på sidan **borttagna användare** :

- Användar typ: medlem, gäst, ingen
- Katalogens synkroniserings status: Ja, nej
- Typ av skapande: inbjudan, e-postverifierat, lokalt konto
- Skapelse tid: senaste 7, 14, 30, 90, 360 eller > 360 dagar sedan
- Befattning: Ange en befattning
- Avdelning: Ange ett avdelnings namn
- Status för inbjudan: väntar på godkännande, accepterat
- Borttagnings datum: senaste 7, 14 eller 30 dagar
- Domän namn: Ange ett domän namn
- Företags namn: Ange ett företags namn
- Permanent borttagnings datum: senaste 7, 14 eller 30 dagar

## <a name="user-list-sorting"></a>Sortering av användar lista

Du kan nu sortera efter namn och User Principal Name på sidorna **alla användare** och **borttagna användare** . Du kan också sortera efter borttagnings datum i listan med **borttagna användare** .

## <a name="user-list-counts"></a>Antal användar listor

Du kan visa det totala antalet användare på sidorna **alla användare** och **borttagna användare** . När du söker eller filtrerar listorna uppdateras antalet för att återspegla det totala antalet användare som hittats.

![Illustration av antal användar listor på sidan alla användare](./media/users-search-enhanced/user-list-sorting.png)

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar

Fråga | Svar
-------- | ------
Varför visas den borttagna användaren fortfarande när den permanenta borttagnings datumet har passerats? | Det permanenta borttagnings datumet visas i UTC-tidszonen, så det kanske inte matchar den aktuella tids zonen. Detta datum är också det tidigaste datumet då användaren kommer att tas bort permanent från organisationen, så det kan fortfarande bearbetas. Permanent borttagna användare tas bort automatiskt från listan.
Vad händer med Mass funktionerna för användare och gäster? | Mass åtgärderna är fortfarande tillgängliga för användare och gäster, inklusive Mass skapande, Mass inbjudan, Mass borttagning och nedladdning av användare. Vi har precis slagit samman dem i en meny som kallas **Mass åtgärder**. Du hittar alternativen för **Mass åtgärder** överst på sidan **alla användare** .
Vad hände med käll kolumnen? | **Käll** kolumnen har ersatts med andra kolumner som innehåller liknande information, samtidigt som du kan filtrera utifrån dessa värden oberoende av varandra. Exempel på detta är **skapande typ**, **katalog som synkroniseras** och **identitets utfärdare**.
Vad hände med kolumnen användar namn? | Kolumnen **användar namn** finns fortfarande där, men den har bytt namn till **användarens huvud namn**. Detta visar den information som finns i kolumnen. Du ser också att det fullständiga huvud namnet för användare visas nu för B2B-gäster. Detta matchar det du fick i MS Graph.  

## <a name="next-steps"></a>Nästa steg

Användar åtgärder

- [Lägga till eller ändra profil information](../fundamentals/active-directory-users-profile-azure-portal.md)
- [Lägga till eller ta bort användare](../fundamentals/add-users-azure-active-directory.md)

Mass åtgärder

- [Hämta lista över användare](users-bulk-download.md)
- [Masstillägg av användare](users-bulk-add.md)
- [Massborttag användare](users-bulk-delete.md)
- [Massåterställ användare](users-bulk-restore.md)
