---
title: Översikt över Runtime SDK
description: Bekanta dig med Object ankare Runtime SDK.
author: craigktreasure
manager: vriveras
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: azure-object-anchors
ms.openlocfilehash: 551374824610c0257aaf52c45768d31849026524
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047549"
---
# <a name="runtime-sdk-overview"></a>Översikt över Runtime SDK

Det här avsnittet innehåller en översikt över Object ankare Runtime SDK, som används för att identifiera objekt med en modell för objekt ankare. Du får en förståelse för hur ett objekt representeras och vilka olika komponenter som används för.

Alla typer som beskrivs nedan finns i namn området **Microsoft. MixedReality. ObjectAnchors** .

## <a name="types"></a>Typer

### <a name="objectmodel"></a>ObjectModel

En [ObjectModel](/dotnet/api/microsoft.azure.objectanchors.objectmodel) representerar ett fysiskt objekts geometri och kodar nödvändiga parametrar för identifiering och attityd uppskattning. Det måste skapas med hjälp av [tjänsten objekt ankare](../quickstarts/get-started-model-conversion.md). Sedan kan ett program läsa in den genererade modell filen med hjälp av API: et för objekt ankare och fråga nätet som är inbäddat i modellen för visualisering.

### <a name="objectsearcharea"></a>ObjectSearchArea

En [ObjectSearchArea](/dotnet/api/microsoft.azure.objectanchors.objectsearcharea) anger det utrymme som ska sökas efter ett eller flera objekt. Den definieras av ett spatial diagram Node-ID och rums gränser i koordinatsystemet som representeras av spatialdata för spatial graf. Object ankare Runtime SDK har stöd för fyra typer av gränser, t. ex. **för vy**, **avgränsnings ruta**, **sfär** och en **plats**.

### <a name="objectquery"></a>ObjectQuery

En [ObjectQuery](/dotnet/api/microsoft.azure.objectanchors.objectquery) visar ett **objekt** som visar hur objekt i en specifik modell kan hittas. Den innehåller följande justerbara-parametrar, vars standardvärden kan hämtas från en objekt modell.

#### <a name="minsurfacecoverage"></a>MinSurfaceCoverage

Egenskapen [MinSurfaceCoverage](/dotnet/api/microsoft.azure.objectanchors.objectquery.minsurfacecoverage) anger värdet för att betrakta en instans som identifierad.

För varje objekt kandidat beräknar en **observatör** en kvot över överlappande ytor mellan omvandlad objekt modell och scenen. därefter rapporteras den sökande till att endast program när täcknings förhållandet är över ett specifikt tröskelvärde.

#### <a name="isexpectedtobestandingongroundplane"></a>IsExpectedToBeStandingOnGroundPlane

Egenskapen [IsExpectedToBeStandingOnGroundPlane](/dotnet/api/microsoft.azure.objectanchors.objectquery.isexpectedtobestandingongroundplane) anger om målobjektet förväntas stå på jord planet.

Ett jord plan är det lägsta vågräta golvet i Sök fältet. Det ger en utmärkt begränsning för möjliga objekts attityder. Om du aktiverar den här flaggan får **observatören** en uppskattning av attityden på ett begränsat utrymme och kan förbättra noggrannheten. Den här parametern kommer att ignoreras om modellen inte förväntas stå på jord planet.

#### <a name="expectedmaxverticalorientationindegrees"></a>ExpectedMaxVerticalOrientationInDegrees

Egenskapen [ExpectedMaxVerticalOrientationInDegrees](/dotnet/api/microsoft.azure.objectanchors.objectquery.expectedmaxverticalorientationindegrees) anger den förväntade maximala vinkeln i grader mellan en objekt förekomsts riktning och gravitation.

Den här parametern innehåller en annan begränsning för uppskattad attityd riktning. Om ett objekt till exempel är upp till höger, kan den här parametern vara 0. Objekt ankare förväntas inte identifiera objekt som skiljer sig från modellen. Om en modell är upp-höger identifierar den inte en instans som har angetts på sidan. En ny modell används för nedrullningsbar layout. Samma regel gäller för ledan.

#### <a name="maxscalechange"></a>MaxScaleChange

Egenskapen [MaxScaleChange](/dotnet/api/microsoft.azure.objectanchors.objectquery.maxscalechange) anger den maximala objekt skalnings ändringen (inom 0 ~ 1) med avseende på spatial mappning. Den uppskattade skalningen tillämpas på transformerade objekt hörn som är centrerade vid ursprung och axel justerade. Uppskattade skalor får inte vara den faktiska skalningen mellan en CAD-modell och dess fysiska representation, men vissa värden som tillåter appen att rendera en objekt modell nära till rums mappning för det fysiska objektet.

#### <a name="searchareas"></a>SearchAreas

Egenskapen [SearchAreas](/dotnet/api/microsoft.azure.objectanchors.objectquery.searchareas) anger en matris med spatialdata som ska hitta ett eller flera objekt.

**Observatören** söker efter objekt i unions området för alla Sök områden som anges i en fråga. I den här versionen kommer vi att returnera högst ett objekt med störst exakthet för att minska svars tiden.

### <a name="objectinstance"></a>ObjectInstance

En [ObjectInstance](/dotnet/api/microsoft.azure.objectanchors.objectinstance) representerar en hypotetisk position där en instans av en specifik modell kan finnas i HoloLens-koordinatsystemet. Varje instans levereras med en `SurfaceCoverage` egenskap som visar hur nöjd den uppskattade attityden är.

En instans skapas genom anrops `ObjectObserver.DetectAsync` metod och uppdateras sedan automatiskt i bakgrunden när du är igång. Ett program kan lyssna efter status ändrings händelsen för en viss instans eller ändra spårnings läget för att pausa/återuppta uppdateringen. En instans tas automatiskt bort från **observatören** när spårningen förloras.

### <a name="objectobserver"></a>ObjectObserver

En [ObjectObserver](/dotnet/api/microsoft.azure.objectanchors.objectobserver) läser in objekt modeller, identifierar sina instanser och rapporterar 6-DOF-attityder för varje instans i HoloLens-koordinatsystemet.

Även om en objekt modell eller instans skapas från en **övervakare**, är deras livs längd oberoende. Ett program kan ta bort en observatör och fortsätta att använda objekt modellen eller instansen.

### <a name="objectdiagnosticssession"></a>ObjectDiagnosticsSession

[ObjectDiagnosticSession](/dotnet/api/microsoft.azure.objectanchors.diagnostics.objectdiagnosticssession) registrerar diagnostiken och skriver data till ett arkiv.

Ett diagnostiskt arkiv innehåller scen punktens moln, observatörens status och information om modellerna. Den här informationen är användbar för att identifiera möjliga körnings problem. Mer information finns i [vanliga frågor och svar](../faq.md).

## <a name="runtime-sdk-usage-and-details"></a>Användning och information om Runtime SDK

Det här avsnittet innehåller grundläggande information om hur du använder Runtime SDK. Det bör ge dig tillräckligt med sammanhang för att bläddra bland exempel programmen för att se hur objekt ankare används holistiskt.

### <a name="initialization"></a>Initiering

Programmen måste anropa `ObjectObserver.IsSupported()` API: et för att avgöra om det finns stöd för objekt ankare på enheten innan det används. Om `ObjectObserver.IsSupported()` API: et returnerar `false` kontrollerar du att programmet har aktiverat uppgraderingen **spatialPerception** Capability och/eller till det senaste HoloLens-OS.

```cs
if (!ObjectObserver.IsSupported())
{
    // Handle the error
}

// This call should grant the access we need.
var status = await ObjectObserver.RequestAccessAsync();
if(status != ObjectObserverStatus.Allowed)
{
    // Handle the error
}
```

Därefter skapar programmet en objekt observatör och läser in nödvändiga modeller som genereras av [konverterings tjänsten för objekt ankare](../quickstarts/get-started-model-conversion.md).

```cs
var observer = new ObjectObserver();

byte[] modelAsBytes; // Load a model into a byte array. Model could be a file, an embedded resource, or a network stream.
var model = await observer.LoadObjectModelAsync(modelAsBytes);

// Note that after a model is loaded, its vertices and normals are transformed into a centered coordinate system for the 
// ease of computing the object pose. The rigid transform can be retrieved through the `OriginToCenterTransform` property.
```

Programmet skapar en fråga för att identifiera instanser av modellen inom ett utrymme.

```cs
var coordinateSystem = default(SpatialGraphCoordinateSystem);
var searchAreaAsBox = ObjectSearchArea.FromOrientedBox(coordinateSystem, default(SpatialOrientedBox));

// Optionally change the parameters, otherwise use the default values embedded in the model.
var query = new ObjectQuery(model);
query.MinSurfaceCoverage = 0.2f;
query.ExpectedMaxVerticalOrientationInDegrees = 1.5f;
query.MaxScaleChange = 0.1f;
query.SearchAreas.Add(searchAreaAsBox);

// Detection could take long, run in a background thread.
var detectedObjects = await observer.DetectAsync(query);
```

Som standard spåras alla identifierade instanser automatiskt av **observatören**. Vi kan välja att ändra dessa instanser genom att ändra deras spårnings läge eller lyssna till status ändrings händelsen.

```cs
foreach (var instance in detectedObjects)
{
    // Supported modes:
    // "LowLatencyCoarsePosition" - consumes less CPU cycles thus fast to update the state.
    // "HighLatencyAccuratePosition" - (not yet implemented) consumes more CPU cycles thus potentially taking longer time to update the state.
    // "Paused" - stops to update the state until mode changed to low or high.
    instance.Mode = ObjectInstanceTrackingMode.LowLatencyCoarsePosition;

    // Listen to state changed event on this instance.
    instance.Changed += InstanceChangedHandler;

    // Optionally dispose an instance if not interested in it.
    //instance.Dispose();
}
```

I status ändrings händelsen kan vi fråga det senaste läget eller ta bort en instans om den förloras.

```cs
var InstanceChangedHandler = new Windows.Foundation.TypedEventHandler<ObjectInstance, ObjectInstanceChangedEventArgs>((sender, args) =>
{
    // Try to query the current instance state.
    var state = sender.TryGetCurrentState();

    if (state.HasValue)
    {
        // Process latest state via state.Value.
        // An object pose includes scale, rotation and translation, applied in the same order
        // to the object model in the centered coordinate system.
    }
    else
    {
        // This object instance is lost for tracking, and will never be recovered.
        // The caller can detach the Changed event handler from this instance and dispose it.
        sender.Dispose();
    }
});
```

Dessutom kan ett program registrera en eller flera diagnostiska sessioner för offline-felsökning.

```cs
string diagnosticsFolderPath = Windows.Storage.ApplicationData.Current.TemporaryFolder.Path;
const uint maxSessionSizeInMegaBytes = uint.MaxValue;

// Recording starts on the creation of a diagnostics session.
var diagnostics = new ObjectDiagnosticsSession(observer, maxSessionSizeInMegaBytes);

// Wait for the observer to do a job.

// Application can report some **pseudo ground-truth** pose for an instance acquired from other means.
diagnostics.ReportActualInstanceLocation(instance, coordinateSystem, Vector3.Zero, Quaternion.Identity);

// Close a session and write the diagnostics into an archive at specified location.
await diagnostics.CloseAsync(System.IO.Path.Combine(diagnosticsFolderPath, "diagnostics.zip"));
```

Slutligen släpper vi resurser genom att avyttra alla objekt.

```cs
foreach(var instance in activeInstances)
{
    instance.Changed -= InstanceChangedHandler;
    instance.Dispose();
}

model.Dispose();
observer.Dispose();
```