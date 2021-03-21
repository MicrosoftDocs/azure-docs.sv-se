---
title: 'Snabb start: anropa Microsoft Graph från en Node.js-konsol-app | Azure'
titleSuffix: Microsoft identity platform
description: I den här snabb starten hämtar och kör du ett kod exempel som visar hur ett Node.js konsol program kan få en åtkomsttoken och anropa ett API som skyddas av en Microsoft Identity Platform-slutpunkt med appens egen identitet
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: 4360810d460c5fc8598ce302ad8b82f65d2d819e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653753"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Snabb start: Hämta en token och anropa Microsoft Graph API från en Node.js-konsolsession som använder appens identitet

I den här snabb starten laddar du ned och kör ett kod exempel som visar hur ett Node.js konsol program kan få en åtkomsttoken med appens identitet för att anropa Microsoft Graph API och visa en [lista över användare](/graph/api/user-list) i katalogen. Kod exemplet visar hur ett obevakat jobb eller en Windows-tjänst kan köras med en program identitet, i stället för en användares identitet.

I den här snabb starten används [Microsoft Authentication Library för Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) med [tilldelningen av klientautentiseringsuppgifter](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Förutsättningar

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) eller en annan kod redigerare

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registrera och ladda ned exempel programmet
>
> Kom igång genom att följa stegen nedan.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>Steg 1: registrera programmet
> Du registrerar programmet och lägger till appens registreringsinformationen i lösningen manuellt med hjälp av följande steg:
>
> 1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure-portalen</a>.
> 1. Om du har åtkomst till flera klienter använder du filtret för **katalog + prenumeration** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: i den översta menyn för att välja den klient som du vill registrera ett program i.
> 1. Sök efter och välj **Azure Active Directory**.
> 1. Under **Hantera** väljer du **Appregistreringar**  >  **ny registrering**.
> 1. Ange ett **namn** för programmet, till exempel `msal-node-cli` . Användare av appen kan se det här namnet och du kan ändra det senare.
> 1. Välj **Register** (Registrera).
> 1. Välj **Certifikat och hemligheter** under **Hantera**.
> 1. Under **klient hemligheter** väljer du **ny klient hemlighet**, anger ett namn och väljer sedan **Lägg till**. Registrera det hemliga värdet på en säker plats för användning i ett senare steg.
> 1. Under **Hantera** väljer du **API-behörigheter**  >  **Lägg till en behörighet**. Välj **Microsoft Graph**.
> 1. Välj **Programbehörigheter**.
> 1. Under noden **användare** väljer du **User. Read. all** och väljer sedan **Lägg till behörigheter**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>Hämta och konfigurera exempel appen
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Steg 1: konfigurera programmet i Azure Portal
> För att kodexemplet för den här snabbstarten ska fungera måste du skapa en klienthemlighet och lägga till Graph-API:ts programbehörighet **User.Read.All**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Gör ändringarna åt mig]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Redan konfigurerad](media/quickstart-v2-netcore-daemon/green-check.png) Programmet konfigureras med de här attributen.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Steg 2: Ladda ned Node.js-exempelprojektet

> [!div renderon="docs"]
> [Ladda ned kod exemplet](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Ladda ned kod exemplet](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>Steg 3: Konfigurera Node.js-exempelprojektet
>
> 1. Extrahera zip-filen till en lokal mapp nära disk roten, till exempel *C:/Azure-samples*.
> 1. Redigera *. kuvert* och ersätt värdena för fälten `TENANT_ID` , `CLIENT_ID` och `CLIENT_SECRET` med följande kodfragment:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Plats:
>    - `Enter_the_Application_Id_Here` – är **program-ID: t** för det program som du registrerade tidigare. Hitta detta ID i **översikts** fönstret i appens registrering i Azure Portal.
>    - `Enter_the_Tenant_Id_Here` – Ersätt värdet med **klient-ID:** t eller **klient namnet** (till exempel contoso.Microsoft.com).  Hitta de här värdena i **översikts** fönstret i appens registrering i Azure Portal.
>    - `Enter_the_Client_Secret_Here` – Ersätt det här värdet med den klient hemlighet som du skapade tidigare. Om du vill generera en ny nyckel använder du **certifikat & hemligheter** i appens registrerings inställningar i Azure Portal.
>
> > [!WARNING]
> > Eventuell hemlig text i käll koden utgör en ökad säkerhets risk. I den här artikeln används en klient hemlighet i klartext för enkelhetens skull. Använd [autentiseringsuppgifter](active-directory-certificate-credentials.md) i stället för klient hemligheter i dina konfidentiella klient program, särskilt de appar som du tänker distribuera till produktion.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Steg 3: administratörs medgivande

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Steg 4: Administratörsmedgivande

Om du försöker köra programmet nu får du ett *HTTP 403-otillåtet* fel: `Insufficient privileges to complete the operation` . Det här felet beror på att en *app-only-behörighet* kräver **Administratörs medgivande**: en global administratör för din katalog måste ge ditt program medgivande. Välj ett av alternativen nedan beroende på din roll:

##### <a name="global-tenant-administrator"></a>Global innehavaradministratör

> [!div renderon="docs"]
> Om du är global innehavaradministratör kan du gå till sidan med **API-behörigheter** i Azure portalens program registrering och välja **bevilja administratörs medgivande för {klient organisationens namn}** (där {klient organisationens namn} är namnet på din katalog).

> [!div renderon="portal" class="sxs-lookup"]
> Om du är global administratör går du till sidan **API-behörigheter** och väljer **Bevilja administratörsmedgivande för Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Gå till sidan API-behörigheter]()

##### <a name="standard-user"></a>Standardanvändare

Om du är en standard användare av din klient organisation måste du be en global administratör att bevilja **administrativt medgivande** för ditt program. Gör detta genom att ge följande URL till administratören:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Plats:
>> * `Enter_the_Tenant_Id_Here` – ersätt det här värdet med **klient-ID** eller **klientnamn** (t.ex. contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here` – är **program-ID (klient)** för programmet som du har registrerat.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Steg 4: kör programmet

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Steg 5: Köra appen

Leta upp exemplets rotmapp (där `package.json` finns) i en kommando tolk eller konsol. Du måste installera beroendena för det här exemplet en gång:

```console
npm install
```

Kör sedan programmet via kommando tolken eller konsolen:

```console
node . --op getUsers
```

Du bör se i konsolen utdata av ett JSON-fragment som representerar en lista med användare i Azure AD-katalogen.

## <a name="about-the-code"></a>Om koden

Nedan diskuteras några av de viktiga aspekterna av exempel programmet.

### <a name="msal-node"></a>MSAL-nod

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) är det bibliotek som används för att logga in användare och begära token som används för att få åtkomst till ett API som skyddas av Microsoft Identity Platform. Enligt beskrivningen begär den här snabb starten token efter program behörigheter (med hjälp av programmets egna identitet) i stället för delegerade behörigheter. Det autentiseringsschema som används i det här fallet kallas [OAuth 2,0](v2-oauth2-client-creds-grant-flow.md)-klientautentiseringsuppgifter. Mer information om hur du använder MSAL-noden med daemon-appar finns i [Scenario: daemon-program](scenario-daemon-overview.md).

 Du kan installera MSAL-noden genom att köra följande NPM-kommando.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>MSAL-initiering

Du kan lägga till referensen för MSAL genom att lägga till följande kod:

```javascript
const msal = require('@azure/msal-node');
```

Initiera sedan MSAL med hjälp av följande kod:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Plats: |Beskrivning |
> |---------|---------|
> | `clientId` | Är **Program-ID (klient)** för det program som registrerats på Azure-portalen. Du hittar det här värdet på appens **översiktssida** på Azure-portalen. |
> | `authority`    | STS-slutpunkten för autentisering av användaren. Vanligtvis `https://login.microsoftonline.com/{tenant}` för offentligt moln, där {klient} är namnet på klientorganisationen eller klient-ID:t.|
> | `clientSecret` | Är klienthemligheten som skapats för appen i Azure-portalen. |

Mer information finns i [referensdokumentationen för `ConfidentialClientApplication`](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)

### <a name="requesting-tokens"></a>Begära token

Om du vill begära en token med appens identitet använder du metoden `acquireTokenByClientCredential`:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Plats:| Beskrivning |
> |---------|---------|
> | `tokenRequest` | Innehåller omfattningarna som begärdes. För konfidentiella klienter bör ett format som liknar `{Application ID URI}/.default` användas för att ange att omfattningarna som begärs är dem som statiskt definieras i appobjektet som anges i Azure-portalen (för Microsoft Graph, `{Application ID URI}` pekar på `https://graph.microsoft.com`). För anpassade webb-API: er `{Application ID URI}` definieras under **exponera ett API** -avsnitt i Azure-portalens program registrering. |
> | `tokenResponse` | Svaret innehåller en åtkomsttoken för de begärda omfången. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om utveckling av daemon-/konsol program med MSAL-noden finns i självstudien:

> [!div class="nextstepaction"]
> [Daemon-program som anropar webb-API: er](tutorial-v2-nodejs-console.md)