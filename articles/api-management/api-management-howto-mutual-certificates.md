---
title: Skydda API Management Server del med autentisering av klient certifikat
titleSuffix: Azure API Management
description: Lär dig hantera klient certifikat och säkra server dels tjänster med autentisering av klient certifikat i Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
ms.service: api-management
ms.topic: article
ms.date: 01/26/2021
ms.author: apimpm
ms.openlocfilehash: 2e4a398ab71878134887fb8fba025cd8aa6122ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99492864"
---
# <a name="secure-backend-services-using-client-certificate-authentication-in-azure-api-management"></a>Säkra server dels tjänster med autentisering av klient certifikat i Azure API Management

API Management ger dig säker åtkomst till backend-tjänsten för ett API med hjälp av klient certifikat. Den här guiden visar hur du hanterar certifikat i en Azure API Management Service-instans med hjälp av Azure Portal. Det beskriver också hur du konfigurerar ett API för att använda ett certifikat för att få åtkomst till en backend-tjänst.

Du kan också hantera API Management certifikat med hjälp av [API Management REST API](/rest/api/apimanagement/2020-06-01-preview/certificate).

## <a name="certificate-options"></a>Certifikat alternativ

API Management innehåller två alternativ för att hantera certifikat som används för att skydda åtkomst till backend-tjänster:

* Referera till ett certifikat som hanteras i [Azure Key Vault](../key-vault/general/overview.md) 
* Lägg till en certifikat fil direkt i API Management

Att använda Key Vault-certifikat rekommenderas eftersom det bidrar till att förbättra API Management säkerhet:

* Certifikat som lagras i nyckel valv kan återanvändas mellan tjänster
* Detaljerade [åtkomst principer](../key-vault/general/secure-your-key-vault.md#data-plane-and-access-policies) kan tillämpas på certifikat som lagras i nyckel valv
* Certifikat som uppdateras i nyckel valvet roteras automatiskt i API Management. Efter uppdateringen i nyckel valvet uppdateras ett certifikat i API Management inom 4 timmar. Du kan också uppdatera certifikatet manuellt med hjälp av Azure Portal eller via hanterings REST API.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Om du inte har skapat en API Management tjänst instans ännu, se [skapa en API Management tjänst instans][Create an API Management service instance].
* Du bör ha din server dels tjänst konfigurerad för autentisering av klient certifikat. Information om hur du konfigurerar certifikatautentisering i Azure App Service finns i [den här artikeln][to configure certificate authentication in Azure WebSites refer to this article]. 
* Du behöver åtkomst till certifikatet och lösen ordet för hantering i ett Azure Key Vault eller ladda upp till tjänsten API Management. Certifikatet måste vara i **PFX** -format. Självsignerade certifikat är tillåtna.

### <a name="prerequisites-for-key-vault-integration"></a>Krav för Key Vault-integrering

1. Anvisningar för hur du skapar ett nyckel valv finns i [snabb start: skapa ett nyckel valv med hjälp av Azure Portal](../key-vault/general/quick-create-portal.md).
1. Aktivera en tilldelad eller användardefinierad [hanterad identitet](api-management-howto-use-managed-service-identity.md) i API Management-instansen.
1. Tilldela en [åtkomst princip för nyckel valvet](../key-vault/general/assign-access-policy-portal.md) till den hanterade identiteten med behörigheter för att hämta och lista certifikat från valvet. Så här lägger du till principen:
    1. I portalen navigerar du till ditt nyckel valv.
    1. Välj **inställningar > åtkomst principer > + Lägg till åtkomst princip**.
    1. Välj **certifikat behörigheter** och välj sedan **Hämta** och **lista**.
    1. I **Välj huvud** namn väljer du resurs namnet för den hanterade identiteten. Om du använder en tilldelad identitet är huvud kontot namnet på din API Management-instans.
1. Skapa eller importera ett certifikat till nyckel valvet. Se [snabb start: Ange och hämta ett certifikat från Azure Key Vault med hjälp av Azure Portal](../key-vault/certificates/quick-create-portal.md).

[!INCLUDE [api-management-key-vault-network](../../includes/api-management-key-vault-network.md)]

## <a name="add-a-key-vault-certificate"></a>Lägg till ett Key Vault-certifikat

Se [krav för Key Vault-integrering](#prerequisites-for-key-vault-integration).

> [!CAUTION]
> När du använder ett Key Vault-certifikat i API Management bör du vara noga med att inte ta bort certifikatet, nyckel valvet eller hanterad identitet som används för att komma åt nyckel valvet.

Så här lägger du till ett Key Vault-certifikat i API Management:

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Under **säkerhet** väljer du **certifikat**.
1. Välj **certifikat**  >  **+ Lägg till**.
1. I **ID** anger du önskat namn.
1. I **certifikat** väljer du **nyckel valv**.
1. Ange identifieraren för ett Key Vault-certifikat eller Välj **Välj** för att välja ett certifikat från ett nyckel valv.
    > [!IMPORTANT]
    > Om du anger ett certifikat-ID för Key Vault själv kontrollerar du att det inte finns någon versions information. Annars roterar inte certifikatet automatiskt i API Management efter en uppdatering i nyckel valvet.
1. I **klient identitet** väljer du en tilldelad dator eller en befintlig användardefinierad hanterad identitet. Lär dig hur du [lägger till eller ändrar hanterade identiteter i din API Management-tjänst](api-management-howto-use-managed-service-identity.md).
    > [!NOTE]
    > Identiteten måste ha behörighet att hämta och lista certifikat från nyckel valvet. Om du inte redan har konfigurerat åtkomst till nyckel valvet meddelar API Management dig så att den automatiskt kan konfigurera identiteten med de behörigheter som krävs.
1. Välj **Lägg till**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-kv.png" alt-text="Lägg till Key Vault-certifikat":::

## <a name="upload-a-certificate"></a>Ladda upp ett certifikat

Så här överför du ett klient certifikat till API Management: 

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Under **säkerhet** väljer du **certifikat**.
1. Välj **certifikat**  >  **+ Lägg till**.
1. I **ID** anger du önskat namn.
1. I **certifikat** väljer du **anpassad**.
1. Bläddra för att välja filen Certificate. pfx och ange lösen ordet.
1. Välj **Lägg till**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-add.png" alt-text="Ladda upp klient certifikat":::

När certifikatet har överförts visas det i fönstret **certifikat** . Om du har många certifikat ska du anteckna tumavtrycket för det önskade certifikatet för att konfigurera ett API för att använda ett klient certifikat för [Gateway-autentisering](#configure-an-api-to-use-client-certificate-for-gateway-authentication).

> [!NOTE]
> Om du vill inaktivera verifiering av certifikat kedjan när du använder, till exempel ett självsignerat certifikat, följer du stegen som beskrivs i [självsignerade certifikat](#self-signed-certificates), längre fram i den här artikeln.

## <a name="configure-an-api-to-use-client-certificate-for-gateway-authentication"></a>Konfigurera ett API för att använda klient certifikat för gateway-autentisering

1. I [Azure Portal](https://portal.azure.com)navigerar du till API Management-instansen.
1. Under **API: er** väljer du **API: er**.
1. Välj ett API i listan. 
2. På fliken **design** väljer du ikonen redigerare i **Server delen** .
3. I **Gateway-autentiseringsuppgifter** väljer du **klient certifikat** och väljer ditt certifikat i list rutan.
1. Välj **Spara**.

    :::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png" alt-text="Använd klient certifikat för gateway-autentisering":::

> [!CAUTION]
> Den här ändringen börjar gälla omedelbart och anrop till åtgärder i detta API använder certifikatet för att autentisera på backend-servern.

> [!TIP]
> När ett certifikat anges för gateway-autentisering för backend-tjänsten för ett API, blir det en del av principen för detta API och kan visas i princip redigeraren.

## <a name="self-signed-certificates"></a>Självsignerade certifikat

Om du använder självsignerade certifikat måste du inaktivera verifiering av certifikat kedjan för att API Management ska kunna kommunicera med Server dels systemet. Annars returneras en felkod på 500. Om du vill konfigurera detta kan du använda [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) PowerShell-cmdletarna (för ny server del) eller [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (för befintliga Server delar) och ange `-SkipCertificateChainValidation` parametern till `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

## <a name="delete-a-client-certificate"></a>Ta bort ett klient certifikat

Om du vill ta bort ett certifikat markerar du det och väljer **ta bort** från snabb menyn (**...**).

:::image type="content" source="media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png" alt-text="Ta bort ett certifikat":::

> [!IMPORTANT]
> Om certifikatet refereras till av eventuella principer visas en varnings skärm. Om du vill ta bort certifikatet måste du först ta bort certifikatet från alla principer som har kon figurer ATS för att använda det.

## <a name="next-steps"></a>Nästa steg

* [Så skyddar du API:er genom att autentisera klientcertifikat i API Management](api-management-howto-mutual-certificates-for-clients.md)
* Lär dig mer om [principer i API Management](api-management-howto-policies.md)


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

