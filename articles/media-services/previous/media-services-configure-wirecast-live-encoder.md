---
title: Konfigurera Wirecast-kodaren för Telestreams att skicka en enskild bit hastighet i real tid | Microsoft Docs
description: 'Det här avsnittet visar hur du konfigurerar Wirecast Live Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för Live encoding. '
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: a736270919f36cb7d4bbd4e686a98ddc57f402fa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016839"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Använda Wirecast-kodaren för att skicka en enda bit hastighet i real tid

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]
 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>

Den här artikeln visar hur du konfigurerar [NetStream Wirecast](https://www.telestream.net/wirecast/overview.htm) Live Encoder för att skicka en enda bit ström till AMS kanaler som är aktiverade för direktsänd kodning. Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

I den här självstudien visas hur du hanterar Azure Media Services (AMS) med Azure Media Services Explorer-verktyget (AMSE). Det här verktyget körs bara på Windows-datorer. Om du använder Mac eller Linux använder du Azure Portal för att skapa [kanaler](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) och [program](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Kodare måste ha stöd för TLS 1,2 när du använder RTMP-protokoll. Använd Wirecast version 13.0.2 eller högre på grund av kraven för TLS 1,2.

## <a name="prerequisites"></a>Förutsättningar
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

    ![Skärm bild som visar skapa kanal valt från en meny.](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Ange ett kanal namn, beskrivnings fältet är valfritt. Under kanal inställningar väljer du **standard** för alternativet Live encoding med inmatnings protokollet inställt på **RTMP**. Du kan lämna alla andra inställningar som de är.

    Kontrol lera att **starta den nya kanalen nu** är markerat.

3. Klicka på **skapa kanal**.

   ![Skärm bild som visar dialog rutan skapa en Live Channel.](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Det kan ta upp till 20 minuter att starta kanalen.
>
>

När kanalen startas kan du [Konfigurera kodaren](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Faktureringen börjar så snart som kanalen försätts i ett klart tillstånd. Mer information finns i [kanalens tillstånd](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Konfigurera Wirecast-kodare för Telestream
I den här självstudien används följande inställningar för utdata. I resten av det här avsnittet beskrivs konfigurations stegen i detalj.

**Video**:

* Codec: H.264
* Profil: Hög (nivå 4.0)
* Bit hastighet: 5000 kbit/s
* Nyckel bild: 2 sekunder (60 sekunder)
* Bild Rute frekvens: 30

**Ljud**:

* Codec: AAC (LC)
* Bithastighet: 192 kbit/s
* Samplings frekvens: 44,1 kHz

### <a name="configuration-steps"></a>Konfigurationssteg
1. Öppna Wirecast-programmet för strömning på den dator som används och konfigurera för RTMP streaming.
2. Konfigurera utdata genom att gå till fliken **utdata** och välja inställningar för **utdata...**.

    Kontrol lera att **utmatnings destinationen** är **RTMP-Server**.
3. Klicka på **OK**.
4. På sidan inställningar anger du att **mål** fältet ska **Azure Media Services**.

    Kodnings profilen är förvald till **Azure H. 264 720p 16:9 (1280x720)**. Om du vill anpassa inställningarna väljer du kugg hjuls ikonen till höger om List rutan och väljer sedan ny för **inställning**.

    ![Skärm bild som visar dialog rutan Välj en mall med en valt.](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Konfigurera kodare för inställningar.

    Namnge för inställningen och kontrol lera följande rekommenderade inställningar:

    **Video**

   * Kodare: MainConcept H. 264
   * Bild rutor per sekund: 30
   * Genomsnittlig bit hastighet: 5000 Kbits/SEK (kan justeras baserat på nätverks begränsningar)
   * Profil: main
   * Nyckel bild var: 60 bild rutor

     **Ljud**

   * Mål bit hastighet: 192 Kbits/SEK
   * Samplings frekvens: 44,100 kHz

     ![Skärm bilden visar kodarens för inställning för AzureTest1.](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Tryck på **Spara**.

    I encoding-fältet finns nu den nyligen skapade profilen som kan väljas.

    Kontrol lera att den nya profilen är markerad.
7. Hämta kanalens inmatnings-URL för att tilldela den till Wirecast **RTMP-slutpunkten**.

    Gå tillbaka till AMSE-verktyget och kontrol lera statusen för kanal slut för ande. När statusen har ändrats från att **Starta** till **körs** kan du hämta INgångs-URL: en.

    När kanalen körs högerklickar du på kanal namnet, navigerar ned till hovra över **Kopiera ingångs-URL till Urklipp** och väljer sedan **primär URL för indatakälla**.  

    ![Skärm bild som visar alternativet Kopiera Indatatyp U R L till Urklipp för primär inskrivning U R L.](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. I fönstret Wirecast- **utdata** klistrar du in den här informationen i fältet **adress** i avsnittet utdata och tilldelar ett ström namn.

    ![Skärm bilden visar inställningarna för utdata.](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Välj **OK**.
2. På **Wirecast** -skärmen bekräftar du att indata-källor för video och ljud är klara och trycker sedan på **Stream** i det övre vänstra hörnet.

    ![Skärm bild som visar Wirecast Stream-knappen.](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Innan du klickar på **Stream** **måste** du se till att kanalen är klar.
> Du bör också se till att lämna kanalen i ett klart tillstånd utan att en feed för inmatnings bidrag är längre än > 15 minuter.
>
>

## <a name="test-playback"></a>Testa uppspelning

Navigera till verktyget AMSE och högerklicka på den kanal som ska testas. Hovra över **uppspelning av för hands versionen på** menyn och välj **med Azure Media Player**.  

![Skärm bild som visar uppspelning av alternativet för förhands granskning med Azure Media Player valt.](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Om data strömmen visas i spelaren har kodaren kon figurer ATS korrekt för att ansluta till AMS.

Om ett fel tas emot måste kanalen återställas och kodarens inställningar justeras. Se [fel söknings](media-services-troubleshooting-live-streaming.md) artikeln för vägledning.  

## <a name="create-a-program"></a>Skapa ett program
1. Skapa ett program när kanal uppspelning har bekräftats. Under fliken **Live** i AMSE-verktyget högerklickar du i program arean och väljer **Skapa nytt program**.  

    ![Skärm bild som visar alternativet för att skapa program valt.](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Ge programmet ett namn och, om det behövs, ändra **Arkiv fönstrets längd** (som är standardvärdet fyra timmar). Du kan också ange en lagrings plats eller låta standardvärdet vara kvar.  
3. Markera kryss rutan **starta programmet nu** .
4. Klicka på **skapa program**.  

   >[!NOTE]
   >Att skapa program tar mindre tid än att skapa en kanal.
       
5. När programmet har körts bekräftar du uppspelningen genom att högerklicka på programmet och navigera för att **spela upp program** och välja **med Azure Media Player**.  
6. När du har bekräftat, högerklickar du på programmet igen och väljer **Kopiera URL: en till Urklipp** (eller hämta informationen från **program information och inställnings** alternativ på menyn).

Strömmen är nu klar att bäddas in i en spelare eller distribueras till en publik för Live-visning.  

## <a name="troubleshooting"></a>Felsökning
Se [fel söknings](media-services-troubleshooting-live-streaming.md) artikeln för vägledning.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
