---
title: Kom igång med Azure AD-autentisering med hjälp av Azure Portal | Microsoft Docs
description: Lär dig hur du använder Azure Portal för att komma åt Azure Active Directory (Azure AD)-autentisering för att använda Azure Media Services API.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d9ad439fe3f41d9e2634fbf9a76cfd21114a5dbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013065"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Kom igång med Azure AD-autentisering med Azure-portalen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Lär dig hur du använder Azure Portal för att komma åt Azure Active Directory-autentisering (Azure AD) för att få åtkomst till Azure Media Services API.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Om du inte har ett konto börjar du med en [kostnads fri utvärderings version av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services konto med hjälp av Azure Portal](media-services-portal-create-account.md).

När du använder Azure AD-autentisering med Azure Media Services har du två autentiserings alternativ:

- **Autentisering av tjänstens huvud namn**. Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, tjänster på mellan nivå eller schemalagda jobb: webbappar, funktions program, Logic Apps, API: er eller en mikrotjänst.
- **Användarautentisering**. Autentisera en person som använder appen för att interagera med Media Services resurser. Det interaktiva programmet bör först uppmana användaren att ange autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller liveuppspelning. 

## <a name="access-the-media-services-api"></a>Åtkomst till Media Services-API:n

På den här sidan kan du välja den autentiseringsmetod som du vill använda för att ansluta till API: et. Sidan innehåller också de värden som du behöver för att ansluta till API: et.

1. I [Azure Portal](https://portal.azure.com/)väljer du ditt Media Services-konto.
2. Välj hur du vill ansluta till Media Services API.
3. Under **Anslut till Media Services-API** väljer du den Media Services API-version som du vill ansluta till.

## <a name="service-principal-authentication--recommended"></a>Autentisering av tjänstens huvud namn (rekommenderas)

Autentiserar en tjänst med hjälp av en Azure Active Directory-app (Azure AD) och hemlighet. Detta rekommenderas för alla tjänster på mellan nivå som anropar Media Services-API: et. Exempel är Web Apps, functions, Logic Apps, API: er och mikrotjänster. Detta är den rekommenderade autentiseringsmetoden.

### <a name="manage-your-azure-ad-app-and-secret"></a>Hantera din Azure AD-App och hemlighet

I avsnittet **Hantera din AAD-app och hemlighet** kan du välja eller skapa en ny Azure AD-App och generera en hemlighet. Av säkerhets synpunkt kan inte hemligheten visas efter att bladet har stängts. Programmet använder program-ID och hemlighet för autentisering för att hämta en giltig token för Media Services.

Kontrol lera att du har behörighet att registrera ett program med din Azure AD-klient och tilldela programmet till en roll i din Azure-prenumeration. Mer information finns i [nödvändig behörighet](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

### <a name="connect-to-media-services-api"></a>Anslut till Media Services API

Med **API: et för anslutning till Media Services** får du värden som du använder för att ansluta ditt tjänst huvud program. Du kan hämta text värden eller kopiera JSON-eller XML-blocken.

## <a name="user-authentication"></a>Användarautentisering

Det här alternativet kan användas för att autentisera en medarbetare eller medlem av en Azure Active Directory som använder en app för att interagera med Media Services resurser. Det interaktiva programmet bör först uppmana användaren att ange användarens autentiseringsuppgifter. Den här autentiseringsmetoden bör endast användas för hanterings program.

### <a name="connect-to-media-services-api"></a>Anslut till Media Services API

Kopiera dina autentiseringsuppgifter för att ansluta ditt användar program från avsnittet **Anslut till Media Services-API** . Du kan hämta text värden eller kopiera JSON-eller XML-blocken.

## <a name="next-steps"></a>Nästa steg

Kom igång med [att ladda upp filer till ditt konto](media-services-portal-upload-files.md).
