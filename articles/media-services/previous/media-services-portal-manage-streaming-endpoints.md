---
title: Hantera slut punkter för direkt uppspelning med Azure Portal | Microsoft Docs
description: Den här artikeln visar hur du hanterar strömnings slut punkter med Azure Portal.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: bda244267e81d959abbc4ae0a8532ddce68d5726
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017400"
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Hantera slutpunkter för direktuppspelning med Azure-portalen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Den här artikeln visar hur du använder Azure Portal för att hantera slut punkter för direkt uppspelning. 

>[!NOTE]
>Läs igenom [översikts](media-services-streaming-endpoints-overview.md) artikeln. 

Information om hur du skalar slut punkten för direkt uppspelning finns i [den här](media-services-portal-scale-streaming-endpoints.md) artikeln.

## <a name="start-managing-streaming-endpoints"></a>Börja hantera slut punkter för direkt uppspelning 

Gör så här för att börja hantera strömnings slut punkter för ditt konto.

1. Välj ditt Azure Media Services-konto i [Azure-portalen](https://portal.azure.com/).
2. I bladet **Inställningar** väljer du **strömnings slut punkter**.
   
    ![Skärm bild som visar sidan medie tjänster med "streaming-slutpunkter" valda från bladet "Inställningar".](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Du faktureras bara när slut punkten för direkt uppspelning är i körnings läge.

## <a name="adddelete-a-streaming-endpoint"></a>Lägga till/ta bort en slut punkt för direkt uppspelning

>[!NOTE]
>Det går inte att ta bort standard slut punkten för direkt uppspelning.

Om du vill lägga till/ta bort slut punkt för direkt uppspelning med Azure Portal gör du följande:

1. Om du vill lägga till en slut punkt för direkt uppspelning klickar du på **+-slut punkten** överst på sidan. 

    Du kanske vill ha flera slut punkter för direkt uppspelning om du planerar att ha olika CDN eller CDN och direkt åtkomst.

2. Tryck på knappen **ta bort** om du vill ta bort en slut punkt för direkt uppspelning.      
3. Klicka på knappen **Starta** för att starta slut punkten för direkt uppspelning.
   
    ![Skärm bild som visar den "slut punkt"-åtgärd som har marker ATS och fönstret "information om strömnings slut punkt" visas.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a name="configuring-the-streaming-endpoint"></a><a id="configure_streaming_endpoints"></a>Konfigurera slut punkten för direkt uppspelning
Med direkt uppspelnings slut punkt kan du konfigurera följande egenskaper:

* Åtkomstkontroll
* Cachekontroll
* Åtkomst principer för mellan platser

Detaljerad information om dessa egenskaper finns i [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>När CDN har Aktiver ATS kan du inte komma åt IP-åtkomst. IP-åtkomst kan bara användas om du inte har CDN.

Du kan konfigurera strömnings slut punkten genom att göra följande:

1. Välj den slut punkt för direkt uppspelning som du vill konfigurera.
2. Klicka på **Inställningar**.

En kort beskrivning av fälten följer.

![Skärm bild som visar åtgärden "Inställningar" som valts för slut punkten för direkt uppspelning.](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Maximal princip för cache: används för att konfigurera cachens livstid för till gångar som hanteras via den här slut punkten för direkt uppspelning Om inget värde anges används standardvärdet. Standardvärdena kan också definieras direkt i Azure Storage. Om Azure CDN har Aktiver ATS för slut punkten för direkt uppspelning bör du inte ange värdet för cache-principen till mindre än 600 sekunder.  
2. Tillåtna IP-adresser: används för att ange IP-adresser som skulle kunna ansluta till den publicerade slut punkten för direkt uppspelning. Om inga IP-adresser har angetts kan alla IP-adresser ansluta. IP-adresser kan anges antingen som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med en IP-adress och en CIDR-nätmask (till exempel "10.0.0.1/22") eller ett IP-intervall med IP-adress och en punktavgränsad decimal näts mask (till exempel "10.0.0.1 (255.255.255.0)").
3. Konfiguration för Akamai Signature header: används för att ange hur signaturen för Signature header från Akamai-servrar har kon figurer ATS. Förfallo datum är UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Skala din Premium-strömnings slut punkt

Mer information finns i [den här](media-services-portal-scale-streaming-endpoints.md) artikeln.

## <a name="enable-azure-cdn-integration"></a><a id="enable_cdn"></a>Aktivera Azure CDN-integrering

När du skapar ett nytt konto är standard slut punkten för direkt uppspelning Azure CDN aktive rad som standard.

Om du senare vill inaktivera/aktivera CDN måste slut punkten för direkt uppspelning vara i **stoppat** tillstånd. Det kan ta upp till två timmar innan Azure CDN-integreringen har Aktiver ATS och för att ändringarna ska vara aktiva över alla CDN pop. Men du kan starta slut punkten för direkt uppspelning och strömma utan avbrott från slut punkten för direkt uppspelning och när integrationen är klar levereras data strömmen från CDN. Under etablerings perioden kommer slut punkten för direkt uppspelning att vara i **Start** läge och du kan observera försämrade prestanda.

CDN-integrering är aktiverat i alla Azure-datacenter förutom Kina och federala myndighets regioner.

När den har Aktiver ATS inaktive ras **Access Control**, * * anpassad värdnamn och konfiguration av **Akamai-signatur** .
 
> [!IMPORTANT]
> Azure Media Services integration med Azure CDN implementeras på **Azure CDN från Verizon** för standard slut punkter för direkt uppspelning. Premium-slutpunkter för direkt uppspelning kan konfigureras med alla **Azure CDN pris nivåer och leverantörer**. Mer information om Azure CDN funktioner finns i CDN- [översikten](../../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Annat som är bra att tänka på

* När CDN är aktiverat för en slut punkt för direkt uppspelning kan klienterna inte begära innehåll direkt från ursprunget. Om du behöver kunna testa ditt innehåll med eller utan CDN kan du skapa en annan slut punkt för direkt uppspelning som inte är CDN-aktiverad.
* Slut punktens värdnamn för direkt uppspelning är detsamma när du har aktiverat CDN. Du behöver inte göra några ändringar i ditt Media Services-arbetsflöde när CDN har Aktiver ATS. Om till exempel slut punktens värdnamn är strasbourg.streaming.mediaservices.windows.net, efter att ha aktiverat CDN, används exakt samma värdnamn.
* För nya slut punkter för direkt uppspelning kan du aktivera CDN genom att skapa en ny slut punkt. för befintliga slut punkter för direkt uppspelning måste du först stoppa slut punkten och sedan aktivera/inaktivera CDN.
* Standard slut punkten för direkt uppspelning kan bara konfigureras med **Verizon standard-CDN-providern** med den klassiska Azure-portalen. Du kan dock aktivera andra Azure CDN-providers med hjälp av REST API: er.

## <a name="configure-cdn-profile"></a>Konfigurera CDN-profil

Du kan konfigurera CDN-profilen genom att välja knappen **Hantera CDN** överst.

![Slutpunkt för direktuppspelning](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
