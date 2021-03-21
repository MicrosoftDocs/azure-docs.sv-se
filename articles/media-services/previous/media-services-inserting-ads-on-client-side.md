---
title: Infoga annonser på klient Sidan | Microsoft Docs
description: Den här artikeln visar hur du infogar annonser i media på klient sidan.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d942099d0abbdfc4ddfa0276184500166250728
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103014969"
---
# <a name="inserting-ads-on-the-client-side"></a>Infoga annonser på klient Sidan

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Den här artikeln innehåller information om hur du infogar olika typer av annonser på klient sidan.

Information om dold textning och stöd för AD i direktsända strömmande videor finns i [stöd för dold textning och AD-infogning](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player stöder för närvarande inte annonser.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Infoga annonser i dina media
Azure Media Services ger stöd för AD-infogning via Windows Media-plattformen: Player-ramverk. Player-ramverk med stöd för AD är tillgängliga för Windows 8-, Silverlight-, Windows Phone 8-och iOS-enheter. Varje Player-ramverk innehåller exempel kod som visar hur du implementerar ett Player-program. Det finns tre olika typer av annonser som du kan infoga i media: list.

* **Linjär** – hel ram annonser som pausar huvud videon.
* **Dislines** – överläggs annonser som visas som huvud video spelas, vanligt vis en logo typ eller en annan statisk bild som placeras i spelaren.
* **Companion** – annonser som visas utanför spelaren.

Annonser kan placeras var som helst i huvud videons tids linje. Du måste meddela spelaren när annonsen ska spelas upp och vilka annonser som ska spelas upp. Detta görs med hjälp av en uppsättning standard-XML-baserade filer: mall för video AD-tjänst (stor), digital video med flera AD-spel (VMAP), pool för medie abstrakt sekvens (MAST) och Digital Video Player AD-gränssnitts definition (VPAID). STORA filer anger vilka annonser som ska visas. VMAP-filer anger när olika annonser ska spelas upp och innehåller enorma XML-filer. Huvudfiler är ett annat sätt att sekvensera annonser som också kan innehålla enorma XML. VPAID-filer definierar ett gränssnitt mellan Videos pelaren och AD-eller AD-servern.

Varje Player-ramverk fungerar annorlunda och var och en kommer att omfattas av den egna artikeln. I den här artikeln beskrivs de grundläggande mekanismerna som används för att infoga annonser. Videos Player-program begär annonser från en AD-server. AD-servern kan svara på flera olika sätt:

* Returnera en omfattande fil
* Returnera en VMAP-fil (med inbäddad stor)
* Returnera en fil för delning (med inbäddad stor)
* Returnera en stor fil med VPAID-annonser

### <a name="using-a-video-ad-service-template-vast-file"></a>Använda en mall för en videofil i en videofil (stor)
En omfattande fil anger vad AD eller Ads som ska visas. Följande XML är ett exempel på en omfattande fil för en linjär annons:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

Den linjära annonsen beskrivs i det <**linjära**>-elementet. Den anger varaktigheten för annonsen, spårnings händelser, klickar du på spåra och ett antal **MediaFile** -element. Spårnings händelser anges i <**TrackingEvents**>-elementet och tillåter att en AD-server spårar olika händelser som inträffar vid visning av AD. I det här fallet spåras händelserna Start, mitt, Slutför och expandera. Händelsen start inträffar när annonsen visas. Mitt punkt-händelsen inträffar när minst 50% av annonsens tids linje har visats. Den fullständiga händelsen inträffar när AD har körts till slutet. Händelsen Expand inträffar när användaren expanderar Videos pelaren till hel skärms läge. Clickthroughs anges med ett <**genomklickning**>-element i ett <**VideoClicks**>-element och anger en URI till en resurs som ska visas när användaren klickar på AD. ClickTracking anges i ett <**ClickTracking**>-element, även inom <**VideoClicks** ->-elementet och anger en spårnings resurs som Player ska begära när användaren klickar på AD. <**MediaFile**>-element anger information om en särskild kodning för en AD. Om det finns mer än ett <**MediaFile**>-element kan Videos pelaren välja den bästa kodningen för plattformen.

Linjära annonser kan visas i en angiven ordning. Det gör du genom att lägga till ytterligare `<Ad>` element i den stora filen och ange ordningen med attributet Sequence. Följande exempel illustrerar detta:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Inlineing-annonser anges också i ett- `<Creative>` element. I följande exempel visas ett- `<Creative>` element som beskriver en som inte är linjär AD.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

Det <**NonLinearAds**>-elementet kan innehålla ett eller flera <som inte är **linjära**> element, som var och en kan beskriva en som inte är linjär AD. <**Dislines**>-elementet anger resursen för den som inte är linjär AD. Resursen kan vara en <**StaticResource**>, ett <**IFrameResource**> eller en <**HTMLResource**>. \<**StaticResource**> beskriver en icke-HTML-resurs och definierar ett creativeType-attribut som anger hur resursen visas:

Bild/gif, bild/JPEG, bild/png – resursen visas i en HTML-<**img**>-tagg.

Application/x-Java Script – resursen visas i en HTML-<**skriptet**> tag.

Program/x-Shockwave-Flash – resursen visas i en Flash Player.

**IFrameResource** beskriver en HTML-resurs som kan visas i en iframe. **HTMLResource** beskriver en del av HTML-koden som kan infogas på en webb sida. **TrackingEvents** anger spårnings händelser och URI: n som ska begäras när händelsen inträffar. I det här exemplet spåras händelserna acceptInvitation och döljs. Mer information om **NonLinearAds** -elementet och dess underordnade finns i IAB.net/vast. Observera att elementet **TrackingEvents** finns i **NonLinearAds** -elementet i stället för elementet som inte är **linjärt** .

Companion-annonser definieras inom ett- `<CompanionAds>` element. `<CompanionAds>`Elementet kan innehålla ett eller flera `<Companion>` element. Varje `<Companion>` -element beskriver en Companion-annons och kan innehålla en `<StaticResource>` , `<IFrameResource>` eller `<HTMLResource>` som anges på samma sätt som i en som inte är linjär AD. En stor fil kan innehålla flera medföljande annonser och Player-programmet kan välja den lämpligaste annons som ska visas. Mer information om enorma finns i den [stora 3,0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Använda en digital video-VMAP (Multiple Active AD Playlist)
Med en VMAP-fil kan du ange när AD-avbrott inträffar, hur lång tid varje avbrott är, hur många annonser som kan visas i en rast och vilka typer av annonser som kan visas under en rast. Följande i ett exempel på en VMAP-fil som definierar en enda AD-rast:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

En VMAP-fil börjar med ett- `<VMAP>` element som innehåller ett eller flera `<AdBreak>` element, som var och en definierar en AD Break. Varje AD Break anger en brytnings typ, ett rast-ID och en tids förskjutning. Attributet breakType anger vilken typ av AD som kan spelas upp under brytningen: linjära, inte linjära eller Visa. Visa Ads-kartor till stora medföljande annonser. Mer än en AD-typ kan anges i en kommaavgränsad lista med kommatecken (inga blank steg). BreakID är en valfri identifierare för AD. TimeOffset anger när annonsen ska visas. Det kan anges på något av följande sätt:

1. Time – i TT: mm: SS eller hh: mm: SS. mmm-format där. mmm är millisekunder. Värdet för det här attributet anger tiden från början av video tids linjen till början av annons rasten.
2. Procent – i n% format där n är den procent andel av video tids linjen som ska spelas innan annonsen spelas upp
3. Start/end – anger att en annons ska visas före eller efter att videon har visats
4. Position – anger ordningen för AD-avbrott när tiden för annons avbrott är okänd, till exempel i direkt uppspelning. Ordningen för varje AD Break anges i #n format där n är ett heltal mellan 1 och mer. 1 betyder att annonsen ska spelas upp i den första affärs möjligheten, 2 anger att annonsen ska spelas upp vid den andra möjligheten och så vidare.

`<AdBreak>`Det kan finnas ett <**AdSource**>-element i-elementet. Elementet <**AdSource**> innehåller följande attribut:

1. ID – anger en identifierare för AD-källan
2. allowMultipleAds – ett booleskt värde som anger om flera annonser kan visas under AD Break
3. followRedirects – ett valfritt booleskt värde som anger om Videos pelaren ska svara på omdirigeringar inom ett AD-svar

Elementet <**AdSource**> ger spelaren ett infogat AD-svar eller en referens till ett AD-svar. Den kan innehålla något av följande element:

* `<VASTAdData>` anger att ett stort AD-svar är inbäddat i VMAP-filen
* `<AdTagURI>` en URI som refererar till ett AD-svar från ett annat system
* `<CustomAdData>` – en godtycklig sträng som representerar ett icke-omfattande svar

I det här exemplet anges ett infogat AD-svar med ett- `<VASTAdData>` element som innehåller ett omfattande AD-svar. Mer information om de andra elementen finns i [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

Elementet <**AdBreak**> kan också innehålla ett <**TrackingEvents**>-element. Med elementet <**TrackingEvents**> kan du spåra början eller slutet av en annons rast eller om ett fel inträffat under AD Break. <**TrackingEvents**>-elementet innehåller ett eller flera <**spårnings**> element, som var och en anger en spårnings händelse och en spårnings-URI. Möjliga spårnings händelser är:

1. breakStart – spårar början av en annons rast
2. breakEnd – spåra slut för ande av en annons rast
3. fel – spårar ett fel som inträffade under AD Break

I följande exempel visas en VMAP-fil som anger spårnings händelser

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Mer information om <- **TrackingEvents**>-elementet och dess underordnade finns i http://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Använda en mall för medie abstrakta sekvenser (MAST)
Med en delnings fil kan du ange utlösare som definierar när en annons visas. Följande är ett exempel på en samlings fil som innehåller utlösare för en för insamlad AD, en mellanliggande annons och en efter rulle-annons.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


En datamaste-fil börjar med ett **Datamast** -element som innehåller ett **trigger** -element. `<triggers>`Elementet innehåller ett eller flera **Utlös ande** element som definierar när en annons ska spelas upp.

**Utlösar** -elementet innehåller ett **startConditions** -element som anger när en annons ska börja spelas upp. **StartConditions** -elementet innehåller ett eller flera `<condition>` element. När var och `<condition>` en av dem utvärderas till sant initieras eller återkallas en utlösare beroende på om den finns `<condition>` i ett **StartConditions** -eller **endConditions** -element. När flera `<condition>` element finns, behandlas de som ett implicit eller, vilket villkor som utvärderas till sant, gör att utlösaren initieras. `<condition>` element kan kapslas. När underordnade `<condition>` element är förinställda, behandlas de som ett implicit och alla villkor måste utvärderas till sant för att utlösaren ska initieras. `<condition>`Elementet innehåller följande attribut som definierar villkoret:

1. **Type** – anger typ av villkor, händelse eller egenskap
2. **namn** – namnet på egenskapen eller händelsen som ska användas under utvärderingen
3. **Value** – värdet som en egenskap kommer att utvärderas mot
4. **operator** – åtgärden som ska användas under utvärderingen: EQ (Equal), NEQ (inte lika med), GTR (större), GEQ (större än eller lika med), lt (mindre än), LEQ (mindre än eller lika med), mod (modulo)

**endConditions** innehåller också `<condition>` element. När ett villkor utvärderas som sant återställs utlösaren. `<trigger>`Elementet innehåller också ett- `<sources>` element som innehåller ett eller flera `<source>` element. `<source>`Elementen definierar URI: n för AD-svaret och typen av AD-svar. I det här exemplet tilldelas en URI till ett omfattande svar.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Använda VPAID (video Player-Ad Interface definition)
VPAID är ett API för att aktivera körbara AD-enheter för att kommunicera med en Videos pelare. Detta möjliggör mycket interaktiva AD-upplevelser. Användaren kan interagera med AD och AD kan svara på åtgärder som vidtagits av visnings programmet. En annons kan till exempel Visa knappar som gör att användaren kan visa mer information eller en längre version av AD. Videos pelaren måste ha stöd för VPAID-API: et och den körbara filen AD måste implementera API: et. När en spelare begär en annons från en AD-server kan servern svara med ett omfattande svar som innehåller en VPAID AD.

En körbar fil för AD skapas i kod som måste köras i en körnings miljö, till exempel Adobe Flash™ eller Java Script som kan köras i en webbläsare. När en AD-server returnerar ett omfattande svar som innehåller en VPAID AD måste värdet för attributet apiFramework i `<MediaFile>` elementet vara "VPAID". Det här attributet anger att den inneslutna AD är en VPAID-körbar AD. Typattributet måste anges till den körbara filens MIME-typ, till exempel "Application/x-Shockwave-Flash" eller "Application/x-Java Script". Följande XML-kodfragment visar `<MediaFile>` elementet från ett omfattande svar som innehåller en VPAID-körbar AD.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

En körbar fil med AD kan initieras med hjälp av `<AdParameters>` elementet `<Linear>` i `<NonLinear>` elementen eller i ett omfattande svar. Mer information om `<AdParameters>` elementet finns i [enorma 3,0](https://www.iab.net/media/file/VASTv3.0.pdf). Mer information om VPAID-API: et finns i [VPAID 2,0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementera en Windows-eller Windows Phone 8-spelare med stöd för AD
Microsoft Media Platform: Player Framework för Windows 8 och Windows Phone 8 innehåller en samling exempel program som visar hur du implementerar ett Videos pelar program med hjälp av ramverket. Du kan ladda ned Player Framework och exemplen från [Player Framework för Windows 8 och Windows Phone 8](https://playerframework.codeplex.com).

När du öppnar lösningen Microsoft. PlayerFramework. XAML. Samples visas ett antal mappar i projektet. Mappen annonsering innehåller exempel koden som är relevant för att skapa en Videos pelare med stöd för AD. I mappen annonsering finns ett antal XAML/CS-filer som visar hur du infogar annonser på ett annat sätt. I följande lista beskrivs var och en:

* AdPodPage. XAML visar hur du visar en AD-pod.
* AdSchedulingPage. XAML visar hur du schemalägger annonser.
* FreeWheelPage. XAML visar hur du använder FreeWheel-plugin-programmet för att schemalägga annonser.
* MastPage. XAML visar hur du schemalägger annonser med en MAST fil.
* ProgrammaticAdPage. XAML visar hur du program mässigt schemalägger annonser i en video.
* ScheduleClipPage. XAML visar hur du schemalägger en AD utan en omfattande fil.
* VastLinearCompanionPage. XAML visar hur du infogar en linjär och Companion AD.
* VastNonLinearPage. XAML visar hur du infogar en icke-linjär AD.
* VmapPage. XAML visar hur du anger annonser med en VMAP-fil.

Vart och ett av dessa exempel använder klassen Media Player som definieras av Player Framework. De flesta exempel använder plugin-program som lägger till stöd för olika AD-svars format. ProgrammaticAdPage-exemplet interagerar med en Media Player-instans.

### <a name="adpodpage-sample"></a>AdPodPage-exempel
I det här exemplet används AdSchedulerPlugin för att definiera när en annons ska visas. I det här exemplet schemaläggs en mellanliggande annons som spelas upp efter fem sekunder. AD-Pod (en grupp annonser som ska visas i ordning) anges i en omfattande fil som returneras från en AD-server. URI: n till den stora filen anges i `<RemoteAdSource>` elementet.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Mer information om AdSchedulerPlugin finns i avsnittet om [annonsering i Player Framework på Windows 8 och Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
I det här exemplet används även AdSchedulerPlugin. Den schemalägger tre annonser, en för insamlad annons, en mellanrulled annons och en efter uppdatering av AD. URI: n till den enorma för varje AD anges i ett- `<RemoteAdSource>` element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
I det här exemplet används FreeWheelPlugin som anger ett käll-attribut som anger en URI som pekar på en SmartXML-fil som anger AD-innehåll samt information om AD-schemaläggning.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage
I det här exemplet används MastSchedulerPlugin som gör att du kan använda en delnings fil. Källattributet anger platsen för delnings filen.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Det här exemplet interagerar med Media Player. ProgrammaticAdPage. XAML-filen instansierar Media Player:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Filen ProgrammaticAdPage. XAML. cs skapar en AdHandlerPlugin, lägger till en TimelineMarker för att ange när en annons ska visas och lägger sedan till en hanterare för MarkerReached-händelsen som läser in en RemoteAdSource med en URI till en omfattande fil och sedan kör AD.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
I det här exemplet används AdSchedulerPlugin för att schemalägga en mellanrullen AD genom att ange en. WMV-fil som innehåller AD.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Det här exemplet illustrerar hur du använder AdSchedulerPlugin för att schemalägga en mellanliggande linjär annons med en Companion AD. `<RemoteAdSource>`Elementet anger platsen för den enorma filen.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
I det här exemplet används AdSchedulerPlugin för att schemalägga en linjär och en icke-linjär AD. Den stora fil platsen anges med- `<RemoteAdSource>` elementet.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
I det här exemplet används VmapSchedulerPlugin för att schemalägga annonser med en VMAP-fil. URI: n till VMAP-filen anges i `<VmapSchedulerPlugin>` elementets källattribut.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementera en iOS-videospelare med stöd för AD
Microsoft Media Platform: Player Framework för iOS innehåller en samling exempel program som visar hur du implementerar ett Videos pelar program med hjälp av ramverket. Du kan ladda ned Player Framework och exemplen från [Azure Media Player Framework](https://github.com/CloudMetal/azure-media-player-framework). Sidan GitHub innehåller en länk till en wiki som innehåller ytterligare information om Player-ramverket och en introduktion till Player-exemplet: [Azure Media Player wiki](https://github.com/CloudMetal/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Schemalägga annonser med VMAP
I följande exempel visas hur du schemalägger annonser med hjälp av en VMAP-fil.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Schemalägga annonser med stora
Följande exempel visar hur du schemalägger en sen bindning som är omfattande AD.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   Följande exempel visar hur du schemalägger en tidig bindning av stora AD.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

I följande exempel visas hur du infogar en annons med hjälp av grovt nedsänkta redigerings program (r)

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

I följande exempel visas hur du schemalägger en AD-pod.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

I följande exempel visas hur du schemalägger en icke-klisterlapp mellan AD. En icke-trög annons spelas bara upp en gång oavsett vilken sökning som görs i visnings programmet.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

I följande exempel visas hur du schemalägger en fästis mellan rulle. En fästis visas varje gång som den angivna punkten på video tids linjen har nåtts.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Följande exempel visar hur du schemalägger en efter uppdatering av AD.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

I följande exempel visas hur du schemalägger en för insummerad AD.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Följande exempel visar hur du schemalägger en mellanliggande överläggning av AD.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
