---
title: ta med fil
description: ta med fil
services: active-directory
author: daveba
ms.service: active-directory
ms.subservice: msi
ms.topic: include
ms.date: 05/31/2018
ms.author: daveba
ms.custom: include file
ms.openlocfilehash: 6e5885e076222cd23ba127f3be41c1218f327ca0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92379826"
---
- Varje hanterad identitet räknas mot objekt kvot gränsen i en Azure AD-klient enligt beskrivningen i [Azure AD-tjänstens gränser och begränsningar](../articles/active-directory/enterprise-users/directory-service-limits-restrictions.md).
-   Den hastighet med vilken hanterade identiteter kan skapas har följande gränser:

    1. Per Azure AD-klient per Azure-region: 200 skapa åtgärder per 20 sekunder.
    2. Per Azure-prenumeration per Azure-region: 40 skapa åtgärder per 20 sekunder.

- När du skapar användarspecifika hanterade identiteter stöds endast alfanumeriska tecken (0-9, a-z och A-Z) och bindestreck (-). För att tilldelningen till en virtuell dator eller skalnings uppsättning för virtuella datorer ska fungera korrekt är namnet begränsat till 24 tecken.
