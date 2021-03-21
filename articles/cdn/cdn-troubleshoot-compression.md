---
title: Felsöka fil komprimering i Azure CDN | Microsoft Docs
description: Lär dig hur du felsöker problem med fil komprimering i Azure Content Delivery Network. Den här artikeln beskriver flera möjliga orsaker.
services: cdn
documentationcenter: ''
author: sohamnc
manager: danielgi
editor: ''
ms.assetid: a6624e65-1a77-4486-b473-8d720ce28f8b
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: f49af1488a0c044639a72fc2ea52ba0a47727a24
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996159"
---
# <a name="troubleshooting-cdn-file-compression"></a>Felsöka CDN-filkomprimering
Den här artikeln hjälper dig att felsöka problem med [CDN-filkomprimering](cdn-improve-performance.md).

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Alternativt kan du också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och klicka på **Hämta support**.

## <a name="symptom"></a>Symptom
Komprimeringen för slut punkten är aktive rad, men filerna returneras okomprimerade.

> [!TIP]
> Om du vill kontrol lera om filerna returneras som komprimerade måste du använda ett verktyg som [Fiddler](https://www.telerik.com/fiddler) eller webbläsarens [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Kontrol lera vilka HTTP-svarshuvuden som returneras med ditt cachelagrade CDN-innehåll.  Om det finns ett huvud som heter `Content-Encoding` **gzip**, **bzip2** eller **DEFLATE**, komprimeras innehållet.
> 
> ![Innehålls kodnings rubrik](./media/cdn-troubleshoot-compression/cdn-content-header.png)
> 
> 

## <a name="cause"></a>Orsak
Det finns flera möjliga orsaker, inklusive:

* Det begärda innehållet är inte kvalificerat för komprimering.
* Komprimering är inte aktiverat för den begärda filtypen.
* HTTP-begäran innehåller inte något huvud som begär en giltig komprimerings typ.
* Ursprunget skickar segmenterat innehåll.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
> [!TIP]
> När du distribuerar nya slut punkter tar det en stund att sprida ändringar i CDN-konfigurationen i nätverket.  Normalt tillämpas ändringarna inom 90 minuter.  Om det här är första gången du konfigurerar komprimering för CDN-slutpunkten bör du vänta 1-2 timmar innan du ser till att komprimerings inställningarna har spridits till pop. 
> 
> 

### <a name="verify-the-request"></a>Verifiera begäran
Först bör vi göra en snabb Sanity kontroll av begäran.  Du kan använda webbläsarens [utvecklingsverktyg](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) för att visa de begär Anden som görs.

* Kontrol lera att begäran skickas till din slut punkts-URL, `<endpointname>.azureedge.net` och inte ditt ursprung.
* Kontrol lera att begäran innehåller ett **Accept-Encoding-** huvud och att värdet för den rubriken innehåller **gzip**, **DEFLATE** eller **bzip2**.

> [!NOTE]
> **Azure CDN från Akamai** -profiler stöder endast **gzip** -kodning.
> 
> 

![Huvuden för CDN-begäran](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profiles"></a>Kontrol lera komprimerings inställningarna (standard-CDN-profiler)
> [!NOTE]
> Det här steget gäller bara om din CDN-profil är en **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon** eller **Azure CDN Standard från Akamai** -profilen. 
> 
> 

Navigera till din slut punkt i [Azure Portal](https://portal.azure.com) och klicka på knappen **Konfigurera** .

* Kontrol lera att komprimering har Aktiver ATS.
* Kontrol lera att MIME-typen för det innehåll som ska komprimeras ingår i listan över komprimerade format.

![Komprimerings inställningar för CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profiles"></a>Kontrol lera komprimerings inställningar (Premium CDN-profiler)
> [!NOTE]
> Det här steget gäller bara om din CDN-profil är ett **Azure CDN Premium från Verizon** -profilen.
> 
> 

Navigera till din slut punkt i [Azure Portal](https://portal.azure.com) och klicka på knappen **Hantera** .  Den kompletterande portalen öppnas.  Hovra över fliken **http-stor** och hovra sedan över de **cachelagrade inställningarna** .  Klicka på **komprimering**. 

* Kontrol lera att komprimering har Aktiver ATS.
* Kontrol lera att listan **filtyper** innehåller en kommaavgränsad lista (inga blank steg) av MIME-typer.
* Kontrol lera att MIME-typen för det innehåll som ska komprimeras ingår i listan över komprimerade format.

![Komprimerings inställningar för CDN Premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached-verizon-cdn-profiles"></a>Kontrol lera att innehållet är cachelagrat (Verizon CDN-profiler)
> [!NOTE]
> Det här steget gäller bara om din CDN-profil är en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** -profilen.
> 
> 

Använd webbläsarens utvecklingsverktyg för att kontrol lera svarshuvuden för att se till att filen cachelagras i den region där den begärs.

* Kontrol lera **Server** svars huvudet.  Rubriken ska ha formatet **plattform (POP/server-ID)**, som visas i följande exempel.
* Kontrol lera **X-cache-** svarets rubrik.  Rubriken ska läsa **träff**.  

![CDN-svarshuvuden](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements-verizon-cdn-profiles"></a>Kontrol lera att filen uppfyller storleks kraven (Verizon CDN-profiler)
> [!NOTE]
> Det här steget gäller bara om din CDN-profil är en **Azure CDN Standard från Verizon** eller **Azure CDN Premium från Verizon** -profilen.
> 
> 

För att vara kvalificerad för komprimering måste en fil uppfylla följande storleks krav:

* Större än 128 byte (innehålls längd: 128)
* Mindre än 3 MB

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Kontrol lera begäran på ursprungs servern för en **via** -rubrik
**Via** HTTP-huvudet anger webb servern som begäran skickas av en proxyserver.  Microsoft IIS-webbservrar komprimerar som standard inte svar när begäran innehåller en **via** -rubrik.  Gör så här för att åsidosätta det här problemet:

* **IIS 6**: [Ange HCNOCOMPRESSIONFORPROXIES = "false" i IIS-metabasens egenskaper](/previous-versions/iis/6.0-sdk/ms525390(v=vs.90))
* **IIS 7 och uppåt**: [ange både **NoCompressionForHttp10** och **noCompressionForProxies** som falskt i Server konfigurationen](https://www.iis.net/configreference/system.webserver/httpcompression)

