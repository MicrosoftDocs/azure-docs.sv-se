---
title: ta med fil
description: ta med fil
services: active-directory
author: msaburnley
ms.service: active-directory
ms.topic: include
ms.date: 03/30/2020
ms.author: ajburnle
ms.custom: include file
ms.openlocfilehash: 61858a478ead93b3709bb3e05c26335c9acbc24d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98697901"
---
## <a name="for-users-in-your-directory"></a>För användare i din katalog

Följ dessa steg om du vill tillåta att användare i katalogen kan begära det här åtkomst paketet. När du definierar en princip för begäran kan du ange enskilda användare eller flera vanliga grupper av användare. Din organisation kanske redan har en grupp, till exempel **alla anställda**.  Om gruppen läggs till i principen för användare som kan begära åtkomst, kan alla medlemmar i gruppen sedan begära åtkomst.

1. I avsnittet **användare som kan begära åtkomst** klickar du på **för användare i din katalog**.

    När du väljer det här alternativet visas nya alternativ för att ytterligare finjustera vem i din katalog som kan begära det här åtkomst paketet.

    ![Åtkomst till paket-begär Anden – för användare i din katalog](./media/active-directory-entitlement-management-request-policy/for-users-in-your-directory.png)

1. Välj något av följande alternativ:

    |  |  |
    | --- | --- |
    | **Specifika användare och grupper** | Välj det här alternativet om du vill att endast de användare och grupper i din katalog som du anger ska kunna begära det här åtkomst paketet. |
    | **Alla medlemmar (exklusive gäster)** | Välj det här alternativet om du vill att alla medlems användare i din katalog ska kunna begära det här åtkomst paketet. Det här alternativet omfattar inte gäst användare som du kanske har bjudit in till din katalog. |
    | **Alla användare (inklusive gäster)** | Välj det här alternativet om du vill att alla medlems användare och gäst användare i din katalog ska kunna begära det här åtkomst paketet. |

    Gäst användare refererar till externa användare som har bjudits in till din katalog med [Azure AD B2B](../articles/active-directory/external-identities/what-is-b2b.md). Mer information om skillnaderna mellan medlems användare och gäst användare finns i [Vad är standard användar behörigheter i Azure Active Directory?](../articles/active-directory/fundamentals/users-default-permissions.md).

1. Om du har valt **vissa användare och grupper** klickar du på **Lägg till användare och grupper**.

1. I fönstret Välj användare och grupper väljer du de användare och grupper som du vill lägga till.

    ![Åtkomst till paket-begär Anden – Välj användare och grupper](./media/active-directory-entitlement-management-request-policy/select-users-groups.png)

1. Klicka på **Välj** för att lägga till användare och grupper.

1. Hoppa ned till [godkännande](#approval) avsnittet.

## <a name="for-users-not-in-your-directory"></a>För användare som inte är i din katalog

 **Användare som inte finns i din katalog** refererar till användare som finns i en annan Azure AD-katalog eller domän. Dessa användare har kanske inte bjudits in till din katalog än. Azure AD-kataloger måste konfigureras för att tillåta inbjudningar i **samarbets begränsningar**. Mer information finns i [Aktivera externt samarbete i B2B och hantera vem som kan bjuda in gäster](../articles/active-directory/external-identities/delegate-invitations.md).

> [!NOTE]
> Ett gäst användar konto skapas för en användare som ännu inte finns i katalogen vars förfrågan godkänns eller godkänns automatiskt. Gästen kommer att bjudas in, men får ingen inbjudan via e-post. I stället får de ett e-postmeddelande när deras tilldelning av åtkomst paket levereras. Senare när gäst användaren inte längre har några åtkomst paket tilldelningar, eftersom deras senaste tilldelning har gått ut eller avbrutits, kommer gäst användar kontot att blockeras från att logga in och sedan tas bort. Om du vill att gäst användare ska finnas kvar i din katalog på obestämd tid, även om de inte har några åtkomst paket, kan du ändra inställningarna för konfiguration av rättighets hantering. Mer information om gäst användar objekt finns i [Egenskaper för en Azure Active Directory B2B-samarbets användare](../articles/active-directory/external-identities/user-properties.md).

Följ de här stegen om du vill tillåta att användare som inte tillhör din katalog begär det här åtkomst paketet:

1. I avsnittet **användare som kan begära åtkomst** klickar du på **för användare som inte finns i din katalog**.

    När du väljer det här alternativet visas nya alternativ.

    ![Åtkomst till paket-begär Anden – för användare som inte är i din katalog](./media/active-directory-entitlement-management-request-policy/for-users-not-in-your-directory.png)

1. Välj något av följande alternativ:

    |  |  |
    | --- | --- |
    | **Vissa anslutna organisationer** | Välj det här alternativet om du vill välja från en lista över organisationer som administratören har lagt till tidigare. Alla användare från de valda organisationerna kan begära det här åtkomst paketet. |
    | **Alla anslutna organisationer** | Välj det här alternativet om alla användare från alla anslutna organisationer kan begära det här åtkomst paketet. |
    | **Alla användare (alla anslutna organisationer och alla nya externa användare)** | Välj det här alternativet om alla användare från alla anslutna organisationer kan begära det här åtkomst paketet och att List inställningarna för B2B-Tillåt eller neka ska ha företräde för alla nya externa användare. |

    En ansluten organisation är en extern Azure AD-katalog eller domän som du har en relation med.

1. Om du har valt **vissa anslutna organisationer** klickar du på **Lägg till kataloger** för att välja från en lista över anslutna organisationer som administratören har lagt till tidigare.

1. Skriv namnet eller domän namnet för att söka efter en tidigare ansluten organisation.

    ![Åtkomst till paket-begär Anden – Välj kataloger](./media/active-directory-entitlement-management-request-policy/select-directories.png)

    Om den organisation som du vill samar beta med inte finns med i listan kan du be administratören att lägga till den som en ansluten organisation. Mer information finns i [lägga till en ansluten organisation](../articles/active-directory/governance/entitlement-management-organization.md).

1. När du har valt alla anslutna organisationer klickar du på **Välj**.

    > [!NOTE]
    > Alla användare från de valda anslutna organisationerna kommer att kunna begära det här åtkomst paketet. Detta inkluderar användare i Azure AD från alla under domäner som är kopplade till organisationen, om inte dessa domäner blockeras av listan Tillåt eller neka i Azure B2B. Mer information finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](../articles/active-directory/external-identities/allow-deny-list.md).

1. Hoppa ned till [godkännande](#approval) avsnittet.

## <a name="none-administrator-direct-assignments-only"></a>Ingen (endast administratörs direkta tilldelningar)

Följ de här stegen om du vill kringgå åtkomst begär Anden och låta administratörer direkt tilldela vissa användare till det här Access-paketet. Användarna behöver inte begära åtkomst paketet. Du kan fortfarande ange livs cykel inställningar, men det finns inga inställningar för begäran.

1. I avsnittet **användare som kan begära åtkomst** klickar du på **Ingen (endast administratörs direkta tilldelningar**).

    ![Åtkomst till paket-begär Anden-inga enbart administratörs direkta tilldelningar](./media/active-directory-entitlement-management-request-policy/none-admin-direct-assignments-only.png)

    När du har skapat åtkomst paketet kan du direkt tilldela vissa interna och externa användare till Access-paketet. Om du anger en extern användare så skapas ett gäst användar konto i din katalog. Information om hur du tilldelar en användare direkt, se [Visa, lägga till och ta bort tilldelningar för ett Access-paket](../articles/active-directory/governance/entitlement-management-access-package-assignments.md).

1. Hoppa nedåt till avsnittet [Aktivera begär Anden](#enable-requests) .

## <a name="approval"></a>Godkännande

I avsnittet godkännande anger du om ett godkännande krävs när användare begär det här åtkomst paketet. Godkännande inställningarna fungerar på följande sätt:

- Endast en av de valda god kännarna eller reserv god kännaren måste godkänna en begäran om godkännande av en enskild fas. 
- Endast en av de valda god kännarna från varje steg måste godkänna en begäran om godkännande med två steg.
- God kännaren kan vara ansvarig, intern sponsor eller extern sponsor beroende på vilken princip som styr åtkomsten.
- Godkännande från varje vald god kännare krävs inte för ett eller 2-stegs godkännande.
- Godkännande beslutet baseras på vilken god kännare som granskar begäran först.

En demonstration av hur du lägger till god kännare i en princip för begäran finns i följande videoklipp:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

En demonstration av hur du lägger till ett godkännande med flera steg i en princip för begäran finns i följande videoklipp:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]

Följ de här stegen för att ange godkännande inställningarna för begär Anden för Access-paketet:

1. Om du vill kräva godkännande för förfrågningar från de valda användarna anger du alternativet **Kräv godkännande** för att växla till **Ja**. Eller, om du vill att begär Anden ska godkännas automatiskt, anger du växla till **Nej**.

1. Om du vill kräva att användarna anger en motivering för att begära åtkomst paketet, ställer du in **justeringen Kräv ändring av begär ande** för att växla till **Ja**.
    
1. Kontrol lera nu om begär Anden kräver ett enda eller 2-stegs godkännande. Ange **hur många steg** som ska växlas till **1** för godkännande av enstaka steg eller ange växla till **2** för godkännande i två steg.

    ![Åtkomst till paket-begär Anden – godkännande inställningar](./media/active-directory-entitlement-management-request-policy/approval.png)

Använd följande steg för att lägga till god kännare när du har valt hur många steg du behöver: 

### <a name="single-stage-approval"></a>Godkännande med ett enda steg

1. Lägg till den **första god kännaren**:
    
    Om principen är inställd för att styra åtkomsten för användare i din katalog, kan du välja **chef som god kännare**. Eller Lägg till en speciell användare genom att klicka på **Lägg till god kännare** när du har valt Välj vissa god kännare i list menyn.
    
    ![Åtkomst till paket-begär Anden – för användare i katalog-första god kännare](./media/active-directory-entitlement-management-request-policy/approval-single-stage-first-approver-manager.png)

    Om den här principen är inställd för att styra åtkomsten för användare som inte finns i din katalog, kan du välja **extern sponsor** eller **intern sponsor**. Eller Lägg till en speciell användare genom att klicka på **Lägg till god kännare** eller grupper under Välj vissa god kännare.
    
    ![Åtkomst till paket-begär Anden – för användare som inte är i katalog-första god kännare](./media/active-directory-entitlement-management-request-policy/out-directory-first-approver.png)
    
1. Om du har valt **hanterare** som den första god kännaren klickar du på **Lägg till reserv** för att välja en eller flera användare eller grupper i din katalog som en reserv god kännare. Reserv god kännare får begäran om hantering av rättigheter inte kan hitta chefen för användaren som begär åtkomst.

    Hanteraren hittas genom rättighets hantering med attributet **Manager** . Attributet finns i användarens profil i Azure AD. Mer information finns i [lägga till eller uppdatera en användares profil information med hjälp av Azure Active Directory](../articles/active-directory/fundamentals/active-directory-users-profile-azure-portal.md).

1. Om du har valt **Välj en viss god kännare** klickar du på **Lägg till god kännare** för att välja en eller flera användare eller grupper i din katalog som ska vara god kännare.

1. I rutan under **beslut måste fattas i hur många dagar?** anger du antalet dagar som en god kännare måste granska en begäran om det här åtkomst paketet.

    Om en begäran inte godkänns inom den här tids perioden kommer den automatiskt att nekas. Användaren måste skicka en annan begäran om åtkomst paketet.

1. Om du vill att god kännare ska kunna ge en motivering för sitt beslut anger du **Ja** som krav på god kännare.

    Motiveringen är synlig för andra god kännare och begär Ande.

### <a name="2-stage-approval"></a>2 – steg godkännande

Om du valde ett 2-stegs godkännande måste du lägga till en andra god kännare.

1. Lägg till den **andra god kännaren**: 
    
    Om användarna finns i din katalog lägger du till en speciell användare som den andra god kännaren genom att klicka på **Lägg till god kännare** under Välj vissa god kännare.

    ![Åtkomst till paket-begär Anden – för användare i katalog-andra god kännare](./media/active-directory-entitlement-management-request-policy/in-directory-second-approver.png)

    Om användarna inte finns i din katalog väljer du **intern sponsor** eller **extern sponsor** som den andra god kännaren. När du har valt god kännaren lägger du till reserv god kännarna.

    ![Åtkomst till paket-begär Anden – för användare utanför katalog-andra god kännare](./media/active-directory-entitlement-management-request-policy/out-directory-second-approver.png) 

1. Ange antalet dagar som den andra god kännaren måste godkänna begäran i rutan under **beslut måste fattas i hur många dagar?**. 

1. Ange justeringen Kräv god kännare för att växla till **Ja** eller **Nej**.

### <a name="alternate-approvers"></a>Alternativa god kännare

Du kan ange alternativa god kännare, på samma sätt som du anger de första och andra god kännarna som kan godkänna begär Anden. Om du har alternativa god kännare kan du se till att förfrågningarna godkänns eller nekas innan de upphör att gälla (tids gräns). Du kan visa en lista över alternativa god kännare för den första god kännaren och andra god kännaren för godkännande i två steg. 

Genom att ange alternativa god kännare, i händelse av att de första eller andra god kännarna inte kunde godkänna eller neka begäran, vidarebefordras den väntande begäran till de alternativa god kännarna, enligt det vidarebefordrings schema du angav under princip konfigurationen. De får ett e-postmeddelande för att godkänna eller neka den väntande begäran.

När begäran har vidarebefordrats till alternativa god kännare kan de första eller andra god kännarna fortfarande godkänna eller neka begäran. Alternativa god kännare använder samma åtkomst webbplats för att godkänna eller neka den väntande begäran.

Vi kan lista personer eller grupper med personer som ska vara god kännare och alternativa god kännare. Se till att du listar olika uppsättningar med personer som de första, andra och alternativa god kännarna.
Om du till exempel har listat Alice och Bob som första god kännare, anger du Carol och Dave som alternativa god kännare. Använd följande steg för att lägga till alternativa god kännare i ett Access-paket:

1. Under den första god kännaren, andra god kännaren eller båda klickar du på **Visa avancerade inställningar för begäran**.

    ![Åtkomst paket – princip – Visa avancerade inställningar för begäran](./media/active-directory-entitlement-management-request-policy/alternate-approvers-click-advanced-request.png)

1. Ange **om ingen åtgärd har vidtagits, vidarebefordra till alternativa god kännare?** växla till **Ja**.

1. Klicka på **Lägg till alternativa god kännare** och välj alternativa god kännare i listan.

    ![Åtkomst paket – princip – Lägg till alternativa god kännare](./media/active-directory-entitlement-management-request-policy/alternate-approvers-add.png)

    Om du väljer chef som god kännare för den första god kännaren har du ytterligare ett alternativ, **andra nivå ansvarig som alternativ god kännare**, tillgängliga för att välja i fältet alternativ god kännare. Om du väljer det här alternativet måste du lägga till en reserv god kännare för att vidarebefordra begäran till om systemet inte kan hitta den andra nivå hanteraren.

1. I rutan **vidarebefordra till alternativ god kännare efter hur många dagar** som god kännaren måste godkänna eller neka en begäran. Om inga god kännare har godkänt eller nekat begäran före varaktigheten för begäran upphör begäran att gälla (tids gräns) och användaren måste skicka en annan begäran om åtkomst paketet. 

    Förfrågningar kan bara vidarebefordras till alternativa god kännare per dag efter det att varaktigheten för begäran har nått halva livs längden och beslutet för huvud god kännaren (erna) måste ha uppnått timeout efter minst fyra dagar. Om tids gränsen för begäran är mindre än eller lika med 3, finns det inte tillräckligt med tid för att vidarebefordra begäran till alternativa god kännare. I det här exemplet är varaktigheten för begäran 14 dagar. Det innebär att varaktigheten för begäran når en halv livs längd på dag 7. Begäran kan därför inte vidarebefordras tidigare än dag 8. Begär Anden kan inte vidarebefordras den sista dagen i varaktigheten för begäran. I exemplet kan den senaste förfrågan vidarebefordras dag 13.

## <a name="enable-requests"></a>Aktivera begär Anden

1. Om du vill att Access-paketet ska göras omedelbart tillgängligt för användare i förfrågnings principen att begära, flytta aktivera växla till **Ja**.

    Du kan alltid aktivera det i framtiden när du är klar med att skapa åtkomst paketet.

    Om du har valt **Ingen (endast administratörers direkta tilldelningar)** och du anger Tillåt till **Nej** kan administratörer inte tilldela det här åtkomst paketet direkt.

    ![Skärm bild som visar alternativet för att aktivera nya begär Anden och tilldelningar.](./media/active-directory-entitlement-management-request-policy/enable-requests.png)

1. Klicka på **Nästa**.

## <a name="add-requestor-information-preview-to-an-access-package"></a>Lägg till information om beställare (för hands version) i ett Access-paket

1. Gå till fliken **information om beställare** och klicka på fliken **frågor** .
 
1. Skriv in vad du vill be beställaren, även kallat visnings strängen, för frågan i rutan **fråga** .

    ![Åtkomst paket – princip – aktivera inställning för begär ande information](./media/active-directory-entitlement-management-request-policy/add-requestor-info-question.png)

1. Om du vill lägga till dina egna lokaliserings alternativ klickar du på **Lägg till lokalisering**.
    1. I fönstret **Lägg till lokaliseringar för fråga** väljer du **språk koden** för det språk som du vill lokalisera frågan i.
    1. Skriv frågan i den **lokaliserade text** rutan på det språk som du har konfigurerat.
    1. När du har lagt till alla nödvändiga lokaliseringar klickar du på **Spara**.

    ![Åtkomst paket – princip – konfigurera lokaliserad text](./media/active-directory-entitlement-management-request-policy/add-localization-question.png)

1. Välj det **svars format** som du vill att beställare ska besvara. Svars formaten är: *kort text*, *flera alternativ* och *långt text*.
 
    ![Åtkomst paket-princip – Välj Visa och redigera flera alternativ svars format](./media/active-directory-entitlement-management-request-policy/answer-format-view-edit.png)
 
1. Om du väljer flera alternativ klickar du på knappen **Visa och redigera** för att konfigurera svars alternativen.
    1. När du har valt Visa och redigera fönstret **Visa/redigera fråga** öppnas.
    1. Ange de svars alternativ som du vill ge den begär Ande när du besvarar frågan i rutan **svars värden** .
    1. Skriv in så många svar som du behöver och klicka sedan på **Spara**.
    
    ![Åtkomst paket-princip – ange flera alternativ](./media/active-directory-entitlement-management-request-policy/answer-multiple-choice.png)
  
1. Om du vill kräva att beställare besvarar den här frågan när du begär åtkomst till ett Access-paket, klickar du på kryss rutan under **obligatorisk**.

1. Klicka på Nästa