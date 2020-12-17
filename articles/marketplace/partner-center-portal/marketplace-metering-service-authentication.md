---
title: Strategier för inläsning av marknads plats för avläsning av tjänst | Azure Marketplace
description: Metoder för autentisering av mätnings tjänst som stöds i Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/21/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: b418a9cae6f6d58dbe82babcfe6fe1e1a5027d43
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657081"
---
# <a name="marketplace-metering-service-authentication-strategies"></a>Strategier för att autentisera service för Marketplace-mätning

Marknads mätnings tjänsten har stöd för två olika metoder för autentisering:

* [Azure AD-säkerhetstoken](../../active-directory/develop/access-tokens.md)
* [Hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md) 

Vi förklarar när och hur du använder olika autentiserings strategier för att på ett säkert sätt skicka anpassade mätare med Marketplace-avläsning.

## <a name="using-the-azure-ad-security-token"></a>Använda Azure AD-säkerhetstoken

Tillämpliga erbjudande typer är transactable-SaaS och Azure-program med hanterad program Plans typ.  

Skicka anpassade mätare genom att använda ett fördefinierat, fast Azure AD-program-ID för autentisering.

Detta är det enda tillgängliga alternativet för SaaS-erbjudanden. Det är ett obligatoriskt steg för att publicera ett SaaS-erbjudande som beskrivs i [Registrera ett SaaS-program](./pc-saas-registration.md).

För Azure-program med hanterade program planer bör du överväga att använda den här strategin i följande fall:

* Du har redan en mekanism för att kommunicera med Server dels tjänsterna och du vill utöka den här mekanismen för att skicka anpassade mätare från en central tjänst.
* Du har komplex anpassad mätare-logik.  Kör den här logiken på en central plats i stället för de hanterade program resurserna.

När du har registrerat ditt program kan du program mässigt begära en Azure AD-säkerhetstoken. Utgivaren förväntas använda denna token och göra en begäran om att lösa det.

Mer information om dessa tokens finns [Azure Active Directory åtkomsttoken](../../active-directory/develop/access-tokens.md).

### <a name="get-a-token-based-on-the-azure-ad-app"></a>Hämta en token baserat på Azure AD-appen

#### <a name="http-method"></a>HTTP-metod

**EFTER**

#### <a name="request-url"></a>*Begärans-URL*

**`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`**

#### <a name="uri-parameter"></a>*URI-parameter*

|  **Parameternamn** |  **Obligatoriskt**  |  **Beskrivning**          |
|  ------------------ |--------------- | ------------------------  |
|  `tenantId`         |   Sant         | Klient-ID för det registrerade Azure AD-programmet.   |
| | | |

#### <a name="request-header"></a>*Begär ande huvud*

|  **Huvudnamn**    |  **Obligatoriskt**  |  **Beskrivning**          |
|  ------------------ |--------------- | ------------------------  |
|  `Content-Type`     |   Sant         | Innehålls typ som är associerad med begäran. Standardvärdet är `application/x-www-form-urlencoded`.  |
| | | |

#### <a name="request-body"></a>*Brödtext i begäran*

|  **Egenskapsnamn**  |  **Obligatoriskt**  |  **Beskrivning**          |
|  ------------------ |--------------- | ------------------------  |
|  `Grant_type`       |   Sant         | Typ av beviljande. Använd `client_credentials`. |
|  `Client_id`        |   Sant         | Klient/app-identifierare som är associerad med Azure AD-appen.|
|  `client_secret`    |   Sant         | Hemlighet som är associerad med Azure AD-appen.  |
|  `Resource`         |   Sant         | Mål resurs för vilken token begärs. Använd `20e940b3-4c77-4b0b-9a53-9e16a1b010a7`. |
| | | |

#### <a name="response"></a>*Response*

|  **Namn**    |  **Typ**  |  **Beskrivning**          |
|  ------------------ |--------------- | ----------------------  |
|  `200 OK`     |   `TokenResponse`    | Begäran lyckades.  |
| | | |

#### <a name="tokenresponse"></a>*TokenResponse*

Token för samplings svar:

```JSON
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

## <a name="using-the-azure-managed-identities-token"></a>Använda Azure-Managed Identities-token

Tillämplig erbjudande typ är Azure-program med typ av hanterad program plan.

Med den här metoden kan den distribuerade resurs identiteten autentiseras för att skicka anpassade mätares användnings händelser.  Du kan bädda in koden som avger användning inom gränserna för din distribution.

>[!Note]
>Publisher bör se till att de resurser som utvecklar användningen är låsta, så den kommer inte att manipuleras.

Ditt hanterade program kan innehålla olika typer av resurser, från Virtual Machines till Azure Functions.  Mer information om hur du autentiserar med hanterade identiteter för olika tjänster finns i [så här använder du hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md#how-can-i-use-managed-identities-for-azure-resources).

Följ till exempel stegen nedan för att autentisera med en virtuell Windows-dator

1. Kontrol lera att hanterad identitet har kon figurer ATS med någon av metoderna:
    * [Azure Portal användar gränssnitt](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
    * [CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
    * [PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
    * [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
    * [Rest](../../active-directory/managed-identities-azure-resources/qs-configure-rest-vm.md#system-assigned-managed-identity))
    * [Azure-SDK:er](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

1. Hämta en åtkomsttoken för program-ID för Marketplace för avläsning av tjänst ( `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` ) med system identitet, RDP till den virtuella datorn, öppna PowerShell-konsolen och kör kommandot nedan

    ```powershell
    # curl is an alias to Web-Invoke PowerShell command
    # Get system identity access tokenn
    $MetadataUrl = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F"
    $Token = curl -H @{"Metadata" = "true"} $MetadataUrl | Select-Object -Expand Content | ConvertFrom-Json
    $Headers = @{}
    $Headers.Add("Authorization","$($Token.token_type) "+ " " + "$($Token.access_token)")
    ```

1. Hämta det hanterade App-ID: t från den aktuella resurs gruppens egenskap ' ManagedBy '

    ```powershell
    # Get subscription and resource group
    $metadata = curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content | ConvertFrom-Json 

    # Make sure the system identity has at least reader permission on the resource group
    $managementUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "?api-version=2019-10-01"
    $resourceGroupInfo = curl -Headers $Headers $managementUrl | select -ExpandProperty Content | ConvertFrom-Json
    $managedappId = $resourceGroupInfo.managedBy 
    ```

1. Tjänsten för avläsning av program vara måste rapportera användning på ett `resourceID` , och `resourceUsageId` om ett hanterat program.

    ```powershell
    # Get resourceUsageId from the managed app
    $managedAppUrl = "https://management.azure.com/subscriptions/" + $metadata.compute.subscriptionId + "/resourceGroups/" + $metadata.compute.resourceGroupName + "/providers/Microsoft.Solutions/applications/" + $managedappId + "\?api-version=2019-07-01"
    $ManagedApp = curl $managedAppUrl -H $Headers | Select-Object -Expand Content | ConvertFrom-Json
    # Use this resource ID to emit usage 
    $resourceUsageId = $ManagedApp.properties.billingDetails.resourceUsageId
    ```

1. Använd [API för Marketplace för avläsning](./marketplace-metering-service-apis.md) av program vara för att generera användning.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett erbjudande för Azure-program](../create-new-azure-apps-offer.md)
* [Planera ett SaaS-erbjudande](../plan-saas-offer.md)
