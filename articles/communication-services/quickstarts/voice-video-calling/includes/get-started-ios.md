---
title: Snabb start – Lägg till samtal till en iOS-app med Azure Communication Services
description: I den här snabb starten får du lära dig hur du använder Azure Communication Services som anropar SDK för iOS.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 22c9d8f8bdf3e6195bf152fa0431ad5ce9bcdfeb
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073389"
---
I den här snabb starten får du lära dig hur du startar ett samtal med Azure Communication Services som anropar SDK för iOS.

[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

> [!NOTE]
> Det här dokumentet använder version 1.0.0 – beta. 8 av anrops-SDK: n.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande förutsättningar:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- En Mac som kör [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), tillsammans med ett giltigt certifikat för utvecklare som installerats i din nyckel Ring.
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- En [token för användar åtkomst](../../access-tokens.md) för Azure Communication Service.

## <a name="setting-up"></a>Konfigurera

### <a name="creating-the-xcode-project"></a>Skapa Xcode-projektet

I Xcode skapar du ett nytt iOS-projekt och väljer app-mallen för **enskild vy** . I den här självstudien används [SwiftUI-ramverket](https://developer.apple.com/xcode/swiftui/), så du bör ange **språket** till **Swift** och **användar gränssnittet** för **SwiftUI**. Du kommer inte att skapa tester under den här snabb starten. Avmarkera **ta med tester**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Skärm bild som visar det nya projekt fönstret i Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installera paketet och beroenden med CocoaPods

1. För att skapa en Podfile för programmet öppnar du terminalen och navigerar till projektmappen och kör ```pod init```
3. Lägg till följande kod i Podfile och spara (kontrol lera att "mål" matchar namnet på projektet):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

3. Kör `pod install`.
3. Öppna `.xcworkspace` med Xcode.

### <a name="request-access-to-the-microphone"></a>Begär åtkomst till mikrofonen

För att få åtkomst till enhetens mikrofon måste du uppdatera appens informations egenskaps lista med en `NSMicrophoneUsageDescription` . Du anger det associerade värdet till en `string` som ska ingå i dialog rutan som systemet använder för att begära åtkomst från användaren.

Högerklicka på `Info.plist` posten för projekt trädet och välj **öppna som**  >  **källkod**. Lägg till följande rader i avsnittet på den översta nivån `<dict>` och spara sedan filen.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Öppna projektets **ContentView. SWIFT** -fil och Lägg till en `import` deklaration överst i filen för att importera `AzureCommunicationCalling library` . Dessutom `AVFoundation` behöver vi detta för att få en begäran om ljud behörighet i koden.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Ersätt implementeringen av `ContentView` struct med vissa enkla UI-kontroller som gör det möjligt för en användare att initiera och avsluta ett samtal. Vi kommer att koppla affärs logik till dessa kontroller i den här snabb starten.

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

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar SDK:

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient är den huvudsakliga start punkten för den anropande SDK: n.|
| CallAgent | CallAgent används för att starta och hantera samtal. |
| CommunicationTokenCredential | CommunicationTokenCredential används som token-autentiseringsuppgifter för att instansiera CallAgent.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier används för att representera identiteten för användaren som kan vara något av följande: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Autentisera klienten

Initiera en `CallAgent` instans med en åtkomsttoken för användare som gör att vi kan ringa och ta emot samtal. Lägg till följande kod i `onAppear` återanropet i **ContentView. SWIFT**:

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

Du måste ersätta `<USER ACCESS TOKEN>` med en giltig åtkomsttoken för din resurs. Se dokumentationen för [användar åtkomst-token](../../access-tokens.md) om du inte redan har en tillgänglig token.

## <a name="start-a-call"></a>Starta ett anrop

`startCall`Metoden anges som den åtgärd som ska utföras när knappen *starta samtal* är knackad. Uppdatera implementeringen för att starta ett samtal med `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.call(participants: callees, options: StartCallOptions())
        }
    }
}
```

Du kan också använda egenskaperna i `StartCallOptions` för att ange inledande alternativ för anropet (d.v.s. det tillåter att anropet startar med mikrofonen avstängd).

## <a name="end-a-call"></a>Avsluta ett anrop

Implementera `endCall` metoden för att avsluta det aktuella anropet när knappen *Avsluta samtal* är knackad.

```swift
func endCall()
{    
    self.call!.hangup(HangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Kör koden

Du kan skapa och köra din app på iOS-simulatorn genom att välja **produkt**  >  **körning** eller genom att använda kortkommandot (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Det slutliga utseendet och känslan av snabb starts appen":::

Du kan göra ett utgående VOIP-anrop genom att ange ett användar-ID i fältet text och sedan trycka på knappen **starta samtal** . `8:echo123`Genom att anropa ansluter du med en eko robot är det bra för att komma igång och kontrol lera att ljud enheterna fungerar. 

> [!NOTE]
> Första gången du gör ett samtal uppmanas du att få åtkomst till mikrofonen. I ett produktions program bör du använda `AVAudioSession` API: et för att [kontrol lera behörighets status](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) och på ett smidigt sätt uppdatera programmets beteende när behörighet inte beviljas.

## <a name="sample-code"></a>Exempelkod

Du kan ladda ned exempel appen från [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling)
