---
title: 'Registrera ett webb-API som anropar webb-API: er | Azure'
titleSuffix: Microsoft identity platform
description: 'Lär dig hur du skapar ett webb-API som anropar underordnade webb-API: er (app Registration).'
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
ms.openlocfilehash: af1047c5f890b1b88ae6d043a30704e84b8dc079
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584322"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Ett webb-API som anropar webb-API: app-registrering

Ett webb-API som anropar underordnade webb-API: er har samma registrering som en skyddad webb-API. Följ instruktionerna i [Protected Web API: app Registration](scenario-protected-web-api-app-registration.md).

Eftersom webbappen nu anropar webb-API: er blir det ett konfidentiellt klient program. Det är anledningen till att extra registrerings information krävs: appen måste dela hemligheter (klientautentiseringsuppgifter) med Microsoft Identity Platform.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-behörigheter

Web Apps anropar API: er för användare för vilka Bearer-token togs emot. Webbapparna måste begära delegerade behörigheter. Mer information finns i [lägga till behörigheter för åtkomst till ditt webb-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel i det här scenariot, [app Code Configuration](scenario-web-api-call-api-app-configuration.md).
