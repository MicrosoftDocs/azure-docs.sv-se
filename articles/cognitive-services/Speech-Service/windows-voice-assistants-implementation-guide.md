---
title: Röst assistenter i rikt linjer för låsnings implementering i Windows – ovan
titleSuffix: Azure Cognitive Services
description: Instruktionerna för att implementera röst aktiverings-och över-lås-funktioner för ett Voice agent-program.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939855"
---
# <a name="implementing-voice-assistants-on-windows"></a>Implementera röst assistenter i Windows

Den här guiden går igenom viktig implementerings information för att skapa en röst assistent i Windows.

## <a name="implementing-voice-activation"></a>Implementera röst aktivering

När du har konfigurerat [din miljö](how-to-windows-voice-assistants-get-started.md) och lärt dig [Hur röst aktiveringen fungerar](windows-voice-assistants-overview.md#how-does-voice-activation-work), kan du börja implementera röst aktivering för ditt eget program i röst assistenten.

### <a name="registration"></a>Registrering

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Se till att mikrofonen är tillgänglig och tillgänglig, och övervaka sedan dess tillstånd

MVA kräver att en mikrofon är tillgänglig och kan användas för att identifiera en röst aktivering. Använd klasserna [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability), [DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher)och [MediaCapture](/uwp/api/windows.media.capture.mediacapture) för att kontrol lera om det finns någon åtkomst till mikrofonen, enhetens närvaro och enhets status (t. ex. volym och ljud av).

### <a name="register-the-application-with-the-background-service"></a>Registrera programmet med bakgrunds tjänsten

För att MVA ska kunna starta programmet i bakgrunden måste programmet registreras med bakgrunds tjänsten. Se en fullständig guide för registrering av bakgrunds tjänster [här](/windows/uwp/launch-resume/register-a-background-task).

### <a name="unlock-the-limited-access-feature"></a>Lås upp funktionen för begränsad åtkomst

Använd din Microsoft-begränsade åtkomst funktions nyckel för att låsa upp funktionen röst assistent. Använd klassen [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) från Windows SDK för att göra detta.

### <a name="register-the-keyword-for-the-application"></a>Registrera nyckelordet för programmet

Programmet behöver registrera sig, dess nyckelords modell och dess språk med Windows.

Börja med att hämta nyckelords detektorn. I den här exempel koden hämtar vi den första detektorn, men du kan välja en viss detektor genom att välja den från `configurableDetectors` .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

När du har hämtat ActivationSignalDetector-objektet anropar du dess `ActivationSignalDetector.CreateConfigurationAsync` metod med signal-ID, modell-ID och visnings namn för att registrera ditt nyckelord och hämta ditt program `ActivationSignalDetectionConfiguration` . Signal-och modell-ID: na bör vara GUID som beslutas av utvecklaren och förblir konsekvent för samma nyckelord.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Kontrol lera att röst aktiverings inställningen är aktive rad

Om du vill använda röst aktivering måste en användare aktivera röst aktivering för systemet och aktivera röst aktivering för deras program. Du kan hitta inställningen under "sekretess inställningar för röst aktivering" i Windows-inställningar. Om du vill kontrol lera status för röst aktiverings inställningen i programmet använder du instansen för `ActivationSignalDetectionConfiguration` från att registrera nyckelordet. Fältet [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) i `ActivationSignalDetectionConfiguration` innehåller ett uppräknings värde som beskriver statusen för röst aktiverings inställningen.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Hämta en ConversationalAgentSession för att registrera appen med MVA-systemet

`ConversationalAgentSession`Är en klass i Windows SDK som gör att appen kan uppdatera Windows med appens tillstånd (inaktiv, upptäcka, lyssna, arbeta, tala) och ta emot händelser, till exempel aktiverings identifiering och system tillstånds ändringar, till exempel skärm låsning. Genom att hämta en instans av AgentSession kan du också registrera programmet med Windows som activatable med röst. Vi rekommenderar att du underhåller en referens till `ConversationalAgentSession` . Hämta sessionen med hjälp av API: `ConversationalAgentSession.GetCurrentSessionAsync` et.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Lyssna på de två aktiverings signalerna: OnBackgroundActivated och OnSignalDetected

Windows kommer att signalera din app när den identifierar ett nyckelord på ett av två sätt. Om appen inte är aktiv (det vill säga att du inte har en referens till en icke-borttagen instans av `ConversationalAgentSession` ), kommer den att starta din app och anropa OnBackgroundActivated-metoden i filen app. XAML. cs i programmet. Om fältet Event argument `BackgroundActivatedEventArgs.TaskInstance.Task.Name` matchar strängen "AgentBackgroundTrigger" utlöstes program starten av röst aktiveringen. Programmet måste åsidosätta den här metoden och hämta en instans av ConversationalAgentSession för att signalera till Windows som nu är aktiv. När programmet är aktivt kommer Windows att signalera förekomsten av röst aktivering med `ConversationalAgentSession.OnSignalDetected` händelsen. Lägg till en händelse hanterare i den här händelsen så snart du hämtar `ConversationalAgentSession` .

## <a name="keyword-verification"></a>Nyckelords verifiering

När ett Voice agent-program har Aktiver ATS av Voice är nästa steg att kontrol lera att nyckelords identifieringen är giltig. Windows tillhandahåller ingen lösning för nyckelords verifiering, men det gör att röst assistenter kan komma åt ljudet från den hypotetiska aktiveringen och slutföra verifieringen på egen hand.

### <a name="retrieve-activation-audio"></a>Hämta aktiverings ljud

Skapa en [AudioGraph](/uwp/api/windows.media.audio.audiograph) och skicka den till `CreateAudioDeviceInputNodeAsync` `ConversationalAgentSession` . Det här läser in grafens ljudbuffert med ljudet som *börjar cirka 3 sekunder innan nyckelordet identifierades*. Detta extra ledande ljud ingår för att hantera en mängd olika nyckelords längder och högtalar hastigheter. Sedan hanterar du händelsen [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) från ljud diagrammet för att hämta ljuddata.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Obs! det ledande ljudet som ingår i ljudbufferten kan orsaka att nyckelords verifieringen inte fungerar. Åtgärda problemet genom att trimma början på ljudbufferten innan du skickar ljudet för nyckelords verifiering. Den här inledande trimningen bör skräddarsys för varje assistent.

### <a name="launch-in-the-foreground"></a>Starta i förgrunden

När nyckelords verifieringen lyckas måste programmet flyttas till förgrunden för att Visa användar gränssnittet. Anropa `ConversationalAgentSession.RequestForegroundActivationAsync` API: et för att flytta programmet till förgrunden.

### <a name="transition-from-compact-view-to-full-view"></a>Över gång från komprimerad vy till fullständig vy

När ditt program först aktive ras av Voice startas det i en komprimerad vy. I [design vägledningen för för hands versionen av röst aktivering](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) hittar du information om olika vyer och över gångar mellan dem för röst assistenter i Windows.

Använd ApplicationView-API: et för att göra över gången från komprimerad vy till fullständig app-vy `TryEnterViewModeAsync` :

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Implementera lås aktivering ovan

Följande steg beskriver kraven för att aktivera en röst assistent i Windows för att köra låset, inklusive referenser till exempel kod och rikt linjer för att hantera programmets livs cykel.

Vägledning om hur du utformar ovan låsnings upplevelser finns i [Guide för bästa praxis](windows-voice-assistants-best-practices.md).

När en app visar en vy över låset anses den vara i hel skärms läge. Mer information om hur du implementerar en app som använder hel skärms läge finns i [dokumentationen för hel skärms läge](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access).

### <a name="transitioning-above-lock"></a>Över gång över lås

En aktivering över låset liknar en aktivering under låset. Om det inte finns några aktiva instanser av programmet kommer en ny instans att startas i bakgrunden och `OnBackgroundActivated` i app. XAML. CS kommer att anropas. Om det finns en instans av programmet får den instansen ett meddelande via `ConversationalAgentSession.SignalDetected` händelsen.

Om programmet inte redan visas över låset måste det anropa `ConversationalAgentSession.RequestForegroundActivationAsync` . Detta utlöser `OnLaunched` metoden i app. XAML. cs som ska navigera till den vy som visas ovan.

### <a name="detecting-lock-screen-transitions"></a>Identifiera lås skärms över gångar

ConversationalAgent-biblioteket i Windows SDK tillhandahåller ett API för att göra lås skärms läget och ändringar i lås skärms läget lätt att komma åt. Om du vill identifiera det aktuella lås skärms läget kontrollerar du `ConversationalAgentSession.IsUserAuthenticated` fältet. Om du vill identifiera ändringar i lås tillstånd lägger du till en händelse hanterare i `ConversationalAgentSession` objektets `SystemStateChanged` händelse. Den utlöses när skärmen ändras från olåst till låst eller vice versa. Om värdet för händelse argumenten är `ConversationalAgentSystemStateChangeType.UserAuthentication` , har lås skärms status ändrats.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Identifiera användar preferens för lås aktivering ovan

Program posten på sidan Sekretess inställningar för röst aktivering har en växling för över-lås funktioner. För att appen ska kunna starta över låset, måste användaren aktivera den här inställningen. Status för behörigheter för att låsa ovan lagras också i ActivationSignalDetectionConfiguration-objektet. Status för AvailabilityInfo. HasLockScreenPermission visar om användaren har getts behörigheten över lås. Om den här inställningen är inaktive rad kan ett röst program uppmana användaren att navigera till rätt inställnings sida på länken "MS-Settings: privacy-voiceactivation" med anvisningar om hur du aktiverar över-lås aktivering för programmet.

## <a name="closing-the-application"></a>Programmet stängs

Om du vill stänga programmet program mässigt under eller under låset, använder du API: `WindowService.CloseWindow()` et. Detta utlöser alla UWP livs cykel metoder, inklusive OnSuspend, vilket gör att programmet kan ta bort sin `ConversationalAgentSession` instans innan den stängs.

> [!NOTE]
> Programmet kan stänga utan att stänga av [Lås instansen nedan](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-). I det här fallet måste den ovan skrivskyddade vyn bli "Rensa", vilket säkerställer att när skärmen har låsts upp, så finns det inga händelse hanterare eller uppgifter som försöker att ändra ovanstående lås vy.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Besök UWP Voice Assistant exempel-appen för exempel och kod genom gång](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
