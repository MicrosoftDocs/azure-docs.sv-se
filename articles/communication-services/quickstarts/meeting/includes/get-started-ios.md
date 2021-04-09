---
title: Snabb start – Lägg till ett team som är möte i en iOS-app med Azure Communication Services
description: I den här snabb starten lär du dig att använda Azure Communication Services Teams Inbäddnings bibliotek för iOS.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104804072"
---
I den här snabb starten får du lära dig hur du ansluter till ett team möte med hjälp av Azure Communication Services Teams Inbäddnings bibliotek för iOS.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten behöver du följande förutsättningar:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- En Mac som kör [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), tillsammans med ett giltigt certifikat för utvecklare som installerats i din nyckel Ring.
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- En [token för användar åtkomst](../../access-tokens.md) för Azure Communication Service.
- [CocoaPods](https://cocoapods.org/) installerat för din versions miljö.

## <a name="setting-up"></a>Konfigurera

### <a name="creating-the-xcode-project"></a>Skapa Xcode-projektet

I Xcode skapar du ett nytt iOS-projekt och väljer **app** -mallen. Vi kommer att använda UIKit-storyboards. Du kommer inte att skapa tester under den här snabb starten. Avmarkera **ta med tester**.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Skärm bild som visar det nya valet av projekt mal len i Xcode.":::

Namnge projektet `TeamsEmbedGettingStarted` .

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Skärm bild som visar den nya projekt informationen i Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installera paketet och beroenden med CocoaPods

1. Skapa en Podfile för ditt program:

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. Kör `pod install`.
3. Öppna den genererade `.xcworkspace` med Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Begär åtkomst till mikrofonen, kameran osv.

Uppdatera appens informations egenskaps lista för att få åtkomst till enhetens maskin vara. Ange det associerade värdet till en `string` som ska ingå i dialog rutan som systemet använder för att begära åtkomst från användaren.

Högerklicka på `Info.plist` posten för projekt trädet och välj **öppna som**  >  **källkod**. Lägg till följande rader i avsnittet på den översta nivån `<dict>` och spara sedan filen.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Lägg till Team Inbäddnings ramverket

1. Ladda ned ramverket.
2. Skapa en `Frameworks` mapp i projekt roten. t.ex. `\TeamsEmbedGettingStarted\Frameworks\`
3. Kopiera de hämtade `TeamsAppSDK.framework` och `MeetingUIClient.framework` ramverkna till den här mappen.
4. Lägg till `TeamsAppSDK.framework` och `MeetingUIClient.framework` till projekt målet på fliken Allmänt. `Add Other`  ->  `Add Files...` navigera till filerna i Framework och Lägg till dem.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Skärm bild som visar de tillagda ramverken i Xcode.":::

5. Om den inte redan finns lägger `$(PROJECT_DIR)/Frameworks` du till i `Framework Search Paths` under fliken Skapa inställningar för projekt mål. Du hittar inställningen genom att ändra filtret från `basic` till. `all` du kan också använda Sök fältet till höger.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Skärm bild som visar Sök vägen för ramverket i Xcode.":::

### <a name="turn-off-bitcode"></a>Inaktivera Bitcode

Ange `Enable Bitcode` alternativet `No` i projektets versions inställningar. Du hittar inställningen genom att ändra filtret från `basic` till. `all` du kan också använda Sök fältet till höger.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Skärm bild som visar alternativet BitCode i Xcode.":::

### <a name="add-framework-signing-script"></a>Lägg till skript för Ramverks signering

Välj appens mål och välj `Build Phases` fliken.  Klicka sedan på `+` följt av `New Run Script Phase` . Se till att den nya fasen inträffar efter `Embed Frameworks` faserna.



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Skärm bild som visar hur du lägger till bygg skriptet i Xcode.":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>Aktivera läget röst över IP-bakgrund.

Välj appens mål och klicka på fliken funktioner.

Aktivera `Background Modes` genom att klicka på `+ Capabilities` överst och välj `Background Modes` .

Välj kryss ruta för `Voice over IP` .

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Skärm bild som visar aktiverat VOIP i Xcode.":::

### <a name="add-a-window-reference-to-appdelegate"></a>Lägg till en fönster referens i AppDelegate

Öppna projektets **AppDelegate. SWIFT** -fil och Lägg till en referens för ' Window '.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>Lägg till en knapp i ViewController

Skapa en knapp i `viewDidLoad` motringningen i **ViewController. SWIFT**.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

Skapa ett uttag för knappen i **ViewController. SWIFT**.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Öppna projektets **ViewController. SWIFT** -fil och Lägg till en `import` deklaration överst i filen för att importera `AzureCommunication library` och `MeetingUIClient` . 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

Ersätt implementeringen av `ViewController` klassen med en enkel knapp så att användaren kan ansluta till ett möte. Vi kommer att koppla affärs logik till knappen i den här snabb starten.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services Teams Inbäddnings bibliotek:

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | MeetingUIClient är den huvudsakliga start punkten för Teams Inbäddnings biblioteket. |
| MeetingUIClientDelegate | MeetingUIClientDelegate används för att ta emot händelser, t. ex. ändringar i anrops tillstånd. |
| MeetingJoinOptions | MeetingJoinOptions används för konfigurerbara alternativ som visnings namn. | 
| CallState | CallState används för att rapportera ändringar i anrops tillstånd. Alternativen är följande: anslutning, waitingInLobby, ansluten och avslutad. |

## <a name="create-and-authenticate-the-client"></a>Skapa och autentisera klienten

Initiera en `MeetingUIClient` instans med en åtkomsttoken för användare som gör att vi kan ansluta till möten. Lägg till följande kod i `viewDidLoad` återanropet i **ViewController. SWIFT**:

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

Ersätt `<USER_ACCESS_TOKEN>` med en giltig åtkomsttoken för din resurs. Se dokumentationen för [användar åtkomst-token](../../access-tokens.md) om du inte redan har en tillgänglig token.

### <a name="setup-token-refreshing"></a>Uppdatera token uppdatera

Skapa en `fetchTokenAsync`-metod. Lägg sedan till din `fetchToken` logik för att hämta användartoken.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

Ersätt `<USER_ACCESS_TOKEN>` med en giltig åtkomsttoken för din resurs.

## <a name="join-a-meeting"></a>Delta i ett möte

`joinMeeting`Metoden anges som den åtgärd som ska utföras när knappen *Anslut till möte* har knackas. Uppdatera implementeringen för att ansluta till ett möte med `MeetingUIClient` :

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Ersätt `<MEETING URL>` med en Teams Mötes länk.

### <a name="get-a-teams-meeting-link"></a>Länken Hämta en team möte

En länk till Team möte kan hämtas med Graph API: er. Detta beskrivs i [Graph-dokumentationen](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Kommunikations tjänsterna som anropar SDK accepterar en länk till en fullständig Teams möte. Den här länken returneras som en del av `onlineMeeting` resursen, som är tillgänglig under [ `joinWebUrl` egenskapen](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) . du kan också hämta nödvändig Mötes information från URL: en **till kopplings mötet** i team mötets inbjudan.

## <a name="run-the-code"></a>Kör koden

Du kan skapa och köra din app på iOS-simulatorn genom att välja **produkt**  >  **körning** eller genom att använda kortkommandot (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="Det slutliga utseendet och känslan av snabb starts appen":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Det slutliga utseendet och känslan när mötet har anslutits":::

> [!NOTE]
> Första gången du ansluter till ett möte uppmanas du att få åtkomst till mikrofonen. I ett produktions program bör du använda `AVAudioSession` API: et för att [kontrol lera behörighets status](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) och på ett smidigt sätt uppdatera programmets beteende när behörighet inte beviljas.

## <a name="add-localization-support-based-on-your-app"></a>Lägg till lokaliserings stöd baserat på din app

Microsoft Teams SDK stöder över 100-strängar och-resurser. Ramverks paketet innehåller bas-och engelska språk. Resten av dem ingår i `Localizations.zip` filen som ingår i paketet.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Lägg till lokaliseringar i SDK baserat på vad appen stöder

1. Ta reda på vilken typ av lokalisering som programmet har stöd för från appen Xcode Project > info > lokaliserings List
2. Zippa upp Localizations.zip som ingår i paketet
3. Kopiera lokaliserings mapparna från den zippade mappen baserat på vad appen stöder för roten i TeamsAppSDK. Framework

## <a name="preparation-for-app-store-upload"></a>Förberedelser för uppladdning av App Store

Ta bort i386 och x86_64 arkitekturer från ramverken vid arkivering.

Lägg till `i386` och `x86_64` arkitekturerna och ta bort skriptet till bygg faserna innan du skapar ramverket design om du vill arkivera programmet.

Välj ditt projekt i projekt navigeringen. I redigerings fönstret går du till skapa faser → Klicka på + signera → skapa en ny körnings skript fas.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>Exempelkod

Du kan ladda ned exempel appen från [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started)
