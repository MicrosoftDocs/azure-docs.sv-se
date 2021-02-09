---
title: Samar beta med andra – QnA Maker
description: Lär dig att samar beta med andra författare och redigerare med hjälp av rollbaserad åtkomst kontroll i Azure.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/15/2020
ms.openlocfilehash: f43fa06cf93d383efcb0fe746de28ab143280e50
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988024"
---
# <a name="collaborate-with-other-authors-and-editors"></a>Samar beta med andra författare och redigerare

Samar beta med andra författare och redigerare som använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) som placeras på din QnA Maker-resurs.

## <a name="access-is-provided-on-the-qna-maker-resource"></a>Åtkomst finns på QnA Maker resursen

Alla behörigheter styrs av de behörigheter som har placerats på QnA Maker resursen. Behörigheterna anpassas till Läs-, Skriv-, publicerings-och fullständig åtkomst.

Den här Azure RBAC-funktionen innehåller:
* Azure Active Directory (AAD) är 100% bakåtkompatibel med nyckelbaserad autentisering för ägare och deltagare. Kunder kan använda antingen nyckelbaserad autentisering eller Azure RBAC-baserad autentisering i sina begär Anden.
* Lägg snabbt till författare och redigerare i alla kunskaps banker i resursen eftersom kontrollen finns på resurs nivå, inte på kunskaps bas nivån.

## <a name="access-is-provided-by-a-defined-role"></a>Åtkomst tillhandahålls av en definierad roll

[!INCLUDE [Azure RBAC permissions table](../includes/role-based-access-control.md)]

## <a name="authentication-flow"></a>Autentiseringsflöde

Följande diagram visar flödet från författarens perspektiv för att logga in på QnA Maker-portalen och använda redigerings-API: erna.

> [!div class="mx-imgBorder"]
> ![Följande diagram visar flödet från författarens perspektiv för att logga in på QnA Maker-portalen och använda redigerings-API: erna.](../media/qnamaker-how-to-collaborate-knowledge-base/rbac-flow-from-portal-to-service.png)

|Steg|Beskrivning|
|--|--|
|1|Portalen hämtar token för QnA Maker resurs.|
|2|Portalen anropar lämplig QnA Maker redigerings-API (APIM) som skickar token i stället för nycklar.|
|3|API för QnA Maker validerar token.|
|4 |API för QnA Maker anropar QnAMaker-tjänsten.|

Om du tänker anropa redigerings- [API: erna](../index.yml)kan du läsa mer om hur du konfigurerar autentisering.

## <a name="authenticate-by-qna-maker-portal"></a>Autentisera med QnA Maker Portal

Om du skapar och samarbetar med hjälp av QnA Maker Portal hanterar QnA Maker portalen alla åtkomst behörigheter när du har lagt till en lämplig roll till resursen för en medarbetare.

## <a name="authenticate-by-qna-maker-apis-and-sdks"></a>Autentisera med QnA Maker-API: er och SDK: er

Om du skapar och samarbetar med hjälp av API: er, antingen via REST eller SDK: er, måste du [skapa ett huvud namn för tjänsten](../../authentication.md#assign-a-role-to-a-service-principal) för att hantera autentiseringen.

## <a name="next-step"></a>Nästa steg

* Utforma en kunskaps bas för språk och för klient program
