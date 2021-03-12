---
title: Översikt över reserverade enheter för media | Microsoft Docs
description: Den här artikeln är en översikt över skalning av medie bearbetning med Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: d0692996c27f969ffc90078db2ddcc849ee15ab1
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012725"
---
# <a name="media-reserved-units"></a>Mediereserverade enheter

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Med Azure Media Services kan du skala medie bearbetning genom att hantera reserverade enheter (MRUs). En MRU ger ytterligare data bearbetnings kapacitet som krävs för kodning av media. Antalet MRUs bestämmer hastigheten med vilken dina medie aktiviteter bearbetas och hur många medie uppgifter som kan bearbetas samtidigt i ett konto. Om ditt konto till exempel har fem MRUs och det finns aktiviteter som ska bearbetas, kan fem medie uppgifter köras samtidigt. Eventuella återstående uppgifter placeras i kö och kan hämtas i tur och ordning när en aktivitet som körs slutförs. Varje MRU som du etablerar resulterar i en kapacitets reservation men ger dig inte en dedikerad resurs. Under tiden med mycket hög efter frågan kanske alla dina MRUs inte börjar bearbeta direkt.

## <a name="choosing-between-different-reserved-unit-types"></a>Välja mellan olika reserverade enhets typer

I följande tabell får du hjälp att fatta ett beslut när du väljer mellan olika kodnings hastigheter.  Den visar varaktigheten för kodning i 7 minuter, 1080p-video beroende på vilken MRU som används.

|RU-typ|Scenario|Exempel resultat för den 7: a minimala 1080p-videon |
|---|---|---|
| **S1**|Kodning med enkel bit hastighet. <br/>Filer vid SD eller under upplösning, inte tids känsliga, låga kostnader.|Kodning till enkel bit hastighet för SD-fil med enkel bit hastighet med "H264, enkel bit hastighet SD 16x9" tar cirka 7 minuter.|
| **S2**|Kodning med enkel bit hastighet och flera bit hastigheter.<br/>Normal användning för både SD-och HD-kodning.|Kodning med för inställningen "H264, enskild bit hastighet" tar cirka 6 minuter.<br/><br/>Kodning med för inställningen "H264, Multiple bit hastighet" tar cirka 12 minuter.|
| **S3**|Kodning med enkel bit hastighet och flera bit hastigheter.<br/>Högupplösta HD-och 4K-lösningar. Tids känslig, snabbare svars tids kodning.|Kodning med en för inställning för "H264, enkel bit hastighet" tar cirka 3 minuter.<br/><br/>Encoding med för inställningen "H264, Multiplable bit/1080p" tar cirka 8 minuter.|

> [!NOTE]
> Om du inte etablerar MRU för ditt konto kommer dina medie aktiviteter att bearbetas med prestandan för ett S1-MRU och uppgifter hämtas sekventiellt. Ingen bearbetnings kapacitet är reserverad, så vänte tiden mellan en aktivitet avslutas och nästa gång beror på tillgängligheten för resurser i systemet.

## <a name="considerations"></a>Överväganden

* För ljud analys-och video analys jobb som utlöses av Media Services v3 eller Video Indexer, rekommenderar vi att du konfigurerar kontot med tio S3-enheter. Om du behöver fler än 10 S3-MRUs öppnar du ett support ärende med hjälp av [Azure Portal](https://portal.azure.com/).
* För att kunna koda uppgifter som inte har MRUs, finns det ingen övre gräns för den tid som dina aktiviteter kan spendera i köade tillstånd, och högst en aktivitet körs i taget.

## <a name="billing"></a>Fakturering

Du debiteras baserat på antalet minuter som de reserverade enheterna är etablerade i ditt konto. Detta inträffar oberoende av om det finns jobb som körs i ditt konto. En detaljerad förklaring finns i avsnittet Vanliga frågor och svar på sidan [Media Services prissättning](https://azure.microsoft.com/pricing/details/media-services/) .

## <a name="quotas-and-limitations"></a>Kvoter och begränsningar

Information om kvoter och begränsningar och hur du öppnar ett support ärende finns i [kvoter och begränsningar](media-services-quotas-and-limitations.md).

## <a name="next-steps"></a>Nästa steg

Prova att skala medie bearbetning med någon av dessa tekniker:

[.Net](media-services-dotnet-encoding-units.md) 
 [Portal](media-services-portal-scale-media-processing.md) 
 [Rest](/rest/api/media/operations/encodingreservedunittype) 
 [Java](https://github.com/rnrneverdies/azure-sdk-for-media-services-java-samples) 
 [Php](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)