---
title: Använda hanterade identiteter i kommunikations tjänster
titleSuffix: An Azure Communication Services quickstart
description: Med hanterade identiteter kan du auktorisera Azure Communication Services-åtkomst från program som körs i virtuella Azure-datorer, Function-appar och andra resurser.
services: azure-communication-services
author: peiliu
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: peiliu
ms.reviewer: mikben
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: aedf54c8c958e96b2bbfa31652b4861ff452f75a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107307447"
---
# <a name="use-managed-identities"></a>Använda hanterade identiteter
Kom igång med Azure Communication Services med hjälp av hanterade identiteter. Kommunikations tjänstens identitet och SMS-SDK: er stöder Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här snabb starten visar hur du auktoriserar åtkomst till identitet och SMS-SDK: er från en Azure-miljö som stöder hanterade identiteter. Det beskriver också hur du testar din kod i en utvecklings miljö.

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free)
- En aktiv Azure Communication Services-resurs finns i [skapa en kommunikations tjänst resurs](./create-communication-resource.md) om du inte har någon.
- Om du vill skicka ett SMS behöver du ett [telefonnummer](./telephony-sms/get-phone-number.md).
- En hanterad identitet för en utvecklings miljö finns i [ge åtkomst med hanterad identitet](./managed-identity-from-cli.md)

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om rollbaserad åtkomst kontroll i Azure](../../../articles/role-based-access-control/index.yml)
- [Läs mer om Azure Identity Library för .NET](/dotnet/api/overview/azure/identity-readme)
- [Skapa token för användar åtkomst](../quickstarts/access-tokens.md)
- [Skicka ett SMS-meddelande](../quickstarts/telephony-sms/send.md)
- [Läs mer om SMS](../concepts/telephony-sms/concepts.md)
