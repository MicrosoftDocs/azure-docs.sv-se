---
title: Välj bort verifierbara autentiseringsuppgifter (förhands granskning)
description: Lär dig att välja för hands versionen av verifierbara autentiseringsuppgifter
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 93c493a9b9941913da270fe763175240c8abf9f3
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106170215"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Välj bort verifierbara autentiseringsuppgifter (förhands granskning)

I den här artikeln:

- Anledningen till varför du kan behöva avanmäla dig.
- De steg som krävs.
- Vad händer med dina data?
- Påverka befintliga verifierbara autentiseringsuppgifter.

> [!IMPORTANT]
> Azure Active Directory verifierbara autentiseringsuppgifter finns för närvarande i en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

- Slutför registrering av verifierbara autentiseringsuppgifter.

## <a name="potential-reasons-for-opting-out"></a>Möjliga orsaker till väljer

För tillfället har vi inte möjlighet att göra ändringar i domän informationen. Det innebär att om du gör ett misstag eller bestämmer dig för att göra en ändring, finns det inget annat alternativ tillgängligt förutom att väljer ut och starta igen.

## <a name="the-steps-required"></a>De steg som krävs

1. Från Azure Portal söka efter verifierbara autentiseringsuppgifter.
2. Välj **Inställningar** på menyn till vänster.
3. Under avsnittet **Återställ din organisation**, väljer du **ta bort alla autentiseringsuppgifter och** avmarkerar inte för hands versionen.

   ![inställningar Återställ org](media/how-to-opt-out/settings-reset.png)

4. Läs varnings meddelandet och fortsätt välja **ta bort och Avanmäl dig**.

   ![ta bort inställningar och Avanmäl](media/how-to-opt-out/delete-and-opt-out.png)

Du har nu valt att visa för hands versionen av verifierbara autentiseringsuppgifter. Fortsätt läsa för att förstå vad som händer under huven.

## <a name="what-happens-to-your-data"></a>Vad händer med dina data?

När du har slutfört väljer av tjänsten Azure Active Directory verifierbara autentiseringsuppgifter kommer följande åtgärder att utföras:

- Nycklarna i Key Vault kommer att tas [bort](../../key-vault/general/soft-delete-overview.md).
- Issuer-objektet tas bort från vår databas.
- Klient organisationen identifierare tas bort från vår databas. 
- Alla kontrakts objekt tas bort från vår databas.

När en avanmälning har skett kan du inte återställa dina genomförda eller utföra åtgärder på din verksamhet. Det här steget är en enkelriktad åtgärd och du måste välja igen, vilket resulterar i att nya skapas.  

## <a name="effect-on-existing-verifiable-credentials"></a>Påverka befintliga verifierbara autentiseringsuppgifter.

Alla verifierbara autentiseringsuppgifter som redan har utfärdats fortsätter att existera. De kommer inte att vara kryptografiskt kryptografiska eftersom du fortfarande kommer att kunna matchas via Jon.
Men när förlitande parter anropar status-API: et, kommer de alltid att få tillbaka ett fel meddelande.

## <a name="next-steps"></a>Nästa steg

- Konfigurera verifierbara autentiseringsuppgifter på din [Azure-klient](get-started-verifiable-credentials.md)