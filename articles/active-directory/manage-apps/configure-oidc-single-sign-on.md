---
title: Förstå OIDC-baserad enkel inloggning (SSO) för appar i Azure Active Directory
description: Förstå OIDC-baserad enkel inloggning (SSO) för appar i Azure Active Directory.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: iangithinji
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: 990e0c09f8a49b83bc68d7123f5f8146a3551575
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107374547"
---
# <a name="understand-oidc-based-single-sign-on"></a>Förstå OIDC-baserad enkel inloggning
I [snabbstartsserien](view-applications-portal.md) om programhantering lärde du dig att använda Azure AD som identitetsprovider (IdP) för ett program. Den här artikeln innehåller mer information om appar som använder OpenID Connect standard för att implementera enkel inloggning. 

## <a name="before-you-begin"></a>Innan du börjar
Processen för att lägga till en app Azure Active Directory klientorganisation beror på vilken typ av enkel inloggning som programmet implementeras för. Mer information om de alternativ för enkel inloggning som är tillgängliga för appar som kan använda Azure AD för identitetshantering finns i [alternativ för enkel inloggning.](sso-options.md) Den här artikeln beskriver OIDC-baserade appar.


## <a name="basic-oidc-configuration"></a>Grundläggande OIDC-konfiguration
I [snabbstartsserien](add-application-portal-setup-oidc-sso.md)finns det en artikel om hur du konfigurerar enkel inloggning. I den får du lära dig hur du lägger till en OIDC-baserad app i din Azure-klientorganisation.

Det som är bra med att lägga till en app som använder OIDC-standarden för enkel inloggning är att konfigurationen är minimal. Här är en kort video som visar hur du lägger till en OIDC-baserad app i din klientorganisation.

Lägga till en OIDC-baserad app i Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Mer information om användar- och administratörsmedgivande finns i [Förstå användar- och administratörsmedgivande.](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)

## <a name="next-steps"></a>Nästa steg

- [Snabbstartsserie om programhantering](add-application-portal-setup-oidc-sso.md)
- [Alternativ för enkel inloggning](sso-options.md)
- [Anvisningar: Loggar in valfri Azure Active Directory-användare med programmönstret för flera klienter](../develop/howto-convert-app-to-be-multi-tenant.md)
