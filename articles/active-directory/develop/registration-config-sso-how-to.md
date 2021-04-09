---
title: Konfigurera enkel inloggning för program
description: Så här konfigurerar du enkel inloggning för ett anpassat program som du utvecklar och registrerar med Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 60a07145e38c19f577ad9864efb599e7b244aa83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653719"
---
# <a name="how-to-configure-single-sign-on-for-an-application"></a>Så här konfigurerar du enkel inloggning för ett program

Aktivering av federerad enkel inloggning (SSO) i appen aktive ras automatiskt när du federerar genom att använda Azure AD för OpenID Connect, SAML 2,0 eller WS-utfodras. Om slutanvändarna behöver logga in trots att de redan har en befintlig session med Azure AD, är det troligt att appen är felkonfigurerad.

* Om du använder ADAL/MSAL ser du till att du har **PromptBehavior** inställd på **Auto** i stället för **Always**.

* Om du skapar en mobilapp kan du behöva ytterligare konfigurationer för att aktivera Brokered eller non-Brokered SSO.

För Android, se [aktivera enkel inloggning mellan appar i Android](../azuread-dev/howto-v1-enable-sso-android.md).<br>

För iOS, se [Aktivera Cross app SSO i iOS](../azuread-dev/howto-v1-enable-sso-ios.md).

## <a name="next-steps"></a>Nästa steg

[Enkel inloggning i Azure AD](../manage-apps/what-is-single-sign-on.md)<br>

[Aktivera enkel inloggning mellan appar i Android](../azuread-dev/howto-v1-enable-sso-android.md)<br>

[Aktivera enkel inloggning mellan appar i iOS](../azuread-dev/howto-v1-enable-sso-ios.md)<br>

[Integrera appar till AzureAD](./quickstart-register-app.md)<br>

[Behörigheter och medgivande i Microsoft Identity Platform](./v2-permissions-and-consent.md)<br>

[AzureAD Microsoft Q&A](/answers/topics/azure-active-directory.html)