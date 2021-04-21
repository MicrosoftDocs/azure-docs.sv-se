---
title: Skydda API Management med klientcertifikatautentisering
titleSuffix: Azure API Management
description: Lär dig hur du hanterar klientcertifikat och säkra servertjänster med hjälp av klientcertifikatautentisering i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9d198f34abe826af2dce0c1ed9950600d4c7bbd3
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812308"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Skydda servertjänster med klientcertifikatautentisering i Azure API Management

API Management kan du skydda åtkomsten till serversidan för ett API med hjälp av klientcertifikat. Den här guiden visar hur du hanterar certifikat i en Azure API Management-tjänstinstans med hjälp av Azure Portal. Den förklarar också hur du konfigurerar ett API för att använda ett certifikat för att få åtkomst till en servertjänst.

Du kan också hantera API Management certifikat med hjälp av [API Management REST API](/rest/api/apimanagement/2020-06-01-preview/certificate).

## <a name="certificate-options"></a>Certifikatalternativ

API Management två alternativ för att hantera certifikat som används för säker åtkomst till servertjänster:

* Referera till ett certifikat som hanteras [i Azure Key Vault](../key-vault/general/overview.md) 
* Lägg till en certifikatfil direkt i API Management

Vi rekommenderar att du använder Key Vault-certifikat eftersom det förbättrar API Management säkerhet:

* Certifikat som lagras i nyckelvalv kan återanvändas i flera tjänster
* Detaljerade [åtkomstprinciper kan](../key-vault/general/security-features.md#privileged-access) tillämpas på certifikat som lagras i nyckelvalv
* Certifikat som uppdateras i nyckelvalvet roteras automatiskt i API Management. Efter uppdateringen i nyckelvalvet uppdateras ett certifikat i API Management inom 4 timmar. Du kan också uppdatera certifikatet manuellt med hjälp Azure Portal eller via REST API.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Om du inte har skapat en API Management-tjänstinstans än kan du gå [till Skapa en API Management-tjänstinstans.][Create an API Management service instance]
* Du bör ha din servertjänst konfigurerad för klientcertifikatautentisering. Information om hur du konfigurerar certifikatautentisering i Azure App Service finns i [den här artikeln.][to configure certificate authentication in Azure WebSites refer to this article] 
* Du behöver åtkomst till certifikatet och lösenordet för hantering i ett Azure-nyckelvalv eller ladda upp till API Management tjänsten. Certifikatet måste vara i **PFX-format.** Själv signerade certifikat tillåts.

### <a name="prerequisites-for-key-vault-integration"></a>Förutsättningar för Key Vault-integrering

1. Anvisningar för hur du skapar ett nyckelvalv finns [i Snabbstart: Skapa ett nyckelvalv med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).
1. Aktivera en system-tilldelad eller användar [tilldelad hanterad identitet](api-management-howto-use-managed-service-identity.md) i API Management instansen.
1. Tilldela en [åtkomstprincip för nyckelvalv](../key-vault/general/assign-access-policy-portal.md) till den hanterade identiteten med behörighet att hämta och lista certifikat från valvet. Så här lägger du till principen:
    1. Navigera till ditt nyckelvalv i portalen.
    1. Välj **Inställningar > Åtkomstprinciper > + Lägg till åtkomstprincip.**
    1. Välj **Certifikatbehörigheter** och välj sedan **Hämta** och **lista**.
    1. I **Välj huvudnamn** väljer du resursnamnet för din hanterade identitet. Om du använder en system tilldelad identitet är huvudnamnet namnet på din API Management instansen.
1. Skapa eller importera ett certifikat till nyckelvalvet. Se [Snabbstart: Ange och hämta ett certifikat från Azure Key Vault med hjälp av Azure Portal](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Lägga till ett nyckelvalvscertifikat

Se [Krav för Key Vault-integrering.](#prerequisites-for-key-vault-integration)

> [!CAUTION]
> När du använder ett nyckelvalvscertifikat i API Management bör du vara noga med att inte ta bort certifikatet, nyckelvalvet eller den hanterade identiteten som används för att få åtkomst till nyckelvalvet.

Så här lägger du till ett nyckelvalvscertifikat API Management:

1. I [Azure Portal](https://portal.azure.com)navigerar du till din API Management instans.
1. Under **Säkerhet** väljer du **Certifikat.**
1. Välj **Certifikat**  >  **+ Lägg till**.
1. I **Id** anger du ett val av namn.
1. I **Certifikat** väljer du **Nyckelvalv.**
1. Ange identifieraren för ett nyckelvalvscertifikat eller välj **Välj för** att välja ett certifikat från ett nyckelvalv.
    > [!IMPORTANT]
    > Om du själv anger ett nyckelvalvscertifikats-ID måste du kontrollera att det inte har versionsinformation. Annars roteras inte certifikatet automatiskt i API Management efter en uppdatering i nyckelvalvet.
1. I **Klientidentitet** väljer du en system tilldelad eller en befintlig användar tilldelad hanterad identitet. Lär dig hur [du lägger till eller ändrar hanterade identiteter i API Management tjänsten](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Identiteten måste ha behörighet att hämta och lista certifikat från nyckelvalvet. Om du inte redan har konfigurerat åtkomst till nyckelvalvet uppmanas du API Management så att den automatiskt kan konfigurera identiteten med nödvändiga behörigheter.
1. Välj **Lägg till**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Lägga till nyckelvalvscertifikat":::

## <a name="upload-a-certificate"></a>Ladda upp ett certifikat

Så här laddar du upp ett klientcertifikat till API Management: 

1. I [Azure Portal](https://portal.azure.com)navigerar du till din API Management instans.
1. Under **Säkerhet** väljer du **Certifikat.**
1. Välj **Certifikat**  >  **+ Lägg till**.
1. I **Id** anger du ett val av namn.
1. I **Certifikat** väljer du **Anpassad.**
1. Bläddra för att välja certifikatets PFX-fil och ange dess lösenord.
1. Välj **Lägg till**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Ladda upp klientcertifikat":::

När certifikatet har laddats upp visas det i **fönstret** Certifikat. Om du har många certifikat anteckning av tumavtrycket för det önskade certifikatet för att konfigurera ett API att använda ett klientcertifikat för [gatewayautentisering](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Om du vill inaktivera verifiering av certifikatkedjan när du till exempel använder ett själv signerat certifikat följer du stegen som beskrivs i Själv [signerade certifikat](#self-signed-certificates)senare i den här artikeln.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Konfigurera ett API för att använda klientcertifikat för gatewayautentisering

1. I [Azure Portal](https://portal.azure.com)navigerar du till din API Management instans.
1. Under **API:er** väljer du **API:er.**
1. Välj ett API i listan. 
2. Välj **redigeringsikonen** i avsnittet **Backend på fliken** Design.
3. I **Autentiseringsuppgifter för gateway** väljer du **Klientcertifikat** och väljer certifikatet i listrutan.
1. Välj **Spara**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Använda klientcertifikat för gatewayautentisering":::

> [!CAUTION]
> Den här ändringen börjar gälla omedelbart och anrop till åtgärder för det API:et använder certifikatet för att autentisera på serverservern.

> [!TIP]
> När ett certifikat har angetts för gatewayautentisering för serverdelstjänsten för ett API, blir det en del av principen för det API:et och kan visas i principredigeraren.

## <a name="self-signed-certificates"></a>Själv signerade certifikat

Om du använder själv signerade certifikat måste du inaktivera verifiering av certifikatkedjan för att API Management kommunicera med serversystemet. Annars returneras felkoden 500. För att konfigurera detta kan du använda (för ny [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) backend) eller (för befintliga [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) backend)PowerShell-cmdlets och ange `-SkipCertificateChainValidation` parametern till `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Ta bort ett klientcertifikat

Om du vill ta bort ett certifikat markerar du det och väljer **sedan Ta** bort från snabbmenyn (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Ta bort ett certifikat":::

> [!IMPORTANT]
> Om certifikatet refereras till av några principer visas en varningsskärm. Om du vill ta bort certifikatet måste du först ta bort certifikatet från alla principer som är konfigurerade att använda det.

## <a name="next-steps"></a>Nästa steg

* [Så skyddar du API:er genom att autentisera klientcertifikat i API Management](api-management-howto-mutual-certificates-for-clients.md)
* Läs mer [om principer i API Management](api-management-howto-policies.md)


[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

