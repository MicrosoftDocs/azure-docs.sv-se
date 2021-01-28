---
title: Azure Enterprise-överföringar
description: Beskriver Azure EA-överföringar
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 7aa57fa20c3a043cdb210ccd8a5ddbf61323716d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943684"
---
# <a name="azure-enterprise-transfers"></a>Azure Enterprise-överföringar

Den här artikeln innehåller en översikt för Enterprise-överföringar.

## <a name="transfer-an-enterprise-account-to-a-new-enrollment"></a>Överföra ett företagskonto till en ny registrering

Vid en kontoöverföring flyttas en kontoägare från en registrering till en annan. Alla relaterade prenumerationer under kontoägaren flyttas till målregistreringen. Använd en kontoöverföring om det finns flera aktiva registreringar och du bara vill flytta specifika kontoägare.

Det här avsnittet tillhandahålls endast i informationssyfte eftersom åtgärden inte kan utföras av en företagsadministratör. Det krävs en supportbegäran för att överföra ett företagskonto till en ny registrering.

Tänk på följande när du ska överföra ett företagskonto till en ny registrering:

- Det är bara de konton som anges i förfrågan som överförs. Om du väljer alla konton så överförs samtliga.
- Källregistreringen behåller statusen aktiv eller utvidgad. Du kan fortsätta att använda registreringen tills den upphör att gälla.

### <a name="prerequisites"></a>Krav

När du begär en kontoöverföring ska du ange följande information:

- Numret på målregistreringen, kontonamnet och e-postadressen för kontoägaren för kontot som ska överföras
- För källregistreringen, registreringsnumret och kontot som ska överföras

Här är några andra saker du bör tänka på innan en kontoöverföring:

- Du behöver godkännande från en EA-administratör för mål- och källregistreringen
- Om en kontoöverföring inte uppfyller dina behov kan du överväga en registreringsöverföring.
- Kontoöverföringen överför alla tjänster och prenumerationer som tillhör aktuella konton.
- När överföringen är klar visas det överförda kontot som inaktivt under källregistreringen och som aktivt under målregistreringen.
- Kontot visar ett slutdatum som motsvarar aktuellt överföringsdatum för källregistreringen och startdatum för målregistreringen.
- All användning som utförts på kontot innan det aktuella överföringsdatumet finns kvar under källregistreringen.

## <a name="transfer-enterprise-enrollment-to-a-new-one"></a>Överföra en företagsregistrering till en ny registrering

En registreringsöverföring är lämpligt när:

- Förskottsbetalningsperioden för en aktuell registrering har gått ut.
- En registrering har status som förfallen/förlängd och ett nytt avtal förhandlas.
- Du har flera registreringar och vill kombinera alla konton och faktureringen under en enda registrering.

Det här avsnittet tillhandahålls endast i informationssyfte eftersom åtgärden inte kan utföras av en företagsadministratör. Det krävs en supportbegäran för att överföra en företags registrering till en ny, om inte registreringen är kvalificerad för [automatisk registrering](#auto-enrollment-transfer).

När du begär att en hel företagsregistrering ska överföras till en ny registrering utförs följande åtgärder:

- Alla tjänster, prenumerationer, konton, avdelningar och hela registreringsstrukturen, inklusive alla EA-avdelningsadministratörer, överförs till en ny målregistrering.
- Registreringsstatus ställs in som _Överförd_. Den överförda registreringen är bara tillgänglig för rapporter om historisk användning.
- Du kan inte lägga till roller eller prenumerationer i en överförd registrering. Överförd status förhindrar mer användning mot registreringen.
- Eventuellt återstående saldo för en Azure-förskottsbetalning i avtalet går förlorad, inklusive framtida perioder.
-    Om registreringen som du överför från har RI-inköp behålls inköps avgiften för RI i käll registreringen, men alla RI-förmåner överförs för användning i den nya registreringen.
-    Inköps avgiften för Marketplace engångs kostnad och eventuella månatliga fasta avgifter som redan har påförts för den gamla registreringen överförs inte till den nya registreringen. Förbrukningsbaserade Marketplace-debiteringar överförs.

### <a name="effective-transfer-date"></a>Effektivt överföringsdatum

Den effektiva överföringsdagen kan vara på eller efter startdatumet för målregistreringen.

Användning i källregistreringen debiteras mot Azure-förskottsbetalningen eller som överförbrukning. Användning som inträffar efter det effektiva överförings datumet överförs till den nya registreringen och debiteras.

### <a name="prerequisites"></a>Krav

När du begär överföring av en registrering ska du ange följande information:

- För källregistreringen, registreringsnumret.
- För målregistreringen, registreringsnumret som överföringen ska göras till.
- Det effektiva datumet för överföringen av registreringen kan vara målregistreringens startdatum eller något senare datum. Det valda datumet kan inte påverka användningen av eventuella överförbruknings fakturor som redan har utfärdats.

Här är några andra saker du bör tänka på innan du överför en registrering:

- Det krävs godkännande från EA-administratörer för både mål- och källregistrering.
- Om överföring av en registrering inte uppfyller dina behov kan du överväga en kontoöverföring.
- Källregistreringens status uppdateras till Överförd och är bara tillgänglig för rapportering av tidigare användning.

### <a name="auto-enrollment-transfer"></a>Överföring av automatisk registrering

Du kan se att en registrering har **överfört** tillstånd, även om du inte har skickat ett support ärende för att begära en registrerings överföring. Det **överförda** status resultatet från den automatiska registreringen av överförings processen. För att överförings processen för automatisk registrering ska ske under förnyelse frasen finns det några objekt som måste ingå i det nya avtalet:

- Tidigare registrerings nummer (det måste finnas i EA-portalen)
- Utgångs datumet för det tidigare registrerings numret är en dag innan det nya avtalets effektiva start datum
- Det nya avtalet har ett fakturerat förskotts betalnings avtal för Azure som har ett aktuellt datum eller som har fått ett nytt datum
- Den nya registreringen skapas i EA-portalen

Om det inte finns några saknade användnings data i EA-portalen mellan den tidigare registreringen och den nya registreringen behöver du inte skapa ett överförings support ärende.

### <a name="azure-prepayment"></a>Azure-förskottsbetalning

Azure-förskottsbetalningar kan inte överföras mellan registreringar. Saldot för en Azure-förskottsbetalning är avtalsmässigt bunden till den registrering där den beställdes. Azure-förskottsbetalningar överförs inte när konton eller registreringar överförs.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Inga tjänster påverkas av konto- och registreringsöverföringar

Det förekommer inga avbrott under en konto- eller registreringsöverföring. De kan genomföras samma dag som förfrågan inkommer om du anger all nödvändig information.

## <a name="transfer-an-enterprise-subscription-to-a-pay-as-you-go-subscription"></a>Överföra en Enterprise-prenumeration till en Betala per användning-prenumeration

Om du vill överföra en Enterprise-prenumeration till en enskild prenumeration med Betala per användning-priser måste du skapa en ny supportbegäran i Azure Enterprise-portalen. Du skapar en supportbegäran genom att välja **+ Ny supportbegäran** i området **Hjälp och support**.

## <a name="change-azure-subscription-or-account-ownership"></a>Ändra Azure-prenumeration eller -kontoinnehavare

Du kan överföra prenumerationer från en kontoinnehavare till en annan i Azure EA-portalen. Mer information finns i [Ändra Azure-prenumeration eller -kontoinnehavare](ea-portal-administration.md#change-azure-subscription-or-account-ownership).

## <a name="subscription-transfer-effects"></a>Åtgärder som utförs vid överföring av prenumerationer

När du överför en Azure-prenumeration till ett konto i samma klientorganisation i Azure Active Directory så behåller alla användare, grupper och huvudnamn för tjänster med [rollbaserad åtkomst (Azure RBAC)](../../role-based-access-control/overview.md) för hantering av resurser sin åtkomst.

Så här visar du användare med rollbaserad åtkomst för prenumerationen:

1. Öppna **Prenumerationer** i Azure-portalen.
2. Välj den prenumeration du vill visa och välj sedan **Åtkomstkontroll (IAM)** .
3. Välj **Rolltilldelningar**. På sidan med rolltilldelningar ser du alla användare som har rollbaserad åtkomst för prenumerationen.

Om prenumerationen överförs till ett konto i en annan klientorganisation i Azure AD så kommer alla användare, grupper och huvudnamn för tjänster med [rollbaserad åtkomst (RBAC)](../../role-based-access-control/overview.md) för hantering av resurser att _förlora_ sin behörighet. Även utan rollbaserad åtkomst kan användare få åtkomst till prenumerationen via olika säkerhetsmekanismer. Här är några exempel:

- Hanteringscertifikat som ger användaren administratörsbehörighet till prenumerationsresurser. Mer information finns i [Skapa och ladda upp ett hanteringscertifikat för Azure](../../cloud-services/cloud-services-certs-create.md).
- Åtkomstnycklar för tjänster som Storage. Mer information finns i [kontoöversikten för Azure Storage](../../storage/common/storage-account-overview.md).
- Autentiseringsuppgifter för fjärråtkomst för tjänster som Azure Virtual Machines.

Om mottagaren behöver begränsa åtkomsten till sina Azure-resurser bör denne överväga att uppdatera eventuella hemligheter som är associerade med tjänsten. De flesta resurser kan uppdateras med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj **Alla resurser** i hubbmenyn.
3. Välj resursen.
4. Välj **Inställningar** på resurssidan för att visa och uppdatera befintliga hemligheter.

## <a name="next-steps"></a>Nästa steg

- Om du behöver hjälp med att felsöka problem med Azure EA-portalen kan du läsa [Felsöka åtkomst till Azure EA-portalen](ea-portal-troubleshoot.md).