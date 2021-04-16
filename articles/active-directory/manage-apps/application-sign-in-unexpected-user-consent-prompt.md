---
title: Oväntad fråga om medgivande vid inloggning till en | Microsoft Docs
description: Så här felsöker du när en användare ser en fråga om medgivande för ett program som du har integrerat med Azure AD som du inte förväntade dig
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37e4384b5a1b400f11b7b7d6ab15beec4d2f8de9
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378067"
---
# <a name="unexpected-consent-prompt-when-signing-in-to-an-application"></a>Oväntad fråga om medgivande vid inloggning till ett program

Många program som integreras med Azure Active Directory kräver behörigheter till olika resurser för att kunna köras. När dessa resurser också är integrerade med Azure Active Directory begärs behörighet att komma åt dem med hjälp av Azure AD-ramverket för medgivande. 

Detta resulterar i att en fråga om medgivande visas första gången ett program används, vilket ofta är en enda gång. 

> [!VIDEO https://www.youtube.com/embed/a1AjdvNDda4]

## <a name="scenarios-in-which-users-see-consent-prompts"></a>Scenarier där användare ser frågor om medgivande

Ytterligare uppmaningar kan förväntas i olika scenarier:

* Uppsättningen behörigheter som krävs av programmet har ändrats.

* Den användare som ursprungligen samtyckde till programmet var inte administratör och nu använder en annan (icke-administratör) användare programmet för första gången.

* Den användare som ursprungligen samtyckde till programmet var administratör, men han eller hon samtyckde inte åt hela organisationen.

* Programmet använder [inkrementellt och dynamiskt medgivande för](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) att begära ytterligare behörigheter efter att medgivandet ursprungligen beviljades. Detta används ofta när valfria funktioner i ett program kräver ytterligare behörigheter utöver de som krävs för baslinjefunktioner.

* Medgivande återkallades efter att det ursprungligen beviljats.

* Utvecklaren har konfigurerat programmet så att det kräver en fråga om medgivande varje gång det används (obs! Detta är inte bästa praxis).

## <a name="next-steps"></a>Nästa steg

-   [Appar, behörigheter och medgivande i Azure Active Directory (v1.0-slutpunkt)](../develop/quickstart-register-app.md)

-   [Omfång, behörigheter och medgivande i Azure Active Directory (v2.0-slutpunkt)](../develop/v2-permissions-and-consent.md)
