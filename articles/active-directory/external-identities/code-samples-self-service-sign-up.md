---
title: Kod exempel för API-koppling för användar flöden – Azure AD
description: Kod exempel för API-kopplingar i självbetjänings registrerings flöden för Azure Active Directory externa identiteter.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3995395cfd6256689bedc7a4a3c83effc65c0b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87905945"
---
# <a name="samples-for-external-identities-self-service-sign-up"></a>Exempel på självbetjänings registrering för externa identiteter

Följande tabeller innehåller länkar till kod exempel för att använda webb-API: er i dina självbetjänings registrerings flöden med [API-kopplingar](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Snabb start för Azure-koppling i Azure

| Exempel                                                                                                                          | Beskrivning                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Den här .NET Core Azure Function-exemplet visar hur du begränsar registreringen till vissa klient domäner och validerar information från användaren. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Det här Node.js Azure Function-exemplet visar hur du begränsar registreringen till vissa klient domäner och validerar information från användaren.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Det här python Azure Function-exemplet visar hur du begränsar registreringen till vissa klient domäner och validerar information från användaren.    |

<!-- \| [Java](../../azure-docs-pr/articles/active-directory/b2b/invite-internal-users.md#use-the-invitation-api-to-send-a-b2b-invitation) |  The sample below illustrates how to call the invitation API to invite an internal user as a B2B user. | -->

## <a name="custom-approval-workflows"></a>Anpassade arbets flöden för godkännande

| Exempel | Beskrivning |
|--------| ----------- |
| [Manuellt godkännande arbets flöde](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connectors-approvals) | Det här exemplet demonstrerar ett arbets flöde från slut punkt till slut punkt för att hantera skapande av gäst användar konton i självbetjänings registrering |

## <a name="identity-verification"></a>Identitets verifiering

| Exempel                                                                                                            | Beskrivning                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| [IDology](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-idology-identity-verification) | Det här exemplet visar hur du verifierar en användar identitet som en del av din självbetjänings registrering genom att använda en API-anslutning för att integrera med IDology. |
| [Experian](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-experian-identity-verification) | Det här exemplet visar hur du verifierar en användar identitet som en del av din självbetjänings registrering genom att använda en API-anslutning för att integrera med Experian:. |
