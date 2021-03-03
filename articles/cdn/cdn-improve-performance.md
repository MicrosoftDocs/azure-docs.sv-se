---
title: Förbättra prestanda genom att komprimera filer i Azure CDN | Microsoft Docs
description: Lär dig hur du kan förbättra fil överförings hastigheten och öka sid inläsnings prestanda genom att komprimera filerna i Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: af1cddff-78d8-476b-a9d0-8c2164e4de5d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/28/2018
ms.author: allensu
ms.openlocfilehash: 7c84d8129e1d0d88601495dec41883077784bb71
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101728203"
---
# <a name="improve-performance-by-compressing-files-in-azure-cdn"></a>Förbättra prestanda genom att komprimera filer i Azure CDN
Fil komprimering är en enkel och effektiv metod för att förbättra fil överförings hastigheten och öka sid inläsnings prestandan genom att minska fil storleken innan den skickas från servern. Fil komprimering kan minska bandbredds kostnaderna och ge användarna ett mer svars upplevelser.

Det finns två sätt att aktivera fil komprimering på:

- Aktivera komprimering på din ursprungs Server. I detta fall skickas Azure CDN längs de komprimerade filerna och levererar dem till klienter som begär dem.
- Aktivera komprimering direkt på CDN POP-servrar (*Compression i farten*). I det här fallet komprimerar CDN filerna och hanterar dem till slutanvändarna, även om de inte har komprimerats av ursprungs servern.

> [!IMPORTANT]
> Azure CDN konfigurations ändringar kan ta lite tid att sprida genom nätverket: 
> - För **Azure CDN Standard från Microsoft**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
> - För **Azure CDN Standard från Akamai**-profiler slutförs spridningen vanligtvis inom en minut. 
> - För **Azure CDN Standard från Verizon**- och **Azure CDN Premium från Verizon**-profiler slutförs spridningen vanligtvis inom 10 minuter. 
> 
> Om du ställer in komprimering för första gången för CDN-slutpunkten bör du vänta 1-2 timmar innan du felsöker för att se till att komprimerings inställningarna har spridit till pop-datorerna.

## <a name="enabling-compression"></a>Aktivera komprimering
Nivåerna standard och Premium CDN ger samma komprimerings funktioner, men användar gränssnittet skiljer sig åt. Mer information om skillnaderna mellan nivåerna standard och Premium CDN finns i [Azure CDN översikt](cdn-overview.md).

### <a name="standard-cdn-profiles"></a>Standard-CDN-profiler 
> [!NOTE]
> Det här avsnittet gäller för **Azure CDN Standard från Microsoft**, **Azure CDN Standard från Verizon** och **Azure CDN Standard från Akamai** -profiler.
> 
> 

1. På sidan CDN-profil väljer du den CDN-slutpunkt som du vill hantera.

    ![CDN-profil slut punkter](./media/cdn-file-compression/cdn-endpoints.png)

    Sidan CDN-slutpunkt öppnas.
2. Välj **komprimering**.

    ![Skärm bild som visar en slut punkt med komprimering valt på menyn Portal.](./media/cdn-file-compression/cdn-compress-select-std.png)

    Sidan komprimering öppnas.
3. Välj **på** för att aktivera komprimering.

    ![Skärm bild som visar aktivering av komprimering.](./media/cdn-file-compression/cdn-compress-standard.png)
4. Använd standard-MIME-typerna eller ändra listan genom att lägga till eller ta bort MIME-typer.

   > [!TIP]
   > Även om det är möjligt, rekommenderar vi inte att du använder komprimering på komprimerade format. Till exempel ZIP, MP3, MP4 eller JPG.
   > 

   > [!NOTE]
   > Det finns för närvarande inte stöd för att ändra standard listan över MIME-typer i Azure CDN Standard från Microsoft.
   > 

5. När du har gjort ändringarna väljer du **Spara**.

### <a name="premium-cdn-profiles"></a>Premium CDN-profiler
> [!NOTE]
> Det här avsnittet gäller endast för **Azure CDN Premium från Verizon** -profiler.
> 

1. På sidan CDN-profil väljer du **Hantera**.

    ![Hantera markering för CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    Hanterings portalen för CDN öppnas.
2. Hovra över fliken **http-stor** och hovra sedan över de **cachelagrade inställningarna** . Välj **komprimering**.

    ![Val av CDN-komprimering](./media/cdn-file-compression/cdn-compress-select.png)

    Komprimerings alternativen visas.

    ![Komprimerings alternativ för CDN-filen](./media/cdn-file-compression/cdn-compress-files.png)
3. Aktivera komprimering genom att välja **komprimering aktive rad**. Ange de MIME-typer som du vill komprimera som en kommaavgränsad lista (inga blank steg) i rutan **filtyper** .

   > [!TIP]
   > Även om det är möjligt, rekommenderar vi inte att du använder komprimering på komprimerade format. Till exempel ZIP, MP3, MP4 eller JPG.
   > 

4. När du har gjort ändringarna väljer du **Uppdatera**.

## <a name="compression-rules"></a>Komprimerings regler

### <a name="azure-cdn-standard-from-microsoft-profiles"></a>Azure CDN Standard från Microsoft-profiler

För **Azure CDN Standard från Microsoft** -profiler komprimeras endast kvalificerade filer. För att vara kvalificerad för komprimering måste en fil:
- Vara av en MIME-typ som har [kon figurer ATS för komprimering](#enabling-compression).
- Vara större än 1 KB
- Vara mindre än 8 MB

Dessa profiler stöder följande komprimerings kodningar:
- gzip (GNU zip)
- brotli 

Om begäran stöder fler än en komprimerings typ prioriteras brotli-komprimeringen.

När en begäran för en till gång anger gzip-komprimering och begäran resulterar i ett cache-missar, Azure CDN utföra gzip-komprimering av till gången direkt på POP-servern. Efteråt hanteras den komprimerade filen från cachen.

Om ursprunget använder Chunked Transfer Encoding (common Table EXPRESSIONS) för att skicka komprimerade data till CDN-POP, stöds inte svars storlekar som är större än 8 MB. 

### <a name="azure-cdn-from-verizon-profiles"></a>Azure CDN från Verizon-profiler

För **Azure CDN Standard från Verizon** och **Azure CDN Premium från Verizon** -profiler komprimeras endast kvalificerade filer. För att vara kvalificerad för komprimering måste en fil:
- Vara större än 128 byte
- Vara mindre än 3 MB

Dessa profiler stöder följande komprimerings kodningar:
- gzip (GNU zip)
- DEFLATE
- bzip2
- brotli 

Om begäran stöder fler än en komprimerings typ prioriteras dessa komprimerings typer framför brotli-komprimering.

När en begäran för en till gång anger brotli-komprimering (HTTP-huvud `Accept-Encoding: br` ) och begäran resulterar i ett cacheminne missar Azure CDN utföra brotli-komprimering av till gången direkt på pop-servern. Efteråt hanteras den komprimerade filen från cachen.

### <a name="azure-cdn-standard-from-akamai-profiles"></a>Azure CDN Standard från Akamai-profiler

För **Azure CDN Standard från Akamai** -profiler är alla filer kvalificerade för komprimering. En fil måste dock vara av en MIME-typ som har [kon figurer ATS för komprimering](#enabling-compression).

Dessa profiler stöder endast gzip-komprimerings kodning. När en profil slut punkt begär en gzip-kodad fil begärs den alltid från ursprunget, oavsett klient förfrågan. 

## <a name="compression-behavior-tables"></a>Komprimerings beteende tabeller
I följande tabeller beskrivs Azure CDN komprimerings beteende för varje scenario:

### <a name="compression-is-disabled-or-file-is-ineligible-for-compression"></a>Komprimering är inaktiverat eller så är filen inte giltig för komprimering
| Klientens begärda format (via Accept-Encoding huvud) | Cachelagrat fil format | CDN-svaret till klienten | &nbsp; &nbsp; &nbsp; &nbsp; Anteckningar &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
| --- | --- | --- | --- |
| Komprimerade |Komprimerade |Komprimerade | |
| Komprimerade |Okomprimerade |Okomprimerade | |
| Komprimerade |Inte cachelagrad |Komprimerad eller okomprimerad |Ursprungs svaret avgör om CDN utför en komprimering. |
| Okomprimerade |Komprimerade |Okomprimerade | |
| Okomprimerade |Okomprimerade |Okomprimerade | |
| Okomprimerade |Inte cachelagrad |Okomprimerade | |

### <a name="compression-is-enabled-and-file-is-eligible-for-compression"></a>Komprimering är aktiverat och filen är tillgänglig för komprimering
| Klientens begärda format (via Accept-Encoding huvud) | Cachelagrat fil format | CDN-svar på klienten | Kommentarer |
| --- | --- | --- | --- |
| Komprimerade |Komprimerade |Komprimerade |CDN-omkodningar mellan format som stöds. <br/>**Azure CDN från Microsoft** stöder inte kodning mellan format och hämtar i stället data från ursprung, komprimerar och cachelagrar separat för formatet. |
| Komprimerade |Okomprimerade |Komprimerade |CDN utför en komprimering. |
| Komprimerade |Inte cachelagrad |Komprimerade |CDN utför en komprimering om ursprunget returnerar en okomprimerad fil. <br/>**Azure CDN från Verizon** överför den okomprimerade filen på den första begäran och komprimerar och cachelagrar sedan filen för efterföljande begär Anden. <br/>Filer med `Cache-Control: no-cache` rubriken komprimeras aldrig. |
| Okomprimerade |Komprimerade |Okomprimerade |CDN utför en dekomprimering. <br/>**Azure CDN från Microsoft** stöder inte dekomprimering och hämtar i stället data från ursprung och cacheminne separat för okomprimerade klienter. |
| Okomprimerade |Okomprimerade |Okomprimerade | |
| Okomprimerade |Inte cachelagrad |Okomprimerade | |

## <a name="media-services-cdn-compression"></a>Media Services CDN-komprimering
För slut punkter som Aktiver ATS för Media Services CDN-direktuppspelning är komprimering aktiverat som standard för följande MIME-typer: 
- Application/VND. MS-sstr + XML 
- program/bindestreck + XML
- URL för program/vnd.apple.mpeg
- Application/f4m + XML 

## <a name="see-also"></a>Se även
* [Felsöka CDN-filkomprimering](cdn-troubleshoot-compression.md)    

