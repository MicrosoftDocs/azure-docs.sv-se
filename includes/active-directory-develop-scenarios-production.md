---
title: ta med fil
description: ta med fil
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 75650d7ff0ac647aeb6dace76c270680b1b89347
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954975"
---
## <a name="enable-logging"></a>Aktivera loggning

För att felsöka fel söknings scenarier och fel sökning av autentiseringsfel, innehåller Microsoft Authentication Library inbyggt loggnings stöd. Loggningen är att varje bibliotek beskrivs i följande artiklar:

:::row:::
    :::column:::
        - [Loggning i MSAL.NET](../articles/active-directory/develop/msal-logging-dotnet.md)
        - [Loggning i MSAL för Android](../articles/active-directory/develop/msal-logging-android.md)
        - [Loggning in MSAL.js](../articles/active-directory/develop/msal-logging-js.md)
    :::column-end:::
    :::column:::
        - [Loggning i MSAL för iOS/macOS](../articles/active-directory/develop/msal-logging-ios.md)
        - [Loggning i MSAL för Java](../articles/active-directory/develop/msal-logging-java.md)
        - [Loggning i MSAL för Python](../articles/active-directory/develop/msal-logging-python.md)
    :::column-end:::
:::row-end:::

Här följer några förslag på data insamling:

- Användarna kan be om hjälp när de har problem. Ett bra tips är att avbilda och lagra loggar tillfälligt. Ange en plats där användarna kan ladda upp loggarna. MSAL tillhandahåller loggnings tillägg för att samla in detaljerad information om autentisering.

- Om telemetri är tillgängligt aktiverar du det via MSAL för att samla in data om hur användarna loggar in i din app.


## <a name="validate-your-integration"></a>Verifiera din integrering

Testa din integrering genom att följa [Check lista för Microsoft Identity Platform-integration](../articles/active-directory/develop/identity-platform-integration-checklist.md).
