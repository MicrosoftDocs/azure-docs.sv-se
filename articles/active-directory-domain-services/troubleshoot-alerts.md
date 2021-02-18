---
title: Vanliga varningar och lösningar i Azure AD Domain Services | Microsoft Docs
description: Lär dig hur du löser vanliga aviseringar som har genererats som en del av hälso tillståndet för Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 4caf804a274956556d6e9ca396c8f08594b11a87
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101092872"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Kända problem: vanliga aviseringar och lösningar i Azure Active Directory Domain Services

Som en central del av identitet och autentisering för program har Azure Active Directory Domain Services (Azure AD DS) ibland problem. Om du stöter på problem finns det några vanliga aviseringar och tillhör ande fel söknings steg som hjälper dig att komma igång igen. Du kan när som helst [öppna en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

Den här artikeln innehåller felsöknings information för vanliga aviseringar i Azure AD DS.

## <a name="aadds100-missing-directory"></a>AADDS100: Katalog saknas

### <a name="alert-message"></a>Aviserings meddelande

*Azure AD-katalogen som är kopplad till din hanterade domän kan ha tagits bort. Den hanterade domänen är inte längre i en konfiguration som stöds. Microsoft kan inte övervaka, hantera, korrigera och synkronisera din hanterade domän.*

### <a name="resolution"></a>Lösning

Det här felet uppstår vanligt vis när en Azure-prenumeration flyttas till en ny Azure AD-katalog och den gamla Azure AD-katalogen som är associerad med Azure AD DS tas bort.

Det här felet kan inte återställas. Om du vill lösa aviseringen [tar du bort din befintliga hanterade domän](delete-aadds.md) och återskapar den i din nya katalog. Om du har problem med att ta bort den hanterade domänen [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C körs i den här katalogen

### <a name="alert-message"></a>Aviserings meddelande

*Azure AD Domain Services kan inte aktive ras i en Azure AD B2C katalog.*

### <a name="resolution"></a>Lösning

Azure AD DS synkroniseras automatiskt med en Azure AD-katalog. Om Azure AD-katalogen har kon figurer ATS för B2C kan inte Azure AD DS distribueras och synkroniseras.

Om du vill använda Azure AD DS måste du återskapa den hanterade domänen i en katalog utan Azure AD B2C med hjälp av följande steg:

1. [Ta bort den hanterade domänen](delete-aadds.md) från din befintliga Azure AD-katalog.
1. Skapa en ny Azure AD-katalog som inte är en Azure AD B2C katalog.
1. [Skapa en ersättande hanterad domän](tutorial-create-instance.md).

Den hanterade domänens hälsa uppdateras automatiskt inom två timmar och aviseringen tas bort.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: adressen finns i ett offentligt IP-adressintervall

### <a name="alert-message"></a>Aviserings meddelande

*IP-adressintervallet för det virtuella nätverk där du har aktiverat Azure AD Domain Services finns i ett offentligt IP-adressintervall. Azure AD Domain Services måste vara aktiverat i ett virtuellt nätverk med ett privat IP-adressintervall. Den här konfigurationen påverkar Microsofts förmåga att övervaka, hantera, uppdatera och synkronisera din hanterade domän.*

### <a name="resolution"></a>Lösning

Innan du börjar ska du kontrol lera att du förstår [adress utrymmen för privata IP v4](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces).

I ett virtuellt nätverk kan virtuella datorer göra förfrågningar till Azure-resurser i samma IP-adressintervall som de har kon figurer ATS för under nätet. Om du konfigurerar ett offentligt IP-adressintervall för ett undernät kanske begär Anden som dirigeras i ett virtuellt nätverk inte når de avsedda webb resurserna. Den här konfigurationen kan leda till oförutsägbara fel med Azure AD DS.

> [!NOTE]
> Om du äger IP-adressintervallet i Internet som har kon figurer ATS i det virtuella nätverket kan den här aviseringen ignoreras. Azure AD Domain Services kan dock inte genomföra [service avtalet](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) med den här konfigurationen eftersom den kan leda till oförutsägbara fel.

För att lösa den här aviseringen tar du bort din befintliga hanterade domän och återskapar den i ett virtuellt nätverk med ett privat IP-adressintervall. Den här processen kan störas eftersom den hanterade domänen inte är tillgänglig och eventuella anpassade resurser som du har skapat som organisationsenheter eller tjänst konton går förlorade.

1. [Ta bort den hanterade domänen](delete-aadds.md) från katalogen.
1. Om du vill uppdatera det virtuella nätverkets IP-adressintervall söker du efter och väljer *virtuella nätverk* i Azure Portal. Välj det virtuella nätverk för Azure AD DS som felaktigt har en offentlig IP-adressintervall angiven.
1. Under **Inställningar** väljer du *adress utrymme*.
1. Uppdatera adress intervallet genom att välja det befintliga adress intervallet och redigera det eller lägga till ytterligare ett adress intervall. Kontrol lera att det nya IP-adressintervallet är i ett privat IP-adressintervall. **Spara** ändringarna när du är klar.
1. Välj **undernät** i det vänstra navigerings fältet.
1. Välj det undernät som du vill redigera eller skapa ett ytterligare undernät.
1. Uppdatera eller ange ett privat IP-adressintervall och **Spara** ändringarna.
1. [Skapa en ersättande hanterad domän](tutorial-create-instance.md). Se till att du väljer det uppdaterade virtuella nätverkets undernät med ett privat IP-adressintervall.

Den hanterade domänens hälsa uppdateras automatiskt inom två timmar och aviseringen tas bort.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: din Azure-prenumeration hittades inte

### <a name="alert-message"></a>Aviserings meddelande

*Din Azure-prenumeration som är associerad med din hanterade domän har tagits bort.  Azure AD Domain Services kräver en aktiv prenumeration för att fortsätta att fungera korrekt.*

### <a name="resolution"></a>Lösning

Azure AD DS kräver en aktiv prenumeration och kan inte flyttas till en annan prenumeration. Om den Azure-prenumeration som den hanterade domänen var associerad med tas bort måste du återskapa en Azure-prenumeration och en hanterad domän.

1. [Skapa en Azure-prenumeration](../cost-management-billing/manage/create-subscription.md).
1. [Ta bort den hanterade domänen](delete-aadds.md) från din befintliga Azure AD-katalog.
1. [Skapa en ersättande hanterad domän](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: din Azure-prenumeration har inaktiverats

### <a name="alert-message"></a>Aviserings meddelande

*Din Azure-prenumeration som är associerad med din hanterade domän är inte aktiv.  Azure AD Domain Services kräver en aktiv prenumeration för att fortsätta att fungera korrekt.*

### <a name="resolution"></a>Lösning

Azure AD DS kräver en aktiv prenumeration. Om den Azure-prenumeration som den hanterade domänen var kopplad till inte är aktiv, måste du förnya den för att återaktivera prenumerationen.

1. [Förnya din Azure-prenumeration](../cost-management-billing/manage/subscription-disabled.md).
2. När prenumerationen har förnyats kan du återaktivera den hanterade domänen med en Azure AD DS-avisering.

När den hanterade domänen är aktive rad uppdateras den hanterade domänens hälsa automatiskt inom två timmar och aviseringen tas bort.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: flyttade prenumerations kataloger

### <a name="alert-message"></a>Aviserings meddelande

*Prenumerationen som används av Azure AD Domain Services har flyttats till en annan katalog. Azure AD Domain Services måste ha en aktiv prenumeration i samma katalog för att fungera korrekt.*

### <a name="resolution"></a>Lösning

Azure AD DS kräver en aktiv prenumeration och kan inte flyttas till en annan prenumeration. Om den Azure-prenumeration som den hanterade domänen var kopplad till flyttas flyttar du tillbaka prenumerationen till föregående katalog eller [tar bort din hanterade domän](delete-aadds.md) från den befintliga katalogen och [skapar en ersättande hanterad domän i den valda prenumerationen](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: det går inte att hitta resurser för din hanterade domän

### <a name="alert-message"></a>Aviserings meddelande

*En resurs som används för din hanterade domän har tagits bort. Den här resursen krävs för att Azure AD Domain Services ska fungera korrekt.*

### <a name="resolution"></a>Lösning

Azure AD DS skapar ytterligare resurser för att fungera korrekt, till exempel offentliga IP-adresser, virtuella nätverks gränssnitt och en belastningsutjämnare. Om någon av dessa resurser tas bort är den hanterade domänen i ett tillstånd som inte stöds och hindrar domänen från att hanteras. Mer information om dessa resurser finns i [nätverks resurser som används av Azure AD DS](network-considerations.md#network-resources-used-by-azure-ad-ds).

Den här aviseringen skapas när en av de nödvändiga resurserna tas bort. Om resursen har tagits bort under 4 timmar sedan, finns det en risk att Azure-plattformen automatiskt kan återskapa den borttagna resursen. Följande steg beskriver hur du kontrollerar hälso status och tidsstämpel för borttagning av resurser:

1. Sök efter och välj **domän tjänster** i Azure Portal. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. I det vänstra navigerings fältet väljer du **hälsa**.
1. På sidan hälsa väljer du aviseringen med ID- *AADDS109*.
1. Aviseringen har en tidsstämpel för första gången den hittades. Om tidsstämpeln är mindre än 4 timmar sedan kan Azure-plattformen automatiskt återskapa resursen och lösa själva aviseringen.

    Om aviseringen är mer än 4 timmar gammal, är den hanterade domänen i ett oåterkalleligt tillstånd. [Ta bort den hanterade domänen](delete-aadds.md) och [skapa sedan en ersättande hanterad domän](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: det undernät som är associerat med din hanterade domän är fullt

### <a name="alert-message"></a>Aviserings meddelande

*Det undernät som valts för distribution av Azure AD Domain Services är fullt och det finns inget utrymme för den ytterligare domänkontrollant som behöver skapas.*

### <a name="resolution"></a>Lösning

Det virtuella nätverkets undernät för Azure AD DS behöver tillräckligt med IP-adresser för de automatiskt skapade resurserna. Det här IP-adressutrymmet innehåller behovet av att skapa ersättnings resurser om det finns en underhålls händelse. Du kan minimera risken för att få slut på tillgängliga IP-adresser genom att inte distribuera ytterligare resurser, t. ex. dina egna virtuella datorer, i samma undernät för virtuella nätverk som den hanterade domänen.

Det här felet kan inte återställas. Om du vill lösa aviseringen [tar du bort din befintliga hanterade domän](delete-aadds.md) och återskapar den. Om du har problem med att ta bort den hanterade domänen [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: tjänstens huvud namn tillåts inte

### <a name="alert-message"></a>Aviserings meddelande

*Ett huvud namn för tjänsten som Azure AD Domain Services använder för att betjäna din domän har inte behörighet att hantera resurser i Azure-prenumerationen. Tjänstens huvud namn måste ha behörighet att betjäna din hanterade domän.*

### <a name="resolution"></a>Lösning

Vissa automatiskt genererade tjänstens huvud namn används för att hantera och skapa resurser för en hanterad domän. Om åtkomst behörigheterna för något av dessa tjänst huvud namn ändras kan inte domänen hantera resurser på rätt sätt. Följande steg visar hur du förstår och ger åtkomst behörighet till ett huvud namn för tjänsten:

1. Läs om [rollbaserad åtkomst kontroll i Azure och hur du beviljar åtkomst till program i Azure Portal](../role-based-access-control/role-assignments-portal.md).
2. Granska åtkomsten till tjänstens huvud namn med ID- *abba844e-bc0e-44b0-947a-dc74e5d09022* och bevilja åtkomst som nekades vid ett tidigare datum.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: inte tillräckligt med IP-adress i den hanterade domänen

### <a name="alert-message"></a>Aviserings meddelande

*Vi har identifierat att under nätet för det virtuella nätverket i den här domänen kanske inte har tillräckligt med IP-adresser. Azure AD Domain Services behöver minst två tillgängliga IP-adresser inom under nätet som den är aktive rad i. Vi rekommenderar att ha minst 3-5 extra IP-adresser i under nätet. Detta kan ha inträffat om andra virtuella datorer distribueras i under nätet, så att antalet tillgängliga IP-adresser förbrukas, eller om det finns en begränsning för antalet tillgängliga IP-adresser i under nätet.*

### <a name="resolution"></a>Lösning

Det virtuella nätverkets undernät för Azure AD DS behöver tillräckligt med IP-adresser för de automatiskt skapade resurserna. Det här IP-adressutrymmet innehåller behovet av att skapa ersättnings resurser om det finns en underhålls händelse. Du kan minimera risken för att få slut på tillgängliga IP-adresser genom att inte distribuera ytterligare resurser, t. ex. dina egna virtuella datorer, i samma undernät för virtuella nätverk som den hanterade domänen.

För att lösa den här aviseringen tar du bort din befintliga hanterade domän och återskapar den i ett virtuellt nätverk med ett stort tillräckligt IP-adressintervall. Den här processen kan störas eftersom den hanterade domänen inte är tillgänglig och eventuella anpassade resurser som du har skapat som organisationsenheter eller tjänst konton går förlorade.

1. [Ta bort den hanterade domänen](delete-aadds.md) från katalogen.
1. Om du vill uppdatera det virtuella nätverkets IP-adressintervall söker du efter och väljer *virtuella nätverk* i Azure Portal. Välj det virtuella nätverket för den hanterade domän som har det lilla IP-adressintervallet.
1. Under **Inställningar** väljer du *adress utrymme*.
1. Uppdatera adress intervallet genom att välja det befintliga adress intervallet och redigera det eller lägga till ytterligare ett adress intervall. Kontrol lera att det nya IP-adressintervallet är tillräckligt stort för den hanterade domänens under näts intervall. **Spara** ändringarna när du är klar.
1. Välj **undernät** i det vänstra navigerings fältet.
1. Välj det undernät som du vill redigera eller skapa ett ytterligare undernät.
1. Uppdatera eller ange ett stort tillräckligt IP-adressintervall och **Spara** sedan ändringarna.
1. [Skapa en ersättande hanterad domän](tutorial-create-instance.md). Se till att du väljer det uppdaterade virtuella nätverkets undernät med ett tillräckligt stort IP-adressintervall.

Den hanterade domänens hälsa uppdateras automatiskt inom två timmar och aviseringen tas bort.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: resurserna kan inte återställas

### <a name="alert-message"></a>Aviserings meddelande

*De resurser som används av Azure AD Domain Services har upptäckts i ett oväntat tillstånd och kan inte återställas.*

### <a name="resolution"></a>Lösning

Det här felet kan inte återställas. Om du vill lösa aviseringen [tar du bort din befintliga hanterade domän](delete-aadds.md) och återskapar den. Om du har problem med att ta bort den hanterade domänen [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

## <a name="aadds114-subnet-invalid"></a>AADDS114: undernät är ogiltigt

### <a name="alert-message"></a>Aviserings meddelande

*Det undernät som valts för distribution av Azure AD Domain Services är ogiltigt och kan inte användas.*

### <a name="resolution"></a>Lösning

Det här felet kan inte återställas. Om du vill lösa aviseringen [tar du bort din befintliga hanterade domän](delete-aadds.md) och återskapar den. Om du har problem med att ta bort den hanterade domänen [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

## <a name="aadds115-resources-are-locked"></a>AADDS115: resurserna är låsta

### <a name="alert-message"></a>Aviserings meddelande

*En eller flera av de nätverks resurser som används av den hanterade domänen kan inte användas eftersom mål omfånget har låsts.*

### <a name="resolution"></a>Lösning

Resurs lås kan tillämpas på Azure-resurser för att förhindra ändringar eller borttagningar. Eftersom Azure AD DS är en hanterad tjänst behöver Azure-plattformen möjlighet att göra konfigurations ändringar. Om ett resurs lås används på vissa Azure AD DS-komponenter kan Azure-plattformen inte utföra sina hanterings uppgifter.

Utför följande steg för att kontrol lera om det finns resurs lås på Azure AD DS-komponenterna och ta bort dem:

1. För var och en av de hanterade domänens nätverks komponenter i resurs gruppen, till exempel virtuellt nätverk, nätverks gränssnitt eller offentlig IP-adress, kontrollerar du åtgärds loggarna i Azure Portal. I dessa åtgärds loggar anges varför en åtgärd misslyckades och var ett resurs lås används.
1. Välj den resurs där ett lås används och välj och ta bort låsen under **Lås**.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: resurserna är oanvändbara

### <a name="alert-message"></a>Aviserings meddelande

*En eller flera av de nätverks resurser som används av den hanterade domänen kan inte användas på grund av princip begränsningar.*

### <a name="resolution"></a>Lösning

Principer tillämpas på Azure-resurser och resurs grupper som styr vilka konfigurations åtgärder som tillåts. Eftersom Azure AD DS är en hanterad tjänst behöver Azure-plattformen möjlighet att göra konfigurations ändringar. Om en princip tillämpas på vissa Azure AD DS-komponenter kanske inte Azure-plattformen kan utföra sina hanterings uppgifter.

Utför följande steg för att kontrol lera om det finns tillämpade principer på Azure AD DS-komponenterna och uppdatera dem:

1. För var och en av de hanterade domänens nätverks komponenter i resurs gruppen, till exempel virtuellt nätverk, NIC eller offentlig IP-adress, kontrollerar du åtgärds loggarna i Azure Portal. Dessa åtgärds loggar anger varför en åtgärd inte kan utföras och var en restriktiv princip tillämpas.
1. Välj den resurs där en princip tillämpas, Välj och redigera principen, under **principer**, så att den är mindre restriktiv.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: synkroniseringen har inte slutförts på ett tag

### <a name="alert-message"></a>Aviserings meddelande

*Den hanterade domänen synkroniserades senast med Azure AD på [Date]. Användarna kanske inte kan logga in på den hanterade domänen eller grupp medlemskapen kanske inte är synkroniserade med Azure AD.*

### <a name="resolution"></a>Lösning

[Kontrol lera Azure AD DS-hälsan](check-health.md) för alla aviseringar som indikerar problem med konfigurationen av den hanterade domänen. Problem med nätverks konfigurationen kan blockera synkroniseringen från Azure AD. Om du kan lösa aviseringar som indikerar ett konfigurations problem väntar du två timmar och kontrollerar tillbaka för att se om synkroniseringen har slutförts.

Följande vanliga orsaker leder till att synkronisering stoppas i en hanterad domän:

* Nödvändig nätverks anslutning har blockerats. Mer information om hur du kontrollerar det virtuella Azure-nätverket efter problem och vad som krävs finns i [Felsöka nätverks säkerhets grupper](alert-nsg.md) och [nätverks kraven för Azure AD DS](network-considerations.md).
*  Lösenordssynkronisering har inte kon figurer ATS eller slutförts när den hanterade domänen distribuerades. Du kan ställa in Lösenordssynkronisering för [endast molnbaserade användare](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) eller [Hybrid användare från lokal](tutorial-configure-password-hash-sync.md).

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: en säkerhets kopia har inte gjorts på ett tag

### <a name="alert-message"></a>Aviserings meddelande

*Den hanterade domänen säkerhetskopierades senast [datum].*

### <a name="resolution"></a>Lösning

[Kontrol lera Azure AD DS-hälsan](check-health.md) för aviseringar som indikerar problem med konfigurationen av den hanterade domänen. Problem med nätverks konfigurationen kan blockera Azure-plattformen från att ta säkerhets kopior. Om du kan lösa aviseringar som indikerar ett konfigurations problem väntar du två timmar och kontrollerar tillbaka för att se om synkroniseringen har slutförts.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: SUS Pension på grund av inaktive rad prenumeration

### <a name="alert-message"></a>Aviserings meddelande

*Den hanterade domänen har pausats eftersom den Azure-prenumeration som är associerad med domänen inte är aktiv.*

### <a name="resolution"></a>Lösning

> [!WARNING]
> Om en hanterad domän har inaktiverats under en längre tid, finns det en risk för att den tas bort. Lös orsaken till uppskjutningen så snabbt som möjligt. Mer information finns i [förstå pausade tillstånd för Azure AD DS](suspension.md).

Azure AD DS kräver en aktiv prenumeration. Om den Azure-prenumeration som den hanterade domänen var kopplad till inte är aktiv, måste du förnya den för att återaktivera prenumerationen.

1. [Förnya din Azure-prenumeration](../cost-management-billing/manage/subscription-disabled.md).
2. När prenumerationen har förnyats kan du återaktivera den hanterade domänen med en Azure AD DS-avisering.

När den hanterade domänen är aktive rad uppdateras den hanterade domänens hälsa automatiskt inom två timmar och aviseringen tas bort.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: SUS Pension på grund av en ogiltig konfiguration

### <a name="alert-message"></a>Aviserings meddelande

*Den hanterade domänen har pausats på grund av en ogiltig konfiguration. Tjänsten har inte kunnat hantera, korrigera eller uppdatera domän kontrol Lanterna för din hanterade domän under en längre tid.*

### <a name="resolution"></a>Lösning

> [!WARNING]
> Om en hanterad domän har inaktiverats under en längre tid, finns det en risk för att den tas bort. Lös orsaken till uppskjutningen så snabbt som möjligt. Mer information finns i [förstå pausade tillstånd för Azure AD DS](suspension.md).

[Kontrol lera Azure AD DS-hälsan](check-health.md) för aviseringar som indikerar problem med konfigurationen av den hanterade domänen. Om du kan lösa aviseringar som indikerar ett konfigurations problem väntar du två timmar och kontrollerar tillbaka för att se om synkroniseringen har slutförts. När du är klar [öppnar du en support förfrågan för Azure][azure-support] för att återaktivera den hanterade domänen.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md