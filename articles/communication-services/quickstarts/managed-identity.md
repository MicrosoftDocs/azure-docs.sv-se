---
title: Använda hanterade identiteter i kommunikations tjänster
titleSuffix: An Azure Communication Services quickstart
description: Med hanterade identiteter kan du auktorisera Azure Communication Services-åtkomst från program som körs i virtuella Azure-datorer, Function-appar och andra resurser.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 2/24/2021
ms.author: peiliu
ms.reviewer: mikben
ms.openlocfilehash: 0d25e5dc97c700daf6655ecd270bfda469a9d353
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657662"
---
# <a name="use-managed-identities"></a>Använda hanterade identiteter
Kom igång med Azure Communication Services med hjälp av hanterade identiteter. Kommunikations tjänstens identitet och SMS-klientens bibliotek stöder Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här snabb starten visar hur du auktoriserar åtkomst till identitets-och SMS-klientcertifikat från en Azure-miljö som stöder hanterade identiteter. Det beskriver också hur du testar din kod i en utvecklings miljö.

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

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end