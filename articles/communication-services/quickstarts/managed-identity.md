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
ms.openlocfilehash: ffda88da451e25b79112a7adf85026158bd27acc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492361"
---
# <a name="use-managed-identities"></a>Använda hanterade identiteter
Kom igång med Azure Communication Services med hjälp av hanterade identiteter. Kommunikations tjänstens identitet och SMS-klientens bibliotek stöder Azure Active Directory (Azure AD)-autentisering med [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

Den här snabb starten visar hur du auktoriserar åtkomst till identitets-och SMS-klientcertifikat från en Azure-miljö som stöder hanterade identiteter. Det beskriver också hur du testar din kod i en utvecklings miljö.

::: zone pivot="programming-language-csharp"
[!INCLUDE [.NET](./includes/managed-identity-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript](./includes/managed-identity-js.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java](./includes/managed-identity-java.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python](./includes/managed-identity-python.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer om rollbaserad åtkomst kontroll i Azure](../../../articles/role-based-access-control/index.yml)
- [Läs mer om Azure Identity Library för .NET](/dotnet/api/overview/azure/identity-readme)
- [Skapa token för användar åtkomst](../quickstarts/access-tokens.md)
- [Skicka ett SMS-meddelande](../quickstarts/telephony-sms/send.md)
- [Läs mer om SMS](../concepts/telephony-sms/concepts.md)
