---
title: Integrera Azure DNS med Azure-resurser – Azure DNS
description: I den här artikeln lär du dig hur du använder Azure DNS tillsammans för att tillhandahålla DNS för dina Azure-resurser.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 7/13/2019
ms.author: rohink
ms.openlocfilehash: 4d8af5815e544698ab833001e5ce6d0f4a30a264
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92487406"
---
# <a name="use-azure-dns-to-provide-custom-domain-settings-for-an-azure-service"></a>Använd Azure DNS för att tillhandahålla anpassade domän inställningar för en Azure-tjänst

Azure DNS tillhandahåller DNS för en anpassad domän för någon av dina Azure-resurser som stöder anpassade domäner eller som har ett fullständigt kvalificerat domän namn (FQDN). Ett exempel är att du har en Azure-webbapp som du vill att användarna ska ha åtkomst till genom att antingen använda contoso.com eller www- \. contoso.com som ett fullständigt domän namn. Den här artikeln vägleder dig genom konfigurationen av Azure-tjänsten med Azure DNS för att använda anpassade domäner.

## <a name="prerequisites"></a>Förutsättningar

För att kunna använda Azure DNS för din anpassade domän måste du först delegera din domän till Azure DNS. Besök [delegera en domän till Azure DNS](./dns-delegate-domain-azure-dns.md) för instruktioner om hur du konfigurerar namnservrar för delegering. När din domän är delegerad till din Azure DNS zon kan du konfigurera de DNS-poster som behövs.

Du kan konfigurera en anpassad eller anpassad domän för [Azure Function-appar](#azure-function-app), [offentliga IP-adresser](#public-ip-address), [App Service (Web Apps)](#app-service-web-apps), [Blob Storage](#blob-storage)och [Azure CDN](#azure-cdn).

## <a name="azure-function-app"></a>Azure-Funktionsapp

Om du vill konfigurera en anpassad domän för Azure Function-appar skapas en CNAME-post samt konfigurationen i själva appen.
 
Navigera till **Funktionsapp** och välj din Function-app. Klicka på **plattforms funktioner** och under **nätverk** klickar du på **anpassade domäner**.

![bladet Function-app](./media/dns-custom-domain/functionapp.png)

Observera den aktuella URL: en på bladet **anpassade domäner** , används den här adressen som alias för den DNS-post som skapats.

![bladet anpassad domän](./media/dns-custom-domain/functionshostname.png)

Gå till din DNS-zon och klicka på **+ post uppsättning**. Fyll i följande information på bladet **Lägg till uppsättning av poster** och klicka på **OK** för att skapa den.

|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Name     | myfunctionapp        | Det här värdet tillsammans med domän namns etiketten är FQDN för det anpassade domän namnet.        |
|Typ     | CNAME        | Använd en CNAME-post med ett alias.        |
|TTL-värde     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Tider        | Timmar används som tids mått         |
|Alias     | adatumfunction.azurewebsites.net        | Det DNS-namn som du skapar alias för, i det här exemplet är det DNS-namnet för adatumfunction.azurewebsites.net som tillhandahålls som standard till Function-appen.        |

Gå tillbaka till din Function-app, klicka på **plattforms funktioner** och under **nätverk** klickar du på **anpassade domäner**. under **anpassade värdnamn** klickar du på **+ Lägg till värdnamn**.

På bladet **Lägg till värdnamn** anger du CNAME-posten i textfältet **hostname** och klickar på **Verifiera**. Om posten hittas visas knappen **Lägg till värdnamn** . Klicka på **Lägg till värdnamn** för att lägga till aliaset.

![funktions appar Lägg till värd namn bladet](./media/dns-custom-domain/functionaddhostname.png)

## <a name="public-ip-address"></a>Offentlig IP-adress

Om du vill konfigurera en anpassad domän för tjänster som använder en offentlig IP-adressresurs, till exempel Application Gateway, Load Balancer, moln tjänst, virtuella Resource Manager-datorer och klassiska virtuella datorer, används en A-post.

Navigera till **nätverkets**  >  **offentliga IP-adress**, Välj den offentliga IP-resursen och klicka på **konfiguration**. Notate IP-adressen som visas.

![offentligt IP-blad](./media/dns-custom-domain/publicip.png)

Gå till din DNS-zon och klicka på **+ post uppsättning**. Fyll i följande information på bladet **Lägg till uppsättning av poster** och klicka på **OK** för att skapa den.


|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Name     | mywebserver        | Det här värdet tillsammans med domän namns etiketten är FQDN för det anpassade domän namnet.        |
|Typ     | A        | Använd en A-post eftersom resursen är en IP-adress.        |
|TTL-värde     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Tider        | Timmar används som tids mått         |
|IP-adress     | `<your ip address>`       | Den offentliga IP-adressen.|

![skapa en A-post](./media/dns-custom-domain/arecord.png)

När en post har skapats kör `nslookup` du för att verifiera att posten matchar.

![offentlig IP DNS-sökning](./media/dns-custom-domain/publicipnslookup.png)

## <a name="app-service-web-apps"></a>App Service (Web Apps)

Följande steg tar dig igenom hur du konfigurerar en anpassad domän för en app service-webbapp.

Navigera till **App Service** och välj den resurs som du konfigurerar ett anpassat domän namn och klicka på **anpassade domäner**.

Observera den aktuella URL: en på bladet **anpassade domäner** , används den här adressen som alias för den DNS-post som skapats.

![bladet anpassade domäner](./media/dns-custom-domain/url.png)

Gå till din DNS-zon och klicka på **+ post uppsättning**. Fyll i följande information på bladet **Lägg till uppsättning av poster** och klicka på **OK** för att skapa den.


|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Name     | mywebserver        | Det här värdet tillsammans med domän namns etiketten är FQDN för det anpassade domän namnet.        |
|Typ     | CNAME        | Använd en CNAME-post med ett alias. Om resursen använde en IP-adress används en A-post.        |
|TTL-värde     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Tider        | Timmar används som tids mått         |
|Alias     | webserver.azurewebsites.net        | Det DNS-namn som du skapar alias för, i det här exemplet är det DNS-namnet för webserver.azurewebsites.net som tillhandahålls som standard till webbappen.        |


![skapa en CNAME-post](./media/dns-custom-domain/createcnamerecord.png)

Gå tillbaka till App Service som har kon figurer ATS för det anpassade domän namnet. Klicka på **anpassade domäner** och sedan på **värd namn**. Klicka på **+ Lägg till värdnamn** om du vill lägga till en CNAME-post som du har skapat.

![Skärm bild som visar knappen + Lägg till värddator namn.](./media/dns-custom-domain/figure1.png)

När processen är klar kan du köra **nslookup** för att verifiera att namn matchningen fungerar.

![bild 1](./media/dns-custom-domain/finalnslookup.png)

Om du vill veta mer om hur du mappar en anpassad domän till App Service kan du gå [till mappa ett befintligt anpassat DNS-namn till Azure Web Apps](../app-service/app-service-web-tutorial-custom-domain.md?toc=%dns%2ftoc.json).

Information om hur du migrerar ett aktivt DNS-namn finns i [Migrera ett aktivt DNS-namn till Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).

Om du behöver köpa en anpassad domän går du till [köpa ett anpassat domän namn för Azure Web Apps](../app-service/manage-custom-dns-buy-domain.md) för att lära dig mer om App Service-domäner.

## <a name="blob-storage"></a>Blob Storage

Följande steg tar dig igenom hur du konfigurerar en CNAME-post för ett Blob Storage-konto med hjälp av metoden för att verifiera. Den här metoden säkerställer att det inte finns någon stillestånds tid.

Gå till **lagrings**  >  **lagrings konton**, Välj ditt lagrings konto och klicka på **anpassad domän**. Notate FQDN under steg 2, det här värdet används för att skapa den första CNAME-posten

![anpassad domän för Blob Storage](./media/dns-custom-domain/blobcustomdomain.png)

Gå till din DNS-zon och klicka på **+ post uppsättning**. Fyll i följande information på bladet **Lägg till uppsättning av poster** och klicka på **OK** för att skapa den.


|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Name     | verifiera. mystorageaccount        | Det här värdet tillsammans med domän namns etiketten är FQDN för det anpassade domän namnet.        |
|Typ     | CNAME        | Använd en CNAME-post med ett alias.        |
|TTL-värde     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Tider        | Timmar används som tids mått         |
|Alias     | asverify.adatumfunctiona9ed.blob.core.windows.net        | Det DNS-namn som du skapar alias för, i det här exemplet är det DNS-namnet för asverify.adatumfunctiona9ed.blob.core.windows.net som anges som standard för lagrings kontot.        |

Gå tillbaka till ditt lagrings konto genom att klicka på **lagrings**  >  **lagrings konton**, Välj ditt lagrings konto och klicka på **anpassad domän**. Skriv in det alias som du skapade utan prefixet för att verifiera i text rutan, markera **Använd indirekt CNAME-validering** och klicka på **Spara**. När det här steget har slutförts går du tillbaka till DNS-zonen och skapar en CNAME-post utan prefixet verify.  Efter den tidpunkten är det säkert att ta bort CNAME-posten med cdnverify-prefixet.

![Skärm bild som visar sidan anpassad domän.](./media/dns-custom-domain/indirectvalidate.png)

Verifiera DNS-matchning genom att köra `nslookup`

Mer information om hur du mappar en anpassad domän till en Blob Storage-slutpunkt finns i [Konfigurera ett eget domän namn för din Blob Storage-slutpunkt](../storage/blobs/storage-custom-domain-name.md?toc=%dns%2ftoc.json)

## <a name="azure-cdn"></a>Azure CDN

Följande steg beskriver hur du konfigurerar en CNAME-post för en CDN-slutpunkt med cdnverify-metoden. Den här metoden säkerställer att det inte finns någon stillestånds tid.

Navigera till **nätverks**  >  -**CDN-profiler** och välj din CDN-profil.

Välj den slut punkt som du arbetar med och klicka på **+ anpassad domän**. Observera **slut punkts namnet** som det här värdet är den post som CNAME-posten pekar på.

![Anpassad CDN-domän](./media/dns-custom-domain/endpointcustomdomain.png)

Gå till din DNS-zon och klicka på **+ post uppsättning**. Fyll i följande information på bladet **Lägg till uppsättning av poster** och klicka på **OK** för att skapa den.

|Egenskap  |Värde  |Beskrivning  |
|---------|---------|---------|
|Name     | cdnverify. mycdnendpoint        | Det här värdet tillsammans med domän namns etiketten är FQDN för det anpassade domän namnet.        |
|Typ     | CNAME        | Använd en CNAME-post med ett alias.        |
|TTL-värde     | 1        | 1 används i 1 timme        |
|TTL-enhet     | Tider        | Timmar används som tids mått         |
|Alias     | cdnverify.adatumcdnendpoint.azureedge.net        | Det DNS-namn som du skapar alias för, i det här exemplet är det DNS-namnet för cdnverify.adatumcdnendpoint.azureedge.net som anges som standard för lagrings kontot.        |

Gå tillbaka till CDN-slutpunkten genom att klicka på **Network**  >  **CDN-profiler** och välj din CDN-profil. Klicka på **+ anpassad domän** och ange ditt CNAME-postalias utan cdnverify-prefixet och klicka på **Lägg till**.

När det här steget har slutförts går du tillbaka till DNS-zonen och skapar en CNAME-post utan prefixet cdnverify.  Efter den tidpunkten är det säkert att ta bort CNAME-posten med cdnverify-prefixet. Mer information om CDN och hur du konfigurerar en anpassad domän utan mellanliggande registrerings steg finns i [mappa Azure CDN innehåll till en anpassad domän](../cdn/cdn-map-content-to-custom-domain.md?toc=%dns%2ftoc.json).

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [konfigurerar omvänd DNS för tjänster som finns i Azure](dns-reverse-dns-for-azure-services.md).
