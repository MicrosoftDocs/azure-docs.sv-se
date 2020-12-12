---
title: Lägg till anpassade godkännanden till självbetjänings registrerings flöden – Azure AD
description: Lägg till API-kopplingar för anpassade godkännande arbets flöden i externa identiteter självbetjänings registrering-Azure Active Directory (Azure AD)
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3165bc28e6d6283bf8578d9c10b11f7b19981002
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355247"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>Lägg till ett anpassat godkännande arbets flöde till självbetjänings registrering

Med [API-kopplingar](api-connectors-overview.md)kan du integrera med dina egna anpassade godkännande arbets flöden med självbetjänings registrering så att du kan hantera vilka gäst användar konton som skapas i din klient organisation.

Den här artikeln innehåller ett exempel på hur du integrerar med ett godkännande system. I det här exemplet samlar användar flödet för självbetjänings registrering användar data under registrerings processen och skickar dem till ditt godkännande system. Sedan kan godkännande systemet:

- Godkänn användaren automatiskt och Tillåt att Azure AD skapar användar kontot.
- Utlös en manuell granskning. Om begäran godkänns använder godkännande systemet Microsoft Graph för att etablera användar kontot. Godkännande systemet kan också meddela användaren att deras konto har skapats.

> [!IMPORTANT]
>Från och med den **4 januari 2021** är Google [inaktuell WebView-inloggning support](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Om du använder Google Federation eller självbetjänings registrering med Gmail bör du [testa dina verksamhetsbaserade interna program för kompatibilitet](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="register-an-application-for-your-approval-system"></a>Registrera ett program för ditt godkännande system

Du måste registrera ditt godkännande system som ett program i din Azure AD-klient så att det kan autentiseras med Azure AD och ha behörighet att skapa användare. Lär dig mer om [grunderna för autentisering och auktorisering för Microsoft Graph](/graph/auth/auth-concepts).

1. Logga in till [Azure-portalen](https://portal.azure.com) som Azure AD-administratör.
2. Under **Azure-tjänster** väljer du **Azure Active Directory**.
3. Välj **Appregistreringar** på den vänstra menyn och välj sedan **ny registrering**.
4. Ange ett **namn** för programmet, t. ex. _inloggnings godkännanden_.

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. Välj **Register** (Registrera). Du kan lämna standardvärdena för övriga fält.

   ![Skärm bild som visar knappen Registrera.](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. Under **Hantera** i den vänstra menyn väljer du **API-behörigheter** och väljer sedan **Lägg till en behörighet**.
7. Välj **Microsoft Graph** på sidan **begär API-behörigheter** och välj sedan **program behörigheter**.
8. Under **Välj behörigheter** expanderar du **användare** och markerar sedan kryss rutan **User. readwrite. all** . Med den här behörigheten kan godkännande systemet skapa användaren vid godkännande. Välj sedan **Lägg till behörigheter**.

   ![Registrera en program sida](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. På sidan **API-behörigheter** väljer du **bevilja administrativt medgivande för (ditt klient namn)** och väljer sedan **Ja**.
10. Under **Hantera** i den vänstra menyn väljer du **certifikat & hemligheter** och väljer sedan **ny klient hemlighet**.
11. Ange en **Beskrivning** av hemligheten, till exempel _godkännande av klient hemlighet_, och välj varaktigheten för när klient hemligheten **upphör att gälla**. Välj **Lägg till**.
12. Kopiera värdet för klient hemligheten.

    ![Kopiera klient hemligheten för användning i godkännande systemet](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. Konfigurera ditt godkännande system så att det använder **program-ID:** t som klient-ID och den **klient hemlighet** som du genererade för att autentisera med Azure AD.

## <a name="create-the-api-connectors"></a>Skapa API-kopplingar

Härnäst ska du [skapa API-kopplingarna](self-service-sign-up-add-api-connector.md#create-an-api-connector) för ditt självbetjänings registrerings användar flöde. Ditt system-API för godkännande kräver två kopplingar och motsvarande slut punkter, precis som exemplen som visas nedan. Dessa API-kopplingar gör följande:

- **Kontrol lera godkännande statusen**. Skicka ett anrop till godkännande systemet omedelbart efter att en användare loggar in med en identitets leverantör för att kontrol lera om användaren har en befintlig godkännandebegäran eller om den redan har nekats. Om ditt godkännande system endast har automatiska godkännande beslut kanske denna API-anslutning inte behövs. Exempel på API-anslutning "kontrol lera godkännande status".

  ![Kontrol lera godkännande status API Connector-konfiguration](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **Begär godkännande** – skicka ett anrop till godkännande systemet när en användare har slutfört sidan för attribut samling, men innan användar kontot skapas för att begära godkännande. Begäran om godkännande kan beviljas automatiskt eller granskas manuellt. Exempel på en API-anslutning med "begär godkännande". 

  ![Begär konfiguration av API-koppling för begäran](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

Följ stegen i [skapa en API-anslutning](self-service-sign-up-add-api-connector.md#create-an-api-connector)för att skapa dessa anslutningar.

## <a name="enable-the-api-connectors-in-a-user-flow"></a>Aktivera API-kopplingar i ett användar flöde

Nu ska du lägga till API-kopplingarna i ett användar flöde för självbetjänings registrering med följande steg:

1. Logga in till [Azure-portalen](https://portal.azure.com/) som Azure AD-administratör.
2. Under **Azure-tjänster** väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **användar flöden (förhands granskning)** och välj sedan det användar flöde som du vill aktivera API-kopplingen för.
5. Välj **API-kopplingar** och välj sedan de API-slutpunkter som du vill anropa i följande steg i användar flödet:

   - **När du har loggat in med en identitetsprovider**: Välj din godkännande status API-koppling, till exempel _kontrol lera godkännande status_.
   - **Innan du skapar användaren**: Välj din API-koppling för godkännande förfrågan, t. ex. _begäran om godkännande_.

   ![Lägga till API: er i användar flödet](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. Välj **Spara**.

## <a name="control-the-sign-up-flow-with-api-responses"></a>Kontrol lera registrerings flödet med API-svar

Ditt godkännande system kan använda sina svar när de anropas för att styra registrerings flödet. 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>Begäran och svar för API-anslutningen "kontrol lera godkännande status"

Exempel på begäran som tagits emot av API: t från API-anslutningen "kontrol lera godkännande status":

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

De exakta anspråk som skickas till API: n beror på vilken information som identitets leverantören tillhandahåller. "e-post" skickas alltid.

#### <a name="continuation-response-for-check-approval-status"></a>Fortsättnings svar för "kontrol lera godkännande status"

API-slutpunkten **kontrol lera godkännande status** bör returnera ett fortsättnings svar om:

- Användaren har inte begärt ett godkännande tidigare.

Exempel på fortsättnings svar:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>Blockerar svar för "kontrol lera godkännande status"

API-slutpunkten **kontrol lera godkännande status** ska returnera ett blockerande svar om:

- Användar godkännande väntar.
- Användaren nekades och bör inte tillåtas att begära godkännande igen.

Följande är exempel på blockering av svar:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-PENDING"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-DENIED"
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>Begäran och svar för API-kopplingen "begär godkännande"

Exempel på en HTTP-begäran som tagits emot av API: et från API-anslutningen "begär godkännande":

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

De exakta anspråk som skickas till API: n beror på vilken information som samlas in från användaren eller tillhandahålls av identitets leverantören.

#### <a name="continuation-response-for-request-approval"></a>Fortsättnings svar för "godkännande av begäran"

API-slutpunkten för **begär ande godkännande** bör returnera ett fortsättnings svar om:

- Användaren kan **_godkännas automatiskt_**.

Exempel på fortsättnings svar:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> Om ett fortsättnings svar tas emot skapar Azure AD ett användar konto och dirigerar användaren till programmet.

#### <a name="blocking-response-for-request-approval"></a>Blockerar svar för "begär godkännande"

API-slutpunkten för **begär ande godkännande** ska returnera ett blockerande svar om:

- En begäran om godkännande av användare har skapats och väntar nu.
- En begäran om godkännande av användare nekades automatiskt.

Följande är exempel på blockering av svar:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-REQUESTED"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-AUTO-DENIED"
}
```

`userMessage`I svaret visas användaren, till exempel:

![Exempel sidan väntar på godkännande](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>Skapa användar konto efter manuellt godkännande

När du har fått manuellt godkännande skapar det anpassade godkännande systemet ett [användar](/graph/azuread-users-concept-overview) konto med hjälp av [Microsoft Graph](/graph/use-the-api). Hur ditt godkännande system etablerar användar kontot beror på vilken identitets leverantör som användes av användaren.

### <a name="for-a-federated-google-or-facebook-user"></a>För en federerad Google-eller Facebook-användare

> [!IMPORTANT]
> Godkännande systemet bör uttryckligen kontrol lera att `identities` `identities[0]` och `identities[0].issuer` är närvarande och att det är `identities[0].issuer` lika med "Facebook" eller "Google" för att använda den här metoden.

Om användaren har loggat in med ett Google-eller Facebook-konto kan du använda API: et för att [skapa användare](/graph/api/user-post-users?tabs=http).

1. Godkännande systemet använder tar emot HTTP-begäran från användar flödet.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Godkännande systemet använder Microsoft Graph för att skapa ett användar konto.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| Parameter                                           | Krävs | Beskrivning                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | Ja      | Kan genereras genom att ta emot `email` anspråket som skickas till API: et, ersätta det `@` med `_` och vänta det `#EXT@<tenant-name>.onmicrosoft.com` . |
| accountEnabled                                      | Ja      | Måste anges till `true` .                                                                                                                                                 |
| e-post                                                | Ja      | Motsvarar det `email` anspråk som skickas till API: et.                                                                                                               |
| userType                                            | Ja      | Måste vara `Guest` . Utser den här användaren som gäst användare.                                                                                                                 |
| identiteter                                          | Ja      | Federerad identitets information.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | Nej       | Andra inbyggda attribut som `displayName` , `city` och andra. Parameter namn är desamma som de parametrar som skickas av API-anslutningen.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | Nej       | Anpassade attribut för användaren. Parameter namn är desamma som de parametrar som skickas av API-anslutningen.                                                            |

### <a name="for-a-federated-azure-active-directory-user"></a>För en federerad Azure Active Directory användare

Om en användare loggar in med ett federerat Azure Active Directory-konto måste du använda [Inbjudnings-API: et](/graph/api/invitation-post) för att skapa användaren och sedan välja [användar uppdaterings-API](/graph/api/user-update) för att tilldela fler attribut till användaren.

1. Godkännande systemet tar emot HTTP-begäran från användar flödet.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. Godkännande systemet skapar inbjudan med hjälp av `email` API-anslutningen.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress":"johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

Exempel på svar:

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. Godkännande systemet använder den inbjudna användarens ID för att uppdatera användarens konto med insamlade användarattribut (valfritt).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>Nästa steg

- Kom igång med våra [snabb starts exempel för Azure Function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
- Checka ut självbetjänings [registreringen för gäst användare med ett manuellt godkännande exempel](code-samples-self-service-sign-up.md#custom-approval-workflows).
