---
title: Kom igång med Azure AD-autentisering
description: Lär dig hur du kommer åt Azure Active Directory (Azure AD)-autentisering för att använda Azure Media Services API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/31/2021
ms.author: inhenkel
ms.openlocfilehash: e45a0eefff3a6a7f7dd37bde3ee3bffaedd93dcd
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166623"
---
# <a name="get-credentials-to-access-media-services-api"></a>Hämta autentiseringsuppgifter för åtkomst Media Services-API

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När du använder Azure AD-autentisering för att komma åt Azure Media Services-API: et har du två autentiseringsalternativ:

- **Autentisering av tjänstens huvud namn** (rekommenderas)

    Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, tjänster på mellan nivå eller schemalagda jobb: webbappar, funktions program, Logic Apps, API: er eller en mikrotjänst.
- **Användarautentisering**

    Autentisera en person som använder appen för att interagera med Media Services resurser. Det interaktiva programmet bör först uppmana användaren att ange autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller liveuppspelning. 

I den här artikeln beskrivs steg för att få åtkomst till Media Services API. Välj bland följande flikar.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har ett konto börjar du med en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/).
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services konto med hjälp av Azure Portal](account-create-how-to.md).

## <a name="portal"></a>[Portal](#tab/portal/)

### <a name="api-access"></a>API-åtkomst

På sidan **API-åtkomst** kan du välja den autentiseringsmetod som du vill använda för att ansluta till API: et. Sidan innehåller också de värden som du behöver för att ansluta till API: et.

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt Media Services-konto.
2. Välj bladet **API-åtkomst** i det vänstra navigerings fältet.
3. Under **Anslut till Media Services-API** väljer du den Media Services API-version som du vill ansluta till (v3 är den senaste versionen av tjänsten).

### <a name="service-principal-authentication--recommended"></a>Autentisering av tjänstens huvud namn (rekommenderas)

Autentiserar en tjänst med hjälp av en Azure Active Directory-app (Azure AD) och hemlighet. Detta rekommenderas för alla tjänster på mellan nivå som anropar Media Services-API: et. Exempel är Web Apps, functions, Logic Apps, API: er och mikrotjänster. Detta är den rekommenderade autentiseringsmetoden.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Hantera din Azure AD-App och hemlighet

I avsnittet **Hantera din AAD-app och hemlighet** kan du välja eller skapa en ny Azure AD-App och generera en hemlighet. Av säkerhets synpunkt kan inte hemligheten visas efter att bladet har stängts. Programmet använder program-ID och hemlighet för autentisering för att hämta en giltig token för Media Services.

Kontrol lera att du har behörighet att registrera ett program med din Azure AD-klient och tilldela programmet till en roll i din Azure-prenumeration. Mer information finns i [nödvändig behörighet](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

#### <a name="connect-to-media-services-api"></a>Anslut till Media Services API

Med **API: et för anslutning till Media Services** får du värden som du använder för att ansluta ditt tjänst huvud program. Du kan hämta text värden eller kopiera JSON-eller XML-blocken.

### <a name="user-authentication"></a>Användarautentisering

Det här alternativet kan användas för att autentisera en medarbetare eller medlem av en Azure Active Directory som använder en app för att interagera med Media Services resurser. Det interaktiva programmet bör först uppmana användaren att ange användarens autentiseringsuppgifter. Den här autentiseringsmetoden bör endast användas för hanterings program.

#### <a name="connect-to-media-services-api"></a>Anslut till Media Services API

Kopiera dina autentiseringsuppgifter för att ansluta ditt användar program från avsnittet **Anslut till Media Services-API** . Du kan hämta text värden eller kopiera JSON-eller XML-blocken.

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Nästa steg

[Självstudie: Ladda upp, koda och strömma videor med Media Services v3](stream-files-tutorial-with-api.md).
