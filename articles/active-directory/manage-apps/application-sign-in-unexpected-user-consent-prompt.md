---
title: Oväntad varning vid inloggning till ett program | Microsoft Docs
description: Så här felsöker du när en användare ser en medgivande-prompt för ett program som du har integrerat med Azure AD som du inte förväntat dig
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6253458bb31388e5f7e6f84b7720cce0dc22886c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259968"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Oväntad varning vid inloggning till ett program

Många program som integreras med Azure Active Directory måste ha behörighet till olika resurser för att kunna köras. När dessa resurser också är integrerade med Azure Active Directory begärs behörighet att komma åt dem med hjälp av Azure AD medgivande Framework. 

Detta resulterar i att ett medgivande visas första gången ett program används, vilket ofta är en engångs åtgärd. 

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenarier där användare ser medgivande-prompter

Ytterligare prompter kan förväntas i olika scenarier:

* Den uppsättning behörigheter som krävs av programmet har ändrats.

* Användaren som ursprungligen godkände till programmet var inte administratör och nu är det en annan användare (icke-administratör) som använder programmet för första gången.

* Användaren som ursprungligen godkände till programmet var en administratör, men de fick inget medgivande för hela organisationen.

* Programmet använder ett [stegvist och dynamiskt medgivande](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) för att begära ytterligare behörigheter när godkännandet ursprungligen beviljades. Detta används ofta när valfria funktioner i ett program ytterligare kräver behörigheter utöver de som krävs för bas linje funktionerna.

* Godkännandet återkallades efter att det har beviljats till en början.

* Utvecklaren har konfigurerat programmet till att begära en medgivande varje gång det används (Obs! det här är inte bästa praxis).

## <a name="next-steps"></a>Nästa steg

-   [Appar, behörigheter och medgivande i Azure Active Directory (v 1.0-slutpunkt)](../develop/quickstart-register-app.md)

-   [Omfång, behörigheter och medgivande i Azure Active Directory (v 2.0-slutpunkt)](../develop/v2-permissions-and-consent.md)