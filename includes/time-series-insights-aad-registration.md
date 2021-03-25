---
title: ta med fil
description: ta med fil
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 22411e5a80f555a3ead05d39466a7a175923d9bc
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105299"
---
* När du har valt lämplig plattform i steg 4 i [Konfigurera plattforms](../articles/active-directory/develop/quickstart-register-app.md#configure-platform-settings) inställningar konfigurerar du **omdirigerings-URI: er** och **åtkomsttoken** på sido panelen till höger om användar gränssnittet.

    * **Omdirigerings-URI: er** måste matcha adressen som anges av autentiseringsbegäran:

        * För appar som finns i en lokal utvecklings miljö väljer du **offentlig klient (mobil & Desktop)**. Se till att ange en **offentlig klient** till **Ja**.
        * För Single-Page appar som finns på Azure App Service väljer du **webb**.

    * Avgör om en **utloggnings-URL** är lämplig.

    * Aktivera det implicita tilldelnings flödet genom att kontrol lera **åtkomsttoken** eller **ID-token**.

    [![Skapa omdirigerings-URI: er](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klicka på **Konfigurera** och sedan på **Spara**.

* Associera Azure Active Directory app-Azure Time Series Insights. Välj **API-behörigheter**  >  **Lägg till en behörighets**-  >  **API min organisation använder**.

    [![Associera ett API med din Azure Active Directory-app](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Skriv `Azure Time Series Insights` i Sök fältet och välj sedan `Azure Time Series Insights` .

* Ange sedan den typ-API-behörighet som din app kräver. Som standard är **delegerade behörigheter** markerade. Välj en behörighets typ och välj sedan **Lägg till behörigheter**.

    [![Ange vilken typ av API-behörighet som appen kräver](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* [Lägg till autentiseringsuppgifter](../articles/active-directory/develop/quickstart-register-app.md#add-credentials) om programmet kommer att anropa din miljös API: er som de ska. Autentiseringsuppgifterna gör att ditt program kan autentisera sig självt, vilket kräver ingen interaktion från en användare vid körning.