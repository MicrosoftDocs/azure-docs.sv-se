---
title: Hämta slut punkter för en Azure AD-App-registrering
titleSuffix: Microsoft identity platform
description: Hur du hittar autentiserings slut punkter för ett anpassat program som du utvecklar eller registrerar med Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ms.openlocfilehash: 2c348bd7a1738742744976c46396f62a3317dc17
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98017092"
---
# <a name="how-to-discover-endpoints"></a>Identifiera slut punkter

Du kan hitta autentiserings slut punkter för ditt program i [Azure Portal](https://portal.azure.com).

1. Logga in på <a href="https://portal.azure.com/" target="_blank">Azure Portal <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.
1. Välj **Azure Active Directory**.
1. Under **Hantera** väljer du **Appregistreringar** och väljer sedan **slut punkter** på den översta menyn.

    Sidan **slut punkter** visas med autentiserings slut punkter för din klient.
    
    Använd slut punkten som matchar autentiseringsprotokollet som du använder tillsammans med **program-ID: t (klient)** för att skapa en autentiserings förfrågan som är specifik för ditt program.

**Nationella moln** (till exempel Azure AD Kina, Tyskland och amerikanska myndigheter) har sin egen app registrerings Portal och Azure AD-autentiserings-slutpunkter. Läs mer i [Översikt över nationella moln](authentication-national-cloud.md).

## <a name="next-steps"></a>Nästa steg

Mer information om slut punkter i de olika Azure-miljöerna finns i [Översikt över nationella moln](authentication-national-cloud.md).
