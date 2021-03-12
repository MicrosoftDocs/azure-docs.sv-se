---
title: Konfigurera Haivision KB-kodaren för att skicka en enda bit Ströms bit ström till Azure | Microsoft Docs
description: Det här avsnittet visar hur du konfigurerar Haivision KB Live Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för Live encoding.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 20c75672bc7198fb028278dc5432cc6a77e3766e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016873"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Använd Haivision KB Live Encoder för att skicka en enda bit Ströms bit ström

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

Det här avsnittet visar hur du konfigurerar [HAVISION KB Live Encoder](https://www.haivision.com/products/kb-series/) Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för direktsänd kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

I den här självstudien visas hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer-verktyget (AMSE). Det här verktyget körs bara på Windows-datorer. Om du använder Mac eller Linux använder du Azure Portal för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Förutsättningar
*   Åtkomst till en Haivision KB-kodare som kör SW v 5.01 eller senare.
* [Skapa ett Azure Media Services-konto](media-services-portal-create-account.md)
* Se till att en slut punkt för direkt uppspelning körs. Mer information finns i [Hantera strömnings slut punkter i ett Media Services konto](media-services-portal-manage-streaming-endpoints.md)
* Installera den senaste versionen av [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) -verktyget.
* Starta verktyget och Anslut till ditt AMS-konto.

## <a name="tips"></a>Tips
* Använd en direktkopplad internetanslutning när det är möjligt.
* En bra tumregel för att bestämma bandbreddskrav är att dubblera sändningens bithastighet. Även om det här inte är ett obligatoriskt krav bidrar det till att minimera påverkan av nätverks belastning.
* När du använder programvarubaserade kodare, Stäng alla onödiga program.

## <a name="create-a-channel"></a>Skapa en kanal
1. I AMSE-verktyget går du till fliken **Live** och högerklickar i kanalområdet. Välj **skapa kanal...** från menyn.
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Ange ett kanal namn, beskrivnings fältet är valfritt. Under kanal inställningar väljer du **standard** för alternativet Live encoding med inmatnings protokollet inställt på **RTMP**. Du kan lämna alla andra inställningar som de är. Kontrol lera att **starta den nya kanalen nu** är markerat.
3. Klicka på **skapa kanal**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Det kan ta upp till 20 minuter att starta kanalen.

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurera Haivision KB-kodare
I den här självstudien används följande inställningar för utdata. I resten av det här avsnittet beskrivs konfigurations stegen i detalj.

Video:
-   Codec: H.264
-   Profil: Hög (nivå 4.0)
-   Bit hastighet: 5000 kbit/s
-   Nyckel bild: 2 sekunder (60 bild rutor)
-   Bild Rute frekvens: 30

In
-   Codec: AAC (LC)
-   Bithastighet: 192 kbit/s
-   Samplings frekvens: 44,1 kHz

## <a name="configuration-steps"></a>Konfigurationssteg
1.  Logga in på Haivision KB-användargränssnittet.
2.  Klicka på **meny knappen** i kanal kontroll centret och välj **Lägg till kanal**  
    ![Skärm bild 2017-08-14 vid 9.15.09](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Skriv **kanal namnet** i fältet namn och klicka på Nästa.  
    ![Skärm bild 2017-08-14 vid 9.19.07](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Välj **kanalens indatakälla** **från List rutan indatakälla och** Klicka på Nästa.
    ![Skärm bild 2017-08-14 vid 9.20.44](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Från List rutan **Encoder-mall** väljer du **H264,-720-AAC-192** och klickar på Nästa.
    ![Skärm bild 2017-08-14 vid 9.23.15](./media/media-services-configure-kb-live-encoder/step5.png)
6.  I list rutan **Välj ny utdata** väljer du **RTMP** och klickar på Nästa.  
    ![Skärm bild 2017-08-14 vid 9.27.51](./media/media-services-configure-kb-live-encoder/step6.png)
7.  I fönstret **kanal utdata** fyller du i Azure Stream-informationen. Klistra in **RTMP** -länken från den första kanal konfigurationen i **Server** delen. Skriv namnet på kanalen i fältet **utdatafil** . I mallen för Stream-namn mal len använder du mallen RTMPStreamName_% video_bitrate% för att ge data strömmen ett namn.
    ![Skärm bild 2017-08-14 vid 9.33.17](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Klicka på Nästa och sedan på Slutför.
9.  Starta Encoder-kanalen genom att klicka på **uppspelnings knappen** .  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Testa uppspelning
Navigera till verktyget AMSE och högerklicka på den kanal som ska testas. Hovra över uppspelning av för hands versionen på menyn och välj med Azure Media Player.

Om data strömmen visas i spelaren har kodaren kon figurer ATS korrekt för att ansluta till AMS.

Om ett fel tas emot måste kanalen återställas och kodarens inställningar justeras. Se fel söknings artikeln för vägledning.

## <a name="create-a-program"></a>Skapa ett program
1.  Skapa ett program när kanal uppspelning har bekräftats. Under fliken Live i AMSE-verktyget högerklickar du i program arean och väljer Skapa nytt program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Ge programmet ett namn och, om det behövs, ändra Arkiv fönstrets längd (som är standardvärdet fyra timmar). Du kan också ange en lagrings plats eller låta standardvärdet vara kvar.
2.  Markera kryss rutan Starta programmet nu.
3.  Klicka på Skapa program.
4.  När programmet har körts bekräftar du uppspelningen genom att högerklicka på programmet och navigera för att spela upp program och välja med Azure Media Player.
5.  När du har bekräftat, högerklickar du på programmet igen och väljer Kopiera URL: en till Urklipp (eller hämta informationen från program information och inställnings alternativ på menyn).

Strömmen är nu klar att bäddas in i en spelare eller distribueras till en publik för Live-visning.

> [!NOTE]
> Att skapa program tar mindre tid än att skapa en kanal.
