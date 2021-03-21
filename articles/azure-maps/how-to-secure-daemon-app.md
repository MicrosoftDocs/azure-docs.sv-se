---
title: Så här skyddar du ett daemon-program
titleSuffix: Azure Maps
description: Använd Azure Portal för att hantera autentisering för att konfigurera ett Trusted daemon-program.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2dd04f404330a6c86e2df09da610e16ba9b721f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895655"
---
# <a name="secure-a-daemon-application"></a>Skydda ett daemon-program

Följande guide gäller bakgrunds processer, timers och jobb som finns i en betrodd och säker miljö. Exempel är Azure-webbjobb, Azure Function-appar, Windows-tjänster och annan tillförlitlig bakgrunds tjänst.

> [!Tip]
> Microsoft rekommenderar att du implementerar Azure Active Directory (Azure AD) och rollbaserad åtkomst kontroll i Azure (Azure RBAC) för produktions program. En översikt över begrepp finns i [Azure Maps autentisering](./azure-maps-authentication.md).

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="scenario-shared-key-authentication"></a>Scenario: autentisering med delad nyckel

När du har skapat ett Azure Maps-konto genereras de primära och sekundära nycklarna. Vi rekommenderar att du använder den primära nyckeln som prenumerations nyckel när du [använder autentisering med delad nyckel för att anropa Azure Maps](./azure-maps-authentication.md#shared-key-authentication). Du kan använda en sekundär nyckel i scenarier som löpande nyckel ändringar. Mer information finns i [autentisering i Azure Maps](./azure-maps-authentication.md).

### <a name="securely-store-shared-key"></a>Lagra delad nyckel på ett säkert sätt

Den primära och sekundära nyckeln tillåter auktorisering till alla API: er för Maps-kontot. Programmen ska lagra nycklarna i ett säkert arkiv som Azure Key Vault. Programmet måste hämta den delade nyckeln som en Azure Key Vault hemlighet för att undvika att lagra den delade nyckeln i oformaterad text i program konfigurationen. Information om hur du konfigurerar en Azure Key Vault finns i [Azure Key Vault Developer Guide](../key-vault/general/developers-guide.md).

Följande steg beskriver den här processen:

1. Skapa en Azure Key Vault.
2. Skapa ett tjänst huvud namn för Azure AD genom att skapa en app-registrering eller hanterad identitet, är det skapade huvud kontot som är ansvarigt för åtkomst Azure Key Vault.
3. Tilldela tjänstens huvud namn åtkomst till Azure Key hemligheter- `Get` behörighet.
4. Tilldela tillfälligt åtkomst till behörighet till hemligheter `Set` åt dig som utvecklare.
5. Ange den delade nyckeln i Key Vault hemligheter och referera till hemligt ID som konfiguration för daemon-programmet och ta bort `Set` behörigheten hemligheter.
6. Implementera Azure AD-autentisering i daemon-programmet för att hämta hemligheten för den delade nyckeln från Azure Key Vault.
7. Skapa Azure Maps REST API begäran med delad nyckel.

> [!Tip]
> Om appen finns i Azure-miljö bör du implementera en hanterad identitet för att minska kostnaderna och komplexiteten med att hantera en hemlighet för att autentisera till Azure Key Vault. Se följande Azure Key Vault [själv studie kurs för att ansluta via hanterad identitet](../key-vault/general/tutorial-net-create-vault-azure-web-app.md).

Daemon-programmet ansvarar för att hämta den delade nyckeln från en säker lagrings plats. Implementeringen med Azure Key Vault kräver autentisering via Azure AD för att få åtkomst till hemligheten. I stället uppmuntrar vi direkt Azure AD-autentisering till Azure Maps till följd av de ytterligare komplexitets-och drifts kraven för att använda autentisering med delad nyckel.

> [!IMPORTANT]
> För att förenkla skapandet av nycklar rekommenderar vi att program använder en nyckel i taget. Programmen kan sedan återskapa den oanvända nyckeln och distribuera den nya återskapade nyckeln till en säker hemlig lagrings plats, till exempel Azure Key Vault.

## <a name="scenario-azure-ad-role-based-access-control"></a>Scenario: rollbaserad åtkomst kontroll i Azure AD

När ett Azure Maps-konto har skapats finns Azure Maps- `x-ms-client-id` värdet på sidan Azure Portal information om autentisering. Det här värdet representerar kontot som ska användas för REST API begär Anden. Värdet ska lagras i program konfigurationen och hämtas innan du gör HTTP-begäranden. Målet med scenariot är att aktivera daemon-programmet att autentisera till Azure AD och anropa Azure Maps REST-API: er.

> [!Tip]
> Vi rekommenderar att du är värd för Azure Virtual Machines, Virtual Machine Scale Sets eller App Services för att möjliggöra fördelarna med hanterade identitets komponenter.

### <a name="daemon-hosted-on-azure-resources"></a>Daemon som finns på Azure-resurser

När du kör på Azure-resurser konfigurerar du Azure Managed Identities för att aktivera låg kostnad, minimal hantering av autentiseringsuppgifter. 

Se [Översikt över hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md) för att ge programmet åtkomst till en hanterad identitet.

Fördelar med hanterade identiteter:

* Azure system Managed X509 Certificate Public Key Cryptography Authentication.
* Azure AD-säkerhet med X509-certifikat i stället för klient hemligheter.
* Azure hanterar och förnyar alla certifikat som är kopplade till den hanterade identitets resursen.
* Förenklad hantering av autentiseringsuppgifter genom att ta bort eventuella behov av en säker hemlig lagrings tjänst som Azure Key Vault. 

### <a name="daemon-hosted-on-non-azure-resources"></a>Daemon som finns på icke-Azure-resurser

När det körs på en icke-Azure-miljö hanterade identiteter är inte tillgängliga. Därför måste du konfigurera ett huvud namn för tjänsten via en Azure AD-programregistrering för daemon-programmet.

1. I listan med Azure-tjänster i Azure Portal väljer du **Azure Active Directory**  >  **Appregistreringar**  >  **ny registrering**.  

    > [!div class="mx-imgBorder"]
    > ![Appregistrering](./media/how-to-manage-authentication/app-registration.png)

2. Om du redan har registrerat appen fortsätter du till nästa steg. Om du inte har registrerat appen, ange ett **namn**, Välj en **Support konto typ** och välj sedan **Registrera**.  

    > [!div class="mx-imgBorder"]
    > ![Registrerings information för appen](./media/how-to-manage-authentication/app-create.png)

3. Om du vill tilldela delegerade API-behörigheter till Azure Maps går du till programmet. Under **Appregistreringar** väljer du **API-behörigheter**  >  **Lägg till en behörighet**. Sök efter och välj **Azure Maps** under **API: er som används i organisationen**.

    > [!div class="mx-imgBorder"]
    > ![Lägg till API-behörigheter för app](./media/how-to-manage-authentication/app-permissions.png)

4. Markera kryss rutan bredvid **åtkomst Azure Maps** och välj sedan **Lägg till behörigheter**.

    > [!div class="mx-imgBorder"]
    > ![Välj API-behörigheter för app](./media/how-to-manage-authentication/select-app-permissions.png)

5. Utför följande steg för att skapa en klient hemlighet eller konfigurera certifikat.

    * Om programmet använder Server-eller programautentisering kan du gå till **certifikat & hemligheter** på din app Registration-sida. Överför sedan antingen ett offentligt nyckel certifikat eller skapa ett lösen ord genom att välja **ny klient hemlighet**.

        > [!div class="mx-imgBorder"]
        > ![Skapa en klient hemlighet](./media/how-to-manage-authentication/app-keys.png)

    * När du har valt **Lägg till** kopierar du hemligheten och lagrar den på ett säkert sätt i en tjänst, till exempel Azure Key Vault. Granska [Azure Key Vault Developer Guide](../key-vault/general/developers-guide.md) för att lagra certifikatet eller hemligheten på ett säkert sätt. Du använder den här hemligheten för att hämta tokens från Azure AD.

        > [!div class="mx-imgBorder"]
        > ![Lägg till en klient hemlighet](./media/how-to-manage-authentication/add-key.png)

### <a name="grant-role-based-access-for-the-daemon-application-to-azure-maps"></a>Bevilja rollbaserad åtkomst för daemon-programmet till Azure Maps

Du beviljar *Azure rollbaserad åtkomst kontroll (Azure RBAC)* genom att tilldela antingen den skapade hanterade identiteten eller tjänstens huvud namn till en eller flera Azure Maps roll definitioner. Om du vill visa de roll definitioner för Azure som är tillgängliga för Azure Maps går du till **åtkomst kontroll (IAM)**. Välj **roller** och Sök sedan efter roller som börjar med *Azure Maps*. Dessa Azure Maps roller är de roller som du kan bevilja åtkomst till.

> [!div class="mx-imgBorder"]
> ![Visa tillgängliga roller](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

1. Gå till ditt **Azure Maps-konto**. Välj **Åtkomstkontroll (IAM)** > **Rolltilldelningar**.

    > [!div class="mx-imgBorder"]
    > ![Bevilja åtkomst med Azure RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **Lägg till** en roll tilldelning på fliken **roll tilldelningar** . 

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar de sammanslagnings tilldelningar som har marker ATS.](./media/how-to-manage-authentication/add-role-assignment.png)

3. Välj en inbyggd Azure Maps roll definition som **Azure Maps data läsare** eller **Azure Maps data deltagare**. Under **tilldela åtkomst till** väljer du **Azure AD-användare, grupp eller tjänstens huvud namn** eller hanterad identitet med användaren tilldelad hanterad identitet **hanterad** identitet  /  . Välj huvud konto. Välj sedan **Spara**.

    > [!div class="mx-imgBorder"]
    > ![Så här lägger du till roll tilldelning](./media/how-to-manage-authentication/how-to-add-role-assignment.png)

4. Du kan bekräfta att roll tilldelningen har tillämpats på fliken roll tilldelning.

## <a name="request-token-with-managed-identity"></a>Begär token med hanterad identitet

När en hanterad identitet har kon figurer ATS för värd resursen använder du Azure SDK eller REST API för att hämta en token för Azure Maps, se information om [att hämta en](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)åtkomsttoken. Efter hand boken är det förväntat att en åtkomsttoken returneras som kan användas för REST API begär Anden.

## <a name="request-token-with-application-registration"></a>Begär token med program registrering

När du har registrerat din app och associerar den med Azure Maps kan du begära åtkomst-token.

* Resurs-ID för Azure AD `https://atlas.microsoft.com/`
* Azure AD App-ID
* Azure AD-klient-ID
* Azure AD App klient hemlighet för registrering

Begäran:

```http
POST /<Azure AD Tenant ID>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<Azure AD App ID>&resource=https://atlas.microsoft.com/&client_secret=<client secret>&grant_type=client_credentials
```

Svar:

```json
{
    "token_type": "Bearer",
    "expires_in": "...",
    "ext_expires_in": "...",
    "expires_on": "...",
    "not_before": "...",
    "resource": "https://atlas.microsoft.com/",
    "access_token": "ey...gw"
}
```

Se [scenarier för autentisering för Azure AD](../active-directory/develop/authentication-vs-authorization.md)för mer detaljerade exempel.

## <a name="next-steps"></a>Nästa steg

Hitta API-användnings mått för ditt Azure Maps-konto:
> [!div class="nextstepaction"]
> [Visa användningsstatistik](how-to-view-api-usage.md)

Utforska exempel som visar hur du integrerar Azure AD med Azure Maps:
> [!div class="nextstepaction"]
> [Azure Maps exempel](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)