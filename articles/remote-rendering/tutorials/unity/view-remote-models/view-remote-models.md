---
title: Visa en modell som renderats på distans
description: Självstudien Hello World "Azure Remote Rendering Hello World" visar hur du visar en modell som återges via en fjärrlagring i Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: f43e5b77580b7071ce48b39190c26a53f99f8cf5
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740160"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Självstudie: Visa en fjärrre renderad modell

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Etablera en Azure Remote Rendering (ARR)-instans
> * Skapa och stoppa en renderingssession
> * Återanvända en befintlig renderingssession
> * Ansluta och koppla från sessioner
> * Läsa in modeller i en renderingssession

## <a name="prerequisites"></a>Förutsättningar

För den här självstudien behöver du:

* En aktiv Azure-prenumeration med användningsbaserade betalning [Skapa ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(ladda ned)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Den senaste versionen av Visual Studio 2019 [(ladda ned)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(ladda ned)](https://git-scm.com/downloads)
* Unity (se [systemkrav för](../../../overview/system-requirements.md#unity) versioner som stöds)
* Kunskap på medelnivå om Unity och språket C# (till exempel att skapa skript och objekt, använda prefabs, konfigurera Unity-händelser osv.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Etablera en Azure Remote Rendering (ARR)-instans

För att få åtkomst Azure Remote Rendering-tjänsten måste du först [skapa ett konto](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>Skapa ett nytt Unity-projekt

> [!TIP]
> [Lagringsplatsen arr-exempel](https://github.com/Azure/azure-remote-rendering) innehåller ett projekt med alla självstudier slutförda, den kan användas som referens. Titta i *Unity\Tutorial-Complete för* det fullständiga Unity-projektet.

Skapa ett nytt projekt från Unity Hub.
I det här exemplet antar vi att projektet skapas i en mapp med namnet **RemoteRendering**.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Nytt Unity-projekt":::

## <a name="include-the-azure-remote-rendering-package"></a>Inkludera Azure Remote Rendering paket

[Följ anvisningarna](../../../how-tos/unity/install-remote-rendering-unity-package.md) för hur du lägger till Azure Remote Rendering-paketet i ett Unity-projekt.


## <a name="configure-the-camera"></a>Konfigurera kameran

1. Välj **noden Huvudkamera.**

1. Öppna snabbmenyn genom att högerklicka på *komponenten Transformera* och välja **alternativet** Återställ:

    ![återställa kameratransform](./media/camera-reset-transform.png)

1. Ange **Rensa flaggor till** Solid *Color*

1. Ange **Bakgrund** till *Svart* (#000000), med helt transparent (0) alfa (A)

    ![Färghjul](./media/color-wheel-black.png)

1. Ange **Cklippningsplan till** *Nära = 0,3* och *Långt = 20*. Det innebär att återgivningen kommer att fästa geometri som är närmare än 30 cm eller längre än 20 meter.

    ![Egenskaper för Unity-kamera](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Justera projektinställningarna

1. Öppna *Redigera > projektinställningar...*
1. Välj **Kvalitet** på den vänstra listmenyn
1. Ändra **Standardkvalitetsnivå för** alla plattformar till *Låg.* Den här inställningen aktiverar effektivare återgivning av lokalt innehåll och påverkar inte kvaliteten på fjärrreenderat innehåll.

    ![ändra kvalitetsinställningar för projekt](./media/settings-quality.png)

1. Välj **Grafik** på den vänstra listmenyn
1. Ändra inställningen **Skriptbar renderingspipeline** *till HybridRenderingPipeline*.\
    ![Skärmbild som visar var du ändrar inställningen Skriptbar renderingspipeline till HybridRenderingPipeline.](./media/settings-graphics-render-pipeline.png)\
    Ibland fyller inte användargränssnittet i listan över tillgängliga pipelinetyper från paketen. Om detta inträffar måste *tillgången HybridRenderingPipeline* dras till fältet manuellt:\
    ![ändra projektgrafikinställningar](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Om du inte kan dra och släppa *tillgången HybridRenderingPipeline* i fältet Render Pipeline Asset (eventuellt eftersom fältet inte finns!) ska du kontrollera att paketkonfigurationen innehåller `com.unity.render-pipelines.universal` paketet.

1. Välj **Player** på den vänstra listmenyn
1. Välj fliken **Universell Windows-plattform inställningar,** som visas som en Windows-ikon.
1. Ändra **XR-inställningarna för** att Windows Mixed Reality enligt nedan:
    1. Aktivera **virtuell verklighet som stöds**
    1. Tryck på knappen "+" och lägg till **Windows Mixed Reality**
    1. Ange **djupformat** till *16-bitars djup*
    1. Se till **att delning av djupbuffert** är aktiverat
    1. Ställ **in Stereorenderingsläge** *på Single Pass Instanced*

    ![spelarinställningar](./media/xr-player-settings.png)

1. I samma fönster ovanför **XR-inställningar expanderar** du **Publiceringsinställningar**
1. Rulla ned **till Funktioner** och välj:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** *(valfritt).* Välj det här alternativet om du vill ansluta Unity-fjärrfelsökaren till din enhet.

1. Under **Enhetsfamiljer som stöds** aktiverar du **Holographic** och **Desktop**
1. Stäng eller docka **panelen Projektinställningar**
1. Öppna *inställningar för >-version*
1. Välj **Universell Windows-plattform**
1. Konfigurera inställningarna så att de matchar dem som finns nedan
1. Tryck på **knappen Växla** plattform.\
![build-inställningar](./media/build-settings.png)
1. När Unity har byter plattform stänger du byggpanelen.

## <a name="validate-project-setup"></a>Verifiera projektkonfigurationen

Utför följande steg för att verifiera att projektinställningarna är korrekta.

1. Välj posten **ValidateProject** på menyn **RemoteRendering** i verktygsfältet i Unity-redigeraren.
1. Granska fönstret **ValidateProject för** att se om det finns fel och åtgärda projektinställningarna vid behov.

    ![Projektvalidering i Unity-redigeraren](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Skapa ett skript för att samordna Azure Remote Rendering anslutning och tillstånd

Det finns fyra grundläggande steg för att visa fjärrre renderade modeller, som beskrivs i flödesschemat nedan. Varje fas måste utföras i ordning. Nästa steg är att skapa ett skript som hanterar programtillståndet och fortsätter genom varje obligatorisk fas.

![ARR-stack 0](./media/remote-render-stack-0.png)

1. I fönstret *Projekt,* under **Tillgångar,** skapar du en ny mapp med namnet *RemoteRenderingCore.* I *RemoteRenderingCore skapar* du sedan en annan mapp med *namnet Skript.*

1. Skapa ett [nytt C#-skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) med **namnet RemoteRenderingCoordinator**.
Projektet bör se ut så här:

    ![Projekthierarki](./media/project-structure.png)

    Det här koordinatorskriptet spårar och hanterar fjärrrenderingstillståndet. Observera att en del av den här koden används för att upprätthålla tillstånd, exponera funktioner för andra  komponenter, utlösa händelser och lagra programspecifika data som inte är direkt relaterade till Azure Remote Rendering. Använd koden nedan som utgångspunkt så tar vi upp och implementerar den specifika Azure Remote Rendering kod senare i självstudien.

1. Öppna **RemoteRenderingCoordinator** i kodredigeraren och ersätt hela innehållet med koden nedan:

```cs
// Copyright (c) Microsoft Corporation. All rights reserved.
// Licensed under the MIT License. See LICENSE in the project root for license information.

using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System;
using System.Linq;
using System.Threading.Tasks;
using UnityEngine;
using UnityEngine.Events;

#if UNITY_WSA
using UnityEngine.XR.WSA;
#endif

/// <summary>
/// Remote Rendering Coordinator is the controller for all Remote Rendering operations.
/// </summary>

// Require the GameObject with a RemoteRenderingCoordinator to also have the ARRServiceUnity component
[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRenderingCoordinator : MonoBehaviour
{
    public enum RemoteRenderingState
    {
        NotSet,
        NotInitialized,
        NotAuthorized,
        NoSession,
        ConnectingToExistingRemoteSession,
        ConnectingToNewRemoteSession,
        RemoteSessionReady,
        ConnectingToRuntime,
        RuntimeConnected
    }

    public static RemoteRenderingCoordinator instance;

    // Account
    // RemoteRenderingDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // For most people '<region>' is either 'westus2' or 'westeurope'
    [SerializeField]
    private string remoteRenderingDomain = "westus2.mixedreality.azure.com";
    public string RemoteRenderingDomain
    {
        get => remoteRenderingDomain.Trim();
        set => remoteRenderingDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountDomain = "<enter your account domain here>";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }    

    [SerializeField]
    private string accountKey = "<enter your account key here>";
    public string AccountKey {
        get => accountKey.Trim();
        set => accountKey = value;
    }

    // These settings are important. All three should be set as low as possible, while maintaining a good user experience
    // See the documentation around session management and the technical differences in session VM size
    [Header("New Session Defaults")]

    public RenderingSessionVmSize renderingSessionVmSize = RenderingSessionVmSize.Standard;
    public uint maxLeaseHours = 0;
    public uint maxLeaseMinutes = 20;

    [Header("Other Configuration")]

    [Tooltip("If you have a known active SessionID, you can fill it in here before connecting")]
    [SerializeField]
    private string sessionIDOverride;
    public string SessionIDOverride {
        get => sessionIDOverride.Trim();
        set => sessionIDOverride = value;
    }

    // When Automatic Mode is true, the coordinator will attempt to automatically proceed through the process of connecting and loading a model
    public bool automaticMode = true;

    public event Action RequestingAuthorization;
    public UnityEvent OnRequestingAuthorization = new UnityEvent();

    public event Action AuthorizedChanged;
    public UnityEvent OnAuthorizationChanged = new UnityEvent();
    private bool authorized;
    public bool Authorized
    {
        get => authorized;
        set
        {
            if (value == true) //This is a one-way value, once we're authorized it lasts until the app is shutdown.
            {
                authorized = value;
                AuthorizedChanged?.Invoke();
            }
        }
    }

    public delegate Task<SessionConfiguration> AccountInfoGetter();

    public static AccountInfoGetter ARRCredentialGetter
    {
        private get;
        set;
    }

    private RemoteRenderingState currentCoordinatorState = RemoteRenderingState.NotSet;
    public RemoteRenderingState CurrentCoordinatorState
    {
        get => currentCoordinatorState;
        private set
        {
            if (currentCoordinatorState != value)
            {
                currentCoordinatorState = value;
                Debug.LogFormat(LogType.Log, LogOption.NoStacktrace, null, "{0}", $"State changed to: {currentCoordinatorState}");
                CoordinatorStateChange?.Invoke(currentCoordinatorState);
            }
        }
    }

    public static event Action<RemoteRenderingState> CoordinatorStateChange;

    public static RenderingSession CurrentSession => instance?.ARRSessionService?.CurrentActiveSession;

    private ARRServiceUnity arrSessionService;

    private ARRServiceUnity ARRSessionService
    {
        get
        {
            if (arrSessionService == null)
                arrSessionService = GetComponent<ARRServiceUnity>();
            return arrSessionService;
        }
    }

    private async Task<SessionConfiguration> GetDevelopmentCredentials()
    {
        Debug.LogWarning("Using development credentials! Not recommended for production.");
        return await Task.FromResult(new SessionConfiguration(AccountDomain, RemoteRenderingDomain, AccountId, AccountKey));
    }

    /// <summary>
    /// Keep the last used SessionID, when launching, connect to this session if its available
    /// </summary>
    private string LastUsedSessionID
    {
        get
        {
            if (!string.IsNullOrEmpty(SessionIDOverride))
                return SessionIDOverride;

            if (PlayerPrefs.HasKey("LastUsedSessionID"))
                return PlayerPrefs.GetString("LastUsedSessionID");
            else
                return null;
        }
        set
        {
            PlayerPrefs.SetString("LastUsedSessionID", value);
        }
    }

    public void Awake()
    {
        //Forward events to Unity events
        RequestingAuthorization += () => OnRequestingAuthorization?.Invoke();
        AuthorizedChanged += () => OnAuthorizationChanged?.Invoke();

        //Attach to event
        AuthorizedChanged += RemoteRenderingCoordinator_AuthorizedChanged;

        if (instance == null)
            instance = this;
        else
            Destroy(this);

        CoordinatorStateChange += AutomaticMode;

        CurrentCoordinatorState = RemoteRenderingState.NotInitialized;
    }

    private void RemoteRenderingCoordinator_AuthorizedChanged()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.NotAuthorized)
            return; //This isn't valid from any other state than NotAuthorized


        //We just became authorized to connect to Azure
        InitializeSessionService();
    }

    /// <summary>
    /// Automatic mode attempts to automatically progress through the connection and loading steps. Doesn't handle error states.
    /// </summary>
    /// <param name="currentState">The current state</param>
    private async void AutomaticMode(RemoteRenderingState currentState)
    {
        if (!automaticMode)
            return;

        //Add a small delay for visual effect
        await Task.Delay(1500);
        switch (currentState)
        {
            case RemoteRenderingState.NotInitialized:
                InitializeARR();
                break;
            case RemoteRenderingState.NotAuthorized:
                RequestAuthorization();
                break;
            case RemoteRenderingState.NoSession:
                JoinRemoteSession();
                break;
            case RemoteRenderingState.RemoteSessionReady:
                ConnectRuntimeToRemoteSession();
                break;
        }
    }

    /// <summary>
    /// Initializes ARR, associating the main camera
    /// Note: This must be called on the main Unity thread
    /// </summary>
    public void InitializeARR()
    {
        //Implement me
    }

    /// <summary>
    /// Create a new remote session manager
    /// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
    /// </summary>
    public async void InitializeSessionService()
    {
        //Implement me
    }

    /// <summary>
    /// Trigger the event for checking authorization, respond to this event by prompting the user for authentication
    /// If authorized, set Authorized = true
    /// </summary>
    public void RequestAuthorization()
    {
        RequestingAuthorization?.Invoke();
    }

    public void BypassAuthorization()
    {
        Authorized = true;
    }

    /// <summary>
    /// Attempts to join an existing session or start a new session
    /// </summary>
    public async void JoinRemoteSession()
    {
        //Implement me
    }

    public void StopRemoteSession()
    {
        //Implement me
    }

    private async Task<bool> IsSessionAvailable(string sessionID)
    {
        bool sessionAvailable = false;
        try
        {
            RenderingSessionPropertiesArrayResult result = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
            if (result.ErrorCode == Result.Success)
            {
                RenderingSessionProperties[] properties = result.SessionProperties;
                if (properties != null)
                {
                    sessionAvailable = properties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
                }
            }
            else
            {
                Debug.LogError($"Failed to get current rendering sessions. Error: {result.Context.ErrorMessage}");
            }
        }
        catch (RRException ex)
        {
            Debug.LogError($"Failed to get current rendering sessions. Error: {ex.Message}");
        }
        return sessionAvailable;
    }

    /// <summary>
    /// Connects the local runtime to the current active session, if there's a session available
    /// </summary>
    public void ConnectRuntimeToRemoteSession()
    {
        //Implement me
    }

    public void DisconnectRuntimeFromRemoteSession()
    {
        //Implement me
    }

    /// <summary>
    /// The session must have its runtime pump updated.
    /// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
    /// </summary>
    private void LateUpdate()
    {
        ARRSessionService?.CurrentActiveSession?.Connection?.Update();
    }

    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelPath">The model's path</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Implement me
        return null;
    }

    private async void OnRemoteSessionStatusChanged(ARRServiceUnity caller, RenderingSession session)
    {
        var properties = await session.GetPropertiesAsync();

        switch (properties.SessionProperties.Status)
        {
            case RenderingSessionStatus.Error:
            case RenderingSessionStatus.Expired:
            case RenderingSessionStatus.Stopped:
            case RenderingSessionStatus.Unknown:
                CurrentCoordinatorState = RemoteRenderingState.NoSession;
                break;
            case RenderingSessionStatus.Starting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
                break;
            case RenderingSessionStatus.Ready:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }

    private void OnLocalRuntimeStatusChanged(ConnectionStatus status, Result error)
    {
        switch (status)
        {
            case ConnectionStatus.Connected:
                CurrentCoordinatorState = RemoteRenderingState.RuntimeConnected;
                break;
            case ConnectionStatus.Connecting:
                CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
                break;
            case ConnectionStatus.Disconnected:
                CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
                break;
        }
    }
}
```

## <a name="create-the-azure-remote-rendering-gameobject"></a>Skapa Azure Remote Rendering GameObject

Koordinatorn för fjärråtergivning och dess nödvändiga skript (*ARRServiceUnity*) är båda MonoBehaviours som måste kopplas till ett GameObject i scenen. *ARRServiceUnity-skriptet* tillhandahålls av ARR för att exponera en stor del av ARR:s funktioner för att ansluta till och hantera fjärrsessioner.

1. Skapa ett nytt GameObject i scenen (Ctrl + Skift + N eller *GameObject->Skapa tom*) och ge den **namnet RemoteRenderingCoordinator**.
1. Lägg till *skriptet RemoteRenderingCoordinator* i **RemoteRenderingCoordinator** GameObject.\
![Lägg till komponenten RemoteRenderingCoordinator](./media/add-coordinator-script.png)
1. Bekräfta att *ARRServiceUnity-skriptet,* som visas som *Tjänst* i kontroll, läggs automatiskt till i GameObject. Om du undrar är det här ett resultat som `[RequireComponent(typeof(ARRServiceUnity))]` finns överst i skriptet **RemoteRenderingCoordinator.**
1. Lägg till Azure Remote Rendering autentiseringsuppgifter, din kontodomän och Remote Rendering domän i koordinatorskriptet:\
![Lägg till dina autentiseringsuppgifter](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Initiera Azure Remote Rendering

Nu när vi har ramverket för vår koordinator implementerar vi var och en av de fyra stegen som börjar med **Remote Rendering**.

![ARR-stack 1](./media/remote-render-stack-1.png)

**Initiera talar** Azure Remote Rendering vilket kameraobjekt som ska användas för rendering och förlopp för tillståndsdatorn **till NotAuthorized**. Det innebär att den har initierats men ännu inte har behörighet att ansluta till en session. Eftersom start av en ARR-session medför en kostnad måste vi bekräfta att användaren vill fortsätta.

När du anger tillståndet **NotAuthorized** anropas **CheckAuthorization,** som anropar händelsen **RequestingAuthorization** och avgör vilka kontoautentiseringsuppgifter som ska användas (**AccountInfo** definieras längst upp i klassen och använder de autentiseringsuppgifter som du definierade via Unity Inspector i steget ovan).

   > [!NOTE]
   > Omkompilering av körning stöds inte av ARR. Om du ändrar skriptet och sparar det medan uppspelningsläget är aktivt kan det leda till att Unity fryser och att aktivitetshanteraren måste stängas av. Se alltid till att du har stoppat uppspelningsläget innan du redigerar skripten.

1. Ersätt innehållet i **InitializeARR** och **InitializeSessionService** med den färdiga koden nedan:

 ```cs
/// <summary>
/// Initializes ARR, associating the main camera
/// Note: This must be called on the main Unity thread
/// </summary>
public void InitializeARR()
{
    RemoteManagerUnity.InitializeManager(new RemoteUnityClientInit(Camera.main));

    CurrentCoordinatorState = RemoteRenderingState.NotAuthorized;
}

/// <summary>
/// Create a new remote session manager
/// If the ARRCredentialGetter is set, use it as it, otherwise use the development credentials 
/// </summary>
public async void InitializeSessionService()
{
    if (ARRCredentialGetter == null)
        ARRCredentialGetter = GetDevelopmentCredentials;

    var accountInfo = await ARRCredentialGetter.Invoke();

    ARRSessionService.OnSessionStatusChanged += OnRemoteSessionStatusChanged;

    ARRSessionService.Initialize(accountInfo);

    CurrentCoordinatorState = RemoteRenderingState.NoSession;
}
```

För att gå från **NotAuthorized** till **NoSession** presenterar vi vanligtvis en modal dialogruta för användaren så att de kan välja (och vi gör just det i ett annat kapitel). För tillfället kringgår vi automatiskt auktoriseringskontrollen genom att anropa **ByPassAuthentication** så snart **händelsen RequestingAuthorization** utlöses.

1. Välj **RemoteRenderingCoordinator** GameObject och leta reda på **OnRequestingAuthorization** Unity-händelsen som visas i Kontroll för **komponenten RemoteRenderingCoordinator.**

1. Lägg till en ny händelse genom att trycka på "+" i det nedre högra högra.
1. Dra komponenten till en egen händelse så att den refererar till sig själv.\
![Kringgå autentisering](./media/bypass-authorization-add-event.png)\
1. I listrutan väljer du **RemoteRenderingCoordinator -> BypassAuthorization**.\
![Skärmbild som visar det valda alternativet RemoteRenderingCoordinator.BypassAuthorization.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Skapa eller ansluta till en fjärrsession

Det andra steget är att skapa eller ansluta till en Remote Rendering-session (se [Remote Rendering-sessioner](../../../concepts/sessions.md) för mer information).

![ARR-stack 2](./media/remote-render-stack-2.png)

Fjärrsessionen är den plats där modellerna återges. Metoden **JoinRemoteSession( )** försöker ansluta till en befintlig session, spåras med egenskapen **LastUsedSessionID** eller om det finns ett tilldelat aktivt sessions-ID **på SessionIDOverride**. **SessionIDOverride** är endast avsett för felsökning. Det bör endast användas när du vet att sessionen finns och vill ansluta till den explicit.

Om inga sessioner är tillgängliga skapas en ny session. Att skapa en ny session är dock en tidskrävande åtgärd. Därför bör du försöka skapa sessioner endast när det behövs och återanvända dem när det är möjligt (mer information om hur du hanterar sessioner finns i Commercial [Ready: Sessionspooling,](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) schemaläggning och metodtips).

> [!TIP]
> **StopRemoteSession()** avslutar den aktiva sessionen. För att undvika onödiga avgifter bör du alltid stoppa sessioner när de inte längre behövs.

Tillståndsdatorn fortsätter nu till **antingen ConnectingToNewRemoteSession** eller **ConnectingToExistingRemoteSession,** beroende på tillgängliga sessioner. Om du öppnar en befintlig session eller skapar en ny session utlöses **händelsen ARRSessionService.OnSessionStatusChanged,** vilket kör vår **OnRemoteSessionStatusChanged-metod.** Vi rekommenderar att detta leder till att tillståndsdatorn avancerar till **RemoteSessionReady**.

1. Om du vill ansluta till en ny session ändrar du koden för att ersätta metoderna **JoinRemoteSession( )** och **StopRemoteSession( )** med de ifyllda exemplen nedan:

```cs
/// <summary>
/// Attempts to join an existing session or start a new session
/// </summary>
public async void JoinRemoteSession()
{
    //If there's a session available that previously belonged to us, and it's ready, use it. Otherwise start a new session.
    RenderingSessionProperties joinResult;
    if (await IsSessionAvailable(LastUsedSessionID))
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToExistingRemoteSession;
        joinResult = await ARRSessionService.OpenSession(LastUsedSessionID);
    }
    else
    {
        CurrentCoordinatorState = RemoteRenderingState.ConnectingToNewRemoteSession;
        joinResult = await ARRSessionService.StartSession(new RenderingSessionCreationOptions(renderingSessionVmSize, (int)maxLeaseHours, (int)maxLeaseMinutes));
    }

    if (joinResult.Status == RenderingSessionStatus.Ready || joinResult.Status == RenderingSessionStatus.Starting)
    {
        LastUsedSessionID = joinResult.Id;
    }
    else
    {
        //The session should be ready or starting, if it's not, something went wrong
        await ARRSessionService.StopSession();
        if(LastUsedSessionID == SessionIDOverride)
            SessionIDOverride = "";
        CurrentCoordinatorState = RemoteRenderingState.NoSession;
    }
}

public void StopRemoteSession()
{
    if (ARRSessionService.CurrentActiveSession != null)
    {
        ARRSessionService.CurrentActiveSession.StopAsync();
    }
}
```

Om du vill spara tid genom att återanvända sessioner ska du inaktivera alternativet **Stoppa** session automatiskt i *ARRServiceUnity-komponenten.* Tänk på att detta gör att sessioner körs även när ingen är ansluten till dem. Sessionen kan köras så länge som *MaxLeaseTime* innan den stängs av av servern (värdet för *MaxLeaseTime* kan ändras i Remote Rendering Coordinator under *New Session Defaults*(Standardinställningar för ny session). Å andra sidan, om du automatiskt stänger av varje session när du kopplar från, måste du vänta tills en ny session startas varje gång, vilket kan vara en något längre process.

> [!NOTE]
> Att stoppa en session börjar gälla omedelbart och kan inte ångras. När den har stoppats måste du skapa en ny session med samma startkostnader.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Ansluta den lokala körningen till fjärrsessionen

Därefter måste programmet ansluta sin lokala körning till fjärrsessionen.

![ARR-stack 3](./media/remote-render-stack-3.png)

Programmet måste också lyssna efter händelser om anslutningen mellan körningen och den aktuella sessionen. dessa tillståndsändringar hanteras i **OnLocalRuntimeStatusChanged**. Den här koden förs vidare till **ConnectingToRuntime**. När du har **anslutit i OnLocalRuntimeStatusChanged** fortsätter tillståndet till **RuntimeConnected**. Att ansluta till körningen är det sista tillstånd som koordinatorn säkerhetsproblem med, vilket innebär att programmet är gjort med all gemensam konfiguration och är redo att påbörja det sessionsspecifika arbetet med att läsa in och återge modeller.

 1. Ersätt metoderna **ConnectRuntimeToRemoteSession( ) och** **DisconnectRuntimeFromRemoteSession( )** med de slutförda versionerna nedan.
 1. Det är viktigt att notera Unity-metoden **LateUpdate** och att den uppdaterar den aktuella aktiva sessionen. På så sätt kan den aktuella sessionen skicka/ta emot meddelanden och uppdatera rambufferten med bildrutorna som tas emot från fjärrsessionen. Det är viktigt att ARR fungerar korrekt.

```cs
/// <summary>
/// Connects the local runtime to the current active session, if there's a session available
/// </summary>
public void ConnectRuntimeToRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null)
    {
        Debug.LogError("Not ready to connect runtime");
        return;
    }

    //Connect the local runtime to the currently connected session
    //This session is set when connecting to a new or existing session

    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged += OnLocalRuntimeStatusChanged;
    ARRSessionService.CurrentActiveSession.ConnectAsync(new RendererInitOptions());
    CurrentCoordinatorState = RemoteRenderingState.ConnectingToRuntime;
}

public void DisconnectRuntimeFromRemoteSession()
{
    if (ARRSessionService == null || ARRSessionService.CurrentActiveSession == null || ARRSessionService.CurrentActiveSession.ConnectionStatus != ConnectionStatus.Connected)
    {
        Debug.LogError("Runtime not connected!");
        return;
    }

    ARRSessionService.CurrentActiveSession.Disconnect();
    ARRSessionService.CurrentActiveSession.ConnectionStatusChanged -= OnLocalRuntimeStatusChanged;
    CurrentCoordinatorState = RemoteRenderingState.RemoteSessionReady;
}

/// <summary>
/// The session must have its runtime pump updated.
/// The Connection.Update() will push messages to the server, receive messages, and update the frame-buffer with the remotely rendered content.
/// </summary>
private void LateUpdate()
{
    ARRSessionService?.CurrentActiveSession?.Connection?.Update();
}
```

> [!NOTE]
> Anslutningen av den lokala körningen till en fjärrsession beror på att **Uppdateringen** anropas på den aktiva sessionen. Om du upptäcker att programmet aldrig går förbi tillståndet **ConnectingToRuntime** kontrollerar du att du **anropar Uppdatera** regelbundet på den aktiva sessionen.

## <a name="load-a-model"></a>Läsa in en modell

Med den nödvändiga grunden på plats är du redo att läsa in en modell i fjärrsessionen och börja ta emot bildrutor.

![Diagram som visar processflödet för att förbereda för att läsa in och visa en modell.](./media/remote-render-stack-4.png)

Metoden **LoadModel** är utformad för att acceptera en modellsökväg, förloppshanterare och överordnad transformering. Dessa argument används för att läsa in en modell i fjärrsessionen, uppdatera användaren om inläsningsförloppet och orientera den fjärrregivna modellen baserat på den överordnade transformeringen.

1. Ersätt **metoden LoadModel** helt med koden nedan:

    ```cs
    /// <summary>
    /// Loads a model into the remote session for rendering
    /// </summary>
    /// <param name="modelName">The model's path</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns>An awaitable Remote Rendering Entity</returns>
    public async Task<Entity> LoadModel(string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

Koden ovan utför följande steg:

1. Skapa en [fjärrentitet](../../../concepts/entities.md).
1. Skapa ett lokalt GameObject som representerar fjärrentiteten.
1. Konfigurera det lokala GameObject att synkronisera dess tillstånd (d.v.s. Transformera) till den fjärranslutna entiteten varje bildruta.
1. Ange ett namn och lägg till [**en WorldAnchor**](https://docs.unity3d.com/550/Documentation/ScriptReference/VR.WSA.WorldAnchor.html) för att underlätta stabiliseringen.
1. Läs in modelldata från Blob Storage till fjärrentiteten.
1. Returnera den överordnade entiteten för senare referens.

## <a name="view-the-test-model"></a>Visa testmodellen

Nu har vi all kod som krävs för att visa en fjärrre renderad modell. Alla fyra av de nödvändiga stegen för fjärrrendering är klara. Nu måste vi lägga till lite kod för att starta modellbelastningsprocessen.

![ARR-stack 4](./media/remote-render-stack-5.png)

1. Lägg till följande kod i **klassen RemoteRenderingCoordinator,** precis under **metoden LoadModel:**

    ```cs
    private bool loadingTestModel = false;
    [ContextMenu("Load Test Model")]
    public async void LoadTestModel()
    {
        if(CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if(loadingTestModel)
        {
            Debug.Log("Test model already loading or loaded!");
            return;
        }
        loadingTestModel = true;
    
        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("TestModelParent");
        testParent.transform.position = new Vector3(0f, 0f, 3f);
    
        // The 'built in engine path' is a special path that references a test model built into Azure Remote Rendering.
        await LoadModel("builtin://Engine", testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```
    
    Den här koden skapar ett GameObject som fungerar som överordnat till testmodellen. Sedan anropas **metoden LoadModel** för att läsa in modellen "builtin://Engine", som är en tillgång som är inbyggd i Azure Remote Rendering för att testa renderingen.

1. Spara koden.
1. Tryck på knappen Spela upp i Unity-redigeraren för att starta processen med att ansluta Azure Remote Rendering och skapa en ny session.
1. Du kommer inte att se så mycket i spelvyn, men konsolen visar programmets status som ändras. Den kommer troligen att `ConnectingToNewRemoteSession` gå vidare till och stanna kvar där, eventuellt i upp till fem minuter.
1. Välj **RemoteRenderingCoordinator** GameObject för att se dess bifogade skript i kontrollanten. Se hur **tjänstkomponenten** uppdateras allt eftersom den går igenom initierings- och anslutningsstegen.
1. Övervaka konsolens utdata – väntar på att tillståndet ska ändras till **RuntimeAnsluten**.
1. När körningen är ansluten högerklickar du på **RemoteRenderingCoordinator** i kontrollanten för att visa snabbmenyn. Klicka sedan på alternativet **Belastningstestmodell** på snabbmenyn, som läggs till av `[ContextMenu("Load Test Model")]` den del av koden ovan.

    ![Läs in från snabbmenyn](./media/load-test-model.png)

1. Titta på konsolen för utdata från **ProgressHandler som** vi skickade till **metoden LoadModel.**
1. Se den fjärrregivna modellen!

> [!NOTE]
> Fjärrmodellen visas aldrig i scenvyn, endast i spelvyn. Det beror på att ARR fjärrreenderar bildrutorna specifikt för kameran i spelvyn och inte är medveten om redigerarkameran (används för att återge scenvyn).

## <a name="next-steps"></a>Nästa steg

![Modellen har lästs in](./media/test-model-rendered.png)

Grattis! Du har skapat ett grundläggande program som kan visa fjärrregivna modeller med hjälp av Azure Remote Rendering. I nästa självstudie ska vi integrera MRTK och importera våra egna modeller.

> [!div class="nextstepaction"]
> [Nästa: Gränssnitt och anpassade modeller](../custom-models/custom-models.md)
