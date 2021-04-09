---
title: Hantera administratörs konton för nöd åtkomst – Azure AD
description: Den här artikeln beskriver hur du använder konton för nöd situationer för att förhindra oavsiktligt låsning av din Azure Active Directory (Azure AD)-organisation.
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: rolyon
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6a1e4b3b44004ec6d03c293bbd10617b3d3af69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98740830"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Hantera konton för nödåtkomst i Azure AD

Det är viktigt att du förhindrar oavsiktligt låst av din Azure Active Directory (Azure AD)-organisation eftersom du inte kan logga in eller aktivera en annan användares konto som administratör. Du kan minska effekten av haveri brist på administrativ åtkomst genom att skapa två eller fler konton för *nöd åtkomst* i din organisation.

Konton för nöd åtkomst är mycket privilegierade och de är inte tilldelade till vissa individer. Konton för nödåtkomst är begränsade till nödsituationer där du inte kan använda normala administratörskonton. Vi rekommenderar att du upprätthåller ett mål för att begränsa användningen av nödfall till bara de tider då det är absolut nödvändigt.

Den här artikeln innehåller rikt linjer för att hantera konton för nöd åtkomst i Azure AD.

## <a name="why-use-an-emergency-access-account"></a>Varför ska jag använda ett konto för nöd åtkomst

En organisation kan behöva använda ett konto för nöd åtkomst i följande situationer:

- Användar kontona är federerade och federationen är för närvarande inte tillgänglig på grund av ett avbrott i ett cell nätverk eller ett strömavbrott i identiteten. Om till exempel värd för identitetsprovider i din miljö inte är igång kan användarna inte logga in när Azure AD omdirigeras till sin identitets leverantör.
- Administratörerna registreras via Azure AD Multi-Factor Authentication och alla deras enskilda enheter är inte tillgängliga eller så är tjänsten inte tillgänglig. Användare kanske inte kan slutföra Multi-Factor Authentication för att aktivera en roll. Till exempel förhindrar ett cell nätverks avbrott att de svarar på telefonsamtal eller tar emot SMS, de enda två autentiseringsmekanismer som de har registrerat för enheten.
- Den person som har den senaste globala administratörs åtkomsten har lämnat organisationen. Azure AD förhindrar att det senaste globala administratörs kontot tas bort, men det förhindrar inte att kontot tas bort eller inaktive ras lokalt. En situation kan göra att organisationen inte kan återställa kontot.
- Oförutsedda omständigheter, t. ex. katastrof katastrof, under vilken en mobil telefon eller andra nätverk kanske inte är tillgänglig. 

## <a name="create-emergency-access-accounts"></a>Skapa konton för nöd åtkomst

Skapa två eller fler konton för nöd åtkomst. Dessa konton ska vara molnbaserade konton som använder \* . onmicrosoft.com-domänen och som inte är federerade eller synkroniserade från en lokal miljö.

När du konfigurerar de här kontona måste följande krav uppfyllas:

- Kontona för nöd åtkomst ska inte vara kopplade till någon enskild användare i organisationen. Se till att dina konton inte är anslutna till några mobila mobil telefoner, maskinvaru-token som reser med enskilda anställda eller andra autentiseringsuppgifter som är specifika för anställda. Den här försiktighets tjänsten omfattar instanser där en enskild medarbetare inte kan kontaktas när autentiseringsuppgiften behövs. Det är viktigt att se till att alla registrerade enheter hålls på en känd, säker plats som har flera sätt att kommunicera med Azure AD.
- Den autentiseringsmekanism som används för ett konto för nöd åtkomst bör skilja sig från det som används av andra administrativa konton, inklusive andra konton för nöd åtkomst.  Om t. ex. din normala Administratörs inloggning är via lokal MFA, skulle Azure AD MFA vara en annan mekanism.  Men om Azure AD MFA är din primära del av autentiseringen för dina administrativa konton, kan du överväga att använda olika metoder för dessa, till exempel att använda villkorlig åtkomst med en tredjepartsleverantör-provider från tredje part via anpassade kontroller.
- Enheten eller autentiseringsuppgiften får inte förfalla eller vara i omfånget för automatisk rensning på grund av bristande användning.  
- Du bör göra roll tilldelningen global administratör permanent för dina konto för nöd åtkomst. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Undanta minst ett konto från telefon-baserad Multi-Factor Authentication

För att minska risken för angrepp på grund av ett komprometterat lösen ord rekommenderar Azure AD att du kräver Multi-Factor Authentication för alla enskilda användare. Den här gruppen omfattar administratörer och alla andra (till exempel finansiella tjänstemän) vars komprometterade konto skulle ha en betydande inverkan.

Minst ett av kontona för nöd åtkomst bör dock inte ha samma Multi-Factor Authentication-mekanism som andra icke-nödfalls konton. Detta inkluderar Multi-Factor Authentication-lösningar från tredje part. Om du har en princip för villkorlig åtkomst för att kräva [Multi-Factor Authentication för varje administratör](../authentication/howto-mfa-userstates.md) för Azure AD och andra anslutna SaaS-appar (program vara som en tjänst) bör du exkludera åtkomst konton för nöd situationer från det här kravet och konfigurera en annan mekanism i stället. Dessutom bör du se till att kontona inte har en princip för multifaktorautentisering med flera användare.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Undanta minst ett konto från principer för villkorlig åtkomst

Under en nöd situation vill du inte att en princip kan blockera åtkomsten för att åtgärda ett problem. Minst ett konto för nöd åtkomst ska uteslutas från alla principer för villkorlig åtkomst.

## <a name="federation-guidance"></a>Vägledning om Federation

Vissa organisationer använder AD Domain Services och ADFS eller liknande identitets leverantör för att federera till Azure AD. [Det får inte finnas några lokala konton med administratörs behörighet](../fundamentals/protect-m365-from-on-premises-attacks.md). Autentiserings-och käll autentisering för konton med administratörs behörighet utanför Azure AD lägger till onödig risk i händelse av avbrott eller komprometterande av dessa system.

## <a name="store-account-credentials-safely"></a>Lagra konto uppgifter på ett säkert sätt

Organisationer måste se till att autentiseringsuppgifterna för kontona för nöd åtkomst hålls skyddade och kända för personer som har behörighet att använda dem. Vissa kunder använder ett smartkort och andra använder lösen ord. Ett lösen ord för ett konto för nöd åtkomst är vanligt vis indelat i två eller tre delar, skrivet på separata pappersark och lagras i säkra, fireproof säkrare som är skyddade, separata platser.

Om du använder lösen ord kontrollerar du att kontona har starka lösen ord som inte förfaller lösen ordet. Det bästa är om lösen orden måste vara minst 16 tecken långa och slumpmässigt skapade.

## <a name="monitor-sign-in-and-audit-logs"></a>Övervaka inloggnings-och gransknings loggar

Organisationer bör övervaka inloggnings-och gransknings loggs aktiviteter från nödfalls kontona och utlösa meddelanden till andra administratörer. När du övervakar aktiviteten på Bryt glas konton kan du kontrol lera att dessa konton endast används för testning eller faktiska nöd situationer. Du kan använda Azure Log Analytics för att övervaka inloggnings loggarna och utlösa e-post och SMS-aviseringar till dina administratörer när Break glas-konton loggar in.

### <a name="prerequisites"></a>Förutsättningar

1. [Skicka inloggnings loggar för Azure AD](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) till Azure Monitor.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>Hämta objekt-ID: n för rast glas kontona

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som tilldelats rollen användar administratör.
1. Välj **Azure Active Directory**  >  **användare**.
1. Sök efter kontot för Bryt glas och välj användarens namn.
1. Kopiera och spara attribut för objekt-ID så att du kan använda det senare.
1. Upprepa föregående steg för andra rast glass konton.

### <a name="create-an-alert-rule"></a>Skapa en varningsregel

1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som tilldelats rollen övervaknings deltagare i Azure Monitor.
1. Välj **alla tjänster**", ange" Log Analytics "i sökningen och välj sedan **Log Analytics arbets ytor**.
1. Välj en arbetsyta.
1. I arbets ytan väljer du **aviseringar**  >  **ny aviserings regel**.
    1. Under **resurs** kontrollerar du att prenumerationen är den som du vill associera varnings regeln med.
    1. Välj **Lägg till** under **villkor**.
    1. Välj **anpassad loggs ökning** under **signal namn**.
    1. Under **Sök fråga** anger du följande fråga och infogar objekt-ID: n för de två rast glas kontona.
        > [!NOTE]
        > För varje ytterligare rast glas konto som du vill inkludera lägger du till ett annat "eller UserId = =" ObjectGuid "" i frågan.

        ![Lägg till objekt-ID: n för rast glas kontona i en varnings regel](./media/security-emergency-access/query-image1.png)

    1. Under **aviserings logik** anger du följande:

        - Baserat på: antal resultat
        - Operator: större än
        - Tröskel värde: 0

    1. Under **utvärdera baserat på** väljer du **perioden (i minuter)** för hur länge du vill att frågan ska köras och hur ofta **(i minuter)** som du vill att frågan ska köras. Frekvensen ska vara mindre än eller lika med perioden.

        ![aviserings logik](./media/security-emergency-access/alert-image2.png)

    1. Välj **Klar**. Nu kan du Visa den uppskattade månads kostnaden för den här aviseringen.
1. Välj en åtgärds grupp användare som ska meddelas via aviseringen. Om du vill skapa ett, se [skapa en åtgärds grupp](#create-an-action-group).
1. Om du vill anpassa e-postmeddelandet som skickas till medlemmarna i åtgärds gruppen väljer du åtgärder under **Anpassa åtgärder**.
1. Under **aviserings information** anger du namnet på varnings regeln och lägger till en valfri beskrivning.
1. Ange händelsens **allvarlighets grad** . Vi rekommenderar att du ställer in det på **kritiskt (allvarlighets grad 0)**.
1. Under **Aktivera regel vid skapande**, låt den vara **Ja**.
1. Om du vill inaktivera aviseringar för en stund markerar du kryss rutan **Ignorera aviseringar** och anger vänte tiden innan aviseringen görs igen och väljer sedan **Spara**.
1. Klicka på **Skapa aviseringsregel**.

### <a name="create-an-action-group"></a>Skapa en åtgärds grupp

1. Välj **skapa en åtgärds grupp**.

    ![skapa en åtgärds grupp för meddelande åtgärder](./media/security-emergency-access/action-group-image3.png)

1. Ange åtgärds gruppens namn och ett kort namn.
1. Verifiera prenumerationen och resurs gruppen.
1. Under åtgärds typ väljer du **e-post/SMS/push/röst**.
1. Ange ett åtgärds namn som **meddela global admin**.
1. Välj **Åtgärds typ** som **e-post/SMS/push/röst**.
1. Välj **Redigera information** för att välja de meddelande metoder som du vill konfigurera och ange nödvändig kontakt information och välj sedan **OK** för att spara informationen.
1. Lägg till eventuella ytterligare åtgärder som du vill utlösa.
1. Välj **OK**.

## <a name="validate-accounts-regularly"></a>Validera konton regelbundet

När du träna personal medlemmar att använda konton för nöd åtkomst och verifiera åtkomst kontona för nöd situationer, minst så här utför du följande steg med jämna mellanrum:

- Se till att personal för säkerhets övervakning är medveten om att konto kontroll aktiviteten pågår.
- Se till att processen för att använda dessa konton är dokumenterad och aktuell.
- Se till att administratörer och säkerhets ansvariga som kan behöva utföra de här stegen under en nöd situation tränas av processen.
- Uppdatera kontots autentiseringsuppgifter, särskilt eventuella lösen ord, för kontona för nöd åtkomst och kontrol lera sedan att kontona för nöd åtkomst kan logga in och utföra administrativa uppgifter.
- Se till att användarna inte har registrerat Multi-Factor Authentication eller lösen ords återställning via självbetjäning (SSPR) till någon enskild användares enhet eller personlig information. 
- Om kontona är registrerade för Multi-Factor Authentication till en enhet, för användning under inloggning eller roll aktivering, måste du se till att enheten är tillgänglig för alla administratörer som kan behöva använda den under en nöd situation. Kontrol lera också att enheten kan kommunicera via minst två nätverks Sök vägar som inte delar ett gemensamt felläge. Enheten kan till exempel kommunicera med Internet via både en anläggnings trådlösa nätverk och ett nätverk för en mobil leverantör.

De här stegen bör utföras med jämna mellanrum och för viktiga ändringar:

- Minst var 90 dag
- När det har gjorts en nyligen genomförd ändring av IT-personal, till exempel en jobb ändring, en avresa eller en ny anställning
- När Azure AD-prenumerationerna i organisationen har ändrats

## <a name="next-steps"></a>Nästa steg

- [Skydda privilegierad åtkomst för hybrid- och molndistributioner i Azure AD](security-planning.md)
- [Lägg till användare med hjälp av Azure AD](../fundamentals/add-users-azure-active-directory.md) och [tilldela den nya användaren rollen som global administratör](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Registrera dig för Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)om du inte redan har registrerat dig
- [Så här kräver du tvåstegsverifiering för en användare](../authentication/howto-mfa-userstates.md)
- [Konfigurera ytterligare skydd för globala administratörer i Microsoft 365](/office365/enterprise/protect-your-global-administrator-accounts)om du använder Microsoft 365
- [Starta en åtkomst granskning av globala administratörer](../privileged-identity-management/pim-how-to-start-security-review.md) och [över gång av befintliga globala administratörer till mer detaljerade administratörs roller](permissions-reference.md)