---
title: Tillägg-appen i Azure Active Directory B2C | Microsoft Docs
description: Återställer B2C-Extensions-app.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/06/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: dc536fa4292d794e8d89a2564ad10a3c10dd0a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94560868"
---
# <a name="azure-ad-b2c-extensions-app"></a>Azure AD B2C: tillägg-appen

När en Azure AD B2C katalog skapas, skapas automatiskt en app `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` som heter i den nya katalogen. Den här appen, som kallas **B2C-Extensions-app**, är synlig i *Appregistreringar*. Den används av tjänsten Azure AD B2C för att lagra information om användare och anpassade attribut. Om appen tas bort kommer Azure AD B2C inte att fungera korrekt och produktionsmiljön kommer att påverkas.

> [!IMPORTANT]
> Ta inte bort B2C-Extensions-appen om du inte planerar att omedelbart ta bort din klient. Om appen fortfarande tas bort i mer än 30 dagar kommer användar informationen att gå förlorad permanent.

## <a name="verifying-that-the-extensions-app-is-present"></a>Kontrol lera att tilläggs programmet finns

Så här kontrollerar du att B2C-Extensions-appen finns:

1. I din Azure AD B2C-klient klickar du på **alla tjänster** i den vänstra navigerings menyn.
1. Sök efter och öppna **Appregistreringar**.
1. Sök efter en app som börjar med **B2C-Extensions-app**

## <a name="recover-the-extensions-app"></a>Återställa tillägg-appen

Om du av misstag har tagit bort B2C-Extensions-appen har du 30 dagar på dig att återställa den. Du kan återställa appen med hjälp av Graph API:

1. Bläddra till [https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/).
1. Logga in på platsen som global administratör för den Azure AD B2C katalog som du vill återställa den borttagna appen för. Den globala administratören måste ha en e-postadress som liknar följande: `username@{yourTenant}.onmicrosoft.com` .
1. Utfärda ett HTTP GET mot URL: en `https://graph.windows.net/myorganization/deletedApplications` med API-version = 1.6. Den här åtgärden visar en lista över alla program som har tagits bort under de senaste 30 dagarna.
1. Hitta programmet i listan där namnet börjar med "B2C-Extensions-app" och kopiera dess `objectid` egenskaps värde.
1. Utfärda ett HTTP-inlägg mot URL: en `https://graph.windows.net/myorganization/deletedApplications/{OBJECTID}/restore` . Ersätt `{OBJECTID}` delen av URL: en med `objectid` från föregående steg.

Du bör nu kunna [se den återställda appen](#verifying-that-the-extensions-app-is-present) i Azure Portal.

> [!NOTE]
> Ett program kan bara återställas om det har tagits bort under de senaste 30 dagarna. Om det har varit mer än 30 dagar kommer data att gå förlorade permanent. Om du behöver mer hjälp kan du kontakta ett support ärende.
