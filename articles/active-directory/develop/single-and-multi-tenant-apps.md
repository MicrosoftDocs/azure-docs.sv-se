---
title: Appar för en och flera klienter i Azure AD
titleSuffix: Microsoft identity platform
description: Lär dig mer om funktionerna och skillnaderna mellan appar för en enda klient organisation och flera klient organisationer i Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/24/2020
ms.author: ryanwi
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 0868d87d977b15a552b04d5dbd6d19de6931f0ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91395914"
---
# <a name="tenancy-in-azure-active-directory"></a>Klientorganisation i Azure Active Directory

Azure Active Directory (Azure AD) ordnar objekt som användare och appar i grupper som kallas *innehavare*. Med klienter kan en administratör ange principer för användarna i organisationen och de appar som organisationen äger för att uppfylla sina säkerhets-och drift principer. 

## <a name="who-can-sign-in-to-your-app"></a>Vem kan logga in i din app?

När det gäller att utveckla appar kan utvecklare välja att konfigurera appen så att den antingen är en enskild klient eller flera innehavare vid registreringen av appen i [Azure Portal](https://portal.azure.com).
* Appar för en enda klient är bara tillgängliga i den klient som de registrerades i, även kallade hem klient organisation.
* Appar för flera klient organisationer är tillgängliga för användare i både hem klient organisation och andra klienter.

I Azure Portal kan du konfigurera din app så att den blir en enskild klient eller flera innehavare genom att ange mål gruppen enligt följande.

| Målgrupp | En/flera innehavare | Vem kan logga in | 
|----------|--------| ---------|
| Konton endast i den här katalogen | Enskild klientorganisation | Alla användar-och gäst konton i din katalog kan använda ditt program eller API.<br>*Använd det här alternativet om mål gruppen är intern för din organisation.* |
| Konton i valfri Azure AD-katalog | Flera klientorganisationer | Alla användare och gäster med ett arbets-eller skol konto från Microsoft kan använda ditt program eller din API. Detta omfattar skolor och företag som använder Microsoft 365.<br>*Använd det här alternativet om mål gruppen är affärs-eller utbildnings kunder.* |
| Konton i alla Azure AD-kataloger och personliga Microsoft-konton (till exempel Skype, Xbox, Outlook.com) | Flera klientorganisationer | Alla användare med ett arbets-eller skolprogram eller en privat Microsoft-konto kan använda ditt program eller API. Det omfattar skolor och företag som använder Microsoft 365 samt personliga konton som används för att logga in på tjänster som Xbox och Skype.<br>*Använd det här alternativet om du vill rikta in dig på den bredaste uppsättningen Microsoft-konton.* | 

## <a name="best-practices-for-multi-tenant-apps"></a>Metod tips för appar för flera klient organisationer

Det kan vara svårt att skapa fantastiska appar för flera klient organisationer på grund av antalet olika principer som IT-administratörer kan ange i sina klienter. Om du väljer att bygga en app för flera klienter följer du dessa rekommendationer:

* Testa din app i en klient som har konfigurerat [principer för villkorlig åtkomst](../azuread-dev/conditional-access-dev-guide.md).
* Följ principen om minst användar åtkomst för att säkerställa att din app endast begär behörigheter som den verkligen behöver. 
* Ange lämpliga namn och beskrivningar för behörigheter som du exponerar som en del av din app. Detta hjälper användare och administratörer att veta vad de accepterar när de försöker använda appens API: er. Mer information finns i avsnittet metod tips i [guiden behörigheter](v2-permissions-and-consent.md).

## <a name="next-steps"></a>Nästa steg

* [Så här konverterar du en app till flera innehavare](howto-convert-app-to-be-multi-tenant.md)
