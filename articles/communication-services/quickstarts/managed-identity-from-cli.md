---
title: Skapa ett Azure Active Directory hanterat identitets program från Azure CLI
titleSuffix: An Azure Communication Services quickstart
description: Med hanterade identiteter kan du auktorisera Azure Communication Services-åtkomst från program som körs i virtuella Azure-datorer, Function-appar och andra resurser. Den här snabb starten fokuserar på att hantera identitet med hjälp av Azure CLI.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: b49ee61f3ee4ea8e8a177bca6aa566a07b7ef113
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044744"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>Bevilja åtkomst med hanterad identitet till kommunikations resursen i utvecklings miljön

Klient biblioteket för Azure Identity ger Azure Active Directory (Azure AD) stöd för token-autentisering för Azure SDK. De senaste versionerna av klient biblioteken för Azure Communication Services för .NET, Java, python och Java Script integreras med Azure Identity Library för att tillhandahålla ett enkelt och säkert sätt att skaffa en OAuth 2,0-token för auktorisering av Azure Communication Services-begäranden.

En fördel med klient biblioteket för Azure Identity är att du kan använda samma kod för att autentisera över flera tjänster oavsett om programmet körs i utvecklings miljön eller i Azure. Klient biblioteket för Azure Identity autentiserar ett säkerhets objekt. När din kod körs i Azure är säkerhets objekt en hanterad identitet för Azure-resurser. I utvecklings miljön finns inte den hanterade identiteten, så klient biblioteket autentiserar antingen användaren eller ett registrerat program i test syfte.

## <a name="prerequisites"></a>Förutsättningar

 - Azure CLI. [Installationsguide](/cli/azure/install-azure-cli)
 - Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free)

## <a name="setting-up"></a>Konfigurera

Hanterade identiteter bör vara aktiverade på de Azure-resurser som du ska auktorisera. Information om hur du aktiverar hanterade identiteter för Azure-resurser finns i någon av följande artiklar:

- [Azure Portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-mall](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager klient bibliotek](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Apptjänster](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Autentisera ett registrerat program i utvecklings miljön

Om utvecklings miljön inte stöder enkel inloggning eller inloggning via en webbläsare kan du använda ett registrerat program för att autentisera från utvecklings miljön.

### <a name="creating-an-azure-active-directory-registered-application"></a>Skapa ett Azure Active Directory registrerat program

Om du vill skapa ett registrerat program från Azure CLI måste du vara inloggad på det Azure-konto där du vill att åtgärderna ska äga rum. Om du vill göra detta kan du använda `az login` kommandot och ange dina autentiseringsuppgifter i webbläsaren. När du har loggat in på ditt Azure-konto från CLI kan vi anropa `az ad sp create-for-rbac` kommandot för att skapa det registrerade programmet.

I följande exempel används Azure CLI för att skapa ett nytt registrerat program

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

`az ad sp create-for-rbac`Kommandot returnerar en lista över egenskaper för tjänstens huvud namn i JSON-format. Kopiera värdena så att du kan använda dem för att skapa de miljövariabler som krävs i nästa steg.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> Det kan ta några minuter att sprida Azures roll tilldelningar.

#### <a name="set-environment-variables"></a>Ange miljövariabler

Klient biblioteket för Azure Identity läser värden från tre miljövariabler vid körning för att autentisera programmet. I följande tabell beskrivs det värde som ska anges för varje miljö variabel.

|Miljövariabel|Värde
|-|-
|`AZURE_CLIENT_ID`|`appId` värde från genererad JSON 
|`AZURE_TENANT_ID`|`tenant` värde från genererad JSON
|`AZURE_CLIENT_SECRET`|`password` värde från genererad JSON

> [!IMPORTANT]
> När du har ställt in miljövariablerna stänger du och öppnar konsol fönstret igen. Om du använder Visual Studio eller en annan utvecklings miljö kan du behöva starta om den för att kunna registrera nya miljövariabler.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om autentisering](../concepts/authentication.md)

Du kanske också vill:

- [Läs mer om Azure Identity Library](/dotnet/api/overview/azure/identity-readme)