---
title: Godkänner inloggnings sidan för Azure AD Microsoft-konton | Microsoft Docs
description: Hur meddelanden på skärmen visar användar namns sökning under inloggning
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2c3b1c8d35c24d78abb8a519ddc8790649eb5f2b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547873"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Inloggnings alternativ för Microsoft-konton i Azure Active Directory

Microsoft 365 inloggnings sidan för Azure Active Directory (Azure AD) stöder arbets-eller skol konton och Microsoft-konton, men beroende på användarens situation kan det vara en eller båda. Inloggnings sidan för Azure AD stöder till exempel följande:

* Appar som accepterar inloggningar från båda typerna av konto
* Organisationer som accepterar gäster

## <a name="identification"></a>Identification
Du kan se om inloggnings sidan som din organisation använder stöder Microsoft-konton genom att titta på tips texten i fältet användar namn. Om tips texten heter "e-post, telefon eller Skype", stöder inloggnings sidan Microsoft-konton.

![Skillnad mellan konto inloggnings sidor](./media/signin-account-support/ui-prompt.png)

[Ytterligare inloggnings alternativ fungerar bara för personliga Microsoft-konton](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , men kan inte användas för att logga in på arbets-eller skol konto resurser.

## <a name="next-steps"></a>Nästa steg

[Anpassa din inloggnings anpassning](../fundamentals/add-custom-domain.md)