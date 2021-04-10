---
title: Självstudie – Använda lagringsblobar via en anpassad Azure CDN-domän och HTTPS
description: Lär dig hur du lägger till en Azure CDN anpassad domän och aktiverar HTTPS på domänen för din anpassade Blob Storage-slutpunkt.
services: cdn
documentationcenter: ''
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 296a51edf024551a03330f4843690e97c6abd47d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564891"
---
# <a name="tutorial-access-storage-blobs-using-an-azure-cdn-custom-domain-over-https"></a>Självstudie – Använda lagringsblobar via en anpassad Azure CDN-domän och HTTPS

När du har integrerat ditt Azure-lagringskonto med Azure Content Delivery Network (CDN) kan du lägga till en anpassad domän och aktivera HTTPS på domänen för din anpassade lagringsslutpunkt. 

## <a name="prerequisites"></a>Förutsättningar

Innan du kan utföra stegen i den här självstudien måste du först integrera ditt Azure-lagringskonto med Azure CDN. Mer information finns i [Snabbstart: Integrera ett Azure-lagringskonto med Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="add-a-custom-domain"></a>Lägga till en anpassad domän
När du skapar en CDN-slutpunkt i din profil ingår som standard slutpunktens namn, som är en underdomän till azureedge.net, i webbadressen för leverans av CDN-innehåll. Du kan också välja att associera en anpassad domän med en CDN-slutpunkt. Då levererar du innehållet till en anpassad domän i din URL i stället för till ett slutpunktsnamn. Om du vill lägga till en anpassad domän för din slutpunkt följer du instruktionerna i den här självstudien: [Lägga till en anpassad domän för din Azure CDN-slutpunkt](cdn-map-content-to-custom-domain.md).

## <a name="configure-https"></a>Konfigurera HTTPS
Genom att använda HTTPS-protokollet för din anpassade domän kan du se till att data levereras säkert på internet via TLS/SSL-kryptering. När webbläsaren är ansluten till en webbplats via HTTPS valideras webbplatsens säkerhetscertifikat och verifierar att det är utfärdat av en giltig certifikatutfärdare. Om du vill konfigurera HTTPS för din domän följer du instruktionerna i den här självstudien: [Konfigurera HTTPS för en anpassad Azure CDN-domän](cdn-custom-ssl.md).

## <a name="shared-access-signatures"></a>Signaturer för delad åtkomst
Om din slutpunkt för bloblagring är konfigurerad att inte tillåta anonym läsåtkomst bör du tillhandahålla en [SAS-token (signatur för delad åtkomst)](cdn-sas-storage-support.md) i varje förfrågan du gör till den anpassade domänen. Som standard tillåter inte slutpunkter för bloblagring anonym läsåtkomst. Mer information om SAS finns i [Hantera anonym läsåtkomst till containrar och blobar](../storage/blobs/anonymous-read-access-configure.md).

Azure CDN ignorerar eventuella begränsningar som läggs till i SAS-token. Alla SAS-token har till exempel en giltighetstid, vilket innebär att innehåll fortfarande nås med en utgången SAS-token tills innehållet tas bort från POP-servrarna (point of presence) i CDN. Du kan styra hur länge data cachelagras i Azure CDN genom att ställa in cache-svarshuvudet. Mer information finns i [Hantera giltighetstid för Azure-lagringsblobar i Azure CDN](cdn-manage-expiration-of-blob-content.md).

Överväg att aktivera cachelagring av frågesträngar om du skapar flera SAS-webbadresser för samma blobslutpunkt. Då ser du till att varje webbadress behandlas som en unik entitet. Mer information finns i [Kontrollera cachelagringsbeteendet i Azure CDN med frågesträngar](cdn-query-string.md).

## <a name="http-to-https-redirection"></a>Omdirigering från HTTP till HTTPS
Du kan välja att omdirigera HTTP-trafik till HTTPS genom att skapa en regel för att omdirigera en URL med [standard regel motorn](cdn-standard-rules-engine.md) eller [Verizon Premium-regel motorn](cdn-verizon-premium-rules-engine.md). Standard regel motor är bara tillgänglig för Azure CDN från Microsoft-profiler, medan Verizon Premium Rules-motorn endast är tillgänglig från Azure CDN Premium från Verizon-profiler.

![Microsoft-Omdirigerad regel](./media/cdn-storage-custom-domain-https/cdn-standard-redirect-rule.png)

I regeln ovan lämnar du värdnamn, sökväg, frågesträng och fragment till att de inkommande värdena används i omdirigeringen. 

![Regel för att omdirigera Verizon](./media/cdn-storage-custom-domain-https/cdn-url-redirect-rule.png)

I regeln ovan refererar *CDN-slutpunkt-Name* till det namn som du konfigurerade för CDN-slutpunkten, som du kan välja i list rutan. Värdet för *origin-path* avser sökvägen i ditt ursprungliga lagringskonto där det statiska innehållet finns. Om du har allt statiskt innehåll i en enda container ska du byta ut *origin-path* mot namnet på containern.

## <a name="pricing-and-billing"></a>Priser och fakturering
När du använder blobar via Azure CDN betalar du [bloblagringspriser](https://azure.microsoft.com/pricing/details/storage/blobs/) för trafiken mellan POP-servrarna och ursprunget (bloblagringen), och [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/) för data som nås via POP-servrarna.

Om du till exempel ha ett lagringskonto i USA som används via Azure CDN och någon i Europa försöker komma åt en av blobarna i lagringskontot via Azure CDN så kontrollerar Azure CDN först den POP-server som ligger närmast Europa för bloben. Om en sådan hittas så använder Azure CDN den kopian av bloben till CDN-priser, eftersom den används via Azure CDN. Om det inte finns någon sådan server kopierar Azure CDN bloben till POP-servern, vilket leder till utgående trafik och transaktionsavgifter enligt bloblagringspriserna. Sedan används filen på POP-servern enligt Azure CDN-avgifter.

## <a name="next-steps"></a>Nästa steg
[Självstudie: Konfigurera Azures CDN-cachelagringsregler](cdn-caching-rules-tutorial.md)