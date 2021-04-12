---
title: Administration i Azure EA-portalen
description: I den här artikeln förklaras några vanliga uppgifter som administratörer utför i Azure EA-portalen.
author: bandersmsft
ms.author: banders
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.custom: contperf-fy21q1
ms.openlocfilehash: 8f643336d50f5ade145454d408599215ed6fd31e
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259446"
---
# <a name="azure-ea-portal-administration"></a>Administration i Azure EA-portalen

I den här artikeln förklaras några vanliga uppgifter som administratörer utför i Azure EA-portalen (https://ea.azure.com). Azure EA-portalen är en onlinehanteringsportal där kunder kan hantera kostnaden för sina Azure EA-tjänster. Grundläggande information om Azure EA-portalen finns i artikeln [Kom igång med Azure EA-portalen](ea-portal-get-started.md).

## <a name="activate-your-enrollment"></a>Aktivera din registrering

För att aktivera din tjänst öppnar den första företagsadministratören [Azure Enterprise-portalen](https://ea.azure.com) och loggar in med e-postadressen från inbjudningsmeddelandet.

Om du har konfigurerats som företagsadministratör behöver du inte få aktiveringsmeddelandet. Gå till [Azure Enterprise-portalen](https://ea.azure.com) och logga in med e-postadressen och lösenordet för ditt arbets-, skol- eller Microsoft-konto.

Om du har fler än en registrering väljer du en som ska aktiveras. Som standard visas endast aktiva registreringar. Om du vill visa registreringshistoriken avmarkerar du alternativet **Aktiv** överst till höger i Azure Enterprise-portalen.

Under **Registrering** visar statusen **Aktiv**.

![Exempel som visar en aktiv registrering](./media/ea-portal-administration/ea-enrollment-status.png)

Endast befintliga Azure-företagsadministratörer kan skapa andra företagsadministratörer.

### <a name="create-another-enterprise-administrator"></a>Skapa en till företagsadministratör

Använd något av följande alternativ, baserat på din situation.

#### <a name="if-youre-already-an-enterprise-administrator"></a>Om du redan är företags administratör

1. Logga in på [Azure Enterprise-portalen](https://ea.azure.com).
1. Gå till **Hantera** > **Registreringsinformation**.
1. Välj **+ Lägg till administratör** längst upp till höger.

Kontrollera att du har användarens e-postadress och önskad autentiseringsmetod, till exempel arbets-, skol- eller Microsoft-konto.

#### <a name="if-youre-not-an-enterprise-administrator"></a>Om du inte är företags administratör

Om du inte är företags administratör kan du kontakta en företags administratör för att begära att de lägger till dig i registreringen. Företags administratören använder föregående steg för att lägga till dig som företags administratör. När du har blivit tillagd i en registrering får du ett aktiveringsmeddelande via e-post.

#### <a name="if-your-enterprise-administrator-cant-help-you"></a>Om din företags administratör inte kan hjälpa dig

Om din företagsadministratör inte kan hjälpa dig skapar du en [supportbegäran i Azure Enterprise-portalen](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Ange följande information:

- Registreringsnummer
- E-postadress som ska läggas till samt autentiseringstyp (arbets-, skol- eller Microsoft-konto)
- E-postgodkännande från en befintlig företagsadministratör
  - Om den befintliga företagsadministratören inte är tillgänglig kontaktar du din partner eller programvarurådgivare och begär att de ändrar kontaktuppgifterna via VLSC-verktyget (Volume Licensing Service Center).

## <a name="create-an-azure-enterprise-department"></a>Skapa en Azure Enterprise-avdelning

Företagsadministratörer och avdelningsadministratörer använder avdelningar för att organisera och rapportera om Azure-tjänster för företag samt användning per avdelning och kostnadsställe. Företagsadministratören kan:

- Lägga till och ta bort avdelningar.
- Associera ett konto med en avdelning.
- Skapa avdelningsadministratörer.
- Tillåta att avdelningsadministratörer visar pris och kostnader.

En avdelningsadministratör kan lägga till nya konton till sina avdelningar. De kan ta bort konton från sina avdelningar men inte från registreringen.

Så här lägger du till en avdelning:

1. Logga in på Azure Enterprise-portalen.
1. I det vänstra fönstret väljer du **Hantera**.
1. Välj fliken **Avdelning** och sedan **+ Lägg till avdelning**.
1. Ange informationen.
   Avdelningsnamnet är det enda obligatoriska fältet. Det måste innehålla minst tre tecken.
1. Välj **Lägg till** när det är klart.

## <a name="add-a-department-administrator"></a>Lägga till en avdelningsadministratör

När en avdelning har skapats kan företagsadministratören lägga till avdelningsadministratörer och associera var och en av dem med en avdelning. Avdelningsadministratörer kan utföra följande åtgärder för sina avdelningar:

- Skapa andra avdelningsadministratörer
- Visa och redigera avdelningsegenskaper såsom namn och kostnadsställe
- Lägga till konton
- Ta bort konton
- Ladda ned användningsinformation
- Visa månatlig användning och avgifter <sup>1</sup>

> <sup>1</sup> En företagsadministratör måste bevilja dessa behörigheter. Om du fick behörighet att visa avdelningens månatliga användning och avgifter men inte kan se dem kontaktar du din partner.

### <a name="to-add-a-department-administrator"></a>Så här lägger du till en avdelningsadministratör

Som företagsadministratör:

1. Logga in på Azure Enterprise-portalen.
1. I det vänstra fönstret väljer du **Hantera**.
1. Välj fliken **Avdelning** och välj sedan avdelningen.
1. Välj **+ Lägg till administratör** och lägg till nödvändig information.
1. För skrivskyddad åtkomst anger du alternativet **Skrivskyddad** till **Ja** och väljer sedan **Lägg till**.

![Exempel som visar dialogrutan Lägg till avdelningsadministratör](./media/ea-portal-administration/ea-create-add-department-admin.png)

### <a name="to-set-read-only-access"></a>Ange skrivskyddad åtkomst

Du kan bevilja avdelningsadministratörer skrivskyddad åtkomst.

- När du skapar en ny avdelningsadministratör anger du alternativet för skrivskydd till **Ja**.

- Så här redigerar du en befintlig avdelningsadministratör:
   1. Välj en avdelning och välj sedan pennsymbolen intill den **avdelningsadministratör** som du vill redigera.
   1. Ange alternativet för skrivskydd till **Ja** och välj sedan **Spara**.

Företagsadministratörer får automatiskt behörighet som avdelningsadministratör.

## <a name="add-an-account"></a>Lägga till ett konto

Strukturen för konton och prenumerationer påverkar hur de administreras och hur de visas på dina fakturor och rapporter. Exempel på typiska organisationsstrukturer är affärsdivisioner, funktionella team och geografiska platser.

Så här lägger du till ett konto:

1. I Azure Enterprise-portalen väljer du **Hantera** i det vänstra navigeringsområdet.
1. Välj fliken **Konto**. På sidan **Konto** väljer du **+ Lägg till konto**.
1. Välj en avdelning eller låt den vara otilldelad, och välj sedan önskad autentiseringstyp.
1. Ange ett användarvänligt namn som identifierar kontot i rapportering.
1. Ange **kontoinnehavarens e-postadress** som ska associeras med det nya kontot.
1. Bekräfta e-postadressen och välj sedan **Lägg till**.

![Exempel som visar listan över konton och alternativet + Lägg till konto](./media/ea-portal-administration/create-ea-add-an-account.png)

Om du vill lägga till ytterligare ett konto väljer du **Lägg till ett annat konto** eller väljer **Lägg till** i det nedre högra hörnet i det vänstra verktygsfältet.

Så här bekräftar du kontoägarskapet:

1. Logga in på Azure Enterprise-portalen.
1. Visa statusen.

   Statusen bör ändras från **Väntar** till **Start-/slutdatum**. Start-/slutdatum är det datum då användaren först loggade in samt avtalets slutdatum.
1. När **varningsmeddelandet** visas måste kontoinnehavaren välja **Fortsätt** för att aktivera kontot första gången kontoinnehavaren loggar in på Azure Enterprise-portalen.

## <a name="change-azure-subscription-or-account-ownership"></a>Ändra Azure-prenumeration eller -kontoinnehavare

Företagsadministratörer kan använda Azure Enterprise-portalen för att överföra kontoägarskapet för utvalda eller alla prenumerationer i en registrering.

När du har överfört en prenumeration eller ett kontoägarskap uppdaterar Microsoft kontoinnehavaren.

Innan du utför överföringen av ägarskapet behöver du förstå dessa principer för rollbaserad åtkomst i Azure (Azure RBAC):

- När du utför överföringar av prenumerationer eller kontoägarskap mellan två organisations-ID:n i samma klientorganisation bevaras Azure RBAC-principer och befintliga roller för tjänstadministratör och medadministratör.
- Överföringar av prenumerationer eller kontoägarskap mellan flera klientorganisationer leder till att dina Azure RBAC-principer och rolltilldelningar går förlorade.
- Principer och administratörsroller överförs inte mellan olika kataloger. Tjänstadministratörer uppdateras till ägare av målkontot.
- Kontrollera att kryssrutan **Flytta prenumerationerna till mottagarens Azure AD-klient** är **omarkerad** för att undvika att Azure RBAC-principer och rolltilldelningar går förlorade när du överför en prenumeration mellan klienter. På så sätt behålls tjänsterna, Azure-rollerna och principerna i Azure AD-klientorganisationen, och endast faktureringsägarskapet för kontot överförs.  
    :::image type="content" source="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Bild som visar omarkerad kryssruta för att flytta prenumerationer till Azure AD-klientorganisation" lightbox="./media/ea-portal-administration/unselected-checkbox-move-subscriptions-to-recipients-tenant.png" :::


Innan du ändrar en kontoägare:

1. I Azure Enterprise-portalen visar du fliken **Konto** och identifierar källkontot. Källkontot måste vara aktivt.
1. Identifiera målkontot och kontrollera att det är aktivt.

Så här överför du kontoägarskap för alla prenumerationer:

1. Logga in på Azure Enterprise-portalen.
1. I det vänstra navigeringsfältet väljer du **Hantera**.
1. Välj fliken **Konto** och hovra över ett konto.
1. Välj ikonen för ändring av kontoägare till höger. Ikonen ser ut som en person.  
    ![Bild som visar symbolen för ändring av kontoägare](./media/ea-portal-administration/create-ea-create-sub-transfer-account-ownership-of-sub.png)
1. Välj målkontot som du vill överföra till och välj sedan **Nästa**.
1. Om du vill överföra kontoägarskapet mellan flera Azure AD-klienter markerar du kryssrutan **Flytta prenumerationerna till mottagarens Azure AD-klient** .  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Bild som visar markerad kryssruta för att flytta prenumerationer till Azure AD-klient" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Bekräfta överföringen och välj **Skicka**.

Så här överför du kontoägarskap för en enskild prenumeration:

1. Logga in på Azure Enterprise-portalen.
1. I det vänstra navigeringsfältet väljer du **Hantera**.
1. Välj fliken **Konto** och hovra över ett konto.
1. Välj ikonen för överföring av prenumerationer till höger. Ikonen ser ut som en sida.  
    ![Bild som visar symbolen för överföring av prenumerationer](./media/ea-portal-administration/ea-transfer-subscriptions.png)
1. Välj målkontot som prenumerationen ska överföras till och välj sedan **Nästa**.
1. Om du vill överföra ägarskapet för prenumerationen mellan flera Azure AD-klienter markerar du kryssrutan **Flytta prenumerationerna till mottagarens Azure AD-klient** .  
    :::image type="content" source="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" alt-text="Bild som visar markerad kryssruta för att flytta prenumerationer till Azure AD-klient" lightbox="./media/ea-portal-administration/selected-checkbox-move-subscriptions-to-recipients-tenant.png" :::
1. Bekräfta överföringen och välj sedan **Skicka**.


## <a name="associate-an-account-to-a-department"></a>Associera ett konto med en avdelning

Företagsadministratörer kan associera befintliga konton till avdelningar under registreringen.

### <a name="to-associate-an-account-to-a-department"></a>Associera ett konto med en avdelning

1. Logga in i Azure EA-portalen som företagsadministratör.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Välj **Avdelning**.
1. Hovra över raden med kontot och välj pennikonen till höger.
1. Välj avdelning i den nedrullningsbara menyn.
1. Välj **Spara**.

## <a name="associate-an-existing-account-with-your-pay-as-you-go-subscription"></a>Associera ett befintligt konto med din Betala per användning-prenumeration

Om du redan har ett befintligt Microsoft Azure-konto i Azure-portalen anger du det associerade skol-, arbets- eller Microsoft-kontot för att associera det med din Enterprise-avtalsregistrering.

### <a name="associate-an-existing-account"></a>Associera ett befintligt konto

1. I Azure Enterprise-portalen väljer du **Hantera**.
1. Välj fliken **Konto**.
1. Välj **+ Lägg till ett konto**.
1. Ange det arbets-, skol- eller Microsoft-konto som är associerat med det befintliga Azure-kontot.
1. Bekräfta det konto som är associerat med det befintliga Azure-kontot.
1. Ange ett namn du vill använda för att identifiera det här kontot i rapportering.
1. Välj **Lägg till**.
1. Du kan lägga till ytterligare ett konto genom att välja alternativet **+ Lägg till ett konto** igen eller gå tillbaka till startsidan genom att välja knappen **Admin**.
1. Om du visar sidan **Konto** visas det nyligen tillagda kontot med statusen **Väntar**.

### <a name="confirm-account-ownership"></a>Bekräfta kontoägarskapet

1. Logga in på det e-postkonto som är associerat med det arbets-, skol- eller Microsoft-konto som du angav.
1. Öppna e-postmeddelandet med titeln _"Invitation to Activate your Account on the Microsoft Azure Service from Microsoft Volume Licensing"_ (Inbjudan till att aktivera ditt konto på Microsoft Azure-tjänsten från Microsoft Volume Licensing).
1. Välj länken **Logga in på Microsoft Azure Enterprise Portal** i inbjudan.
1. Välj **Logga in**.
1. Ange ditt arbets-, skol- eller Microsoft-konto samt lösenordet för att logga in och bekräfta kontoägarskapet.

### <a name="azure-marketplace"></a>Azure Marketplace

De flesta prenumerationer kan konverteras från Betala per användning-miljön till Azure Enterprise-avtal, men inte Azure Marketplace-tjänster. För att du ska få en enda vy över alla prenumerationer och avgifter rekommenderar vi att du lägger till Azure Marketplace-tjänsterna till Azure Enterprise-portalen.

1. Logga in på Azure Enterprise-portalen.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Välj fliken **Registrering**.
1. Visa avsnittet **Registreringsinformation**.
1. Till höger om Azure Marketplace-fältet väljer du pennikonen för att aktivera. Välj **Spara**.

Kontoinnehavaren kan nu köpa valfria Azure Marketplace-tjänster som tidigare ägdes i Betala per användning-prenumerationen.

När de nya Azure Marketplace-prenumerationerna har aktiverats under din Azure EA-registrering avbryter du de Azure Marketplace-tjänster som skapades i Betala per användning-miljön. Det här steget är viktigt så att dina Azure Marketplace-prenumerationer inte får en dålig status när ditt Betala per användning-betalningsmedel upphör.

### <a name="msdn"></a>MSDN

MSDN-prenumerationer konverteras automatiskt till MSDN Dev/Test, och Azure EA-erbjudandet förlorar eventuell befintlig betalningskredit.

### <a name="azure-in-open"></a>Azure i Open

Om du associerar en Azure i Open-prenumeration med ett Enterprise-avtal blir alla ej förbrukade Azure i Open-krediter ogiltiga. Vi rekommenderar därför att du förbrukar all kredit i en Azure i Open-prenumeration innan du lägger till kontot i ditt Enterprise-avtal.  

### <a name="accounts-with-support-subscriptions"></a>Konton med supportprenumerationer

Om ditt Enterprise-avtal inte har en supportprenumeration och du lägger till ett befintligt konto med en supportprenumeration till Azure Enterprise-portalen överförs inte din MOSA-supportprenumeration automatiskt. Du behöver då på nytt köpa en supportprenumeration i Azure EA under respitperioden – senast i slutet av efterföljande månad.

## <a name="department-spending-quotas"></a>Avdelningens utgiftskvoter

EA-kunder kan ange eller ändra utgiftskvoter för varje avdelning under en registrering. Utgiftskvotens belopp anges för den aktuella förskottsbetalningsperioden. I slutet av den aktuella förskottsbetalningsperioden förlänger systemet den befintliga utgiftskvoten till nästa förskottsbetalningsperiod om inte värdena uppdateras.

Avdelningsadministratören kan visa utgiftskvoten utan bara företagsadministratören kan uppdatera kvotbeloppet. Företagsadministratören och avdelningsadministratören får meddelanden när kvoten har nått 50 %, 75 %, 90 % och 100%.

### <a name="enterprise-administrator-to-set-the-quota"></a>Företagsadministratör för att ange kvoten:

 1. Öppna Azure EA-portalen.
 1. Välj **Hantera** i det vänstra navigeringsfältet.
 1. Välj fliken **Avdelning**.
 1. Välj avdelning.
 1. Välj pennsymbolen i avsnittet Avdelningsinformation eller välj symbolen **+ Lägg till avdelning** för att lägga till en utgiftskvot tillsammans med en ny avdelning.
 1. Under användningsinformationen anger du ett utgiftskvotbelopp i registreringens valuta i rutan Spending Quota $ (Utgiftskvot $) (måste vara större än 0).
    - Avdelningsnamnet och kostnadsstället kan också redigeras nu.
 1. Välj **Spara**.

Avdelningens utgiftskvot syns nu i vyn Department List (Avdelningslista) under fliken Department (Avdelning). I slutet av den aktuella förskottsbetalningen behåller Azure EA-portalen utgiftskvoterna till nästa förskottsbetalningsperiod.

Avdelningskvotens belopp är oberoende av den aktuella Azure-förskottsbetalningen, och kvotbeloppet och aviseringarna gäller bara för förstapartsanvändning. Avdelningens utgiftskvot är bara för information och tillämpar inte utgiftsgränser.

### <a name="department-administrator-to-view-the-quota"></a>Avdelningsadministratör för att visa kvoten:

1. Öppna Azure EA-portalen.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Klicka på fliken **Avdelning** och visa vyn Department List (Avdelningslista) med utgiftskvoter.

Om du är en indirekt kund måste din kanalpartner aktivera kostnadsfunktioner.

## <a name="enterprise-user-roles"></a>Roller för företagsanvändare

Du kan administrera kostnader och användning för Azure EA i Azure EA-portalen. Det finns tre huvudsakliga roller i Azure EA-portalen:

- EA-administratör
- Avdelningsadministratör
- Kontoinnehavare

De olika rollerna har olika åtkomstnivå och behörighet.

Du kan läsa mer om användarroller under [Enterprise user roles](./understand-ea-roles.md#enterprise-user-roles) (Roller för företagsanvändare).

## <a name="add-an-azure-ea-account"></a>Lägga till ett Azure EA-konto

Azure EA-kontot är en organisationsenhet i Azure EA-portalen. Den används för att administrera prenumerationer samt för rapportering. Om du ska kunna komma åt och använda Azure-tjänster måste du eller någon annan skapa ett konto åt dig.

Du kan läsa mer om Azure-konton under [Lägga till ett konto](#add-an-account).

## <a name="enterprise-devtest-offer"></a>Enterprise Dev/Test-erbjudande

Som Azure Enterprise-administratör kan du aktivera kontoinnehavare i organisationen för att skapa prenumerationer baserade på EA Dev/Test-erbjudandet. För att göra det väljer du rutan Dev/Test för kontoägaren i Azure EA-portalen.

När du har markerat Dev/Test-rutan meddelar du kontoinnehavaren att han/hon kan ställa in de EA Dev/Test-prenumerationer som behövs för teamet med Dev/Test-prenumeranter.

Erbjudandet gör att aktiva Visual Studio-prenumeranter kan köra arbetsbelastningar för utveckling och testning i Azure till specialpris. Det ger åtkomst till det fullständiga galleriet med Dev/Test-avbildningar inklusive Windows 8.1 och Windows 10.

### <a name="to-set-up-the-enterprise-devtest-offer"></a>Konfigurera Enterprise Dev/Test-erbjudandet:

1. Logga in som företagsadministratör.
1. Välj **Hantera** i det vänstra navigeringsfältet.
1. Välj fliken **Konto**.
1. Välj raden för det konto där du vill aktivera Dev/Test-åtkomst.
1. Välj pennsymbolen till höger om raden.
1. Markera kryssrutan Dev/Test.
1. Välj **Spara**.

När en användare läggs till som kontoinnehavare via Azure EA-portalen konverteras alla Azure-prenumerationer associerade med kontoinnehavaren som baseras på antingen PAYG Dev/Test-erbjudandet eller erbjudanden med månatlig kredit för Visual Studio-prenumeranter till EA Dev/Test-erbjudandet. Prenumerationer baserade på andra erbjudandetyper, till exempel PAYG (betala per användning), associerade med kontoinnehavaren konverteras till Microsoft Azure Enterprise-erbjudanden.

Dev/Test-erbjudandet gäller för närvarande inte för Azure Gov-kunder.

## <a name="create-a-subscription"></a>Skapa en prenumeration

Kontoägare kan visa och hantera prenumerationer. Du kan använda prenumerationer för att ge team i din organisation åtkomst till utvecklingsmiljöer och projekt. Det kan till exempel gälla testning, produktion, utveckling och mellanlagring.

När du skapar olika prenumerationer för varje programmiljö hjälper du till att skydda varje miljö.

- Du kan även tilldela ett annat tjänstadministratörskonto för varje prenumeration.
- Du kan associera prenumerationer med valfritt antal tjänster.
- Kontoägaren skapar prenumerationer och tilldelar ett tjänstadministratörskonto till varje prenumeration i sitt konto.

### <a name="add-a-subscription"></a>Lägga till en prenumeration

Använd följande information för att lägga till en prenumeration.

Första gången du lägger till en prenumeration i ditt konto uppmanas du att godkänna prenumerationsavtalet för Microsoft Online (MOSA) och en prisplan. Även om de inte gäller för Enterprise-avtalskunder krävs MOSA och prisplanen för att du ska kunna skapa din prenumeration. Tillägget till din Microsoft Azure Enterprise-avtalsregistrering ersätter ovanstående, och din kontraktsmässiga relation ändras inte. När du uppmanas väljer du den ruta som anger att du godkänner villkoren.

_Microsoft Azure Enterprise_ är standardnamnet när en prenumeration skapas. Du kan ändra namnet för att skilja det från andra prenumerationer i din registrering samt för att se till att det går att känna igen i rapporter för företagsnivå.

Så här lägger du till en prenumeration:

1. I Azure Enterprise-portalen loggar du in på kontot.
1. Välj fliken **Admin** och sedan **Prenumeration** längst upp på sidan.
1. Kontrollera att du är inloggad som kontoägare för kontot.
1. Välj **+ Lägg till prenumeration** och sedan **Köp**.

   Första gången du lägger till en prenumeration på ett konto måste du ange dina kontaktuppgifter. När du lägger till ytterligare prenumerationer läggs dina kontaktuppgifter till.

1. Välj **Prenumerationer** och välj sedan den prenumeration som du skapade.
1. Välj **Redigera prenumerationsinformation**.
1. Redigera **Prenumerationsnamn** och **Tjänstadministratör** och markera sedan kryssrutan.

   Prenumerationsnamnet visas i rapporter. Det är namnet på det projekt som är associerat med prenumerationen i utvecklingsportalen.

Det kan ta upp till 24 timmar innan nya prenumerationer visas i prenumerationslistan. När du har skapat en prenumeration kan du:

- [Redigera prenumerationsdetaljer](https://portal.azure.com)
- [Hantera prenumerationstjänster](https://portal.azure.com/#home)

## <a name="delete-subscription"></a>Ta bort prenumeration

Ta bort en prenumeration där du är kontoinnehavare:

1. Logga in på Azure-portalen med autentiseringsuppgifterna som är kopplade till ditt konto.
1. Välj **Prenumerationer** på navmenyn.
1. På prenumerationsfliken i det övre vänstra hörnet på sidan väljer du den prenumeration du vill ha för att avbryta och välj **Avbryt prenumeration** för att starta fliken Avbryt.
1. Ange prenumerationens namn och välj en uppsägningsorsak och väljer knappen **Avbryt prenumeration**.

Bara kontoadministratörer kan avbryta prenumerationer.

Mer information finns i [Vad händer när jag har sagt upp min prenumeration?](cancel-azure-subscription.md#what-happens-after-subscription-cancellation).

## <a name="delete-an-account"></a>Ta bort ett konto

Borttagning av konto kan bara slutföras för aktiva konton utan aktiva prenumerationer.

1. I EA-portalen väljer du **Hantera** i det vänstra navigeringsfältet.
1. Välj fliken **Konto**.
1. I tabellen med konton väljer du de konton du vill ta bort.
1. Välj X-symbolen till höger om kontoraden.
1. När det inte finns några aktiva prenumerationer under kontot väljer du knappen **Ja** under kontoraden för att bekräfta borttagningen av kontot.

## <a name="update-notification-settings"></a>Uppdatera meddelandeinställningar

Företagsadministratörer registreras automatiskt för att få användningsmeddelanden kopplade till sin registrering. Varje företagsadministratör kan ändra hur ofta meddelanden ska skickas, eller så kan de stängas av helt.

Du kan se kontaktuppgifterna för meddelanden i avsnittet **Meddelandekontakt** i Azure EA-portalen. Genom att hantera dina meddelandekontakter ser du till att rätt personer i organisationen får meddelanden om Azure EA.

Så här visar du aktuella meddelandeinställningar:

1. Öppna Azure EA-portalen och navigera till **Hantera** > **Meddelandekontakt**.
2. E-postadress – e-postadressen som är kopplad till företagsadministratörens Microsoft-konto, arbetskonto eller skolkonto som tar emot meddelanden.
3. Frekvens för meddelanden om utestående saldo – hur ofta meddelanden skickas till respektive företagsadministratör.

Så här lägger du till en kontakt:

1. Välj **+ Lägg till kontakt**.
2. Ange e-postadressen och bekräfta den.
3. Välj **Spara**.

Den nya meddelandekontakten visas i avsnittet **Meddelandekontakt**. Om du vill ändra meddelandefrekvensen markerar du meddelandekontakten och väljer pennsymbolen till höger om den markerade raden. Ställ in frekvensen som **varje dag**, **varje vecka**, **varje månad** eller **aldrig**.

Du kan välja att hoppa över livscykelmeddelandena _täckningsperiodens slutdatum närmar sig_ och _datumet för inaktivering och avetablering närmar sig_. Om du hoppar över livscykelmeddelandena skickas inga meddelanden om slutdatumet för täckningsperioden eller avtalet.

## <a name="azure-sponsorship-offer"></a>Azure-sponsringserbjudande

Azure-sponsringserbjudandet är ett begränsat, sponsrat Microsoft Azure-konto. Inbjudan skickas via e-post till ett begränsat antal kunder som väljs ut av Microsoft. Om du är berättigad till Microsoft Azure-sponsringserbjudandet så har du fått en e-postinbjudan till ditt konto-id.

Om du vill ha mer information kan du skapa en [supportbegäran för sponsringsaktivering](https://aka.ms/azrsponsorship).

## <a name="conversion-to-work-or-school-account-authentication"></a>Konvertering till autentisering med arbets- eller skolkonto

Azure Enterprise-användare kan konvertera autentiseringstypen från ett Microsoft-konto (MSA eller Live ID) till ett arbets- eller skolkonto (som använder Azure Active Directory).

Så här börjar du:

1. Lägg till arbets- eller skolkontot i Azure EA-portalen i de rollers som krävs.
1. Om du får felmeddelanden kanske kontot inte är giltigt i den aktiva katalogen.  Azure använder användarhuvudnamn (UPN), som inte alltid är identiskt med e-postadressen.
1. Autentisera till Azure EA-portalen med arbets- eller skolkonto.

### <a name="to-convert-subscriptions-from-microsoft-accounts-to-work-or-school-accounts"></a>Så här konverterar du prenumerationer från Microsoft-konton för arbets- eller skolkonton:

1. Logga in på hanteringsportalen med det Microsoft-konto som äger prenumerationerna.
1. Använd överföring av kontoägarskap för att flytta till det nya kontot.
1. Nu bör Microsoft-kontot vara fritt från aktiva prenumerationer och kan tas bort.
1. Borttagna konton blir kvar i vyn i portalen med inaktiv status av historiska faktureringsskäl.  Du kan filtrera bort dem från vyn genom att markera kryssrutan för att bara visa aktiva konton.

## <a name="account-subscription-ownership-faq"></a>Vanliga frågor och svar om prenumerationsägarskap

I det här dokumentet besvaras frågor som rör ägarskapet för kontoprenumerationen.

### <a name="can-i-associate-my-existing-azure-account-to-azure-ea-enrollment"></a>Kan jag associera mitt befintliga Azure-konto med Azure EA-registrering?

Ja. Alla Azure-prenumerationer som du är kontoinnehavare för konverteras till ditt Enterprise-avtal. Bland dessa ingår prenumerationer som använder månatlig kredit, till exempel Visual Studio, AzurePass, MPN, BizSpark och fler. Du kommer att förlora månadskredit när du konverterar sådana prenumerationer.

### <a name="how-many-azure-account-owners-can-you-have-per-subscription"></a>Hur många Azure-kontoinnehavare kan du ha per prenumeration?

Bara en kontoinnehavare tillåts per prenumeration.  Ytterligare roller kan läggas till med rollbaserad åtkomst eller Åtkomstkontroll (IAM) på prenumerationsfliken i det övre vänstra hörnet på sidan på [Azure-portalen](https://portal.azure.com).

### <a name="is-it-possible-to-transfer-subscription-ownership-to-another-account"></a>Kan jag överföra äganderätten till prenumerationen till ett annat konto?

Ja, du kan överföra äganderätten till prenumerationen till ett annat konto. Exempel: Om konto A har tre prenumerationer kan företagsadministratören överföra en prenumeration till konto B, en till konto C och en till konto D. Det går även att överföra alla prenumerationer till konto E.

Så här överför du prenumerationer:

1. I Azure Enterprise-portalen väljer du **Hantera** > **Konto**.
1. Hovra över **Konto** längst till höger för att se alternativen **Överför ägarskap** (personikonen) och **Överför prenumeration** (listikonen). De här alternativen är endast synliga för aktiva konton.

### <a name="can-an-azure-account-owner-be-listed-under-more-than-one-department"></a>Kan en Azure-kontoinnehavare listas under mer än en avdelning?

Nej, en kontoinnehavare kan bara associeras med en enda avdelning. Principen är till för att säkerställa korrekt övervakning och fördelning av kostnader och utgifter kopplade till avdelningen de är justerade mot under EA-registreringen i Azure EA-portalen.

### <a name="can-an-azure-account-owner-be-listed-as-a-security-group"></a>Kan en Azure-kontoinnehavare listas som en säkerhetsgrupp?

Nej, en prenumerationsägare måste vara ett unikt Microsoft-konto (MSA) eller en Azure Active Directory-autentisering (Azure AD). För att ta hänsyn till följden i organisationen kan du skapa allmänna konton och använda Azure AD för att hantera prenumerationsåtkomst.

### <a name="can-an-individual-user-own-multiple-subscriptions"></a>Kan en enskild användare äga flera prenumerationer?

En Azure-kontoinnehavare kan skapa och hantera ett obegränsat antal prenumerationer.

### <a name="how-can-i-accessview-all-my-organizations-subscriptions"></a>Hur kan jag få åtkoms till/visa alla min organisations prenumerationer?

Idag måste detta göras med en princip. Det betyder att du skulle kräva att ditt konto läggs till i en prenumerationsroll med rollbaserad åtkomst för varje prenumeration som skapas.

### <a name="where-do-i-go-to-create-a-subscription"></a>Var skapar jag en prenumeration?

Innan du kan skapa en Azure EA-prenumeration måste ditt konto läggas till i rollen som kontoinnehavare av din administratören för din EA-registrering i Azure EA-portalen. Du måste sedan logga in på Azure EA-portalen för att få din rättighet att skapa prenumerationer för EA-erbjudanden. Vi rekommenderar att din första EA-prenumeration skapas via länken ”+ Add Subscription” (+ Lägg till prenumeration) på prenumerationsfliken i EA-portalen.  Men när ditt konto är berättigat kan det vara enklare att skapa prenumerationer i portal.azure.com på prenumerationsfliken i det övre högra hörnet på sidan, där du både kan skapa och byta namn på prenumerationen i ett enda steg.

### <a name="who-can-create-a-subscription"></a>Vem kan skapa en prenumeration?

Om du vill skapa en prenumeration för ett Azure-erbjudande för företag måste du vara berättigad i rollen som kontoinnehavare i [EA-portalen](https://ea.azure.com).

## <a name="azure-ea-term-glossary"></a>Ordlista för Azure EA

- **Konto**: En organisationsenhet i Azure Enterprise-portalen. Den används för att administrera prenumerationer samt för rapportering.
- **Kontoinnehavare**: Den person som hanterar prenumerationer och tjänstadministratörer i Azure. Den kan visa användningsdata för det här kontot och dess associerade prenumerationer.
- **Ändringsprenumeration**: En ettårig eller sammanfallande prenumeration under registreringsändringen.
- **Förskottsbetalning**: Förskottsbetalning av ett årligt penningbelopp för Azure-tjänster till ett rabatterat förskottsbetalningspris för användning mot denna förskottsbetalning.
- **Avdelningsadministratör**: Den person som hanterar avdelningar, skapar nya konton och kontoinnehavare, visar användningsinformation för de avdelningar som den hanterar samt kan visa kostnader när den beviljas behörigheter.
- **Registreringsnummer**: En unik identifierare som tillhandahålls av Microsoft för att identifiera den specifika registrering associerad med ett Enterprise-avtal.
- **Företagsadministratör**: Den person som hanterar avdelningar, avdelningsägare, konton och kontoinnehavare i Azure. De kan hantera företagsadministratörer och visa data, fakturerade kvantiteter och ej fakturerade kostnader för alla konton och prenumerationer associerade med företagsregistreringen.
- **Enterprise-avtal**: Ett Microsoft-licensavtal för kunder med centraliserade inköp som vill standardisera hela organisationen på Microsoft-teknik och underhålla en IT-infrastruktur på en standard med Microsoft-programvara.
- **Enterprise-avtalsregistrering**: En registrering i Enterprise-avtalsprogrammet som tillhandahåller Microsoft-produkter i volym till rabatterade priser.
- **Microsoft-konto**: En webbaserad tjänst som gör att deltagande webbplatser kan autentisera en användare med en enskild uppsättning autentiseringsuppgifter.
- **Microsoft Azure Enterprise-registreringsändring (registreringsändring)** : En ändring som har signerats av ett företag, vilket ger åtkomst till Azure som en del av deras företagsregistrering.
- **Azure Enterprise-portalen**: Den portal som våra företagskunder använder för att hantera sina Azure-konton och tillhörande prenumerationer.
- **Förbrukad resurskvantitet**: Kvantiteten för en enskild Azure-tjänst som användes under en månad.
- **Tjänstadministratör**: Den person som kommer åt och hanterar prenumerationer och utvecklingsprojekt i Azure Enterprise-portalen.
- **Prenumeration**: Representerar en Azure Enterprise-portalprenumeration och är en container för Azure-tjänster som hanteras av samma tjänstadministratör.
- **Arbets- eller skolkonto**: För organisationer som har konfigurerat Azure Active Directory med federation till molnet och där alla konton finns i en enda klientorganisation.

### <a name="enrollment-statuses"></a>Registreringsstatusar

- **Nytt**: Den här statusen tilldelas till en registrering som har skapats inom 24 timmar och kommer att uppdateras till väntande status inom 24 timmar.
- **Väntar**: Registreringsadministratören måste logga in på Azure Enterprise-portalen. Efter inloggningen växlar registreringen till aktiv status.
- **Aktiv**: Registreringen är aktiv och konton och prenumerationer kan skapas i Azure Enterprise-portalen. Registreringen är aktiv tills Enterprise-avtalets slutdatum.
- **Obegränsad utökad giltighet**: En obegränsad utökad giltighet sker efter Enterprise-avtalets slutdatum. Med den kan Azure EA-kunder som har valt den utökade perioden fortsätta använda Azure-tjänster på obegränsad tid i slutet av deras Enterprise-avtal.

   Innan Azure EA-registreringen når Enterprise-avtalets slutdatum bör registreringsadministratören välja vilket av följande alternativ som ska ske:

  - Förnya registreringen genom att utöka Azure-förskottsbetalningen.
  - Överföra till en ny registrering.
  - Migrera till MOSP-programmet (Microsoft Online Subscription program).
  - Bekräfta inaktiveringen av alla tjänster som är associerade med registreringen.
- **Upphört**: Azure EA-kunden avanmäls från den utökade giltigheten, och Azure EA-registreringen har nått Enterprise-avtalets slutdatum. Registreringen upphör att gälla och alla associerade tjänster kommer att inaktiveras.
- **Överförd**: Registreringar där alla associerade konton och tjänster har överförts till en ny registrering visas med en överförd status.
  >[!NOTE]
  > Registreringar överförs inte automatiskt om ett nytt registreringsnummer genereras under förnyandet. Du måste inkludera ditt tidigare registreringsnummer i dina förnyelseuppgifter för att medge automatisk överföring.

## <a name="next-steps"></a>Nästa steg

- Läs om hur du kan spara pengar med [reservationer för virtuella datorer](ea-portal-vm-reservations.md).
- Om du behöver hjälp med att felsöka problem med Azure EA-portalen kan du läsa [Felsöka åtkomst till Azure EA-portalen](ea-portal-troubleshoot.md).