---
title: Integrera med en klientapp med hjälp av Speech SDK
titleSuffix: Azure Cognitive Services
description: så här gör du förfrågningar till ett publicerat program för anpassade kommandon från tal-SDK som körs i ett UWP-program.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.custom: devx-track-csharp
ms.openlocfilehash: fa3a6d16b79800043bdcd3f183dd86fa278dd1a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026035"
---
# <a name="integrate-with-a-client-application-using-speech-sdk"></a>Integrera med ett klient program med hjälp av tal-SDK

I den här artikeln får du lära dig hur du gör förfrågningar till ett publicerat program för anpassade kommandon från tal-SDK som körs i ett UWP-program. För att upprätta en anslutning till programmet för anpassade kommandon behöver du:

- Publicera ett program för anpassade kommandon och hämta ett program-ID (app-ID)
- Skapa en Universell Windows-plattform-klientsession (UWP) med hjälp av tal-SDK så att du kan prata med dina anpassade kommandon i programmet

## <a name="prerequisites"></a>Förutsättningar

Det krävs ett program med anpassade kommandon för att kunna slutföra den här artikeln. Om du inte har skapat ett program för anpassade kommandon kan du göra det efter snabb starterna:
> [!div class = "checklist"]
> * [Skapa ett program för anpassade kommandon](quickstart-custom-commands-application.md)

Du behöver också:
> [!div class = "checklist"]
> * [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) eller senare. Den här guiden baseras på Visual Studio 2019.
> * En Azure-prenumerations nyckel för tal tjänster. [Hämta ett kostnads fritt](overview.md#try-the-speech-service-for-free) eller skapa det på [Azure Portal](https://portal.azure.com)
> * [Gör det möjligt för din enhet att utveckla](/windows/uwp/get-started/enable-your-device-for-development)

## <a name="step-1-publish-custom-commands-application"></a>Steg 1: publicera anpassade kommandon-program

1. Öppna dina tidigare skapade program för anpassade kommandon
1. Gå till **Inställningar**, Välj **Luis-resurs**
1. Om **förutsägelse resursen** inte är tilldelad väljer du en fråga förutsägelse nyckel eller skapar en ny

    Frågans förutsägelse nyckel krävs alltid innan ett program publiceras. För mer information om LUIS-resurser, referera till [skapa Luis-resurs](../luis/luis-how-to-azure-subscription.md)

1. Gå tillbaka till redigerings kommandon, Välj **publicera**

   > [!div class="mx-imgBorder"]
   > ![Publicera programmet](media/custom-commands/setup-speech-sdk-publish-application.png)

1. Kopiera app-ID: t från publicerings meddelandet för senare användning
1. Kopiera tal resurs nyckeln för senare användning

## <a name="step-2-create-a-visual-studio-project"></a>Steg 2: skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-uwp-create-proj.md)]

## <a name="step-3-add-sample-code"></a>Steg 3: Lägg till exempel kod

I det här steget lägger vi till XAML-koden som definierar programmets användar gränssnitt och lägger till koden för C#-bakomliggande implementering.

### <a name="xaml-code"></a>XAML-kod

Skapa programmets användar gränssnitt genom att lägga till XAML-koden.

1. I **Solution Explorer** öppnar du `MainPage.xaml`

1. I designerns XAML-vy ersätter du hela innehållet med följande kodfragment:

   ```xml
   <Page
       x:Class="helloworld.MainPage"
       xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
       xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
       xmlns:local="using:helloworld"
       xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
       xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
       mc:Ignorable="d"
       Background="{ThemeResource ApplicationPageBackgroundThemeBrush}">

       <Grid>
           <StackPanel Orientation="Vertical" HorizontalAlignment="Center"
                       Margin="20,50,0,0" VerticalAlignment="Center" Width="800">
               <Button x:Name="EnableMicrophoneButton" Content="Enable Microphone"
                       Margin="0,10,10,0" Click="EnableMicrophone_ButtonClicked"
                       Height="35"/>
               <Button x:Name="ListenButton" Content="Talk"
                       Margin="0,10,10,0" Click="ListenButton_ButtonClicked"
                       Height="35"/>
               <StackPanel x:Name="StatusPanel" Orientation="Vertical"
                           RelativePanel.AlignBottomWithPanel="True"
                           RelativePanel.AlignRightWithPanel="True"
                           RelativePanel.AlignLeftWithPanel="True">
                   <TextBlock x:Name="StatusLabel" Margin="0,10,10,0"
                              TextWrapping="Wrap" Text="Status:" FontSize="20"/>
                   <Border x:Name="StatusBorder" Margin="0,0,0,0">
                       <ScrollViewer VerticalScrollMode="Auto"
                                     VerticalScrollBarVisibility="Auto" MaxHeight="200">
                           <!-- Use LiveSetting to enable screen readers to announce
                                the status update. -->
                           <TextBlock
                               x:Name="StatusBlock" FontWeight="Bold"
                               AutomationProperties.LiveSetting="Assertive"
                               MaxWidth="{Binding ElementName=Splitter, Path=ActualWidth}"
                               Margin="10,10,10,20" TextWrapping="Wrap"  />
                       </ScrollViewer>
                   </Border>
               </StackPanel>
           </StackPanel>
           <MediaElement x:Name="mediaElement"/>
       </Grid>
   </Page>
   ```

Vyn Design uppdateras för att Visa programmets användar gränssnitt.

### <a name="c-code-behind-source"></a>C#-källkod – bakom källa

Lägg till källa för bakomliggande kod så att programmet fungerar som förväntat. Källan för bakomliggande kod innehåller:

- Obligatoriska `using` instruktioner för `Speech` `Speech.Dialog` namn områdena och
- En enkel implementering för att säkerställa mikrofon åtkomst, kabelansluten till en knapp hanterare
- Grundläggande användar gränssnitt hjälper dig att presentera meddelanden och fel i programmet
- En landnings punkt för den initierings kod Sök väg som kommer att fyllas i senare
- En hjälpare som kan spela upp text till tal (utan strömnings stöd)
- En tom knapp hanterare för att starta lyssningen som kommer att fyllas i senare

Lägg till koden bakom källan enligt följande:

1. Öppna käll filen bakomliggande kod i **Solution Explorer** `MainPage.xaml.cs` (grupperad under `MainPage.xaml` )

1. Ersätt filens innehåll med följande kod: 

   ```csharp
   using Microsoft.CognitiveServices.Speech;
   using Microsoft.CognitiveServices.Speech.Audio;
   using Microsoft.CognitiveServices.Speech.Dialog;
   using System;
   using System.IO;
   using System.Text;
   using Windows.UI.Xaml;
   using Windows.UI.Xaml.Controls;
   using Windows.UI.Xaml.Media;

   namespace helloworld
   {
       public sealed partial class MainPage : Page
       {
           private DialogServiceConnector connector;

           private enum NotifyType
           {
               StatusMessage,
               ErrorMessage
           };

           public MainPage()
           {
               this.InitializeComponent();
           }

           private async void EnableMicrophone_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               bool isMicAvailable = true;
               try
               {
                   var mediaCapture = new Windows.Media.Capture.MediaCapture();
                   var settings =
                       new Windows.Media.Capture.MediaCaptureInitializationSettings();
                   settings.StreamingCaptureMode =
                       Windows.Media.Capture.StreamingCaptureMode.Audio;
                   await mediaCapture.InitializeAsync(settings);
               }
               catch (Exception)
               {
                   isMicAvailable = false;
               }
               if (!isMicAvailable)
               {
                   await Windows.System.Launcher.LaunchUriAsync(
                       new Uri("ms-settings:privacy-microphone"));
               }
               else
               {
                   NotifyUser("Microphone was enabled", NotifyType.StatusMessage);
               }
           }

           private void NotifyUser(
               string strMessage, NotifyType type = NotifyType.StatusMessage)
           {
               // If called from the UI thread, then update immediately.
               // Otherwise, schedule a task on the UI thread to perform the update.
               if (Dispatcher.HasThreadAccess)
               {
                   UpdateStatus(strMessage, type);
               }
               else
               {
                   var task = Dispatcher.RunAsync(
                       Windows.UI.Core.CoreDispatcherPriority.Normal,
                       () => UpdateStatus(strMessage, type));
               }
           }

           private void UpdateStatus(string strMessage, NotifyType type)
           {
               switch (type)
               {
                   case NotifyType.StatusMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Green);
                       break;
                   case NotifyType.ErrorMessage:
                       StatusBorder.Background = new SolidColorBrush(
                           Windows.UI.Colors.Red);
                       break;
               }
               StatusBlock.Text += string.IsNullOrEmpty(StatusBlock.Text)
                   ? strMessage : "\n" + strMessage;

               if (!string.IsNullOrEmpty(StatusBlock.Text))
               {
                   StatusBorder.Visibility = Visibility.Visible;
                   StatusPanel.Visibility = Visibility.Visible;
               }
               else
               {
                   StatusBorder.Visibility = Visibility.Collapsed;
                   StatusPanel.Visibility = Visibility.Collapsed;
               }
               // Raise an event if necessary to enable a screen reader
               // to announce the status update.
               var peer = Windows.UI.Xaml.Automation.Peers.FrameworkElementAutomationPeer.FromElement(StatusBlock);
               if (peer != null)
               {
                   peer.RaiseAutomationEvent(
                       Windows.UI.Xaml.Automation.Peers.AutomationEvents.LiveRegionChanged);
               }
           }

           // Waits for and accumulates all audio associated with a given
           // PullAudioOutputStream and then plays it to the MediaElement. Long spoken
           // audio will create extra latency and a streaming playback solution
           // (that plays audio while it continues to be received) should be used --
           // see the samples for examples of this.
           private void SynchronouslyPlayActivityAudio(
               PullAudioOutputStream activityAudio)
           {
               var playbackStreamWithHeader = new MemoryStream();
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("RIFF"), 0, 4); // ChunkID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // ChunkSize: max
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("WAVE"), 0, 4); // Format
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("fmt "), 0, 4); // Subchunk1ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 4); // Subchunk1Size: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // AudioFormat: PCM
               playbackStreamWithHeader.Write(BitConverter.GetBytes(1), 0, 2); // NumChannels: mono
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16000), 0, 4); // SampleRate: 16kHz
               playbackStreamWithHeader.Write(BitConverter.GetBytes(32000), 0, 4); // ByteRate
               playbackStreamWithHeader.Write(BitConverter.GetBytes(2), 0, 2); // BlockAlign
               playbackStreamWithHeader.Write(BitConverter.GetBytes(16), 0, 2); // BitsPerSample: 16-bit
               playbackStreamWithHeader.Write(Encoding.ASCII.GetBytes("data"), 0, 4); // Subchunk2ID
               playbackStreamWithHeader.Write(BitConverter.GetBytes(UInt32.MaxValue), 0, 4); // Subchunk2Size

               byte[] pullBuffer = new byte[2056];

               uint lastRead = 0;
               do
               {
                   lastRead = activityAudio.Read(pullBuffer);
                   playbackStreamWithHeader.Write(pullBuffer, 0, (int)lastRead);
               }
               while (lastRead == pullBuffer.Length);

               var task = Dispatcher.RunAsync(
                   Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
               {
                   mediaElement.SetSource(
                       playbackStreamWithHeader.AsRandomAccessStream(), "audio/wav");
                   mediaElement.Play();
               });
           }

           private void InitializeDialogServiceConnector()
           {
               // New code will go here
           }

           private async void ListenButton_ButtonClicked(
               object sender, RoutedEventArgs e)
           {
               // New code will go here
           }
       }
   }
   ```
    > [!NOTE]
    > Om du ser fel: "typen ' object ' har definierats i en sammansättning som inte är refererad"
    > 1. Rätt-klient din lösning.
    > 1. Välj **Hantera NuGet-paket för lösningen**, Välj **uppdateringar** 
    > 1. Om du ser **Microsoft. NetCore. UniversalWindowsPlatform** i uppdaterings listan uppdaterar du **Microsoft. NetCore. UniversalWindowsPlatform** till den senaste versionen

1. Lägg till följande kod i metod texten i `InitializeDialogServiceConnector`

   ```csharp
   // This code creates the `DialogServiceConnector` with your subscription information.
   // create a DialogServiceConfig by providing a Custom Commands application id and Cognitive Services subscription key
   // the RecoLanguage property is optional (default en-US); note that only en-US is supported in Preview
   const string speechCommandsApplicationId = "YourApplicationId"; // Your application id
   const string speechSubscriptionKey = "YourSpeechSubscriptionKey"; // Your subscription key
   const string region = "YourServiceRegion"; // The subscription service region. Note: only 'westus2' is currently supported

   var speechCommandsConfig = CustomCommandsConfig.FromSubscription(speechCommandsApplicationId, speechSubscriptionKey, region);
   speechCommandsConfig.SetProperty(PropertyId.SpeechServiceConnection_RecoLanguage, "en-us");
   connector = new DialogServiceConnector(speechCommandsConfig);
   ```

1. Ersätt strängarna `YourApplicationId` , `YourSpeechSubscriptionKey` och `YourServiceRegion` med dina egna värden för din app, tal prenumeration och [region](regions.md)

1. Lägg till följande kodfragment i slutet av metod texten i `InitializeDialogServiceConnector`

   ```csharp
   //
   // This code sets up handlers for events relied on by `DialogServiceConnector` to communicate its activities,
   // speech recognition results, and other information.
   //
   // ActivityReceived is the main way your client will receive messages, audio, and events
   connector.ActivityReceived += (sender, activityReceivedEventArgs) =>
   {
       NotifyUser(
           $"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

       if (activityReceivedEventArgs.HasAudio)
       {
           SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
       }
   };

   // Canceled will be signaled when a turn is aborted or experiences an error condition
   connector.Canceled += (sender, canceledEventArgs) =>
   {
       NotifyUser($"Canceled, reason={canceledEventArgs.Reason}");
       if (canceledEventArgs.Reason == CancellationReason.Error)
       {
           NotifyUser(
               $"Error: code={canceledEventArgs.ErrorCode}, details={canceledEventArgs.ErrorDetails}");
       }
   };

   // Recognizing (not 'Recognized') will provide the intermediate recognized text
   // while an audio stream is being processed
   connector.Recognizing += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Recognizing! in-progress text={recognitionEventArgs.Result.Text}");
   };

   // Recognized (not 'Recognizing') will provide the final recognized text
   // once audio capture is completed
   connector.Recognized += (sender, recognitionEventArgs) =>
   {
       NotifyUser($"Final speech-to-text result: '{recognitionEventArgs.Result.Text}'");
   };

   // SessionStarted will notify when audio begins flowing to the service for a turn
   connector.SessionStarted += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Now Listening! Session started, id={sessionEventArgs.SessionId}");
   };

   // SessionStopped will notify when a turn is complete and
   // it's safe to begin listening again
   connector.SessionStopped += (sender, sessionEventArgs) =>
   {
       NotifyUser($"Listening complete. Session ended, id={sessionEventArgs.SessionId}");
   };
   ```

1. Lägg till följande kodfragment i bröd texten i- `ListenButton_ButtonClicked` metoden i- `MainPage` klassen

   ```csharp
   // This code sets up `DialogServiceConnector` to listen, since you already established the configuration and
   // registered the event handlers.
   if (connector == null)
   {
       InitializeDialogServiceConnector();
       // Optional step to speed up first interaction: if not called,
       // connection happens automatically on first use
       var connectTask = connector.ConnectAsync();
   }

   try
   {
       // Start sending audio
       await connector.ListenOnceAsync();
   }
   catch (Exception ex)
   {
       NotifyUser($"Exception: {ex.ToString()}", NotifyType.ErrorMessage);
   }
   ```

1. Välj **Arkiv**  >  **Spara alla** på Meny raden för att spara ändringarna

## <a name="try-it-out"></a>Prova

1. Välj **bygge**  >  **build-lösning** i meny raden för att bygga programmet. Koden ska kompileras utan fel.

1. Starta programmet genom att välja **Felsök**  >  **Starta fel sökning** (eller tryck på **F5**). Fönstret **HelloWorld** visas.

   ![Exempel på UWP Virtual Assistant-program i C#-snabb start](media/sdk/qs-voice-assistant-uwp-helloworld-window.png)

1. Välj **aktivera mikrofon**. Välj **Ja** om åtkomst behörighets förfrågan öppnas.

   ![Åtkomst behörighets förfrågan för mikrofon](media/sdk/qs-csharp-uwp-10-access-prompt.png)

1. Välj **prata** och tala en engelsk fras eller mening i enhetens mikrofon. Ditt tal överförs till den direkta linjens tal kanal och skrivs till text, som visas i fönstret.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Anvisningar: skicka aktivitet till klient program (för hands version)](./how-to-custom-commands-send-activity-to-client.md)