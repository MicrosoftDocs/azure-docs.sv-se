---
title: Migrera ett aktivt DNS-namn
description: Lär dig hur du migrerar ett anpassat DNS-domännamn som redan har tilldelats till en Live-plats till Azure App Service utan drift avbrott.
tags: top-support-issue
ms.assetid: 10da5b8a-1823-41a3-a2ff-a0717c2b5c2d
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: e1b50675bef0f883ff617b3098a742d3491b3c13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89484314"
---
# <a name="migrate-an-active-dns-name-to-azure-app-service"></a>Migrera ett aktivt DNS-namn till Azure App Service

Den här artikeln visar hur du migrerar ett aktivt DNS-namn till [Azure App Service](../app-service/overview.md) utan drift avbrott.

När du migrerar en Live-plats och dess DNS-domännamn till App Service, betjänar det DNS-namnet redan direktsänd trafik. Du kan undvika drift stopp i DNS-matchning under migreringen genom att binda det aktiva DNS-namnet till din App Service app-förebyggande syfte.

Om du inte är oroar över stillestånds tid i DNS-matchning, se [mappa ett befintligt anpassat DNS-namn till Azure App Service](app-service-web-tutorial-custom-domain.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här instruktionen:

- [Se till att din app service-app inte är i den kostnads fria nivån](app-service-web-tutorial-custom-domain.md#checkpricing).

## <a name="bind-the-domain-name-preemptively"></a>Bind domän namnet förebyggande syfte

När du binder en anpassad domän förebyggande syfte utför du båda följande innan du gör några ändringar i dina befintliga DNS-poster:

- Verifiera domänägarskap
- Aktivera domän namnet för din app

När du slutligen migrerar ditt anpassade DNS-namn från den gamla platsen till App Service-appen, kommer det inte att finnas några avbrott i DNS-matchningen.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records.md)]

### <a name="get-domain-verification-id"></a>Hämta ID för domän verifiering

Hämta domän verifierings-ID: t för din app genom att följa stegen i [Hämta domän verifierings-ID](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).

### <a name="create-domain-verification-record"></a>Skapa domän verifierings post

Om du vill verifiera domän ägarskapet lägger du till en TXT-post för domän verifiering. Värd namnet för TXT-posten beror på vilken typ av DNS-posttyp du vill mappa. Se följande tabell ( `@` normalt representerar rot domänen):

| Exempel på DNS-post | TXT-värd | TXT-värde |
| - | - | - |
| \@ skogen | _asuid_ | [ID för domän verifiering för din app](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| www (sub) | _asuid. www_ | [ID för domän verifiering för din app](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |
| \* användning | _asuid_ | [ID för domän verifiering för din app](app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id) |

På sidan DNS-poster noterar du post typen för det DNS-namn som du vill migrera. App Service stöder mappningar från CNAME-och A-poster.

> [!NOTE]
> Jokertecken `*` validerar inte under domäner med en befintlig CNAME-post. Du kan behöva skapa en TXT-post explicit för varje under domän.

### <a name="enable-the-domain-for-your-app"></a>Aktivera domänen för din app

1. I [Azure Portal](https://portal.azure.com)i det vänstra navigerings fönstret på sidan app väljer du **anpassade domäner**. 

    ![Meny för anpassad domän](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. På sidan **anpassade domäner** väljer du **Lägg till anpassad domän**.

    ![Lägg till värddatornamn](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

1. Skriv det fullständigt kvalificerade domän namnet som du vill migrera, som motsvarar den TXT-post som du skapar, till exempel `contoso.com` , `www.contoso.com` eller `*.contoso.com` . Välj **Verifiera**.

    Knappen **Lägg till anpassad domän** är aktive rad. 

1. Se till att **post typen hostname** är inställd på den DNS-posttyp som du vill migrera. Välj **Lägg till värddatornamn**.

    ![Lägg till DNS-namnet i appen](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    Det kan ta en stund innan det nya värdnamnet återspeglas på sidan **Anpassade domäner** för appen. Försök att uppdatera webbläsaren så att informationen uppdateras.

    ![CNAME-posten har lagts till](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    Ditt anpassade DNS-namn är nu aktiverat i din Azure-App. 

## <a name="remap-the-active-dns-name"></a>Mappa om det aktiva DNS-namnet

Det enda som återstår att göra är att mappa om den aktiva DNS-posten så att den pekar på App Service. Just nu pekar den fortfarande på din gamla plats.

<a name="info"></a>

### <a name="copy-the-apps-ip-address-a-record-only"></a>Kopiera appens IP-adress (endast en post)

Hoppa över det här avsnittet om du mappar om en CNAME-post. 

Om du vill mappa om en A-post behöver du App Service appens externa IP-adress, som visas på sidan **anpassade domäner** .

På sidan **Anpassade domäner** kopierar du appens IP-adress.

![Portalnavigering till Azure-app](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="update-the-dns-record"></a>Uppdatera DNS-posten

Gå tillbaka till sidan DNS-poster i din domän leverantör och välj den DNS-post som du vill mappa om.

För `contoso.com` rot domän exemplet mappar du om A-eller CNAME-posten som exemplen i följande tabell: 

| FQDN-exempel | Posttyp | Värd | Värde |
| - | - | - | - |
| contoso.com (rot) | A | `@` | IP-adress från [Kopiera appens IP-adress](#info) |
| www \. -contoso.com (sub) | CNAME | `www` | _&lt;APPNAME>. azurewebsites.net_ |
| \*. contoso.com (jokertecken) | CNAME | _\*_ | _&lt;APPNAME>. azurewebsites.net_ |

Spara inställningarna.

DNS-frågor ska börja matcha till din App Service-app omedelbart efter det att DNS-spridningen sker.

## <a name="migrate-domain-from-another-app"></a>Migrera domän från en annan app

Du kan migrera en aktiv anpassad domän i Azure, mellan prenumerationer eller inom samma prenumeration. En sådan migrering utan drift stopp kräver att käll appen och mål appen tilldelas samma anpassade domän vid en viss tidpunkt. Därför måste du kontrol lera att de två apparna inte har distribuerats till samma distributions enhet (internt kallat ett webb utrymme). Ett domän namn kan bara tilldelas en app i varje distributions enhet.

Du kan hitta distributions enheten för din app genom att titta på domän namnet för FTP/S-URL: en `<deployment-unit>.ftp.azurewebsites.windows.net` . Kontrol lera och se till att distributions enheten skiljer sig mellan käll appen och mål programmet. Distributions enheten för en app bestäms av [App Service plan](overview-hosting-plans.md) den finns i. Den väljs slumpmässigt av Azure när du skapar planen och kan inte ändras. Azure ser bara till att två planer finns i samma distributions enhet när du [skapar dem i samma resurs grupp *och* samma region](app-service-plan-manage.md#create-an-app-service-plan), men det finns ingen logik för att se till att planerna är i olika distributions enheter. Det enda sättet att skapa en plan i en annan distributions enhet är att fortsätta att skapa en plan i en ny resurs grupp eller region tills du får en annan distributions enhet.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du binder ett anpassat TLS/SSL-certifikat till App Service.

> [!div class="nextstepaction"]
> [Skydda ett anpassat DNS-namn med en TLS-bindning i Azure App Service](configure-ssl-bindings.md)
