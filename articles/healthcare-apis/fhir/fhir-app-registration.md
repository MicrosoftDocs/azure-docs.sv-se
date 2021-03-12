---
title: Registrera Azure Active Directory appar för Azure API för FHIR
description: I den här självstudien beskrivs vilka program som måste registreras för Azure API för FHIR och FHIR-servern för Azure.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: b01f65b6be31007cebc7a5b03a1c57f6b901fe53
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103020239"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Registrera Azure Active Directory appar för Azure API för FHIR

Du har flera konfigurations alternativ att välja mellan när du konfigurerar Azure API för FHIR eller FHIR-servern för Azure (OSS). För öppen källkod måste du skapa en egen resurs program registrering. För Azure API för FHIR skapas detta resurs program automatiskt.

## <a name="application-registrations"></a>Program registreringar

För att ett program ska kunna interagera med Azure AD måste det registreras. I FHIR-serverns kontext finns det två typer av program registreringar att diskutera:

1. Registreringar av resurs program.
1. Registreringar av klient program.

**Resurs program** är representationer i Azure AD för ett API eller en resurs som skyddas med Azure AD, särskilt det skulle vara Azure-API: et för FHIR. Ett resurs program för Azure API för FHIR skapas automatiskt när du etablerar tjänsten, men om du använder servern med öppen källkod måste du [Registrera ett resurs program](register-resource-azure-ad-client-app.md) i Azure AD. Det här resurs programmet kommer att ha en ID-URI. Vi rekommenderar att denna URI är samma som URI: n för FHIR-servern. Denna URI ska användas som `Audience` för FHIR-servern. Ett klient program kan begära åtkomst till den här FHIR-servern när den begär en token.

*Klient program* är registreringar av klienter som kommer att begära token. I OAuth 2,0 skiljer vi ofta mellan minst tre olika typer av program:

1. **Konfidentiella klienter**, även kallade Web Apps i Azure AD. Konfidentiella klienter är program som använder [auktoriseringskod](../../active-directory/azuread-dev/v1-protocols-oauth-code.md) för att få en token för en inloggad användares räkning med giltiga autentiseringsuppgifter. De kallas konfidentiella klienter eftersom de kan lagra en hemlighet och presentera den här hemligheten på Azure AD när du utbyter Authentication-koden för en token. Eftersom konfidentiella klienter kan autentisera sig själva med hjälp av klient hemligheten är de betrodda mer än offentliga klienter och kan ha längre livs längd token och beviljas en uppdateringstoken. Läs mer om hur du [registrerar en konfidentiell klient](register-confidential-azure-ad-client-app.md). Observera att det är viktigt att registrera svars-URL: en då klienten får auktoriseringskod.
1. **Offentliga klienter**. Detta är klienter som inte kan behålla en hemlighet. Detta är vanligt vis ett program för mobil enhet eller ett JavaScript-program med en enda sida, där en hemlighet i klienten kan identifieras av en användare. Offentliga klienter använder också kod flöde för auktorisering, men de kan inte presentera en hemlighet när de hämtar en token och de kan ha kortare livs längder och ingen uppdateringstoken. Läs mer om hur du [registrerar en offentlig klient](register-public-azure-ad-client-app.md).
1. Tjänst klienter. Dessa klienter får token åt sig själva (inte för en användares räkning) med hjälp av [flödet för klientautentiseringsuppgifter](../../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md). De representerar vanligt vis program som ansluter till FHIR-servern på ett icke-interaktivt sätt. Ett exempel är en inmatnings process. När du använder en tjänst klient är det inte nödvändigt att starta processen för att hämta en token med ett anrop till `/authorize` slut punkten. En tjänst klient kan gå direkt till `/token` slut punkten och presentera klient-ID och klient hemlighet för att hämta en token. Läs mer om hur du [registrerar en tjänst klient](register-service-azure-ad-client-app.md)

## <a name="next-steps"></a>Nästa steg

I den här översikten har du genomgått de typer av program registreringar som du kan behöva för att kunna arbeta med ett FHIR-API.

Baserat på din installation kan du läsa instruktions guider för att registrera dina program

* [Registrera ett resursprogram](register-resource-azure-ad-client-app.md)
* [Registrera ett konfidentiellt klientprogram](register-confidential-azure-ad-client-app.md)
* [Registrera ett offentligt klientprogram](register-public-azure-ad-client-app.md)
* [Registrera ett tjänst program](register-service-azure-ad-client-app.md)

När du har registrerat dina program kan du Distribuera Azure API för FHIR.

>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-powershell-quickstart.md)