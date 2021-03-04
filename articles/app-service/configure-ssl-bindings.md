---
title: Skydda en anpassad DNS med en TLS/SSL-bindning
description: Skydda HTTPS-åtkomst till din anpassade domän genom att skapa en TLS/SSL-bindning med ett certifikat. Förbättra din webbplats säkerhet genom att tvinga HTTPS eller TLS 1,2.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 04/30/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: a713d869a754f17f4030c531999c822a2cbb76ee
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102039809"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Skydda ett anpassat DNS-namn med en TLS/SSL-bindning i Azure App Service

Den här artikeln visar hur du skyddar den [anpassade domänen](app-service-web-tutorial-custom-domain.md) i din [App Service app](./index.yml) eller [Function-app](../azure-functions/index.yml) genom att skapa en certifikat bindning. När du är klar kan du komma åt din App Service-app vid `https://` slut punkten för ditt anpassade DNS-namn (t `https://www.contoso.com` . ex.). 

![Webbapp med anpassat TLS/SSL-certifikat](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Att skydda en [anpassad domän](app-service-web-tutorial-custom-domain.md) med ett certifikat innebär två steg:

- [Lägg till ett privat certifikat till App Service](configure-ssl-certificate.md) som uppfyller alla [krav för privata certifikat](configure-ssl-certificate.md#private-certificate-requirements).
-  Skapa en TLS-bindning till motsvarande anpassad domän. Det andra steget beskrivs i den här artikeln.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Uppgradera prisnivån för din app
> * Skydda en anpassad domän med ett certifikat
> * Använda HTTPS
> * Använda TLS 1.1/1.2
> * Automatisera hantering av TLS med skript

## <a name="prerequisites"></a>Förutsättningar

För att följa den här instruktions guiden:

- [Skapa en App Service-app](./index.yml)
- [Mappa ett domän namn till din app](app-service-web-tutorial-custom-domain.md) eller [köp och konfigurera det i Azure](manage-custom-dns-buy-domain.md)
- [Lägg till ett privat certifikat i din app](configure-ssl-certificate.md)

> [!NOTE]
> Det enklaste sättet att lägga till ett privat certifikat är att [skapa ett kostnads fritt App Service-hanterat certifikat](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (förhands granskning).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Skydda en anpassad domän

Gör så här:

I <a href="https://portal.azure.com" target="_blank">Azure Portal</a>väljer du **app Services** på menyn till vänster  >  **\<app-name>** .

Starta dialog rutan **TLS/SSL-bindning** från den vänstra navigeringen i din app genom att:

- Välja **anpassade domäner**  >  **Lägg till bindning**
- Välja **TLS/SSL-inställningar**  >  **Lägg till TLS/SSL-bindning**

![Lägg till bindning till domän](./media/configure-ssl-bindings/secure-domain-launch.png)

I **anpassad domän** väljer du den anpassade domän som du vill lägga till en bindning för.

Om din app redan har ett certifikat för den valda anpassade domänen går du till [skapa bindning](#create-binding) direkt. Annars fortsätter du.

### <a name="add-a-certificate-for-custom-domain"></a>Lägg till ett certifikat för anpassad domän

Om din app inte har något certifikat för den valda anpassade domänen har du två alternativ:

- **Ladda upp PFX-certifikat** – Följ arbets flödet på [överför ett privat certifikat](configure-ssl-certificate.md#upload-a-private-certificate)och välj sedan det här alternativet här.
- **Importera App Service Certificate** – Följ arbets flödet vid [Import av ett app service certifikat](configure-ssl-certificate.md#import-an-app-service-certificate)och välj sedan det här alternativet här.

> [!NOTE]
> Du kan också [skapa ett kostnads fritt certifikat](configure-ssl-certificate.md#create-a-free-managed-certificate-preview) (förhands granskning) eller [Importera ett Key Vault certifikat](configure-ssl-certificate.md#import-a-certificate-from-key-vault), men du måste göra det separat och sedan gå tillbaka till dialog rutan **TLS/SSL-bindning** .

### <a name="create-binding"></a>Skapa bindning

Använd följande tabell som hjälp för att konfigurera TLS-bindning i dialog rutan **TLS/SSL-bindning** och klicka sedan på **Lägg till bindning**.

| Inställning | Beskrivning |
|-|-|
| Anpassad domän | Domän namnet som TLS/SSL-bindningen ska läggas till för. |
| Tumavtryck för privat certifikat | Certifikatet som ska bindas. |
| TLS/SSL-typ | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** -flera SNI SSL-bindningar kan läggas till. Med det här alternativet kan flera TLS/SSL-certifikat skydda flera domäner på samma IP-adress. De flesta moderna webbläsare (inklusive Internet Explorer, Chrome, Firefox och Opera) stöder SNI (mer information finns i [servernamnindikator](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP SSL** -det går bara att lägga till en IP SSL-bindning. Med det här alternativet kan endast ett TLS/SSL-certifikat skydda en dedikerad offentlig IP-adress. När du har konfigurerat bindningen följer du stegen i [mappa om poster för IP SSL](#remap-records-for-ip-ssl).<br/>IP SSL stöds endast i **standard** -nivån eller högre. </li></ul> |

När åtgärden har slutförts ändras den anpassade domänens TLS/SSL-tillstånd till **säker**.

![TLS/SSL-bindningen lyckades](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Ett **säkert** tillstånd i de **anpassade domänerna** innebär att den skyddas med ett certifikat, men App Service inte kontrollerar om certifikatet är självsignerat eller har upphört att gälla, till exempel, vilket även kan leda till att webbläsare visar ett fel eller en varning.

## <a name="remap-records-for-ip-ssl"></a>Mappa om poster för IP SSL

Om du inte använder IP SSL i din app kan du gå vidare till [testa https för din anpassade domän](#test-https).

Det finns två ändringar som du måste göra, eventuellt:

- Som standard använder din app en delad offentlig IP-adress. När du binder ett certifikat med IP SSL skapar App Service en ny, dedikerad IP-adress för din app. Om du har mappat en A-post till din app uppdaterar du domän registret med den nya, dedikerade IP-adressen.

    Din apps **Anpassad domän**-sida uppdateras med den nya dedikerade IP-adressen. [Kopiera den här IP-adressen](app-service-web-tutorial-custom-domain.md#info) och [mappa om A-posten](app-service-web-tutorial-custom-domain.md#map-an-a-record) till den nya IP-adressen.

- Om du har en SNI SSL bindning till `<app-name>.azurewebsites.net` , [mappa om alla CNAME-mappningar](app-service-web-tutorial-custom-domain.md#map-a-cname-record) så att de pekar till `sni.<app-name>.azurewebsites.net` i stället (Lägg till `sni` prefixet).

## <a name="test-https"></a>Testa HTTPS

I olika webbläsare, bläddrar du till `https://<your.custom.domain>` för att kontrol lera att appen fungerar.

:::image type="content" source="./media/configure-ssl-bindings/app-with-custom-ssl.png" alt-text="Skärm bild som visar ett exempel på att bläddra till din anpassade domän med contoso.com-URL: en markerad.":::

Din program kod kan inspektera protokollet via huvudet "x-AppService-proto". Rubriken kommer att ha värdet `http` eller `https` . 

> [!NOTE]
> Om din app visar fel i certifikatverifieringen så använder du förmodligen ett självsignerat certifikat.
>
> Om detta inte är fallet kan du ha utelämnat mellanliggande certifikat när du exporterade certifikatet till PFX-filen.

## <a name="prevent-ip-changes"></a>Förhindra IP-ändringar

Din inkommande IP-adress kan ändras när du tar bort en bindning, även om denna bindning är IP SSL. Detta är särskilt viktigt när du förnyar ett certifikat som redan finns i en IP SSL-bindning. Du kan förhindra att appens IP-adress ändras genom att följa stegen nedan i ordning:

1. Ladda upp det nya certifikatet.
2. Bind det nya certifikatet till önskad anpassad domän utan att ta bort det gamla. Med den här åtgärden ersätts bindningen i stället för att den gamla tas bort.
3. Ta bort det gamla certifikatet. 

## <a name="enforce-https"></a>Använda HTTPS

Som standard kan alla fortfarande komma åt din app med HTTP. Du kan omdirigera alla HTTP-begäranden till HTTPS-porten.

Välj **SSL-inställningar** i den vänstra navigeringen på din appsida. I **Endast HTTPS** väljer du **På**.

![Använda HTTPS](./media/configure-ssl-bindings/enforce-https.png)

När åtgärden har slutförts går du till någon av de HTTP-webbadresser som pekar på din app. Exempel:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Framtvinga TLS-version

Din app tillåter [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 som standard, vilket är den rekommenderade TLS-nivån enligt branschstandarder, t.ex. [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Följ dessa steg om du vill genomdriva en högre TLS-version:

Välj **SSL-inställningar** i den vänstra navigeringen på din appsida. I **TLS-version** väljer du sedan den lägsta TLS-version du vill använda. Den här inställningen styr endast inkommande samtal. 

![Kräv TLS 1.1 eller 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

När åtgärden är klar avvisar appen alla anslutningar med lägre TLS-version.

## <a name="handle-tls-termination"></a>Hantera TLS-avslutning

I App Service sker [TLS-avslutning](https://wikipedia.org/wiki/TLS_termination_proxy) vid utjämning av nätverks belastning, så alla HTTPS-begäranden når din app som okrypterade HTTP-förfrågningar. Om din applogik behöver kontrollera om användarbegäranden är krypterade eller inte kan du kontrollera `X-Forwarded-Proto`-rubriken.

Språkspecifika konfigurations guider, till exempel [konfigurations](configure-language-nodejs.md#detect-https-session) guiden för Linux-Node.js, visar hur du identifierar en https-session i program koden.

## <a name="automate-with-scripts"></a>Automatisera med skript

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Fler resurser

* [Använda ett TLS-/SSL-certifikat i koden i Azure App Service](configure-ssl-certificate-in-code.md)
* [Vanliga frågor och svar: App Service certifikat](./faq-configuration-and-management.md)