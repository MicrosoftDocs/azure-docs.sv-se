---
title: Azure Enterprise-överföringar
description: Beskriver Azure EA-överföringar
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: banders
ms.openlocfilehash: a770fe2362e1e1c98c683f694f7f834ac6f4ea6c
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836861"
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

Det här avsnittet tillhandahålls endast i informationssyfte eftersom åtgärden inte kan utföras av en företagsadministratör. Det krävs en supportbegäran för att överföra en företagsregistrering till en ny.

När du begär att en hel företagsregistrering ska överföras till en ny registrering utförs följande åtgärder:

- Alla tjänster, prenumerationer, konton, avdelningar och hela registreringsstrukturen, inklusive alla EA-avdelningsadministratörer, överförs till en ny målregistrering.
- Registreringsstatus ställs in som _Överförd_. Den överförda registreringen är bara tillgänglig för rapporter om historisk användning.
- Du kan inte lägga till roller eller prenumerationer i en överförd registrering. Statusen Överförd förhindrar ytterligare användning mot registreringen.
- Eventuellt återstående saldo för en Azure-förskottsbetalning i avtalet går förlorad, inklusive framtida perioder.
-    Om registreringen du överför från innehåller RI-köp blir inköpspriset för RI kvar källregistreringen, men alla RI-förmåner överförs för användning i den nya registreringen.
-    Engångsavgiften för köp på Marketplace och alla månatliga fasta avgifter som redan påförts på den gamla registreringen överförs inte till den nya registreringen. Förbrukningsbaserade Marketplace-debiteringar överförs.

### <a name="effective-transfer-date"></a>Effektivt överföringsdatum

Den effektiva överföringsdagen kan vara på eller efter startdatumet för målregistreringen.

Användning i källregistreringen debiteras mot Azure-förskottsbetalningen eller som överförbrukning. Användning efter det effektiva överföringsdatumet överförs till den nya registreringen och debiteras där.

### <a name="prerequisites"></a>Krav

När du begär överföring av en registrering ska du ange följande information:

- För källregistreringen, registreringsnumret.
- För målregistreringen, registreringsnumret som överföringen ska göras till.
- Det effektiva datumet för överföringen av registreringen kan vara målregistreringens startdatum eller något senare datum. Det valda datumet påverkar inte användningen i eventuella fakturor för överförbrukning som redan har utfärdats.

Här är några andra saker du bör tänka på innan du överför en registrering:

- Det krävs godkännande från EA-administratörer för både mål- och källregistrering.
- Om överföring av en registrering inte uppfyller dina behov kan du överväga en kontoöverföring.
- Källregistreringens status uppdateras till Överförd och är bara tillgänglig för rapportering av tidigare användning.

### <a name="azure-prepayment"></a>Azure-förskottsbetalning

Azure-förskottsbetalningar kan inte överföras mellan registreringar. Saldot för en Azure-förskottsbetalning är avtalsmässigt bunden till den registrering där den beställdes. Azure-förskottsbetalningar överförs inte när konton eller registreringar överförs.

### <a name="no-services-affected-for-account-and-enrollment-transfers"></a>Inga tjänster påverkas av konto- och registreringsöverföringar

Det förekommer inga avbrott under en konto- eller registreringsöverföring. De kan genomföras samma dag som förfrågan inkommer om du anger all nödvändig information.

## <a name="change-account-owner"></a>Ändra kontoägare

Du kan överföra prenumerationer från en kontoinnehavare till en annan i Azure EA-portalen. Mer information finns i [Ändra kontoinnehavare](ea-portal-get-started.md#change-account-owner).

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