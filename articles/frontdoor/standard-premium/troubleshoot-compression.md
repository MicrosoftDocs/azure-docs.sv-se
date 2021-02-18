---
title: Fel sökning av fil komprimering i Azures främre dörr standard/Premium
description: Lär dig hur du felsöker problem med fil komprimering i Azures front dörr. Den här artikeln beskriver flera möjliga orsaker.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2020
ms.author: qixwang
ms.openlocfilehash: c912ebde5499ec2f9f68e5f5762af60823faefe1
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100581"
---
# <a name="troubleshooting-azure-front-door-standardpremium-file-compression"></a>Felsöka fil komprimering för Azure-frontend-standard/Premium

> [!Note]
> Den här dokumentationen gäller för Azure frontend-standard/Premium (för hands version). Letar du efter information om Azures frontend-dörr? Visa [här](../front-door-overview.md).

Den här artikeln hjälper dig att felsöka problem med fil komprimerings problem i Azure-front dörren.

> [!IMPORTANT]
> Azure front dörr standard/Premium (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="symptom"></a>Symptom

Komprimering av vägen är aktiverat, men filer returneras okomprimerade.

> [!TIP]
> Om du vill kontrol lera om filerna returneras som komprimerade måste du använda ett verktyg som [Fiddler](https://www.telerik.com/fiddler) eller webbläsarens [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Kontrol lera vilka HTTP-svarshuvuden som returneras med ditt cachelagrade CDN-innehåll.  Om det finns ett huvud som heter `Content-Encoding` **gzip**, **bzip2** eller **DEFLATE**, komprimeras innehållet.
> 
> ![Innehålls kodnings rubrik](../media/troubleshoot-compression/content-header.png)
> 

## <a name="cause"></a>Orsak

Det finns flera möjliga orsaker, inklusive:

* Det begärda innehållet är inte tillgängligt för komprimering.
* Komprimering är inte aktiverat för den begärda filtypen.
* HTTP-begäran innehåller inget huvud som begär en giltig komprimerings typ.
* Ursprunget skickar segmenterat innehåll.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

> [!TIP]
> När du distribuerar nya slut punkter kan AFD konfigurations ändringar ta lite tid att sprida genom nätverket.  Normalt tillämpas ändringarna inom 90 minuter.  Om det här är första gången du konfigurerar komprimering för CDN-slutpunkten bör du vänta 1-2 timmar innan du ser till att komprimerings inställningarna har spridits till pop. 
> 

### <a name="verify-the-request"></a>Verifiera begäran

Först bör vi kontrol lera begäran. Du kan använda webbläsarens **[utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)** för att visa de begär Anden som görs.

* Kontrol lera att begäran skickas till din slut punkts-URL, `<endpointname>.z01.azurefd.net` och inte ditt ursprung.
* Kontrol lera att begäran innehåller ett **Accept-Encoding-** huvud och att värdet för den rubriken innehåller **gzip**, **DEFLATE** eller **bzip2**.

![Huvuden för CDN-begäran](../media/troubleshoot-compression/request-headers.png)

### <a name="verify-compression-settings"></a>Kontrol lera komprimerings inställningarna

Navigera till din slut punkt i [Azure Portal](https://portal.azure.com) och välj knappen **Konfigurera** på panelen vägar. Kontrol lera att komprimering har **Aktiver ATS**.

![Komprimerings inställningar för CDN](../media/troubleshoot-compression/compression-settings.png)

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Kontrol lera begäran på ursprungs servern för en **via** -rubrik

**Via** HTTP-huvudet anger webb servern som begäran skickas av en proxyserver.  Microsoft IIS-webbservrar komprimerar som standard inte svar när begäran innehåller en **via** -rubrik.  Om du vill åsidosätta det här beteendet gör du följande:

* **IIS 6**: Ange HcNoCompressionForProxies = "false" i IIS-metabasens egenskaper. Mer information finns i [IIS 6-komprimering](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90)).
* **IIS 7 och uppåt**: ange både **noCompressionForHttp10** och **noCompressionForProxies** till *false* i Server konfigurationen. Mer information finns i [http-komprimering](https://www.iis.net/configreference/system.webserver/httpcompression).
