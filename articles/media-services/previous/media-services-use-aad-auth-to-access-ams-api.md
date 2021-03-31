---
title: Åtkomst Azure Media Services-API med Azure Active Directory autentisering | Microsoft Docs
description: Lär dig mer om begrepp och steg som du bör vidta för att använda Azure Active Directory (Azure AD) för att autentisera åtkomst till Azure Media Services API.
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
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 3688f0f9266844ce584f3f2d2e1a7c39ed3e4f70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103009026"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Ansluta till API:et för Azure Media Services med Azure AD-autentisering 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure Media Services API är ett RESTful-API. Du kan använda den för att utföra åtgärder på medie resurser genom att använda en REST API eller genom att använda tillgängliga klient-SDK: er. Azure Media Services erbjuder ett Media Services klient-SDK för Microsoft .NET. Du måste autentiseras innan du kan få åtkomst till Media Services-resurser och Media Services-API:et. 

Media Services stöder [Azure Active Directory (Azure AD)-baserad autentisering](../../active-directory/fundamentals/active-directory-whatis.md). Tjänsten Azure Media REST kräver att användaren eller programmet som gör REST API begär Anden antingen har rollen **deltagare** eller **ägare** för att få åtkomst till resurserna. Mer information finns i [Vad är rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).  

Det här dokumentet ger en översikt över hur du får åtkomst till Media Services API med hjälp av REST-eller .NET-API: er.

> [!NOTE]
> Access Control auktorisering inaktuellt den 1 juni 2018.

## <a name="access-control"></a>Åtkomstkontroll

För att Azure Media REST-begäran ska lyckas måste den anropande användaren ha en deltagar-eller ägar roll för det Media Services konto som den försöker komma åt.  
Endast en användare med ägar rollen kan ge åtkomst till åtkomst till användare eller appar för åtkomst till medie resursen (konto). Deltagar rollen har enbart åtkomst till medie resursen.
Obehöriga begär Anden kan inte utföras med status koden 401. Om den här felkoden visas kontrollerar du om användaren har tilldelats rollen som deltagare eller ägare till användarens Media Services konto. Du kan kontrol lera detta i Azure Portal. Sök efter ditt medie konto och klicka sedan på fliken **åtkomst kontroll** . 

![Fliken åtkomst kontroll](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Typer av autentisering 
 
När du använder Azure AD-autentisering med Azure Media Services har du två autentiserings alternativ:

- **Användarautentisering**. Autentisera en person som använder appen för att interagera med Media Services resurser. Det interaktiva programmet bör först uppmana användaren att ange användarens autentiseringsuppgifter. Ett exempel är en hanteringskonsolapp som används av behöriga användare för att övervaka kodningsjobb eller liveuppspelning. 
- **Autentisering av tjänstens huvud namn**. Autentisera en tjänst. Program som ofta använder den här autentiseringsmetoden är appar som kör daemon-tjänster, tjänster på mellan nivå eller schemalagda jobb. Exempel är webbappar, Function-appar, Logic Apps, API och mikrotjänster.

### <a name="user-authentication"></a>Användarautentisering 

Program som ska använda metoden för användarautentisering är hantering eller övervakning av inbyggda appar: mobilappar, Windows-appar och konsol program. Den här typen av lösning är användbar när du vill ha mänsklig interaktion med tjänsten i något av följande scenarier:

- Övervaknings instrument panel för dina kodnings jobb.
- Övervaknings instrument panel för dina Live-strömmar.
- Hanterings program för Station ära eller mobila användare som administrerar resurser i ett Media Services konto.

> [!NOTE]
> Den här autentiseringsmetoden bör inte användas för program som riktas mot konsumenter. 

Ett internt program måste först hämta en åtkomsttoken från Azure AD och sedan använda det när du gör HTTP-begäranden till Media Services REST API. Lägg till åtkomsttoken i begär ande huvudet. 

Följande diagram visar ett typiskt program för autentisering av interaktiva program: 

![Diagram över interna appar](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

I föregående diagram representerar talen flödet för förfrågningarna i kronologisk ordning.

> [!NOTE]
> När du använder metoden för användarautentisering delar alla appar samma (standard) interna programs klient-ID och omdirigerings-URI för ursprungligt program. 

1. Fråga användaren om autentiseringsuppgifter.
2. Begär en Azure AD-åtkomsttoken med följande parametrar:  

   * Azure AD-klientens slut punkt.

       Klient informationen kan hämtas från Azure Portal. Placera markören över namnet på den inloggade användaren i det övre högra hörnet.
   * Media Services resurs-URI. 

       Denna URI är samma för Media Services konton som finns i samma Azure-miljö (till exempel https: \/ /rest.Media.Azure.net).

   * Media Services (inbyggt) program klient-ID.
   * Media Services (inbyggt) omdirigerings-URI.
   * Resurs-URI för REST-Media Services.
        
       URI: n representerar REST API slut punkten (till exempel https://test03.restv2.westus.media.azure.net/api/) .

     Information om hur du hämtar värden för dessa parametrar finns i [använda Azure Portal för att komma åt Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md) med alternativet autentisering av användare.

3. Azure AD-åtkomsttoken skickas till klienten.
4. Klienten skickar en begäran till Azure Media REST API med Azure AD-åtkomsttoken.
5. Klienten hämtar data från Media Services.

Information om hur du använder Azure AD-autentisering för att kommunicera med REST-begäranden med hjälp av Media Services .NET-klient-SDK finns i [använda Azure AD-autentisering för att få åtkomst till Media Services API med .net](media-services-dotnet-get-started-with-aad.md). 

Om du inte använder Media Services .NET-klient-SDK måste du manuellt skapa en begäran om Azure AD-åtkomsttoken med hjälp av de parametrar som beskrivs i steg 2. Mer information finns i [så här använder du Azure AD Authentication Library för att hämta Azure AD-token](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Program som ofta använder den här autentiseringsmetoden är appar som kör tjänster på mellan nivå och schemalagda jobb: webbappar, funktions program, Logic Apps, API: er och mikrotjänster. Den här autentiseringsmetoden är också lämplig för interaktiva program där du kanske vill använda ett tjänst konto för att hantera resurser.

När du använder autentiseringsmetoden för tjänstens huvud namn för att bygga konsument scenarier hanteras autentisering vanligt vis på mellan nivå (via en viss API) och inte direkt i ett mobil-eller Skriv bords program. 

Om du vill använda den här metoden skapar du ett Azure AD-program och tjänstens huvud namn i sin egen klient organisation. När du har skapat programmet ger du appen deltagare eller ägar rollen åtkomst till Media Services kontot. Du kan göra detta i Azure Portal med hjälp av Azure CLI eller med ett PowerShell-skript. Du kan också använda ett befintligt Azure AD-program. Du kan registrera och hantera din Azure AD-App och tjänstens huvud namn [i Azure Portal](media-services-portal-get-started-with-aad.md). Du kan också göra detta med hjälp av [Azure CLI](media-services-use-aad-auth-to-access-ams-api.md) eller [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Appar på mellan nivå](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

När du har skapat ditt Azure AD-program får du värden för följande inställningar. Du behöver dessa värden för autentisering:

- Klient-ID 
- Klienthemlighet 

I föregående bild representerar talen flödet för förfrågningarna i kronologisk ordning:
    
1. En app på mellan nivå (webb-API eller webb program) begär en Azure AD-åtkomsttoken som har följande parametrar:  

   * Azure AD-klientens slut punkt.

       Klient informationen kan hämtas från Azure Portal. Placera markören över namnet på den inloggade användaren i det övre högra hörnet.
   * Media Services resurs-URI. 

       Denna URI är samma för Media Services konton som finns i samma Azure-miljö (till exempel https: \/ /rest.Media.Azure.net).

   * Resurs-URI för REST-Media Services.

       URI: n representerar REST API slut punkten (till exempel https://test03.restv2.westus.media.azure.net/api/) .

   * Azure AD-program värden: klient-ID och klient hemlighet.
    
     Information om hur du hämtar värden för dessa parametrar finns i [använda Azure Portal för att komma åt Azure AD-autentiseringsinställningar](media-services-portal-get-started-with-aad.md) med hjälp av alternativet för autentisering av tjänstens huvud namn.

2. Azure AD-åtkomsttoken skickas till mellan nivån.
4. Mellan nivån skickar begäran till Azure Media-REST API med Azure AD-token.
5. Mellan nivån hämtar data från Media Services.

Mer information om hur du använder Azure AD-autentisering för att kommunicera med REST-begäranden med hjälp av Media Services .NET-klient-SDK finns i [använda Azure AD-autentisering för att få åtkomst till Azure Media Services API med .net](media-services-dotnet-get-started-with-aad.md). 

Om du inte använder Media Services .NET-klient-SDK måste du skapa en Azure AD-Tokenbegäran manuellt med hjälp av de parametrar som beskrivs i steg 1. Mer information finns i [så här använder du Azure AD Authentication Library för att hämta Azure AD-token](../../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>Felsökning

Undantag: "Fjärrservern returnerade ett fel: (401) obehörig."

Lösning: för att Media Services REST-begäran ska lyckas måste den anropande användaren vara en deltagar-eller ägar roll i det Media Services konto som den försöker komma åt. Mer information finns i avsnittet [åtkomst kontroll](media-services-use-aad-auth-to-access-ams-api.md#access-control) .

## <a name="resources"></a>Resurser

Följande artiklar är översikter över Azure AD-autentisering: 

- [Autentiserings scenarier som hanteras av Azure AD](../../active-directory/develop/authentication-vs-authorization.md)
- [Lägga till, uppdatera eller ta bort ett program i Azure AD](../../active-directory/develop/quickstart-register-app.md)
- [Lägga till eller ta bort Azure-rolltilldelningar med hjälp av Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Nästa steg

* Använd Azure Portal för att [få åtkomst till Azure AD-autentisering för att använda Azure Media Services API](media-services-portal-get-started-with-aad.md).
* Använd Azure AD-autentisering för att [komma åt Azure Media Services API med .net](media-services-dotnet-get-started-with-aad.md).
