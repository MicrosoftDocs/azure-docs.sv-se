---
title: Grafik bindning
description: Installation av grafik bindningar och användnings fall
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 69bcc521b4cd00320a5fbecc5244e913ac16c68b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593916"
---
# <a name="graphics-binding"></a>Grafik bindning

För att kunna använda Azures fjärrrendering i ett anpassat program måste den integreras i programmets åter givnings pipeline. Den här integrationen är ansvaret för grafik bindningen.

När grafik bindningen har kon figurer ATS ger den till gång till olika funktioner som påverkar den åter givnings avbildningen. Dessa funktioner kan delas in i två kategorier: allmänna funktioner som alltid är tillgängliga och vissa funktioner som endast är relevanta för den valda `Microsoft.Azure.RemoteRendering.GraphicsApiType` .

## <a name="graphics-binding-in-unity"></a>Grafik bindning i Unity

I Unity hanteras hela bindningen av de strukturer som `RemoteUnityClientInit` skickas till `RemoteManagerUnity.InitializeManager` . Om du vill ställa in grafik läget måste `GraphicsApiType` fältet ställas in på den valda bindningen. Fältet fylls i automatiskt beroende på om en XRDevice finns. Beteendet kan åsidosättas manuellt med följande beteenden:

* **HoloLens 2**: grafik bindningen för [Windows Mixed Reality](#windows-mixed-reality) används alltid.
* **Flat UWP Desktop-app**: [simulering](#simulation) används alltid.
* **Unity Editor**: [simuleringen](#simulation) används alltid om inte ett WMR VR-headset är anslutet i vilket fall arr kommer att inaktive ras för att felsöka icke-arr-relaterade delar av programmet. Se även [Holographic fjärr kommunikation](../how-tos/unity/holographic-remoting.md).

Den enda andra relevanta delen för Unity har åtkomst till den [grundläggande bindningen](#access). alla andra avsnitt nedan kan hoppas över.

## <a name="graphics-binding-setup-in-custom-applications"></a>Grafik bindnings konfiguration i anpassade program

Om du vill välja en grafik bindning gör du följande två steg: först måste grafik bindningen vara statiskt initierad när programmet initieras:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization();
managerInit.GraphicsApi = GraphicsApiType.WmrD3D11;
managerInit.ConnectionType = ConnectionType.General;
managerInit.Right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.GraphicsApi = GraphicsApiType::WmrD3D11;
managerInit.ConnectionType = ConnectionType::General;
managerInit.Right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering

```

Anropet ovan krävs för att initiera Azures fjärrrendering i Holographic-API: erna. Den här funktionen måste anropas innan något Holographic-API anropas och innan andra API: er för fjärrrendering används. På samma sätt ska motsvarande avinitierings funktion `RemoteManagerStatic.ShutdownRemoteRendering();` anropas när inga Holographic-API: er anropas längre.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Åtkomst till grafik bindning

När en klient har kon figurer ATS kan den grundläggande grafik bindningen nås med get- `RenderingSession.GraphicsBinding` metoden. Till exempel kan den senaste ram statistiken hämtas så här:

```cs
RenderingSession currentSession = ...;
if (currentSession.GraphicsBinding != null)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>Grafik-API: er

Det finns för närvarande två grafik-API: er som kan väljas `WmrD3D11` och `SimD3D11` . `Headless`Det finns ännu en tredjedel av klient sidan som inte stöds.

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` är standard bindningen som ska köras på HoloLens 2. `GraphicsBindingWmrD3d11`Bindningen skapas. I det här läget är Azure-fjärrrendering hookar direkt i Holographic-API: erna.

För att komma åt de härledda grafik bindningarna måste basen `GraphicsBinding` omvandlas.
Det finns två saker som måste utföras för att använda WMR-bindningen:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Meddela fjärrrendering av det använda koordinatsystemet

```cs
RenderingSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    //...
}
```

Där ovanstående `ptr` måste vara en pekare till ett ursprungligt `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` objekt som definierar det världs utrymmes koordinatsystem där koordinaterna i API: et uttrycks i.

#### <a name="render-remote-image"></a>Återge fjärran sluten avbildning

I början av varje ram måste den fjärranslutna ramen återges i den bakre bufferten. Detta görs genom att anropa `BlitRemoteFrame` , vilket fyller både färg-och djup information för båda ögonen i det aktuella bindnings målet. Det är därför viktigt att göra det när du har bundit den fullständiga back bufferten som ett Render-mål.

> [!WARNING]
> När fjärravbildningen var blit i den bakre bufferten ska det lokala innehållet återges med en teknik för en enda pass-teknik, t. ex. med hjälp av **SV_RenderTargetArrayIndex**. Att använda andra tekniker för stereo åter givning, till exempel rendera varje öga i ett separat pass, kan leda till betydande prestanda försämring eller grafiska artefakter och bör undvikas.

```cs
RenderingSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulering

`GraphicsApiType.SimD3D11` är simulerings bindningen och om den väljs skapas `GraphicsBindingSimD3d11` grafik bindningen. Det här gränssnittet används för att simulera huvud förflyttning, till exempel i ett Skriv bords program och återger en monoscopic-avbildning.

Om du vill implementera simulerings bindningen är det viktigt att förstå skillnaden mellan den lokala kameran och den fjärranslutna ramen enligt beskrivningen på [kamera](../overview/features/camera.md) sidan.

Två kameror behövs:

* **Lokal kamera**: den här kameran representerar den aktuella kamera positionen som styrs av program logiken.
* **Proxy kamera**: den här kameran matchar den aktuella *fjärran sluten ram* som skickades av servern. Eftersom det finns en tids fördröjning mellan klienten som begär en ram och dess ankomst, är *fjärran sluten* alltid en bit bakom den lokala kamerans rörelse.

Den grundläggande metoden här är att både fjärravbildningen och det lokala innehållet återges i ett mål på skärmen med hjälp av proxy-kameran. Proxy-avbildningen projiceras sedan om i det lokala kamera utrymmet, vilket förklaras i [slutet av omprojektionen av väntande steg](../overview/features/late-stage-reprojection.md).

`GraphicsApiType.SimD3D11` stöder även Stereoscopic-rendering, som måste aktive ras under `InitSimulation` installations anropet nedan. Installationen är lite mer engagerad och fungerar på följande sätt:

#### <a name="create-proxy-render-target"></a>Skapa Proxy för rendering mål

Fjärrinnehållet och det lokala innehållet måste återges för att rendera målet för färg-och djupet som heter proxy med hjälp av proxy-kamerans data som tillhandahålls av `GraphicsBindingSimD3d11.Update` funktionen.

Proxyservern måste matcha den bakre buffertens upplösning och måste vara int *DXGI_FORMAT_R8G8B8A8_UNORM* -eller *DXGI_FORMAT_B8G8R8A8_UNORM* -formatet. När det gäller Stereoscopic-rendering, både färg-proxyns struktur och, om djupet används, så måste du ha två mat ris lager i stället för ett i djupet. När en session är klar `GraphicsBindingSimD3d11.InitSimulation` måste anropas före anslutning till den:

```cs
RenderingSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

Funktionen init måste tillhandahållas med pekare till den interna D3D-enheten samt färg-och djup texturen för proxyns åter givnings mål. När den har initierats `RenderingSession.ConnectAsync` och `Disconnect` kan anropas flera gånger, men när du växlar till en annan session, `GraphicsBindingSimD3d11.DeinitSimulation` måste anropas först på den gamla sessionen innan den `GraphicsBindingSimD3d11.InitSimulation` kan anropas i en annan session.

#### <a name="render-loop-update"></a>Uppdatera upprepnings slinga

Uppdateringen rendera loop består av flera steg:

1. Varje ram, innan en åter givning sker, `GraphicsBindingSimD3d11.Update` anropas med den aktuella Camera-transformeringen som skickas över till den server som ska renderas. Samtidigt bör den returnerade proxysammansättningen tillämpas på den proxyserver som ska renderas i proxyns åter givnings mål.
Om den returnerade proxykonfigurationen `SimulationUpdate.frameId` är null finns det inga fjärrdata än. I det här fallet i stället för att rendera in i proxyns åter givnings mål, ska allt lokalt innehåll återges till den bakre bufferten direkt med hjälp av aktuella kamera data och nästa två steg hoppas över.
1. Programmet bör nu binda proxyns åter givnings mål och anropa `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy` . Då fylls information om fjärrfärg och djup i proxyns åter givnings mål. Alla lokala innehåll kan nu återges på proxyservern med hjälp av proxy-kamera omvandlingen.
1. Sedan måste den bakre bufferten vara kopplad till ett åter givnings mål och `GraphicsBindingSimD3d11.ReprojectProxy` anropas med den punkt där den bakre bufferten kan visas.

```cs
RenderingSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

#### <a name="simulation-update-structures"></a>Simulerings uppdaterings strukturer

I varje ram kräver **uppdatering av Render-loopen** från föregående avsnitt att du anger en uppsättning kamera parametrar som motsvarar den lokala kameran och returnerar en uppsättning kamera parametrar som motsvarar nästa tillgängliga RAM kamera. De här två uppsättningarna registreras i `SimulationUpdateParameters` respektive `SimulationUpdateResult` struktur:

```cs
public struct SimulationUpdateParameters
{
    public int FrameId;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};

public struct SimulationUpdateResult
{
    public int FrameId;
    public float NearPlaneDistance;
    public float FarPlaneDistance;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};
```

Struktur medlemmarna har följande betydelse:

| Medlem | Beskrivning |
|--------|-------------|
| FrameId | ID för kontinuerlig ram. Krävs för SimulationUpdateParameters-indatamängd och måste ökas kontinuerligt för varje ny ram. Kommer att vara 0 i SimulationUpdateResult om inga ramdata är tillgängliga än. |
| ViewTransform | Vänster-höger-stereo-par av ramens omvandlings-matriser för kamera visning. Endast medlemmen är giltig för monoscopic-rendering `Left` . |
| FieldOfView | Vänster-höger-stereo-par av ramens kamera fält – för-visning i [OpenXR-fältet i View-konventionen](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles). Endast medlemmen är giltig för monoscopic-rendering `Left` . |
| NearPlaneDistance | avstånd i nära plan som används för projektions mat ris för den aktuella fjärran sluten ram. |
| FarPlaneDistance | det långt planet avstånd som används för projektions mat ris för den aktuella fjärran sluten ram. |

Med stereo-par `ViewTransform` och `FieldOfView` kan du ange båda värdena för ögon-kameran om åter givning av Stereoscopic har Aktiver ATS. Annars `Right` kommer medlemmarna att ignoreras. Som du kan se överförs bara kamerans omvandling som oformaterade matriser för 4x4-omvandling medan inga matriser för projektion har angetts. De faktiska matriserna beräknas av Azure-fjärrrenderingen internt med de angivna fälten i vyn och den aktuella nära planet och den i långt planet som angetts i [CameraSettings-API: et](../overview/features/camera.md).

Eftersom du kan ändra nära planet och långt planet på [CameraSettings](../overview/features/camera.md) under körningen efter behov och tjänsten tillämpar dessa inställningar asynkront, bär varje SimulationUpdateResult också det speciella nära planet och det långt planet som används under åter givningen av motsvarande ram. Du kan använda dessa plan värden för att anpassa dina projektions-matriser för att återge lokala objekt så att de matchar fjärrrams rendering.

Slutligen, medan **simulerings uppdaterings** anropet kräver OpenXR för standardisering och algoritmer, kan du använda konverterings funktionerna som illustreras i följande exempel på struktur ifyllning:

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(int frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters = default;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (parameters.FieldOfView.Left.FromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        throw new ArgumentException("Invalid projection settings");
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out int frameId)
{
    projectionMatrix = default;
    viewTransform = default;
    frameId = 0;

    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (result.FieldOfView.Left.ToProjectionMatrix(result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention.ZeroToOne, out projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (FovFromProjectionMatrix(projectionMatrix, parameters.FieldOfView.Left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (FovToProjectionMatrix(result.FieldOfView.Left, result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

Med de här konverterings funktionerna kan du snabbt växla mellan 4x4 och en matris med en vanlig perspektiv, beroende på dina behov av lokal åter givning. Dessa konverterings funktioner innehåller verifierings logik och returnerar fel, utan att ange ett giltigt resultat, om matriser för indatatyper eller indatatyper är ogiltiga.

## <a name="api-documentation"></a>API-dokumentation

* [C# RemoteManagerStatic. StartupRemoteRendering ()](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [C# GraphicsBinding-klass](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [C# GraphicsBindingWmrD3d11-klass](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [C# GraphicsBindingSimD3d11-klass](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [C++ RemoteRenderingInitialization struct](/cpp/api/remote-rendering/remoterenderinginitialization)
* [C++ GraphicsBinding-klass](/cpp/api/remote-rendering/graphicsbinding)
* [C++ GraphicsBindingWmrD3d11-klass](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [C++ GraphicsBindingSimD3d11-klass](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Nästa steg

* [Kamera](../overview/features/camera.md)
* [Omprojektion av sena steg](../overview/features/late-stage-reprojection.md)
* [Självstudie: Visa fjärranslutna modeller](../tutorials/unity/view-remote-models/view-remote-models.md)