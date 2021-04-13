---
title: Snabbstart – Lägga till anrop till en iOS-app med Azure Communication Services
description: I den här snabbstarten lär du dig hur du använder Azure Communication Services Anropa SDK för iOS.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e1eed3f9449843e6c2dd8c77719402e709fdeb23
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327558"
---
I den här snabbstarten lär du dig att starta ett anrop med hjälp av Azure Communication Services Anropa SDK för iOS.

[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

> [!NOTE]
> Det här dokumentet använder version 1.0.0-beta.9 av anropande SDK.

## <a name="prerequisites"></a>Förutsättningar

För att kunna slutföra den här självstudien behöver du följande:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 
- En Mac som [kör Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), tillsammans med ett giltigt utvecklarcertifikat som är installerat i din nyckelring.
- En distribuerad Communication Services resurs. [Skapa en Communication Services resurs](../../create-communication-resource.md).
- En [användaråtkomsttoken](../../access-tokens.md) för din Azure-kommunikationstjänst.

## <a name="setting-up"></a>Inrätta

### <a name="creating-the-xcode-project"></a>Skapa Xcode-projektet

I Xcode skapar du ett nytt iOS-projekt och väljer mallen **App med enkel** vy. I den här [självstudien används SwiftUI-ramverket](https://developer.apple.com/xcode/swiftui/), så du bör ange **Språket** till **Swift** **och Användargränssnitt** **till SwiftUI.** Du kommer inte att skapa tester under den här snabbstarten. Avmarkera Inkludera tester om **du vill.**

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Skärmbild som visar fönstret Nytt projekt i Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installera paketet och beroenden med CocoaPods

1. Om du vill skapa en podfile för ditt program öppnar du terminalen och navigerar till projektmappen och kör ```pod init```
3. Lägg till följande kod i podfilen och spara (se till att "target" matchar namnet på ditt projekt):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.9'
     pod 'AzureCommunication', '~> 1.0.0-beta.9'
     pod 'AzureCore', '~> 1.0.0-beta.9'
   end
   ```

3. Kör `pod install`.
3. Öppna med `.xcworkspace` Xcode.

### <a name="request-access-to-the-microphone"></a>Begär åtkomst till mikrofonen

För att få åtkomst till enhetens mikrofon måste du uppdatera appens informationsegenskapslista med en `NSMicrophoneUsageDescription` . Du anger det associerade värdet till `string` en som ska ingå i dialogrutan som systemet använder för att begära åtkomst från användaren.

Högerklicka på posten `Info.plist` i projektträdet och välj **Öppna som**  >  **källkod.** Lägg till följande rader i avsnittet `<dict>` på den översta nivån och spara sedan filen.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Konfigurera appramverket

Öppna projektets **ContentView.swift-fil** och lägg till en deklaration överst i filen `import` för att importera `AzureCommunicationCalling library` . Importera dessutom , `AVFoundation` vi behöver detta för ljudbehörighetsbegäran i koden.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Ersätt implementeringen av `ContentView` structen med några enkla UI-kontroller som gör att en användare kan initiera och avsluta ett anrop. Vi kopplar affärslogik till dessa kontroller i den här snabbstarten.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Objektmodell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Calling SDK:

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient är den huvudsakliga startpunkten för den anropande SDK:n.|
| CallAgent | CallAgent används för att starta och hantera anrop. |
| CommunicationTokenCredential | CommunicationTokenCredential används som token-autentiseringsuppgifter för att instansiera CallAgent.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier används för att representera identiteten för användaren, vilket kan vara något av följande: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Autentisera klienten

Initiera en instans `CallAgent` med en token för användaråtkomst som gör att vi kan göra och ta emot anrop. Lägg till följande kod i `onAppear` återanropet **i ContentView.swift:**

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Du måste ersätta med `<USER ACCESS TOKEN>` en giltig användaråtkomsttoken för din resurs. Läs dokumentationen om [användaråtkomsttoken](../../access-tokens.md) om du inte redan har en tillgänglig token.

## <a name="start-a-call"></a>Starta ett anrop

Metoden `startCall` anges som den åtgärd som ska utföras när du trycker på *knappen* Starta anrop. Uppdatera implementeringen för att starta ett anrop med `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.startCall(participants: callees, options: StartCallOptions())
        }
    }
}
```

Du kan också använda egenskaperna i för att ange de första alternativen för anropet (d.v.s. det tillåter att anropet startas med mikrofonen `StartCallOptions` avstängd).

## <a name="end-a-call"></a>Avsluta ett anrop

Implementera metoden `endCall` för att avsluta det aktuella anropet när du trycker *på* knappen Avsluta anrop.

```swift
func endCall()
{    
    self.call!.hangUp(HangUpOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Kör koden

Du kan skapa och köra appen i iOS-simulatorn genom att välja **Produktkörning** eller med  >   hjälp av kortkommandot (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Slutligt utseende på snabbstartsappen":::

Du kan göra ett utgående VOIP-anrop genom att ange ett användar-ID i textfältet och trycka på **knappen Starta anrop.** När du anropar ansluts du till en ekorobot. Det här är perfekt för att komma igång `8:echo123` och verifiera att dina ljudenheter fungerar. 

> [!NOTE]
> Första gången du gör ett anrop uppmanas du av systemet att få åtkomst till mikrofonen. I ett produktionsprogram bör du använda API:et för att kontrollera behörighetsstatusen och smidigt uppdatera programmets `AVAudioSession` beteende när behörigheten inte beviljas. [](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)

## <a name="sample-code"></a>Exempelkod

Du kan ladda ned exempelappen från [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling)
