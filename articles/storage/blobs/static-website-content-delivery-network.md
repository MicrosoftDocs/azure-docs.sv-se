---
title: Integrera en statisk webbplats med Azure CDN-Azure Storage
description: Lär dig hur du cachelagrar statisk webbplats innehåll från ett Azure Storage-konto med hjälp av Azure Content Delivery Network (CDN).
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.author: normesta
ms.date: 04/07/2020
ms.openlocfilehash: e458f98d82c910ec845ebf951b7f6470b6aba10d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95527334"
---
# <a name="integrate-a-static-website-with-azure-cdn"></a>Integrera en statisk webbplats med Azure CDN

Du kan aktivera [Azure Content Delivery Network (CDN)](../../cdn/cdn-overview.md) för att cachelagra innehåll från en [statisk webbplats](storage-blob-static-website.md) som finns på ett Azure Storage-konto. Du kan använda Azure CDN för att konfigurera den anpassade domän slut punkten för din statiska webbplats, etablera anpassade TLS/SSL-certifikat och konfigurera anpassade regler för omskrivning. Konfiguration av Azure CDN resulterar i ytterligare avgifter men ger konsekvent korta svarstider till din webbplats från hela världen. Azure CDN också tillhandahålla TLS-kryptering med ditt eget certifikat. 

Information om priser för Azure CDN finns i [Azure CDN-priser](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="enable-azure-cdn-for-your-static-website"></a>Aktivera Azure CDN för din statiska webbplats

Du kan aktivera Azure CDN för din statiska webbplats direkt från ditt lagrings konto. Om du vill ange avancerade konfigurationsinställningar för din CDN-slutpunkt, som [optimering av nedladdning av stora filer](../../cdn/cdn-optimization-overview.md#large-file-download), kan du istället använda [Azure CDN-tillägget](../../cdn/cdn-create-new-endpoint.md) till att skapa en CDN-profil och en slutpunkt.

1. Leta upp ditt lagrings konto i Azure Portal och Visa konto översikten.

1. Under menyn **BLOB service** väljer du **Azure CDN** för att öppna sidan **Azure CDN** :

    ![Skapa en CDN-slutpunkt](media/storage-blob-static-website-custom-domain/cdn-storage-new.png)

1. I avsnittet **CDN-profil** anger du om du vill skapa en ny CDN-profil eller använda en befintlig. En CDN-profil är en samling CDN-slutpunkter som delar en pris nivå och leverantör. Ange sedan ett namn för CDN som är unikt i din prenumeration.

1. Ange en pris nivå för CDN-slutpunkten. Mer information om priser finns i [Content Delivery Network priser](https://azure.microsoft.com/pricing/details/cdn/). Mer information om vilka funktioner som är tillgängliga för varje nivå finns i [jämföra Azure CDN produkt funktioner](../../cdn/cdn-features.md).

1. Ange ett namn på CDN-slutpunkten i fältet **namn på CDN-slutpunkt** . CDN-slutpunkten måste vara unik i Azure och ger den första delen av slut punkts-URL: en. Formuläret validerar att slut punktens namn är unikt.

1. Ange din statiska webbplats slut punkt i fältet **Ursprungligt värdnamn** . 

   Du hittar din statiska webbplats slut punkt genom att navigera till inställningarna för den **statiska webbplatsen** för ditt lagrings konto.  Kopiera den primära slut punkten och klistra in den i CDN-konfigurationen.

   > [!IMPORTANT]
   > Se till att ta bort protokoll identifieraren (*t. ex.* https) och avslutande snedstreck i URL: en. Om till exempel den statiska webbplats slut punkten är `https://mystorageaccount.z5.web.core.windows.net/` , anger du `mystorageaccount.z5.web.core.windows.net` i fältet **Ursprungligt värdnamn** .

   Följande bild visar ett exempel på en slut punkts konfiguration:

   ![Skärm bild som visar exempel på CDN-slutpunkts konfiguration](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Välj **skapa** och vänta sedan på att CDN ska etableras. När slutpunkten har skapats visas den i listan med slutpunkter. (Om du har fel i formuläret visas ett utrops tecken bredvid fältet.)

1. Kontrol lera att CDN-slutpunkten är korrekt konfigurerad genom att klicka på slut punkten för att navigera till dess inställningar. Från CDN-översikten för ditt lagrings konto letar du reda på slut punktens värdnamn och navigerar till slut punkten, som du ser i följande bild. Formatet på CDN-slutpunkten ser ut ungefär så här `https://staticwebsitesamples.azureedge.net` .

    ![Skärm bild som visar översikt över CDN-slutpunkt](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

1. När CDN-slutpunkten har tillhandahållits visas innehållet i den index.html-fil som du tidigare har laddat upp till din statiska webbplats genom att navigera till CDN-slutpunkten.

1. Om du vill granska ursprungs inställningarna för CDN-slutpunkten navigerar du till **ursprung** under avsnittet **Inställningar** för CDN-slutpunkten. Du ser att fältet **ursprungs typ** är inställt på *anpassat ursprung* och att fältet **Ursprungligt värdnamn** visar din statiska webbplats slut punkt.

    ![Skärm bild som visar ursprungs inställningar för CDN-slutpunkt](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="remove-content-from-azure-cdn"></a>Ta bort innehåll från Azure CDN

Om du inte längre vill cachelagra ett objekt i Azure CDN kan du vidta något av följande steg:

* Gör containern privat i stället för offentlig. Mer information finns i [Hantera anonym Läs behörighet till behållare och blobbar](./anonymous-read-access-configure.md).
* Inaktivera eller ta bort CDN-slutpunkten med hjälp av Azure Portal.
* Ändra din värdbaserade tjänst så att den inte längre svarar på förfrågningar för objektet.

Ett objekt som redan har cachelagrats i Azure CDN förblir cachelagrat tills objektets Time to Live-period har löpt ut eller tills slutpunkten har [rensats](../../cdn/cdn-purge-endpoint.md). När Time to Live-perioden har löpt ut avgör Azure CDN om CDN-slutpunkten fortfarande är giltigt och om objektet fortfarande är tillgängligt anonymt. Om så inte är fallet kommer objektet inte längre att cachelagras.

## <a name="next-steps"></a>Nästa steg

Valfritt Lägg till en anpassad domän till din Azure CDN-slutpunkt. Se [Självstudier: Lägg till en anpassad domän i Azure CDN slut punkten](../../cdn/cdn-map-content-to-custom-domain.md).