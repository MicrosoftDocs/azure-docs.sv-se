---
title: 'Registrera en webbapp som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du registrerar en webbapp som anropar webb-API: er'
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: bb9a1ca6c2c81e3b0d5dbeff06f4de012446cf79
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756329"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>En webbapp som anropar webb-API: app-registrering

En webbapp som anropar webb-API: er har samma registrering som en webbapp som loggar användare i. Följ sedan anvisningarna i [en webbapp som loggar in användare: app-registrering](scenario-web-app-sign-user-app-registration.md).

Men även om webbappen nu anropar webb-API: er blir det ett konfidentiellt klient program. Därför krävs en extra registrering. Appen måste dela klientautentiseringsuppgifterna, eller *hemligheter*, med Microsoft Identity Platform.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Web Apps anropar API: er för den inloggade användaren. För att göra det måste de begära *delegerade behörigheter*. Mer information finns i [lägga till behörigheter för åtkomst till ditt webb-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [kod konfiguration](scenario-web-app-call-api-app-configuration.md).
