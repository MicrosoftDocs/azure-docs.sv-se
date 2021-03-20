---
title: Hitta ett API för en anpassad-utvecklad app | Azure
description: Så här konfigurerar du de behörigheter du behöver för att komma åt ett visst API i ditt anpassade Azure AD-program
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 28cfb3d8b09c9661d16ac6e7146c50e7043d913a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99581966"
---
# <a name="how-to-find-a-specific-api-needed-for-a-custom-developed-application"></a>Så här hittar du ett specifikt API som krävs för ett anpassat, utvecklat program

Åtkomst till API: er kräver konfiguration av åtkomstscope och roller. Om du vill exponera dina resurs program webb-API: er för klient program, konfigurerar du åtkomstscope och roller för API: et. Om du vill att ett klient program ska ha åtkomst till ett webb-API konfigurerar du behörigheter för åtkomst till API: et i appens registrering.

## <a name="configuring-a-resource-application-to-expose-web-apis"></a>Konfigurera resursprogram för att göra webb API:er tillgängliga

När du exponerar ditt webb-API visas API: et i listan **Välj en API** när du lägger till behörigheter till en app-registrering. Om du vill lägga till åtkomstscope följer du stegen som beskrivs i [Konfigurera ett program för att exponera webb-API: er](quickstart-configure-app-expose-web-apis.md).

## <a name="configuring-a-client-application-to-access-web-apis"></a>Konfigurera ett klient program för åtkomst till webb-API: er

När du lägger till behörigheter till din app-registrering kan du **lägga till API-åtkomst** till exponerade webb-API: er. För att få åtkomst till webb-API: er följer du stegen som beskrivs i [Konfigurera ett klient program för åtkomst till webb-API: er](quickstart-configure-app-access-web-apis.md).

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure Active Directory applikations manifestet](./reference-app-manifest.md)