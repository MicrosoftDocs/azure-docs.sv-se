---
title: Använda Kundhanterade nycklar eller BYOK REST API
description: I den här självstudien ska du använda Kundhanterade nycklar eller ta med din egen nyckel (BYOK) med ett Azure Media Services lagrings konto.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498293"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>Självstudie: använda Kundhanterade nycklar eller BYOK med Media Services REST API

Med 2020-05-01-API: et kan du använda en kundhanterad RSA-nyckel med ett Azure Media Services-konto som har en Systemhanterad identitet. Den här självstudien innehåller en Postman-samling och-miljö för att skicka REST-begäranden till Azure-tjänster. De tjänster som används är:

- Azure Active Directory (Azure AD) program registrering för Postman
- Microsoft Graph API
- Azure Storage
- Azure Key Vault
- Azure Media Services

I den här självstudien får du lära dig att använda Postman för att:

> [!div class="checklist"]
> - Hämta token för användning med Azure-tjänster.
> - Skapa en resurs grupp och ett lagrings konto.
> - Skapa ett Media Services-konto med en Systemhanterad identitet.
> - Skapa ett nyckel valv för att lagra en kundhanterad RSA-nyckel.
> - Uppdatera Media Services kontot för att använda RSA-nyckeln med lagrings kontot.
> - Använd variabler i Postman.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt utvärderings konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

1. Registrera ett huvud namn för tjänsten med rätt behörigheter.
1. Installera [Postman](https://www.postman.com).
1. Hämta Postman-samlingen för den här själv studie kursen på [Azure-exempel: Media-Services-Kundhanterade nycklar-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registrera ett huvud namn för tjänsten med de behörigheter som krävs

1. [Skapa ett huvud namn för tjänsten](../../active-directory/develop/howto-create-service-principal-portal.md).
1. Gå till [Alternativ 2: skapa en ny program hemlighet](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) för att hämta tjänstens huvud namns hemlighet.

   > [!IMPORTANT]
   >Kopiera och spara hemligheten för senare användning. Du kan inte komma åt hemligheten när du har lämnat sidan hemligt i portalen.

1. Tilldela behörigheter till tjänstens huvud namn, så som visas på följande skärm bild:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Skärm bild som visar de behörigheter som krävs för tjänstens huvud namn.":::
   Behörigheter anges av tjänst, behörighets namn, typ och beskrivning. Azure Key Vault: personifiering av användare, delegerad, fullständig åtkomst till Azure Key Vault. Azure Service Management: användar personifiering, delegerad, åtkomst till Azure Service Management som organisations användare. Azure Storage: personifiering av användare, delegerad, åtkomst Azure Storage. Medie tjänster: användar personifiering, delegerad, åtkomst till Media Services. Microsoft Graph: användare. läsa, delegerad, logga in och läsa användar profil.
   :::image-end:::

### <a name="install-postman"></a>Installera Postman

Om du inte redan har installerat Postman för användning med Azure kan du hämta den på [Postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Hämta och importera samlingen

Hämta Postman-samlingen för den här själv studie kursen på [Azure-exempel: Media-Services-Kundhanterade nycklar-BYOK](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

## <a name="install-the-postman-collection-and-environment"></a>Installera Postman-samlingen och-miljön

1. Kör Postman.
1. Välj **Importera**.
1. Välj **överför filer**.
1. Gå till den plats där du sparade samlingen och miljö filerna.
1. Välj samlings-och miljö filer.
1. Välj **Öppna**. En varning visas som säger att filerna inte importeras som ett API, utan som samlingar. Den här varningen är felfri. Det är vad du vill.

Samlingen visas nu i samlingarna som BYOK. Dessutom visas miljövariablerna i dina miljöer.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Förstå REST API begär anden i samlingen

Samlingen ger följande REST API begär Anden.

> [!NOTE]
>
>- Begär Anden måste skickas i den angivna sekvensen.
>- De flesta förfrågningar har test skript som dynamiskt skapar globala variabler för nästa begäran i sekvensen.
>- Du behöver inte skapa globala variabler manuellt.

I Postman visas dessa variabler inom hakparenteser. Till exempel `{{bearerToken}}`.

1. Hämta en Azure AD-token: testet ställer in den globala variabeln **bearerToken**.
2. Hämta en Microsoft Graph token: testet ställer in den globala variabeln **graphToken**.
3. Hämta information om tjänstens huvud namn: testet anger den globala variabeln **servicePrincipalObjectId**.
4. Skapa ett lagrings konto: testet ställer in den globala variabeln **storageAccountId**.
5. Skapa ett Media Services-konto med en Systemhanterad identitet: testet anger den globala variabeln **principalId**.
6. Skapa ett nyckel valv för att bevilja åtkomst till tjänstens huvud namn: testet ställer in den globala variabeln **keyVaultId**.
7. Hämta en Key Vault token: testet ställer in den globala variabeln **keyVaultToken**.
8. Skapa RSA-nyckeln i nyckel valvet: testet ställer in den globala variabeln **keyId**.
9. Uppdatera Media Services kontot för att använda nyckeln med lagrings kontot: det finns inget test skript för den här begäran.

## <a name="define-environment-variables"></a>Definiera miljövariabler

1. Välj den nedrullningsbara listan i miljön för att växla till den miljö som du laddade ned.
1. Upprätta miljövariabler i Postman. De används också som variabler i hakparenteser. Till exempel `{{tenantId}}`.

    - **tenantId**: ditt klient-ID.
    - **servicePrincipalId**: ID för tjänstens huvud namn som du upprättar med din favorit metod, till exempel portal eller cli.
    - **servicePrincipalSecret**: hemligheten som skapats för tjänstens huvud namn.
    - **prenumeration**: ditt PRENUMERATIONS-ID.
    - **storageName**: det namn som du vill ge till lagringen.
    - **accountName**: det Media Services konto namn som du vill använda.
    - **keyVaultName**: nyckel valvets namn som du vill använda.
    - **resourceLocation**: platsen **Central** eller där du vill placera dina resurser. Den här samlingen har bara testats med **Central**.
    - **resourceGroup**: resurs gruppens namn.

    Följande variabler är standard för att arbeta med Azure-resurser. Därför behöver du inte ändra dem.

    - **armResource**: `https://management.core.windows.net`
    - **graphResource**: `https://graph.windows.net/`
    - **keyVaultResource**: `https://vault.azure.net`
    - **armEndpoint**: `management.azure.com`
    - **graphEndpoint**: `graph.windows.net`
    - **aadEndpoint**: `login.microsoftonline.com`
    - **keyVaultDomainSuffix**: `vault.azure.net`

## <a name="send-the-requests"></a>Skicka begär Anden

När du har definierat miljövariabler kan du köra begär anden en i taget i föregående sekvens. Eller så kan du använda Postman-löpare för att köra samlingen.

## <a name="change-the-key"></a>Ändra nyckeln

Media Services identifierar automatiskt när nyckeln ändras. Skapa en annan nyckel version för samma nyckel för att testa den här processen. Media Services ska identifiera nyckeln på mindre än 15 minuter.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda de resurser som du har skapat och *du inte vill fortsätta att fakturera*, tar du bort dem.

## <a name="next-steps"></a>Nästa steg

Gå till nästa artikel om du vill lära dig att:
> [!div class="nextstepaction"]
> [Koda en fjärrfil baserat på URL och strömma videon med REST](stream-files-tutorial-with-rest.md)