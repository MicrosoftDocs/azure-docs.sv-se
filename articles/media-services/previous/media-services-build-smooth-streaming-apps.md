---
title: Själv studie kurs om Smooth Streaming Windows Store-appar | Microsoft Docs
description: Lär dig hur du använder Azure Media Services för att skapa ett C#-program i Windows Store med en XML-media element för uppspelning av smidigt strömmande innehåll.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 0fa5d8c5-3d5f-4886-ae55-fb6de4f5256d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 20c7068fa0cb56699fdbc6d75b279abadd583832
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103017128"
---
# <a name="how-to-build-a-smooth-streaming-windows-store-application"></a>Så här skapar du ett Smooth Streaming Windows Store-program

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]  

Med Smooth Streaming-klient-SDK för Windows 8 kan utvecklare skapa Windows Store-program som kan spela upp och leva Smooth Streaming innehåll. Förutom den grundläggande uppspelningen av Smooth Streaming innehåll innehåller SDK också omfattande funktioner som Microsoft PlayReady-skydd, kvalitets nivå begränsning, Live DVR, ljud Ströms växling, lyssnar efter status uppdateringar (till exempel kvalitets nivå ändringar) och så vidare. Mer information om vilka funktioner som stöds finns i [viktig](https://www.iis.net/learn/media/smooth-streaming/smooth-streaming-client-sdk-for-windows-8-release-notes)information. Mer information finns i [Player Framework för Windows 8](https://playerframework.codeplex.com/). 

Den här självstudien innehåller fyra lektioner:

1. Skapa ett Basic Smooth Streaming Store-program
2. Lägg till ett skjutreglage för att styra medie förloppet
3. Välj Smooth Streaming strömmar
4. Välj Smooth Streaming spår

## <a name="prerequisites"></a>Förutsättningar
> [!NOTE]
> Windows Store-projekt version 8,1 och tidigare stöds inte i Visual Studio 2017.  Mer information finns i [Visual Studio 2017 Platform Targeting and Compatibility](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs) (Visual Studio 2017 – målplattform och plattformskompatibilitet).

* Windows 8 32-bitars eller 64-bitars.
* Visual Studio version 2012 till 2015.
* [Microsoft Smooth Streaming-klient-SDK för Windows 8](https://visualstudiogallery.msdn.microsoft.com/04423d13-3b3e-4741-a01c-1ae29e84fea6?SRC=Home).

Den färdiga lösningen för varje lektion kan laddas ned från MSDN Developer Code Samples (kod Galleri): 

* [Lektion 1](https://code.msdn.microsoft.com/Smooth-Streaming-Client-0bb1471f) – en enkel Windows 8 Smooth Streaming Media Player, 
* [Lektion 2](https://code.msdn.microsoft.com/A-simple-Windows-8-Smooth-ee98f63a) – en enkel Windows 8-Smooth Streaming Media Player med en kontroll för skjutreglaget 
* [Lektion 3](https://code.msdn.microsoft.com/A-Windows-8-Smooth-883c3b44) – en Windows 8 Smooth Streaming Media Player med data Ströms val  
* [Lektion 4](https://code.msdn.microsoft.com/A-Windows-8-Smooth-aa9e4907)  – en Windows 8-Smooth Streaming Media Player med Spåra markering.

## <a name="lesson-1-create-a-basic-smooth-streaming-store-application"></a>Lektion 1: skapa ett grundläggande Smooth Streaming Store-program

I den här lektionen ska du skapa ett Windows Store-program med en media element-kontroll för att spela upp smidigt strömmande innehåll.  Det program som körs ser ut så här:

![Smooth Streaming Windows Store-programexempel][PlayerApplication]

Mer information om hur du utvecklar Windows Store-program finns i [utveckla fantastiska appar för Windows 8](https://developer.microsoft.com/en-us/windows/). Den här lektionen innehåller följande procedurer:

1. Skapa ett Windows Store-projekt
2. Utforma användar gränssnittet (XAML)
3. Ändra koden bakom filen
4. Kompilera och testa programmet

### <a name="to-create-a-windows-store-project"></a>Skapa ett Windows Store-projekt

1. Köra Visual Studio; version 2012 till 2015 stöds.
1. Från menyn **Arkiv** klickar du på **nytt** och sedan på **projekt**.
1. I dialog rutan nytt projekt skriver eller väljer du följande värden:

    | Name | Värde |
    | --- | --- |
    | Mallgrupp |Installerat/templates/Visual C#/Windows Store |
    | Mall |Tom app (XAML) |
    | Name |SSPlayer |
    | Location |C:\SSTutorials |
    | Namn på lösning |SSPlayer |
    | Skapa katalog för lösning |välja |

1. Klicka på **OK**.

### <a name="to-add-a-reference-to-the-smooth-streaming-client-sdk"></a>Lägga till en referens till Smooth Streaming-klient-SDK

1. Från Solution Explorer högerklickar du på **SSPlayer** och klickar sedan på **Lägg till referens**.
1. Ange eller välj följande värden:

    | Name | Värde |
    | --- | --- |
    | Referens grupp |Windows/tillägg |
    | Referens |Välj Microsoft Smooth Streaming client SDK för Windows 8 och Microsoft Visual C++ runtime-paketet |

1. Klicka på **OK**. 

När du har lagt till referenserna måste du välja mål plattform (x64 eller x86). det går inte att lägga till referenser för någon processor plattforms konfiguration.  I Solution Explorer visas gult varnings tecken för dessa tillagda referenser.

### <a name="to-design-the-player-user-interface"></a>Utforma användar gränssnittet för Player

1. Från Solution Explorer dubbelklickar du på **mainpage. XAML** för att öppna den i designvyn.
2. Leta upp märkordet **&lt; Grid &gt;** och **&lt; /Grid &gt;** och klistra in följande kod mellan de två taggarna:

   ```xml
         <Grid.RowDefinitions>

            <RowDefinition Height="20"/>    <!-- spacer -->
            <RowDefinition Height="50"/>    <!-- media controls -->
            <RowDefinition Height="100*"/>  <!-- media element -->
            <RowDefinition Height="80*"/>   <!-- media stream and track selection -->
            <RowDefinition Height="50"/>    <!-- status bar -->
         </Grid.RowDefinitions>

         <StackPanel Name="spMediaControl" Grid.Row="1" Orientation="Horizontal">
            <TextBlock x:Name="tbSource" Text="Source :  " FontSize="16" FontWeight="Bold" VerticalAlignment="Center" />
            <TextBox x:Name="txtMediaSource" Text="https://ecn.channel9.msdn.com/o9/content/smf/smoothcontent/elephantsdream/Elephants_Dream_1024-h264-st-aac.ism/manifest" FontSize="10" Width="700" Margin="0,4,0,10" />
            <Button x:Name="btnSetSource" Content="Set Source" Width="111" Height="43" Click="btnSetSource_Click"/>
            <Button x:Name="btnPlay" Content="Play" Width="111" Height="43" Click="btnPlay_Click"/>
            <Button x:Name="btnPause" Content="Pause"  Width="111" Height="43" Click="btnPause_Click"/>
            <Button x:Name="btnStop" Content="Stop"  Width="111" Height="43" Click="btnStop_Click"/>
            <CheckBox x:Name="chkAutoPlay" Content="Auto Play" Height="55" Width="Auto" IsChecked="{Binding AutoPlay, ElementName=mediaElement, Mode=TwoWay}"/>
            <CheckBox x:Name="chkMute" Content="Mute" Height="55" Width="67" IsChecked="{Binding IsMuted, ElementName=mediaElement, Mode=TwoWay}"/>
         </StackPanel>

         <StackPanel Name="spMediaElement" Grid.Row="2" Height="435" Width="1072"
                    HorizontalAlignment="Center" VerticalAlignment="Center">
            <MediaElement x:Name="mediaElement" Height="356" Width="924" MinHeight="225"
                          HorizontalAlignment="Center" VerticalAlignment="Center" 
                          AudioCategory="BackgroundCapableMedia" />
            <StackPanel Orientation="Horizontal">
                <Slider x:Name="sliderProgress" Width="924" Height="44"
                        HorizontalAlignment="Center" VerticalAlignment="Center"
                        PointerPressed="sliderProgress_PointerPressed"/>
                <Slider x:Name="sliderVolume" 
                        HorizontalAlignment="Right" VerticalAlignment="Center" Orientation="Vertical" 
                        Height="79" Width="148" Minimum="0" Maximum="1" StepFrequency="0.1" 
                        Value="{Binding Volume, ElementName=mediaElement, Mode=TwoWay}" 
                        ToolTipService.ToolTip="{Binding Value, RelativeSource={RelativeSource Mode=Self}}"/>
            </StackPanel>
         </StackPanel>

         <StackPanel Name="spStatus" Grid.Row="4" Orientation="Horizontal">
            <TextBlock x:Name="tbStatus" Text="Status :  " 
               FontSize="16" FontWeight="Bold" VerticalAlignment="Center" HorizontalAlignment="Center" />
            <TextBox x:Name="txtStatus" FontSize="10" Width="700" VerticalAlignment="Center"/>
         </StackPanel>
   ```
   Media element-kontrollen används för uppspelning av media. Skjutreglaget-kontrollen med namnet sliderProgress kommer att användas i nästa lektion för att styra medie förloppet.
3. Tryck på **CTRL + S** för att spara filen.

Media element-kontrollen stöder inte direkt Smooth Streaming innehåll. Om du vill aktivera stöd för Smooth Streaming måste du registrera Smooth Streaming byte Stream-hanteraren efter fil namns tillägg och MIME-typ.  För att registrera dig använder du metoden MediaExtensionManager. RegisterByteStreamHandler i namn rymden Windows. Media.

I den här XAML-filen är vissa händelse hanterare associerade med kontrollerna.  Du måste definiera dessa händelse hanterare.

### <a name="to-modify-the-code-behind-file"></a>Ändra koden bakom filen

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa kod**.
2. Lägg till följande using-instruktion högst upp i filen:

    ```csharp
        using Windows.Media;
    ```

3. I början av **mainpage** -klassen lägger du till följande data medlem:

    ```csharp
        private MediaExtensionManager extensions = new MediaExtensionManager();
    ```

4. Lägg till följande två rader i slutet av **mainpage** -konstruktorn:

    ```csharp
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "text/xml");
        extensions.RegisterByteStreamHandler("Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", ".ism", "application/vnd.ms-sstr+xml");
    ```

5. Klistra in följande kod i slutet av **mainpage** -klassen:
   ```csharp
         # region UI Button Click Events
         private void btnPlay_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Play();
         txtStatus.Text = "MediaElement is playing ...";
         }
         private void btnPause_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Pause();
         txtStatus.Text = "MediaElement is paused";
         }
         private void btnSetSource_Click(object sender, RoutedEventArgs e)
         {

         sliderProgress.Value = 0;
         mediaElement.Source = new Uri(txtMediaSource.Text);

         if (chkAutoPlay.IsChecked == true)
         {
             txtStatus.Text = "MediaElement is playing ...";
         }
         else
         {
             txtStatus.Text = "Click the Play button to play the media source.";
         }
         }
         private void btnStop_Click(object sender, RoutedEventArgs e)
         {

         mediaElement.Stop();
         txtStatus.Text = "MediaElement is stopped";
         }
         private void sliderProgress_PointerPressed(object sender, PointerRoutedEventArgs e)
         {

         txtStatus.Text = "Seek to position " + sliderProgress.Value;
         mediaElement.Position = new TimeSpan(0, 0, (int)(sliderProgress.Value));
         }
         # endregion
   ```
   Händelse hanteraren för sliderProgress_PointerPressed definieras här.  Det finns flera arbeten att göra för att få IT-arbete, som kommer att behandlas i nästa lektion i den här självstudien.
6. Tryck på **CTRL + S** för att spara filen.

Den färdiga koden bakom filen ska se ut så här:

![CodeView i Visual Studio för Smooth Streaming Windows Store-program][CodeViewPic]

### <a name="to-compile-and-test-the-application"></a>Kompilera och testa programmet

1. Klicka på **Configuration Manager** på menyn **build** .
2. Ändra **plattformen för aktiva lösningar** för att matcha din utvecklings plattform.
3. Tryck på **F6** för att kompilera projektet. 
4. Tryck på **F5** för att köra programmet.
5. Överst i programmet kan du antingen använda standard-Smooth Streaming-URL: en eller ange en annan. 
6. Klicka på **Ange källa**. Eftersom **automatisk uppspelning** är aktiverat som standard, ska mediet spelas upp automatiskt.  Du kan styra mediet med knapparna **spela upp**, **pausa** och **stoppa** .  Du kan styra medie volymen med hjälp av det lodräta skjutreglaget.  Det vågräta skjutreglaget för att styra medie förloppet är dock inte fullt implementerat ännu. 

Du har slutfört lesson1.  Under den här lektionen använder du en media element-kontroll för att spela upp Smooth Streaming innehåll.  I nästa lektion ska du lägga till ett skjutreglage för att kontrol lera förloppet för Smooth Streaming innehållet.

## <a name="lesson-2-add-a-slider-bar-to-control-the-media-progress"></a>Lektion 2: Lägg till ett Slider-fält för att styra medie förloppet

I lektion 1 skapade du ett Windows Store-program med en media element XAML-kontroll för uppspelning av Smooth Streaming medie innehåll.  Det innehåller några grundläggande Media funktioner som att starta, stoppa och pausa.  I den här lektionen ska du lägga till en kontroll för skjutreglaget i programmet.

I den här självstudien kommer vi att använda en timer för att uppdatera Skjutreglagets position baserat på den aktuella positionen för media element-kontrollen.  Start-och slut tiden för skjutreglaget måste också uppdateras i händelse av Live-innehåll.  Detta kan hanteras bättre i uppdaterings händelsen för den anpassningsbara källan.

Medie källor är objekt som genererar medie data.  Käll matcharen tar en URL eller byte-dataström och skapar en lämplig medie källa för innehållet.  Käll matcharen är standard sättet för programmen att skapa medie källor. 

Den här lektionen innehåller följande procedurer:

1. Registrera Smooth Streaming-hanteraren 
2. Lägg till händelse hanterare för adaptiv käll hanterings nivå
3. Lägg till händelse hanterare för adaptiva käll nivåer
4. Lägga till media element händelse hanterare
5. Lägg till kod relaterad kod i skjutreglaget
6. Kompilera och testa programmet

### <a name="to-register-the-smooth-streaming-byte-stream-handler-and-pass-the-propertyset"></a>Registrera Smooth Streaming byte-Stream-hanteraren och skicka propertyset

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa kod**.
2. Lägg till följande using-instruktion i början av filen:

   ```csharp
        using Microsoft.Media.AdaptiveStreaming;
   ```
3. I början av MainPage-klassen lägger du till följande data medlemmar:

   ```csharp
         private Windows.Foundation.Collections.PropertySet propertySet = new Windows.Foundation.Collections.PropertySet();             
         private IAdaptiveSourceManager adaptiveSourceManager;
   ```
4. I **mainpage** -konstruktorn lägger du till följande kod efter **this.Initialize-komponenterna (),** raden och registrerings kod raderna som skrevs i föregående lektion:

   ```csharp
        // Gets the default instance of AdaptiveSourceManager which manages Smooth 
        //Streaming media sources.
        adaptiveSourceManager = AdaptiveSourceManager.GetDefault();
        // Sets property key value to AdaptiveSourceManager default instance.
        // {A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}" must be hardcoded.
        propertySet["{A5CE1DE8-1D00-427B-ACEF-FB9A3C93DE2D}"] = adaptiveSourceManager;
   ```
5. I **mainpage** -konstruktorn ändrar du de två RegisterByteStreamHandler-metoderna för att lägga till de följande parametrarna:

   ```csharp
         // Registers Smooth Streaming byte-stream handler for ".ism" extension and, 
         // "text/xml" and "application/vnd.ms-ss" mime-types and pass the propertyset. 
         // http://*.ism/manifest URI resources will be resolved by Byte-stream handler.
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "text/xml", 
            propertySet );
         extensions.RegisterByteStreamHandler(

            "Microsoft.Media.AdaptiveStreaming.SmoothByteStreamHandler", 
            ".ism", 
            "application/vnd.ms-sstr+xml", 
         propertySet);
   ```
6. Tryck på **CTRL + S** för att spara filen.

### <a name="to-add-the-adaptive-source-manager-level-event-handler"></a>Så här lägger du till händelse hanteraren för den anpassningsbara käll hanterarens nivå

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa kod**.
2. I **mainpage** -klassen lägger du till följande data medlem:

   ```csharp
     private AdaptiveSource adaptiveSource = null;
   ```
3. Lägg till följande händelse hanterare i slutet av **mainpage** -klassen:

   ```csharp
         # region Adaptive Source Manager Level Events
         private void mediaElement_AdaptiveSourceOpened(AdaptiveSource sender, AdaptiveSourceOpenedEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
         }

         # endregion Adaptive Source Manager Level Events
   ```
4. I slutet av **mainpage** -konstruktorn lägger du till följande rad för att prenumerera på den anpassningsbara källans öppna händelse:

   ```csharp
         adaptiveSourceManager.AdaptiveSourceOpenedEvent += 
           new AdaptiveSourceOpenedEventHandler(mediaElement_AdaptiveSourceOpened);
   ```
5. Tryck på **CTRL + S** för att spara filen.

### <a name="to-add-adaptive-source-level-event-handlers"></a>Så här lägger du till händelse hanterare för adaptiva käll nivåer

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa kod**.
2. I **mainpage** -klassen lägger du till följande data medlem:

   ```csharp
     private AdaptiveSourceStatusUpdatedEventArgs adaptiveSourceStatusUpdate; 
     private Manifest manifestObject;
   ```
3. I slutet av **mainpage** -klassen lägger du till följande händelse hanterare:

   ```csharp
         # region Adaptive Source Level Events
         private void mediaElement_ManifestReady(AdaptiveSource sender, ManifestReadyEventArgs args)
         {

            adaptiveSource = args.AdaptiveSource;
            manifestObject = args.AdaptiveSource.Manifest;
         }

         private void mediaElement_AdaptiveSourceStatusUpdated(AdaptiveSource sender, AdaptiveSourceStatusUpdatedEventArgs args)
         {

            adaptiveSourceStatusUpdate = args;
         }

         private void mediaElement_AdaptiveSourceFailed(AdaptiveSource sender, AdaptiveSourceFailedEventArgs args)
         {

            txtStatus.Text = "Error: " + args.HttpResponse;
         }

         # endregion Adaptive Source Level Events
   ```
4. I slutet av **media element AdaptiveSourceOpened** -metoden lägger du till följande kod för att prenumerera på händelserna:

   ```csharp
         adaptiveSource.ManifestReadyEvent +=

                    mediaElement_ManifestReady;
         adaptiveSource.AdaptiveSourceStatusUpdatedEvent += 

            mediaElement_AdaptiveSourceStatusUpdated;
         adaptiveSource.AdaptiveSourceFailedEvent += 

            mediaElement_AdaptiveSourceFailed;
   ```
5. Tryck på **CTRL + S** för att spara filen.

Samma händelser är tillgängliga på den anpassade käll hanterings nivån och kan användas för att hantera funktioner som är gemensamma för alla medie element i appen. Varje AdaptiveSource innehåller sina egna händelser och alla AdaptiveSource-händelser kommer att överlappas under AdaptiveSourceManager.

### <a name="to-add-media-element-event-handlers"></a>Lägga till händelse hanterare för medie element

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa kod**.
2. I slutet av **mainpage** -klassen lägger du till följande händelse hanterare:

   ```csharp
         # region Media Element Event Handlers
         private void MediaOpened(object sender, RoutedEventArgs e)
         {

            txtStatus.Text = "MediaElement opened";
         }

         private void MediaFailed(object sender, ExceptionRoutedEventArgs e)
         {

            txtStatus.Text= "MediaElement failed: " + e.ErrorMessage;
         }

         private void MediaEnded(object sender, RoutedEventArgs e)
         {

            txtStatus.Text ="MediaElement ended.";
         }

         # endregion Media Element Event Handlers
   ```
3. I slutet av **mainpage** -konstruktorn lägger du till följande kod till nedsänkt till händelserna:

   ```csharp
         mediaElement.MediaOpened += MediaOpened;
         mediaElement.MediaEnded += MediaEnded;
         mediaElement.MediaFailed += MediaFailed;
   ```
4. Tryck på **CTRL + S** för att spara filen.

### <a name="to-add-slider-bar-related-code"></a>Så här lägger du till kod för skjutreglaget

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa kod**.
2. Lägg till följande using-instruktion i början av filen:

   ```csharp
        using Windows.UI.Core;
   ```
3. I **mainpage** -klassen lägger du till följande data medlemmar:

   ```csharp
         public static CoreDispatcher _dispatcher;
         private DispatcherTimer sliderPositionUpdateDispatcher;
   ```
4. Lägg till följande kod i slutet av **mainpage** -konstruktorn:

   ```csharp
         _dispatcher = Window.Current.Dispatcher;
         PointerEventHandler pointerpressedhandler = new PointerEventHandler(sliderProgress_PointerPressed);
         sliderProgress.AddHandler(Control.PointerPressedEvent, pointerpressedhandler, true);    
   ```
5. Lägg till följande kod i slutet av **mainpage** -klassen:

   ```csharp
         # region sliderMediaPlayer
         private double SliderFrequency(TimeSpan timevalue)
         {

            long absvalue = 0;
            double stepfrequency = -1;

            if (manifestObject != null)
            {
                absvalue = manifestObject.Duration - (long)manifestObject.StartTime;
            }
            else
            {
                absvalue = mediaElement.NaturalDuration.TimeSpan.Ticks;
            }

            TimeSpan totalDVRDuration = new TimeSpan(absvalue);

            if (totalDVRDuration.TotalMinutes >= 10 && totalDVRDuration.TotalMinutes < 30)
            {
               stepfrequency = 10;
            }
            else if (totalDVRDuration.TotalMinutes >= 30 
                     && totalDVRDuration.TotalMinutes < 60)
            {
                stepfrequency = 30;
            }
            else if (totalDVRDuration.TotalHours >= 1)
            {
                stepfrequency = 60;
            }

            return stepfrequency;
         }

         void updateSliderPositionoNTicks(object sender, object e)
         {

            sliderProgress.Value = mediaElement.Position.TotalSeconds;
         }

         public void setupTimer()
         {

            sliderPositionUpdateDispatcher = new DispatcherTimer();
            sliderPositionUpdateDispatcher.Interval = new TimeSpan(0, 0, 0, 0, 300);
            startTimer();
         }

         public void startTimer()
         {

            sliderPositionUpdateDispatcher.Tick += updateSliderPositionoNTicks;
            sliderPositionUpdateDispatcher.Start();
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderStartTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.StartTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Minimum = absvalue;
            });
         }

         // Slider start and end time must be updated in case of live content
         public async void setSliderEndTime(long startTime)
         {

            await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
            {
                TimeSpan timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime);
                double absvalue = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero);
                sliderProgress.Maximum = absvalue;
            });
         }

         # endregion sliderMediaPlayer
   ```

   > [!NOTE]
   > CoreDispatcher används för att göra ändringar i UI-tråden från en icke-UI-tråd. När det gäller Flask halsar i dispatcher-tråden kan utvecklaren välja att använda dispatcher som tillhandahålls av UI-elementet som de vill uppdatera.  Exempel:

   ```csharp
         await sliderProgress.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () => { TimeSpan 

         timespan = new TimeSpan(adaptiveSourceStatusUpdate.EndTime); 
         double absvalue  = (int)Math.Round(timespan.TotalSeconds, MidpointRounding.AwayFromZero); 

         sliderProgress.Maximum = absvalue; }); 
   ```
6. Lägg till följande kod i slutet av **mediaElement_AdaptiveSourceStatusUpdated** -metoden:

   ```csharp
         setSliderStartTime(args.StartTime);
         setSliderEndTime(args.EndTime);
   ```
7. Lägg till följande kod i slutet av **MediaOpened** -metoden:

   ```csharp
         sliderProgress.StepFrequency = SliderFrequency(mediaElement.NaturalDuration.TimeSpan);
         sliderProgress.Width = mediaElement.Width;
         setupTimer();
   ```
8. Tryck på **CTRL + S** för att spara filen.

### <a name="to-compile-and-test-the-application"></a>Kompilera och testa programmet

1. Tryck på **F6** för att kompilera projektet. 
2. Tryck på **F5** för att köra programmet.
3. Överst i programmet kan du antingen använda standard-Smooth Streaming-URL: en eller ange en annan. 
4. Klicka på **Ange källa**. 
5. Testa skjutreglaget.

Du har slutfört lektion 2.  I den här lektionen lade du till ett skjutreglage i programmet. 

## <a name="lesson-3-select-smooth-streaming-streams"></a>Lektion 3: Välj Smooth Streaming strömmar
Smooth Streaming kan strömma innehåll med flera språk ljud spår som kan väljas av visnings programmen.  I den här lektionen får du göra det möjligt för användarna att välja strömmar. Den här lektionen innehåller följande procedurer:

1. Ändra XAML-filen
2. Ändra koden bakom filen
3. Kompilera och testa programmet

### <a name="to-modify-the-xaml-file"></a>Ändra XAML-filen

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa designer**.
2. Leta upp &lt; Grid. RowDefinitions &gt; och ändra RowDefinitions så att de ser ut så här:

   ```xml
         <Grid.RowDefinitions>            
            <RowDefinition Height="20"/>
            <RowDefinition Height="50"/>
            <RowDefinition Height="100"/>
            <RowDefinition Height="80"/>
            <RowDefinition Height="50"/>
         </Grid.RowDefinitions>
   ```
3. I &lt; rutnätet &gt; &lt; /Grid &gt; -taggar lägger du till följande kod för att definiera en listruta, så att användarna kan se listan över tillgängliga strömmar och välja strömmar:

   ```xml
         <Grid Name="gridStreamAndBitrateSelection" Grid.Row="3">
            <Grid.RowDefinitions>
                <RowDefinition Height="300"/>
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition Width="250*"/>
                <ColumnDefinition Width="250*"/>
            </Grid.ColumnDefinitions>
            <StackPanel Name="spStreamSelection" Grid.Row="1" Grid.Column="0">
                <StackPanel Orientation="Horizontal">
                    <TextBlock Name="tbAvailableStreams" Text="Available Streams:" FontSize="16" VerticalAlignment="Center"></TextBlock>
                    <Button Name="btnChangeStreams" Content="Submit" Click="btnChangeStream_Click"/>
                </StackPanel>
                <ListBox x:Name="lbAvailableStreams" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                    ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
                    <ListBox.ItemTemplate>
                        <DataTemplate>
                            <CheckBox Content="{Binding Name}" IsChecked="{Binding isChecked, Mode=TwoWay}" />
                        </DataTemplate>
                    </ListBox.ItemTemplate>
                </ListBox>
            </StackPanel>
         </Grid>
   ```
4. Tryck på **CTRL + S** för att spara ändringarna.

### <a name="to-modify-the-code-behind-file"></a>Ändra koden bakom filen

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa kod**.
2. I namn området SSPlayer lägger du till en ny klass:

   ```csharp
        #region class Stream
   
        public class Stream
        {
            private IManifestStream stream;
            public bool isCheckedValue;
            public string name;
   
            public string Name
            {
                get { return name; }
                set { name = value; }
            }
   
            public IManifestStream ManifestStream
            {
                get { return stream; }
                set { stream = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    // mMke the video stream always checked.
                    if (stream.Type == MediaStreamType.Video)
                    {
                        isCheckedValue = true;
                    }
                    else
                    {
                        isCheckedValue = value;
                    }
                }
            }
   
            public Stream(IManifestStream streamIn)
            {
                stream = streamIn;
                name = stream.Name;
            }
        }
        #endregion class Stream
   ```
3. I början av MainPage-klassen lägger du till följande variabel definitioner:

   ```csharp
         private List<Stream> availableStreams;
         private List<Stream> availableAudioStreams;
         private List<Stream> availableTextStreams;
         private List<Stream> availableVideoStreams;
   ```
4. I MainPage-klassen lägger du till följande region:
   ```csharp
        #region stream selection
        ///<summary>
        ///Functionality to select streams from IManifestStream available streams
        /// </summary>
   
        // This function is called from the mediaElement_ManifestReady event handler 
        // to retrieve the streams and populate them to the local data members.
        public void getStreams(Manifest manifestObject)
        {
            availableStreams = new List<Stream>();
            availableVideoStreams = new List<Stream>();
            availableAudioStreams = new List<Stream>();
            availableTextStreams = new List<Stream>();
   
            try
            {
                for (int i = 0; i<manifestObject.AvailableStreams.Count; i++)
                {
                    Stream newStream = new Stream(manifestObject.AvailableStreams[i]);
                    newStream.isChecked = false;
   
                    //populate the stream lists based on the types
                    availableStreams.Add(newStream);
   
                    switch (newStream.ManifestStream.Type)
                    {
                        case MediaStreamType.Video:
                            availableVideoStreams.Add(newStream);
                            break;
                        case MediaStreamType.Audio:
                            availableAudioStreams.Add(newStream);
                            break;
                        case MediaStreamType.Text:
                            availableTextStreams.Add(newStream);
                            break;
                    }
   
                    // Select the default selected streams from the manifest.
                    for (int j = 0; j<manifestObject.SelectedStreams.Count; j++)
                    {
                        string selectedStreamName = manifestObject.SelectedStreams[j].Name;
                        if (selectedStreamName.Equals(newStream.Name))
                        {
                            newStream.isChecked = true;
                            break;
                        }
                    }
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function set the list box ItemSource
        private async void refreshAvailableStreamsListBoxItemSource()
        {
            try
            {
                //update the stream check box list on the UI
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableStreams.ItemsSource = availableStreams; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
   
        // This function creates a selected streams list
        private void createSelectedStreamsList(List<IManifestStream> selectedStreams)
        {
            bool isOneVideoSelected = false;
            bool isOneAudioSelected = false;
   
            // Only one video stream can be selected
            for (int j = 0; j<availableVideoStreams.Count; j++)
            {
                if (availableVideoStreams[j].isChecked && (!isOneVideoSelected))
                {
                    selectedStreams.Add(availableVideoStreams[j].ManifestStream);
                    isOneVideoSelected = true;
                }
            }
   
            // Select the first video stream from the list if no video stream is selected
            if (!isOneVideoSelected)
            {
                availableVideoStreams[0].isChecked = true;
                selectedStreams.Add(availableVideoStreams[0].ManifestStream);
            }
   
            // Only one audio stream can be selected
            for (int j = 0; j<availableAudioStreams.Count; j++)
            {
                if (availableAudioStreams[j].isChecked && (!isOneAudioSelected))
                {
                    selectedStreams.Add(availableAudioStreams[j].ManifestStream);
                    isOneAudioSelected = true;
                    txtStatus.Text = "The audio stream is changed to " + availableAudioStreams[j].ManifestStream.Name;
                }
            }
   
            // Select the first audio stream from the list if no audio steam is selected.
            if (!isOneAudioSelected)
            {
                availableAudioStreams[0].isChecked = true;
                selectedStreams.Add(availableAudioStreams[0].ManifestStream);
            }
   
            // Multiple text streams are supported.
            for (int j = 0; j < availableTextStreams.Count; j++)
            {
                if (availableTextStreams[j].isChecked)
                {
                    selectedStreams.Add(availableTextStreams[j].ManifestStream);
                }
            }
        }
   
        // Change streams on a smooth streaming presentation with multiple video streams.
        private async void changeStreams(List<IManifestStream> selectStreams)
        {
            try
            {
                IReadOnlyList<IStreamChangedResult> returnArgs =
                    await manifestObject.SelectStreamsAsync(selectStreams);
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }
        }
        #endregion stream selection
   ```
5. Leta upp metoden mediaElement_ManifestReady, Lägg till följande kod i slutet av funktionen:
   ```csharp
        getStreams(manifestObject);
        refreshAvailableStreamsListBoxItemSource();
   ```
    När media element-manifestet är klart hämtar koden en lista över tillgängliga strömmar och fyller i list rutan med användar gränssnittet.
6. I MainPage-klassen, leta upp UI-knapparna Klicka på händelse region och Lägg sedan till följande funktions definition:
   ```csharp
        private void btnChangeStream_Click(object sender, RoutedEventArgs e)
        {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();
   
            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);
   
            // Change streams on the presentation
            changeStreams(selectedStreams);
        }
   ```

### <a name="to-compile-and-test-the-application"></a>Kompilera och testa programmet

1. Tryck på **F6** för att kompilera projektet. 
2. Tryck på **F5** för att köra programmet.
3. Överst i programmet kan du antingen använda standard-Smooth Streaming-URL: en eller ange en annan. 
4. Klicka på **Ange källa**. 
5. Standard språket är audio_eng. Försök att växla mellan audio_eng och audio_es. Varje gång du väljer en ny ström måste du klicka på knappen Skicka.

Du har slutfört Lektion 3.  I den här lektionen lägger du till funktioner för att välja strömmar.

## <a name="lesson-4-select-smooth-streaming-tracks"></a>Lektion 4: Välj Smooth Streaming spår

En Smooth Streaming presentation kan innehålla flera videofiler som är kodade med olika kvalitets nivåer (bit hastigheter) och lösningar. I den här lektionen får du göra det möjligt för användarna att välja spår. Den här lektionen innehåller följande procedurer:

1. Ändra XAML-filen
2. Ändra koden bakom filen
3. Kompilera och testa programmet

### <a name="to-modify-the-xaml-file"></a>Ändra XAML-filen

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa designer**.
2. Leta upp &lt; Rutnäts &gt; tag gen med namnet **gridStreamAndBitrateSelection** och Lägg till följande kod i slutet av taggen:
   ```xml
         <StackPanel Name="spBitRateSelection" Grid.Row="1" Grid.Column="1">
         <StackPanel Orientation="Horizontal">
             <TextBlock Name="tbBitRate" Text="Available Bitrates:" FontSize="16" VerticalAlignment="Center"/>
             <Button Name="btnChangeTracks" Content="Submit" Click="btnChangeTrack_Click" />
         </StackPanel>
         <ListBox x:Name="lbAvailableVideoTracks" Height="200" Width="200" Background="Transparent" HorizontalAlignment="Left" 
                  ScrollViewer.VerticalScrollMode="Enabled" ScrollViewer.VerticalScrollBarVisibility="Visible">
             <ListBox.ItemTemplate>
                 <DataTemplate>
                     <CheckBox Content="{Binding Bitrate}" IsChecked="{Binding isChecked, Mode=TwoWay}"/>
                 </DataTemplate>
             </ListBox.ItemTemplate>
         </ListBox>
         </StackPanel>
   ```
3. Tryck på **CTRL + S** för att spara ändringarna

### <a name="to-modify-the-code-behind-file"></a>Ändra koden bakom filen

1. Från Solution Explorer högerklickar du på **mainpage. XAML** och klickar sedan på **Visa kod**.
2. I namn området SSPlayer lägger du till en ny klass:
   ```csharp
        #region class Track
        public class Track
        {
            private IManifestTrack trackInfo;
            public string _bitrate;
            public bool isCheckedValue;
   
            public IManifestTrack TrackInfo
            {
                get { return trackInfo; }
                set { trackInfo = value; }
            }
   
            public string Bitrate
            {
                get { return _bitrate; }
                set { _bitrate = value; }
            }
   
            public bool isChecked
            {
                get { return isCheckedValue; }
                set
                {
                    isCheckedValue = value;
                }
            }
   
            public Track(IManifestTrack trackInfoIn)
            {
                trackInfo = trackInfoIn;
                _bitrate = trackInfoIn.Bitrate.ToString();
            }
            //public Track() { }
        }
        #endregion class Track
   ```
3. I början av MainPage-klassen lägger du till följande variabel definitioner:
   ```csharp
        private List<Track> availableTracks;
   ```
4. I MainPage-klassen lägger du till följande region:
   ```csharp
        #region track selection
        /// <summary>
        /// Functionality to select video streams
        /// </summary>
   
        /// This Function gets the tracks for the selected video stream
        public void getTracks(Manifest manifestObject)
        {
            availableTracks = new List<Track>();
   
            IManifestStream videoStream = getVideoStream();
            IReadOnlyList<IManifestTrack> availableTracksLocal = videoStream.AvailableTracks;
            IReadOnlyList<IManifestTrack> selectedTracksLocal = videoStream.SelectedTracks;
   
            try
            {
                for (int i = 0; i < availableTracksLocal.Count; i++)
                {
                    Track thisTrack = new Track(availableTracksLocal[i]);
                    thisTrack.isChecked = true;
   
                    for (int j = 0; j < selectedTracksLocal.Count; j++)
                    {
                        string selectedTrackName = selectedTracksLocal[j].Bitrate.ToString();
                        if (selectedTrackName.Equals(thisTrack.Bitrate))
                        {
                            thisTrack.isChecked = true;
                            break;
                        }
                    }
                    availableTracks.Add(thisTrack);
                }
            }
            catch (Exception e)
            {
                txtStatus.Text = e.Message;
            }
        }
   
        // This function gets the video stream that is playing
        private IManifestStream getVideoStream()
        {
            IManifestStream videoStream = null;
            for (int i = 0; i < manifestObject.SelectedStreams.Count; i++)
            {
                if (manifestObject.SelectedStreams[i].Type == MediaStreamType.Video)
                {
                    videoStream = manifestObject.SelectedStreams[i];
                    break;
                }
            }
            return videoStream;
        }
   
        // This function set the UI list box control ItemSource
        private async void refreshAvailableTracksListBoxItemSource()
        {
            try
            {
                // Update the track check box list on the UI 
                await _dispatcher.RunAsync(CoreDispatcherPriority.Normal, ()
                    => { lbAvailableVideoTracks.ItemsSource = availableTracks; });
            }
            catch (Exception e)
            {
                txtStatus.Text = "Error: " + e.Message;
            }        
        }
   
        // This function creates a list of the selected tracks.
        private void createSelectedTracksList(List<IManifestTrack> selectedTracks)
        {
            // Create a list of selected tracks
            for (int j = 0; j < availableTracks.Count; j++)
            {
                if (availableTracks[j].isCheckedValue == true)
                {
                    selectedTracks.Add(availableTracks[j].TrackInfo);
                }
            }
        }
   
        // This function selects the tracks based on user selection 
        private void changeTracks(List<IManifestTrack> selectedTracks)
        {
            IManifestStream videoStream = getVideoStream();
            try
            {
                videoStream.SelectTracks(selectedTracks);
            }
            catch (Exception ex)
            {
                txtStatus.Text = ex.Message;
            }
        }
        #endregion track selection
   ```
5. Leta upp metoden mediaElement_ManifestReady, Lägg till följande kod i slutet av funktionen:
   ```csharp
         getTracks(manifestObject);
         refreshAvailableTracksListBoxItemSource();
   ```
6. I MainPage-klassen, leta upp UI-knapparna Klicka på händelse region och Lägg sedan till följande funktions definition:
   ```csharp
         private void btnChangeStream_Click(object sender, RoutedEventArgs e)
         {
            List<IManifestStream> selectedStreams = new List<IManifestStream>();

            // Create a list of the selected streams
            createSelectedStreamsList(selectedStreams);

            // Change streams on the presentation
            changeStreams(selectedStreams);
         }
   ```
   
### <a name="to-compile-and-test-the-application"></a>Kompilera och testa programmet

1. Tryck på **F6** för att kompilera projektet. 
2. Tryck på **F5** för att köra programmet.
3. Överst i programmet kan du antingen använda standard-Smooth Streaming-URL: en eller ange en annan. 
4. Klicka på **Ange källa**. 
5. Som standard är alla spår i video strömmen markerade. Om du vill experimentera med bit hastighets ändringarna kan du välja den lägsta tillgängliga bit hastigheten och sedan välja den högsta tillgängliga bit hastigheten. Du måste klicka på skicka efter varje ändring.  Du kan se video kvalitets ändringarna.

Du har slutfört lektion 4.  I den här lektionen lägger du till funktioner för att välja spår.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="other-resources"></a>Andra resurser:
* [Så här skapar du ett Smooth Streaming Windows 8 JavaScript-program med avancerade funktioner](https://blogs.iis.net/cenkd/archive/2012/08/10/how-to-build-a-smooth-streaming-windows-8-javascript-application-with-advanced-features.aspx)
* [Teknisk översikt över Smooth Streaming](https://www.iis.net/learn/media/on-demand-smooth-streaming/smooth-streaming-technical-overview)

[PlayerApplication]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-1.png
[CodeViewPic]: ./media/media-services-build-smooth-streaming-apps/SSClientWin8-2.png
