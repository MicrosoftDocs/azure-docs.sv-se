---
title: Visa en modell som renderats på distans
description: I självstudien "Hello World" i Azure Remote rendering visas en modell som återges via fjärr anslutning av Azure
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3596dd7b2cd01e80d9cf4db9bbc179048e7fcf
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105035973"
---
# <a name="tutorial-viewing-a-remotely-rendered-model"></a>Självstudie: Visa en fjärrrenderad modell

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Etablera en ARR-instans (Azure Remote rendering)
> * Skapa och stoppa en åter givnings session
> * Återanvänd en befintlig åter givnings session
> * Ansluta och koppla från sessioner
> * Läs in modeller till en åter givnings session

## <a name="prerequisites"></a>Förutsättningar

För den här självstudien behöver du:

* En aktiv Azure-prenumeration med betala per användning [skapa ett konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)
* Windows SDK 10.0.18362.0 [(Hämta)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Den senaste versionen av Visual Studio 2019 [(Hämta)](https://visualstudio.microsoft.com/vs/older-downloads/)
* GIT [(nedladdning)](https://git-scm.com/downloads)
* Unity (se [system krav](../../../overview/system-requirements.md#unity) för versioner som stöds)
* Mellanliggande kunskap om Unit och C#-språket (till exempel: skapa skript och objekt, använda prefabs, konfigurera Unity-händelser osv.)

## <a name="provision-an-azure-remote-rendering-arr-instance"></a>Etablera en ARR-instans (Azure Remote rendering)

För att få åtkomst till tjänsten Azure Remote rendering måste du först [skapa ett konto](../../../how-tos/create-an-account.md#create-an-account).

## <a name="create-a-new-unity-project"></a>Skapa ett nytt Unity-projekt

> [!TIP]
> [Databasen arr samples](https://github.com/Azure/azure-remote-rendering) innehåller ett projekt med alla självstudier som har slutförts. den kan användas som referens. Leta i *Unity\Tutorial-Complete* efter hela Unity-projektet.

Skapa ett nytt projekt från Unity Hub.
I det här exemplet antar vi att projektet skapas i en mapp med namnet **RemoteRendering**.

:::image type="content" source="./media/unity-new-project.PNG" alt-text="Nytt Unity-projekt":::

## <a name="include-the-azure-remote-rendering-package"></a>Ta med Azure Remote rendering-paketet

[Följ anvisningarna](../../../how-tos/unity/install-remote-rendering-unity-package.md) för hur du lägger till Azure Remote rendering-paketet i ett Unity-projekt.


## <a name="configure-the-camera"></a>Konfigurera kameran

1. Välj den **primära Camera** -noden.

1. Öppna snabb menyn genom att högerklicka på *Transform* -komponenten och välj alternativet för **återställning** :

    ![Återställ kamera omvandling](./media/camera-reset-transform.png)

1. Ange **Rensa flaggor** till *solid färg*

1. Ange **bakgrund** till *svart* (#000000), med helt transparent (0) alpha (a)

    ![Färg hjul](./media/color-wheel-black.png)

1. Ange **klippnings plan** till *nära = 0,3* och *mycket = 20*. Det innebär att rendera kommer att vara en klipps geometri som är närmare än 30 cm eller längre än 20 meter.

    ![Egenskaper för Unity-kamera](./media/camera-properties.png)

## <a name="adjust-the-project-settings"></a>Justera projekt inställningarna

1. Öppna *redigera > projekt inställningar...*
1. Välj **kvalitet** i den vänstra List menyn
1. Ändra **standard kvalitets nivån** för alla plattformar till *låg*. Med den här inställningen aktive ras effektivare åter givning av lokalt innehåll och det påverkar inte kvaliteten på fjärrrenderat innehåll.

    ![Ändra inställningar för projekt kvalitet](./media/settings-quality.png)

1. Välj **grafik** från den vänstra List menyn
1. Ändra inställningen för inställnings **pipelinen för skript åter givning** till *HybridRenderingPipeline*. \
    ![Skärm bild som pekar på den plats där du ändrar inställningen för inställnings pipelinen för skript åter givning till HybridRenderingPipeline.](./media/settings-graphics-render-pipeline.png)\
    Ibland fyller användar gränssnittet inte listan över tillgängliga pipelin typer från paketen. Om detta inträffar måste *HybridRenderingPipeline* -till gången dras till fältet manuellt: \
    ![Ändra inställningar för projekt grafik](./media/hybrid-rendering-pipeline.png)

    > [!NOTE]
    > Om du inte kan dra och släppa *HybridRenderingPipeline* -till gången i fältet rendera pipeliner (kanske eftersom fältet inte finns!) ser du till att paket konfigurationen innehåller `com.unity.render-pipelines.universal` paketet.

1. Välj **spelare** på den vänstra List menyn
1. Välj fliken **universell Windows-plattform inställningar** som visas som en Windows-ikon.
1. Ändra **XR-inställningarna** så att de stöder Windows Mixed Reality enligt nedan:
    1. Aktivera **virtuell verklighet som stöds**
    1. Tryck på knappen "+" och Lägg till **Windows Mixed Reality**
    1. Ange **djup formatet** *16-bitars djup*
    1. Se till att **djup delning av buffert** är aktiverat
    1. Ställ in **stereo åter givnings läge** på *en enskild pass instans*

    ![inställningar för spelare](./media/xr-player-settings.png)

1. I samma fönster, över **XR-inställningar**, expanderar du **publicerings inställningar**
1. Rulla ned till **funktioner** och välj:
    * **InternetClient**
    * **InternetClientServer**
    * **SpatialPerception**
    * **PrivateNetworkClientServer** (*valfritt*). Välj det här alternativet om du vill ansluta enhetens fjärrfelsökning till enheten.

1. Aktivera **Holographic** och **Desktop** under **stödda enhets familjer**
1. Stäng eller docka panelen **projekt inställningar**
1. Öppna *fil->Bygg inställningar*
1. Välj **universell Windows-plattform**
1. Konfigurera inställningarna så att de matchar de som finns nedan
1. Tryck på knappen **Växla plattform** . \
![Build-inställningar](./media/build-settings.png)
1. När Unity ändrar plattformar stänger du panelen Bygg.

## <a name="validate-project-setup"></a>Verifiera projekt konfiguration

Utför följande steg för att kontrol lera att projekt inställningarna är korrekta.

1. Välj posten **ValidateProject** på **RemoteRendering** -menyn i verktygsfältet i Unity Editor.
1. Granska **ValidateProject** -fönstret och leta efter fel och korrigera projekt inställningar där det behövs.

    ![Project-validering av Unit Editor](./media/remote-render-unity-validation.png)

## <a name="create-a-script-to-coordinate-azure-remote-rendering-connection-and-state"></a>Skapa ett skript för att koordinera anslutning och tillstånd för Azure Remote rendering

Det finns fyra grundläggande steg för att Visa fjärranslutna modeller som beskrivs i flödesschemat nedan. Varje steg måste utföras i ordning. Nästa steg är att skapa ett skript som hanterar program tillståndet och fortsätter med varje steg som krävs.

![ARR-stack 0](./media/remote-render-stack-0.png)

1. I fönstret *projekt* , under **till gångar**, skapar du en ny mapp med namnet *RemoteRenderingCore*. Skapa sedan en annan mapp med namnet *skript* i *RemoteRenderingCore*.

1. Skapa ett [nytt C#-skript](https://docs.unity3d.com/Manual/CreatingAndUsingScripts.html) med namnet **RemoteRenderingCoordinator**.
Projektet bör se ut så här:

    ![Projekthierarkin](./media/project-structure.png)

    Detta koordinator skript spårar och hanterar status för fjärrrendering. I anmärkning används en del av den här koden för att bibehålla tillstånd, exponera funktioner för andra komponenter, utlösa händelser och lagra programspecifika data som inte är *direkt* relaterade till Azure-fjärrrendering. Använd koden nedan som utgångs punkt och vi kommer att adressera och implementera den aktuella koden för Azures fjärrrendering senare i självstudien.

1. Öppna **RemoteRenderingCoordinator** i kod redigeraren och ersätt hela innehållet med koden nedan:

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

    // AccountDomain must be '<region>.mixedreality.azure.com' - if no '<region>' is specified, connections will fail
    // The list of regions is available at https://docs.microsoft.com/azure/remote-rendering/reference/regions
    [SerializeField]
    private string accountDomain = "westus2.mixedreality.azure.com";
    public string AccountDomain
    {
        get => accountDomain.Trim();
        set => accountDomain = value;
    }

    [Header("Development Account Credentials")]
    [SerializeField]
    private string accountId = "<enter your account id here>";
    public string AccountId {
        get => accountId.Trim();
        set => accountId = value;
    }

    [SerializeField]
    private string accountAuthenticationDomain = "<enter your account authentication domain here>";
    public string AccountAuthenticationDomain
    {
        get => accountAuthenticationDomain.Trim();
        set => accountAuthenticationDomain = value;
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
                Debug.Log($"State changed to: {currentCoordinatorState}");
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
        return await Task.FromResult(new SessionConfiguration(AccountAuthenticationDomain, AccountDomain, AccountId, AccountKey));
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
        var allSessions = await ARRSessionService.Client.GetCurrentRenderingSessionsAsync();
        return allSessions.SessionProperties.Any(x => x.Id == sessionID && (x.Status == RenderingSessionStatus.Ready || x.Status == RenderingSessionStatus.Starting));
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

## <a name="create-the-azure-remote-rendering-gameobject"></a>Skapa Azure-GameObject för fjärrrendering

*ARRServiceUnity*(Remote rendering Coordinator och det nödvändiga skriptet) är båda MonoBehaviours som måste kopplas till en GameObject i scenen. *ARRServiceUnity* -skriptet tillhandahålls av arr för att visa mycket av arr: s funktioner för att ansluta till och hantera fjärrsessioner.

1. Skapa en ny GameObject i scenen (Ctrl + Shift + N eller *GameObject->skapa tom*) och ge den namnet **RemoteRenderingCoordinator**.
1. Lägg till *RemoteRenderingCoordinator* -skriptet till **RemoteRenderingCoordinator** -GameObject. \
![Lägg till RemoteRenderingCoordinator-komponent](./media/add-coordinator-script.png)
1. Bekräfta att *ARRServiceUnity* -skriptet, som visas som *tjänst* i kontrollanten, automatiskt läggs till i GameObject. Om du undrar är detta ett resultat som har `[RequireComponent(typeof(ARRServiceUnity))]` högst upp i **RemoteRenderingCoordinator** -skriptet.
1. Lägg till dina autentiseringsuppgifter för Azure-fjärrrendering, din konto domän för autentisering och konto domänen i koordinator skriptet: \
![Lägg till dina autentiseringsuppgifter](./media/configure-coordinator-script.png)

## <a name="initialize-azure-remote-rendering"></a>Initiera Azure Remote rendering

Nu när vi har ramverket för vår koordinator kommer vi att implementera var och en av de fyra stegen som börjar med att **initiera Fjärrrendering**.

![ARR-stack 1](./media/remote-render-stack-1.png)

**Initiera** meddelar Azure Remote rendering vilket Camera-objekt som ska användas för åter givning och försätter tillstånds datorn i **NotAuthorized**. Det innebär att den är initierad men ännu inte auktoriserad för att ansluta till en session. Eftersom en ARR-session påbörjar en kostnad måste vi bekräfta att användaren vill fortsätta.

När du anger **NotAuthorized** -tillstånd anropas **CheckAuthorization** , som anropar händelsen **RequestingAuthorization** och avgör vilka kontoautentiseringsuppgifter som ska användas (**AccountInfo** definieras nära klassens överkant och använder de autentiseringsuppgifter som du definierade via enhets kontrollen i steget ovan).

   > [!NOTE]
   > Omkompilering av Runtime stöds inte av ARR. Att ändra skriptet och spara det medan uppspelnings läget är aktivt kan resultera i uppsatta inlåsningar och måste tvingas stänga av med aktivitets hanteraren. Se alltid till att du har stoppat uppspelnings läget innan du redigerar skripten.

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

För att kunna fortsätta från **NotAuthorized** till **nosession**, visar vi vanligt vis en modal dialog ruta till användaren så att de kan välja (och vi ska göra det i ett annat kapitel). För närvarande kringgår vi automatiskt verifierings kontrollen genom att anropa **ByPassAuthentication** så snart händelsen **RequestingAuthorization** utlöses.

1. Välj **RemoteRenderingCoordinator** -GameObject och leta upp **OnRequestingAuthorization** Unity-händelsen som visas i kontrollanten för **RemoteRenderingCoordinator** -komponenten.

1. Lägg till en ny händelse genom att trycka på "+" längst ned till höger.
1. Dra komponenten till en egen händelse för att referera till sig själv. \
![Kringgå autentisering](./media/bypass-authorization-add-event.png)\
1. I list rutan väljer du **RemoteRenderingCoordinator-> BypassAuthorization**. \
![Skärm bild som visar det valda alternativet RemoteRenderingCoordinator. BypassAuthorization.](./media/bypass-authorization-event.png)

## <a name="create-or-join-a-remote-session"></a>Skapa eller ansluta till en fjärran sluten session

Det andra steget är att skapa eller ansluta till en session med fjärrrendering (se [fjärrstyrda sessioner](../../../concepts/sessions.md) för mer information).

![ARR stack 2](./media/remote-render-stack-2.png)

Fjärrsessionen är den plats där modellerna återges. Metoden **JoinRemoteSession ()** försöker ansluta till en befintlig session, spåras med egenskapen **LastUsedSessionID** eller om det finns ett tilldelat aktivt sessions-ID på **SessionIDOverride**. **SessionIDOverride** är endast avsedd för dina fel söknings ändamål, den bör endast användas när du vet att sessionen finns och vill ansluta till den explicit.

Om inga sessioner är tillgängliga skapas en ny session. Att skapa en ny session är dock en tids krävande åtgärd. Därför bör du endast försöka skapa sessioner när det behövs och återanvända dem när det är möjligt (se att det [är kommersiellt klart: sessioner, schemaläggning och bästa praxis](../commercial-ready/commercial-ready.md#fast-startup-time-strategies) för mer information om att hantera sessioner).

> [!TIP]
> **StopRemoteSession ()** kommer att avsluta den aktiva sessionen. För att undvika onödiga avgifter bör du alltid stoppa sessioner när de inte längre behövs.

Tillstånds datorn kommer nu att förloppet till antingen **ConnectingToNewRemoteSession** eller **ConnectingToExistingRemoteSession**, beroende på tillgängliga sessioner. Om du öppnar en befintlig session eller skapar en ny session utlöses händelsen **ARRSessionService. OnSessionStatusChanged** , vilket gör att vår **OnRemoteSessionStatusChanged** -metod körs. Vi rekommenderar att du fortsätter att försätta tillstånds datorn på **RemoteSessionReady**.

1. Om du vill ansluta till en ny session ändrar du koden för att ersätta metoderna **JoinRemoteSession ()** och **StopRemoteSession ()** med de slutförda exemplen nedan:

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

Om du vill spara tid genom att återanvända sessioner ska du inaktivera alternativet **auto-stop-sessionen** i *ARRServiceUnity* -komponenten. Tänk på att det inte går att köra sessioner, även om det inte finns någon anslutning till dem. Din session kan köras så länge din *MaxLeaseTime* innan den stängs av på servern (värdet för *MaxLeaseTime* kan ändras i koordinatorn för Fjärrrendering under *nya sessioner standardvärden*). Om du å andra sidan stänger av varje session vid från koppling måste du vänta på att en ny session ska startas varje gång, vilket kan vara en ganska lång process.

> [!NOTE]
> Att stoppa en session börjar gälla omedelbart och kan inte återställas. När du har stoppat måste du skapa en ny session med samma start kostnad.

## <a name="connect-the-local-runtime-to-the-remote-session"></a>Ansluta den lokala körnings miljön till fjärrsessionen

Därefter måste programmet ansluta sin lokala körning till fjärrsessionen.

![ARR-stack 3](./media/remote-render-stack-3.png)

Programmet måste också lyssna efter händelser om anslutningen mellan körningen och den aktuella sessionen. dessa tillstånds ändringar hanteras i **OnLocalRuntimeStatusChanged**. Den här koden förflyttar oss vårt tillstånd till **ConnectingToRuntime**. När den är ansluten i **OnLocalRuntimeStatusChanged** förflyttas statusen till **RuntimeConnected**. Anslutning till körnings miljön är det sista tillstånd som koordinatorn tar sig själv med, vilket innebär att programmet görs med all gemensam konfiguration och är redo att påbörja det sessionsbaserade arbetet med att läsa in och återge modeller.

 1. Ersätt metoderna **ConnectRuntimeToRemoteSession ()** och **DisconnectRuntimeFromRemoteSession ()** med de slutförda versionerna nedan.
 1. Det är viktigt att anteckna enhets metoden **LateUpdate** och att den uppdaterar den aktuella aktiva sessionen. Detta gör att den aktuella sessionen kan skicka/ta emot meddelanden och uppdatera buffertens buffert med de ramar som tas emot från fjärrsessionen. Det är viktigt att ARR fungerar korrekt.

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
> Att ansluta den lokala körningen till en fjärrsession beror på att **uppdateringen** anropas för den aktiva sessionen. Om du upptäcker att ditt program aldrig förfaller förbi **ConnectingToRuntime** -läget kan du se till att du regelbundet anropar **uppdatering** på den aktiva sessionen.

## <a name="load-a-model"></a>Läs in en modell

Med den nödvändiga grunden på plats är du redo att läsa in en modell i fjärrsessionen och börja ta emot ramar.

![Diagram som visar process flödet för att förbereda för att läsa in och visa en modell.](./media/remote-render-stack-4.png)

Metoden **LoadModel** är utformad för att acceptera en modell Sök väg, en förlopps hanterare och en överordnad transformering. Dessa argument används för att läsa in en modell i fjärrsessionen, uppdatera användaren vid inläsnings förloppet och orientera den fjärranslutna åter givnings modellen baserat på överordnad transformering.

1. Ersätt **LoadModel** -metoden helt med koden nedan:

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

1. Skapa en [fjärran sluten entitet](../../../concepts/entities.md).
1. Skapa en lokal GameObject som representerar den fjärranslutna entiteten.
1. Konfigurera den lokala GameObject för att synkronisera dess tillstånd (t. ex. Transform) till fjärrentiteten varje bild ruta.
1. Ange ett namn och Lägg till en [**WorldAnchor**](https://docs.unity3d.com/ScriptReference/XR.WSA.WorldAnchor.html) för att under lätta stabiliseringen.
1. Läs in modell data från Blob Storage till den fjärranslutna entiteten.
1. Returnera den överordnade entiteten, för senare referens.

## <a name="view-the-test-model"></a>Visa test modellen

Nu har vi all kod som krävs för att visa en fjärrrenderad modell, och alla fyra nödvändiga steg för fjärrrendering har slutförts. Nu måste vi lägga till en liten kod för att starta processen för modell inläsning.

![ARR-stack 4](./media/remote-render-stack-5.png)

1. Lägg till följande kod i **RemoteRenderingCoordinator** -klassen, strax under metoden **LoadModel** är fin:

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
    
    Den här koden skapar en GameObject som fungerar som överordnad test modell. Sedan anropar den **LoadModel** -metoden för att läsa in modellen "Builtin://Engine", som är en till gång inbyggd i Azure-fjärrrendering för att kunna testa åter givningen.

1. Spara koden.
1. Tryck på uppspelnings knappen i Unity-redigeraren för att starta processen med att ansluta till Azures fjärrrendering och skapa en ny session.
1. Det går inte att se mycket i spelläget, men konsolen visar status för programmet som ändras. Det kommer sannolikt att `ConnectingToNewRemoteSession` förskjutas och stanna kvar där, eventuellt i upp till fem minuter.
1. Välj **RemoteRenderingCoordinator** -GameObject för att se dess kopplade skript i kontrollanten. Se uppdateringen av tjänst komponenten när den fortskrider genom initierings **-** och anslutnings stegen.
1. Övervaka konsolens utdata – väntar på att tillstånd ska ändras till **RuntimeConnected**.
1. När körningen är ansluten högerklickar du på **RemoteRenderingCoordinator** i kontrollanten för att Visa snabb menyn. Klicka sedan på alternativet **Läs in test modell** i snabb menyn som har lagts till av en `[ContextMenu("Load Test Model")]` del av vår kod ovan.

    ![Läs in från snabb menyn](./media/load-test-model.png)

1. Titta på konsolen för utdata från den **ProgressHandler** som vi skickade till **LoadModel** -metoden.
1. Se den fjärranslutna modellen!

> [!NOTE]
> Fjärrmodellen visas aldrig i vyn scen, bara i vyn spel. Detta beror på att ARR återger ramar på distans, särskilt för spelets perspektiv, och är inte medvetna om redigerings kameran (används för att rendera scen visningen).

## <a name="next-steps"></a>Nästa steg

![Inläst modell](./media/test-model-rendered.png)

Grattis! Du har skapat ett grundläggande program som kan visa fjärranslutna modeller med Azure fjärrrendering. I nästa självstudie kommer vi att integrera MRTK och importera våra egna modeller.

> [!div class="nextstepaction"]
> [Nästa: gränssnitt och anpassade modeller](../custom-models/custom-models.md)
