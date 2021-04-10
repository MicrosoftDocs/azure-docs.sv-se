---
title: Konfigurera offline PlayReady streaming
description: Den här artikeln visar hur du konfigurerar Azure Media Services v3-kontot för strömmande PlayReady för Windows 10 offline.
services: media-services
keywords: BINDESTRECK, DRM, Widevine offline-läge, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2020
ms.author: willzhan
ms.custom: devx-track-csharp
ms.openlocfilehash: aecae72b0bea07a0d8e240b3dcae7ee9b9662f95
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640714"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Offline PlayReady streaming för Windows 10 med Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services stöd för nedladdning/uppspelning av data offline med DRM-skydd. Den här artikeln beskriver offline-stöd för Azure Media Services för Windows 10/PlayReady-klienter. Du kan läsa om offline-läges stödet för iOS/FairPlay-och Android/Widevine-enheter i följande artiklar:

- [FairPlay-direktuppspelning offline för iOS](offline-fairplay-for-ios.md)
- [Offline-Widevine strömning för Android](offline-widevine-for-android.md)

> [!NOTE]
> Offline-DRM debiteras bara för att skapa en enskild begäran om en licens när du laddar ned innehållet. Eventuella fel faktureras inte.

## <a name="background-on-offline-mode-playback"></a>Uppspelning av bakgrund i offline-läge

Det här avsnittet innehåller en bakgrund i uppspelning av offline-läge, särskilt varför:

* I vissa länder/regioner är Internet tillgänglighet och/eller bandbredd fortfarande begränsad. Användarna kan välja att ladda ned för att kunna se innehållet i tillräckligt med hög upplösning för tillfredsställande visnings upplevelse. I det här fallet är problemet ofta inte nätverks tillgänglig, och den är begränsad nätverks bandbredd. OTT/OVP-providers ber om stöd för offline-läge.
* Som anges i konferensen Netflix 2016, kvartals visare, är nedladdning av innehåll en "OFT-begärd funktion" och "Vi är öppna för IT", som kallas för Reed Hastings, Netflix VD.
* En del innehålls leverantörer får inte tillåta leverans av DRM-licenser utöver ett land/regions gräns. Om en användare behöver resa utomlands och fortfarande vill titta på innehåll krävs offline-nedladdning.
 
Utmanings läget för att implementera offline-läge är följande:

* MP4 stöds av många spelare, Encoder-verktyg, men det finns ingen bindning mellan MP4-behållaren och DRM.
* På lång sikt är CFF med CENC ett sätt att gå. Men idag är support eko systemet för tools/Player inte det där. Vi behöver en lösning, idag.
 
Idén är: fil formatet för smidig strömning ([Piff](/iis/media/smooth-streaming/protected-interoperable-file-format)) med H264,/AAC har en bindning med PLAYREADY (AES-128-/maskin). En enskild smidig strömmande. ISMV-fil (förutsatt att ljudet är Muxed i video) är en fMP4 och kan användas för uppspelning. Om ett smidigt strömmande innehåll går via PlayReady-kryptering blir varje. ISMV-fil en PlayReady-skyddad fragmenterad MP4. Vi kan välja en. ISMV-fil med önskad bit hastighet och byta namn på den som. mp4 för nedladdning.

Det finns två alternativ för att vara värd för PlayReady Protected MP4 för progressiv nedladdning:

* En kan publicera denna MP4 i samma behållare/media service till gång och utnyttja Azure Media Services strömnings slut punkt för progressiv nedladdning.
* En kan använda SAS-lokaliserare för progressiv nedladdning direkt från Azure Storage, förbi Azure Media Services.
 
Du kan använda två typer av PlayReady-licens leverans:

* PlayReady licens leverans tjänst i Azure Media Services;
* PlayReady licens servrar som finns var som helst.

Nedan finns två typer av test till gångar, den första som använder PlayReady License Delivery i AMS medan den andra använder min PlayReady-licensserver som finns på en virtuell Azure-dator:

## <a name="asset-1"></a>Till gångs #1

* URL för progressiv nedladdning: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (AMS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/`

## <a name="asset-2"></a>Till gångs #2

* URL för progressiv nedladdning: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (lokalt): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx`

För uppspelnings testning använde vi ett universellt Windows-program i Windows 10. I [universella Windows 10](https://github.com/Microsoft/Windows-universal-samples)-exempel finns ett Basic Player-exempel som kallas [anpassningsbart strömnings exempel](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Allt vi behöver göra är att lägga till koden för att vi ska kunna välja nedladdad video och använda den som källa, i stället för anpassningsbar direkt uppspelnings källa. Knappen ändringar är i klickar du på händelse hanteraren:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![Uppspelning av PlayReady-skyddade fMP4 i offlineläge](./media/offline-playready-for-windows/offline-playready1.jpg)

Eftersom videon är under PlayReady-skydd kommer skärm bilden inte att kunna ta med videon.

I sammanfattning har vi uppnått offline-läge på Azure Media Services:

* Innehålls kodning och PlayReady-kryptering kan göras i Azure Media Services eller andra verktyg.
* Innehållet kan finnas i Azure Media Services eller Azure Storage för progressiv nedladdning.
* PlayReady-licensens leverans kan vara från Azure Media Services eller någon annan stans.
* Det för beredda strömmande innehållet kan fortfarande användas för direkt uppspelning via tank streck eller smidigt med PlayReady som DRM.
