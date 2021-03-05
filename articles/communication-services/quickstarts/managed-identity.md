---
title: Använda hanterade identiteter i kommunikations tjänster (.NET)
titleSuffix: An Azure Communication Services quickstart
description: Med hanterade identiteter kan du auktorisera Azure Communication Services-åtkomst från program som körs i virtuella Azure-datorer, Function-appar och andra resurser.
services: azure-communication-services
author: stefang931
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 12/04/2020
ms.author: gistefan
ms.reviewer: mikben
ms.openlocfilehash: ee691d4809a68a0ba60f60a2240b76a1e53104bc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102171581"
---
# <a name="use-managed-identities-net"></a>Använda hanterade identiteter (.NET)

Kom igång med Azure Communication Services med hjälp av hanterade identiteter i .NET. Kommunikations tjänsterna administration och SMS-klient bibliotek stöder Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här snabb starten visar hur du auktoriserar åtkomst till administrations-och SMS-klientcertifikat från en Azure-miljö som stöder hanterade identiteter. Det beskriver också hur du testar din kod i en utvecklings miljö.

## <a name="prerequisites"></a>Förutsättningar

 - Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free)
 - En aktiv kommunikations tjänst resurs och anslutnings sträng. [Skapa en kommunikations tjänst resurs](./create-communication-resource.md?pivots=platform-azp&tabs=windows).

## <a name="setting-up"></a>Konfigurera

### <a name="enable-managed-identities-on-a-virtual-machine-or-app-service"></a>Aktivera hanterade identiteter på en virtuell dator eller App Service

Hanterade identiteter bör vara aktiverade på de Azure-resurser som du ska auktorisera. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [App Services](../../app-service/overview-managed-identity.md)

#### <a name="assign-azure-roles-with-the-azure-portal"></a>Tilldela Azure-roller med Azure Portal

1. Gå till Azure-portalen.
1. Gå till resursen Azure Communication Service.
1. Navigera till Access Control (IAM)-menyn – > + Lägg till > Lägg till roll tilldelning.
1. Välj rollen "deltagare" (detta är den enda roll som stöds).
1. Välj "användare tilldelad hanterad identitet" (eller "systemtilldelad hanterad identitet") och välj sedan den önskade identiteten. Spara ditt val.

![Hanterad identitets roll](media/managed-identity-assign-role.png)

#### <a name="assign-azure-roles-with-powershell"></a>Tilldela Azure-roller med PowerShell

Information om hur du tilldelar roller och behörigheter med hjälp av PowerShell finns i [lägga till eller ta bort Azure roll tilldelningar med Azure PowerShell](../../../articles/role-based-access-control/role-assignments-powershell.md)

## <a name="add-managed-identity-to-your-communication-services-solution"></a>Lägg till hanterad identitet i kommunikations tjänst lösningen

### <a name="install-the-client-library-packages"></a>Installera klient biblioteks paketen

```console
dotnet add package Azure.Communication.Identity
dotnet add package Azure.Communication.Configuration
dotnet add package Azure.Communication.Sms
dotnet add package Azure.Identity
```

### <a name="use-the-client-library-packages"></a>Använda klient biblioteks paketen

Lägg till följande `using` direktiv i koden för att använda Azure-identiteten och Azure Storage klient biblioteken.

```csharp
using Azure.Identity;
using Azure.Communication.Identity;
using Azure.Communication.Configuration;
using Azure.Communication.Sms;
```

Exemplen nedan använder [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential). Den här autentiseringsuppgiften är lämplig för produktions-och utvecklings miljöer.

### <a name="create-an-identity-and-issue-a-token"></a>Skapa en identitet och utfärda en token

I följande kod exempel visas hur du skapar ett tjänst klient objekt med Azure Active Directory tokens, och sedan använder klienten för att utfärda en token för en ny användare:

```csharp
     public async Task<Response<CommunicationUserToken>> CreateIdentityAndIssueTokenAsync(Uri resourceEndpoint) 
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          var client = new CommunicationIdentityClient(resourceEndpoint, credential);
          var identityResponse = await client.CreateUserAsync();
     
          var tokenResponse = await client.IssueTokenAsync(identity, scopes: new [] { CommunicationTokenScope.VoIP });

          return tokenResponse;
     }
```

### <a name="send-an-sms-with-azure-active-directory-tokens"></a>Skicka ett SMS med Azure Active Directory-token

Följande kod exempel visar hur du skapar ett tjänst klient objekt med Azure Active Directory tokens, och sedan använder klienten för att skicka ett SMS-meddelande:

```csharp

     public async Task SendSmsAsync(Uri resourceEndpoint, PhoneNumber from, PhoneNumber to, string message)
     {
          TokenCredential credential = new DefaultAzureCredential();
     
          SmsClient smsClient = new SmsClient(resourceEndpoint, credential);
          smsClient.Send(
               from: from,
               to: to,
               message: message,
               new SendSmsOptions { EnableDeliveryReport = true } // optional
          );
     }
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om autentisering](../concepts/authentication.md)

Du kanske också vill:

- [Lär dig mer om rollbaserad åtkomst kontroll i Azure](../../../articles/role-based-access-control/index.yml)
- [Läs mer om Azure Identity Library för .NET](/dotnet/api/overview/azure/identity-readme)
- [Skapa token för användar åtkomst](../quickstarts/access-tokens.md)
- [Skicka ett SMS-meddelande](../quickstarts/telephony-sms/send.md)
- [Läs mer om SMS](../concepts/telephony-sms/concepts.md)