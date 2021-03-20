---
title: Cookie-definitioner
titleSuffix: Azure AD B2C
description: Tillhandahåller definitioner för de cookies som används i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5e44e2e1eb37e808e60134a6fba5051552e84029
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85389351"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Cookies-definitioner för Azure AD B2C

I följande avsnitt finns information om de cookies som används i Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSite

Tjänsten Microsoft Azure AD B2C är kompatibel med SameSite Browser-konfigurationer, inklusive stöd för `SameSite=None` med- `Secure` attributet.

För att skydda åtkomsten till webbplatser kommer webbläsare att införa en ny säker modell som förutsätter att alla cookies bör skyddas från extern åtkomst om inget annat anges. Chrome-webbläsaren är den första att implementera den här ändringen, från och med [Chrome 80 i februari 2020](https://www.chromium.org/updates/same-site). Mer information om hur du förbereder ändringen av Chrome finns i [utvecklare: bli redo för nya SameSite = none; Inställningar för säker cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) på krom-bloggen.

Utvecklare måste använda den nya cookie-inställningen för `SameSite=None` att ange cookies för åtkomst över platser. När `SameSite=None` attributet finns måste ett ytterligare `Secure` attribut användas så att cookies från flera platser bara kan nås via HTTPS-anslutningar. Validera och testa alla dina program, inklusive de program som använder Azure AD B2C.

Mer information finns i:

* [Hantera SameSite-cookieändringar i webbläsaren Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Påverkan på kund webbplatser och Microsoft-tjänster och produkter i Chrome version 80 eller senare](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

I följande tabell visas de cookies som används i Azure AD B2C.

| Name | Domain | Förfallodatum | Syfte |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Avsluta [webbläsarsessionen](session-behavior.md) | Innehåller användar medlemskaps data mellan klienter. Klienterna som en användare är medlem av och medlemskaps nivån (administratör eller användare). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att dirigera begär anden till lämplig produktions instans. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att spåra transaktioner (antal autentiseringsbegäranden till Azure AD B2C) och den aktuella transaktionen. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att underhålla SSO-sessionen. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, anpassad domän | Slutet på [webbläsarsessionen](session-behavior.md), lyckad autentisering | Används för att underhålla status för begäran. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Förfalsknings-token för begäran mellan webbplatser som används för CRSF-skydd. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för Azure AD B2C nätverks routning. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Kontext |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att lagra medlemskaps data för resurs leverantörens klient organisation. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att lagra relä-cookien. |
