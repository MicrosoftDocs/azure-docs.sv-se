---
title: Lösen ords återställning via självbetjäning i Azure Active Directory B2C | Microsoft Docs
description: Visar hur du konfigurerar lösen ords återställning via självbetjäning för dina kunder i Azure Active Directory B2C
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0c9edaf3356ea4c1a521a89f2ec60a4b6ba1a5ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87481503"
---
# <a name="set-up-self-service-password-reset-for-your-customers"></a>Konfigurera lösen ords återställning via självbetjäning för dina kunder

Med funktionen för lösen ords återställning via självbetjäning kan dina kunder som har registrerat sig för lokala konton återställa sina lösen ord på egen hand. Detta minskar avsevärt belastningen på support personalen, särskilt om ditt program har miljon tals kunder som använder det regelbundet. För närvarande är den enda återställnings metoden som stöds med hjälp av en verifierad e-postadress.

> [!NOTE]
> Den här artikeln gäller lösen ords återställning via självbetjäning som används i kontexten för standard **inloggnings** användar flödet, som använder sig av inloggning som identitets leverantör för **lokalt konto** . Om du behöver fullständigt anpassningsbara lösen ords återställning av användar flöden som anropas från din app, se [den här artikeln](user-flow-overview.md).
>
>

Som standard har din katalog ingen självbetjäning för återställning av lösen ord aktiverat. Använd följande steg för att aktivera det:

1. Logga in på [Azure Portal](https://portal.azure.com/) som prenumerations administratör. Detta är samma arbets-eller skol konto eller samma Microsoft-konto som du använde för att skapa din katalog.
2. Öppna **Azure Active Directory** (i navigerings fältet på vänster sida).
3. Rulla ned på bladet alternativ och välj **lösen ords återställning**.
4. Ange **återställning av lösen ord**  för självbetjäning aktiverat för **alla**.
5. Klicka på **Spara** längst upp på sidan. Du är klar!

Testa genom att använda funktionen "kör nu" på alla inloggnings användar flöden som har lokala konton som identitets leverantör. På inloggnings sidan för lokalt konto (där du anger en e-postadress och ett lösen ord eller ett användar namn och lösen ord) klickar du på **det går inte att komma åt ditt konto?** för att verifiera kund upplevelsen.

> [!NOTE]
> Sidorna för lösen ords återställning via självbetjäning kan anpassas med hjälp av [funktionen för företags anpassning](../active-directory/fundamentals/customize-branding.md).
>
>

