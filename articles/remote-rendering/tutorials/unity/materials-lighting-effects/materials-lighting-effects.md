---
title: Finjustera material, belysning och effekter
description: Ändra modell material och belysning. Lägg till ytterligare effekter som dispositions-och klipp plan.
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 8eb73fcfde7e294896a12289486ff71794a00ae6
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591726"
---
# <a name="tutorial-refining-materials-lighting-and-effects"></a>Självstudie: förfina material, belysning och effekter

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Markerings-och dispositions modeller och modell komponenter
> * Använd olika material för modeller
> * Segmentera genom modeller med klipp plan
> * Lägg till enkla animeringar för fjärrrenderade objekt

## <a name="prerequisites"></a>Förutsättningar

* Den här självstudien bygger på [Självstudier: manipulera modeller](..\manipulate-models\manipulate-models.md).

## <a name="highlighting-and-outlining"></a>Markera och disponera

Att tillhandahålla visuell feedback till användaren är en viktig del av användar upplevelsen i alla program. Azure fjärrrendering tillhandahåller visuella feedback-mekanismer genom [hierarkiska tillstånds åsidosättningar](../../../overview/features/override-hierarchical-state.md). Åsidosättningar av hierarkiskt tillstånd implementeras med komponenter som är kopplade till lokala modell instanser. Vi har lärt dig hur du skapar de här lokala instanserna i [synkroniseringen av Fjärrobjektets graf till Unity-hierarkin](../manipulate-models/manipulate-models.md#synchronizing-the-remote-object-graph-into-the-unity-hierarchy).

Först skapar vi en omslutning runt [**HierarchicalStateOverrideComponent**](/dotnet/api/microsoft.azure.remoterendering.hierarchicalstateoverridecomponent) -komponenten. **HierarchicalStateOverrideComponent** är det lokala skript som styr åsidosättningarna på den fjärranslutna entiteten. [**Självstudiernas till gångar**](../custom-models/custom-models.md#import-assets-used-by-this-tutorial) innehåller en abstrakt basklass som heter **BaseEntityOverrideController**, som vi utökar för att skapa omslutningen.

1. Skapa ett nytt skript med namnet **EntityOverrideController** och ersätt innehållet med följande kod:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class EntityOverrideController : BaseEntityOverrideController
    {
        public override event Action<HierarchicalStates> FeatureOverrideChange;

        private ARRHierarchicalStateOverrideComponent localOverride;
        public override ARRHierarchicalStateOverrideComponent LocalOverride
        {
            get
            {
                if (localOverride == null)
                {
                    localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();
                    if (localOverride == null)
                    {
                        localOverride = gameObject.AddComponent<ARRHierarchicalStateOverrideComponent>();
                    }

                    var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();

                    if (remoteStateOverride == null)
                    {
                        // if there is no HierarchicalStateOverrideComponent on the remote side yet, create one
                        localOverride.Create(RemoteManagerUnity.CurrentSession);
                    }
                    else
                    {
                        // otherwise, bind our local stateOverride component to the remote component
                        localOverride.Bind(remoteStateOverride);

                    }
                }
                return localOverride;
            }
        }

        private RemoteEntitySyncObject targetEntity;
        public override RemoteEntitySyncObject TargetEntity
        {
            get
            {
                if (targetEntity == null)
                    targetEntity = gameObject.GetComponent<RemoteEntitySyncObject>();
                return targetEntity;
            }
        }

        private HierarchicalEnableState ToggleState(HierarchicalStates feature)
        {
            HierarchicalEnableState setToState = HierarchicalEnableState.InheritFromParent;
            switch (LocalOverride.RemoteComponent.GetState(feature))
            {
                case HierarchicalEnableState.ForceOff:
                case HierarchicalEnableState.InheritFromParent:
                    setToState = HierarchicalEnableState.ForceOn;
                    break;
                case HierarchicalEnableState.ForceOn:
                    setToState = HierarchicalEnableState.InheritFromParent;
                    break;
            }

            return SetState(feature, setToState);
        }

        private HierarchicalEnableState SetState(HierarchicalStates feature, HierarchicalEnableState enableState)
        {
            if (GetState(feature) != enableState) //if this is actually different from the current state, act on it
            {
                LocalOverride.RemoteComponent.SetState(feature, enableState);
                FeatureOverrideChange?.Invoke(feature);
            }

            return enableState;
        }

        public override HierarchicalEnableState GetState(HierarchicalStates feature) => LocalOverride.RemoteComponent.GetState(feature);

        public override void ToggleHidden() => ToggleState(HierarchicalStates.Hidden);

        public override void ToggleSelect() => ToggleState(HierarchicalStates.Selected);

        public override void ToggleSeeThrough() => ToggleState(HierarchicalStates.SeeThrough);

        public override void ToggleTint(Color tintColor = default)
        {
            if (tintColor != default) LocalOverride.RemoteComponent.TintColor = tintColor.toRemote();
            ToggleState(HierarchicalStates.UseTintColor);
        }

        public override void ToggleDisabledCollision() => ToggleState(HierarchicalStates.DisableCollision);

        public override void RemoveOverride()
        {
            var remoteStateOverride = TargetEntity.Entity.FindComponentOfType<HierarchicalStateOverrideComponent>();
            if (remoteStateOverride != null)
            {
                remoteStateOverride.Destroy();
            }

            if (localOverride == null)
                localOverride = gameObject.GetComponent<ARRHierarchicalStateOverrideComponent>();

            if (localOverride != null)
            {
                Destroy(localOverride);
            }
        }
    }
    ```

**LocalOverride** huvud jobb är att skapa en länk mellan sig själv och dess `RemoteComponent` . **LocalOverride** gör det möjligt för oss att ange tillstånds flaggor på den lokala komponenten som är kopplade till den fjärranslutna entiteten. Åsidosättningarna och deras tillstånd beskrivs på sidan [hierarkiska tillstånds åsidosättningar](../../../overview/features/override-hierarchical-state.md) . 

Den här implementeringen växlar bara ett tillstånd i taget. Det är dock helt möjligt att kombinera flera åsidosättningar på enskilda entiteter och att skapa kombinationer på olika nivåer i hierarkin. Om du t. ex. kombinerar `Selected` och `SeeThrough` på en enskild komponent så ger den en disposition när du också gör den transparent. Eller, om du ställer in `Hidden` `ForceOn` en underordnad enhets åsidosättning för att `Hidden` `ForceOff` dölja allting förutom underordnat objekt.

Om du vill tillämpa tillstånd för entiteter kan vi ändra **RemoteEntityHelper** som skapats tidigare.

1. Ändra **RemoteEntityHelper** -klassen för att implementera den abstrakta klassen **BaseRemoteEntityHelper** . Den här ändringen gör att du kan använda en visare som finns i **Självstudiernas till gångar**. Den bör se ut så här när den ändras:

    ```cs
    public class RemoteEntityHelper : BaseRemoteEntityHelper
    ```

2. Åsidosätt abstrakta metoder med följande kod:

    ```cs
    public override BaseEntityOverrideController EnsureOverrideComponent(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent == null)
            overrideComponent = entityGameObject.AddComponent<EntityOverrideController>();
        return overrideComponent;
    }

    public override HierarchicalEnableState GetState(Entity entity, HierarchicalStates feature)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        return overrideComponent.GetState(feature);
    }

    public override void ToggleHidden(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void ToggleSelect(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSelect();
    }

    public override void ToggleSeeThrough(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleSeeThrough();
    }

    public Color TintColor = new Color(0.0f, 1.0f, 0.0f, 0.1f);
    public override void ToggleTint(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleTint(TintColor);
    }

    public override void ToggleDisableCollision(Entity entity)
    {
        var overrideComponent = EnsureOverrideComponent(entity);
        overrideComponent.ToggleHidden();
    }

    public override void RemoveOverrides(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var overrideComponent = entityGameObject.GetComponent<EntityOverrideController>();
        if (overrideComponent != null)
        {
            overrideComponent.RemoveOverride();
            Destroy(overrideComponent);
        }
    }
    ```

Den här koden ser till att en **EntityOverrideController** -komponent läggs till i målentiteten och anropar sedan en av växlings metoderna. Om du vill kan du, på **TestModel** -GameObject, anropa dessa hjälp metoder genom att lägga till **RemoteEntityHelper** som en motringning till `OnRemoteEntityClicked` händelsen i **RemoteRayCastPointerHandler** -komponenten.

![Pekare återanrop](./media/pointer-event-callbacks.png)

Nu när de här skripten har lagts till i modellen, när de är anslutna till körnings miljön, ska **AppMenu** -kontrollanten ha ytterligare gränssnitt aktiverade för att interagera med **EntityOverrideController** -skriptet. Kolla in menyn **modell verktyg** om du vill se de olåsta visnings kontrol Lanterna.

I det här läget bör dina **TestModel** -GameObject-komponenter se ut ungefär så här:

![Testa modellen med ytterligare skript](./media/test-model-updated.png)

Här är ett exempel på Stacking-åsidosättningar på en enskild enhet. Vi använde `Select` och `Tint` för att ge både dispositioner och färger:

![Testa modell färgs ton Välj](./media/select-tint-test-model.png)

## <a name="cut-planes"></a>Klippa ut planer

[Klipp ut plan](../../../overview/features/cut-planes.md) är en funktion som kan läggas till i en fjärran sluten entitet. Oftast skapar du en ny fjärran sluten entitet som inte är kopplad till några nät data som ska innehålla den klippta plan komponenten. Positionen och orienteringen för det klippta planet bestäms av positionen och orienteringen för den fjärranslutna entitet som den är kopplad till.

Vi skapar ett skript som automatiskt skapar en fjärran sluten entitet, lägger till en klipp Plans komponent och synkroniserar transformeringen av ett lokalt objekt med entiteten klipp ut plan. Sedan kan vi använda **CutPlaneViewController** för att omsluta det avskurna planet i ett gränssnitt som gör det möjligt för oss att ändra det.

1. Skapa ett nytt skript med namnet **RemoteCutPlane** och ersätt dess kod med koden nedan:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    public class RemoteCutPlane : BaseRemoteCutPlane
    {
        public Color SliceColor = new Color(0.5f, 0f, 0f, .5f);
        public float FadeLength = 0.01f;
        public Axis SliceNormal = Axis.Y_Neg;

        public bool AutomaticallyCreate = true;

        private CutPlaneComponent remoteCutPlaneComponent;
        private bool cutPlaneReady = false;

        public override bool CutPlaneReady 
        { 
            get => cutPlaneReady;
            set 
            { 
                cutPlaneReady = value;
                CutPlaneReadyChanged?.Invoke(cutPlaneReady);
            }
        }

        public override event Action<bool> CutPlaneReadyChanged;

        public UnityBoolEvent OnCutPlaneReadyChanged = new UnityBoolEvent();

        public void Start()
        {
            // Hook up the event to the Unity event
            CutPlaneReadyChanged += (ready) => OnCutPlaneReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateCutPlane();
                    break;
                default:
                    DestroyCutPlane();
                    break;
            }
        }

        public override void CreateCutPlane()
        {
            //Implement me
        }

        public override void DestroyCutPlane()
        {
            //Implement me
        }
    }
    ```

    Den här koden utökar **BaseRemoteCutPlane** -klassen som ingår i **självstudiernas till gångar**. På samma sätt som den fjärranslutna modellen kopplar det här skriptet och lyssnar efter `RemoteRenderingState` ändringar från fjärrkoordinaten. När koordinatorn når `RuntimeConnected` status försöker den ansluta automatiskt om den är den. Det finns också en `CutPlaneComponent` variabel som vi ska spåra. Det här är Azure Remote rendering-komponenten som synkroniseras med det avskurna planet i fjärrsessionen. Låt oss ta en titt på vad vi behöver göra för att skapa det avskurna planet.

2. Ersätt `CreateCutPlane()` metoden med den färdiga versionen nedan:

    ```cs
    public override void CreateCutPlane()
    {
        if (remoteCutPlaneComponent != null)
            return; //Nothing to do!

        //Create a root object for the cut plane
        var cutEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

        //Bind the remote entity to this game object
        cutEntity.BindToUnityGameObject(this.gameObject);

        //Sync the transform of this object so we can move the cut plane
        var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
        syncComponent.SyncEveryFrame = true;

        //Add a cut plane to the entity
        remoteCutPlaneComponent = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.CutPlaneComponent, cutEntity) as CutPlaneComponent;

        //Configure the cut plane
        remoteCutPlaneComponent.Normal = SliceNormal;
        remoteCutPlaneComponent.FadeColor = SliceColor.toRemote();
        remoteCutPlaneComponent.FadeLength = FadeLength;
        CutPlaneReady = true;
    }
    ```

    Här skapar vi en fjärran sluten entitet och binder den till en lokal GameObject. Vi ser till att den fjärranslutna entiteten kommer att synkroniseras med den lokala transformeringen genom att ställa in `SyncEveryFrame` på `true` . Sedan använder vi `CreateComponent` anropet för att lägga till ett `CutPlaneComponent` till fjärrobjektet. Slutligen konfigurerar vi det avskurna planet med inställningarna som definierats överst i MonoBehaviour. Låt oss se vad det tar att rensa ett klipp plan genom att implementera `DestroyCutPlane()` metoden.

3. Ersätt `DestroyCutPlane()` metoden med den färdiga versionen nedan:

    ```cs
    public override void DestroyCutPlane()
    {
        if (remoteCutPlaneComponent == null)
            return; //Nothing to do!

        remoteCutPlaneComponent.Owner.Destroy();
        remoteCutPlaneComponent = null;
        CutPlaneReady = false;
    }
    ```

Eftersom fjärrobjektet är ganska enkelt och vi bara rensar Fjärrslutpunkten (och behåller vårt lokala objekt) är det enkelt att bara anropa `Destroy` fjärrobjektet och ta bort vår referens till det.

**AppMenu** innehåller en visnings kontroll enhet som automatiskt ansluts till ditt avskurna plan och gör att du kan interagera med den. Det krävs inte att du använder **AppMenu** eller någon av visnings styrenheterna, men de gör en bättre upplevelse. Testa nu det avskurna planet och dess visnings kontroll enhet.

1. Skapa en ny, Tom GameObject i scenen och ge den namnet **CutPlane**.
1. Lägg till **RemoteCutPlane** -komponenten i **CutPlane** -GameObject.

   ![Konfiguration av klipp ut plan komponent](./media/cut-plane-config.png)

1. Tryck på Play i Unity-redigeraren för att läsa in och ansluta till en fjärran sluten session.
1. Använd MRTK hand simulering, ta bort och rotera (håll ned CTRL för att rotera) CutPlane för att flytta den runt scenen. Titta på IT-sektorn i **TestModel** för att visa interna komponenter.

![Exempel på klipp ut plan](./media/cut-plane-example-engine.png)

## <a name="configuring-the-remote-lighting"></a>Konfigurera fjärr belysningen

Fjärrrendering-sessionen stöder ett helt spektrum av [belysnings alternativ](../../../overview/features/lights.md). Vi skapar skript för den [himmelsblå texturen](../../../overview/features/sky.md) och en enkel karta för två enhets ljus typer som ska användas med fjärrrendering.

### <a name="sky-texture"></a>Luft rummets struktur

Det finns ett antal inbyggda Cubemaps som du kan välja mellan när du ändrar luft rummets struktur. De läses in i sessionen och appliceras på den himmelsblå texturen. Det är också möjligt att [läsa in egna texturer](../../../concepts/textures.md) och använda dem som luft rummets ljus.

Vi ska skapa ett **RemoteSky** -skript som innehåller en lista över de inbyggda Cubemaps som är inbyggda i form av belastnings parametrar. Sedan låter vi användaren välja och läsa in ett av alternativen.

1. Skapa ett nytt skript med namnet **RemoteSky** och ersätt hela innehållet med koden nedan:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using System;
    using System.Collections.Generic;
    using UnityEngine;

    public class RemoteSky : BaseRemoteSky
    {
        public override Dictionary<string, LoadTextureFromSasOptions> AvailableCubemaps => builtInTextures;

        private bool canSetSky;
        public override bool CanSetSky
        {
            get => canSetSky;
            set
            {
                canSetSky = value;
                CanSetSkyChanged?.Invoke(canSetSky);
            }
        }

        private string currentSky = "DefaultSky";
        public override string CurrentSky
        {
            get => currentSky;
            protected set
            {
                currentSky = value;
                SkyChanged?.Invoke(value);
            }
        }

        private Dictionary<string, LoadTextureFromSasOptions> builtInTextures = new Dictionary<string, LoadTextureFromSasOptions>()
        {
            {"Autoshop",new LoadTextureFromSasOptions("builtin://Autoshop", TextureType.CubeMap)},
            {"BoilerRoom",new LoadTextureFromSasOptions("builtin://BoilerRoom", TextureType.CubeMap)},
            {"ColorfulStudio",new LoadTextureFromSasOptions("builtin://ColorfulStudio", TextureType.CubeMap)},
            {"Hangar",new LoadTextureFromSasOptions("builtin://Hangar", TextureType.CubeMap)},
            {"IndustrialPipeAndValve",new LoadTextureFromSasOptions("builtin://IndustrialPipeAndValve", TextureType.CubeMap)},
            {"Lebombo",new LoadTextureFromSasOptions("builtin://Lebombo", TextureType.CubeMap)},
            {"SataraNight",new LoadTextureFromSasOptions("builtin://SataraNight", TextureType.CubeMap)},
            {"SunnyVondelpark",new LoadTextureFromSasOptions("builtin://SunnyVondelpark", TextureType.CubeMap)},
            {"Syferfontein",new LoadTextureFromSasOptions("builtin://Syferfontein", TextureType.CubeMap)},
            {"TearsOfSteelBridge",new LoadTextureFromSasOptions("builtin://TearsOfSteelBridge", TextureType.CubeMap)},
            {"VeniceSunset",new LoadTextureFromSasOptions("builtin://VeniceSunset", TextureType.CubeMap)},
            {"WhippleCreekRegionalPark",new LoadTextureFromSasOptions("builtin://WhippleCreekRegionalPark", TextureType.CubeMap)},
            {"WinterRiver",new LoadTextureFromSasOptions("builtin://WinterRiver", TextureType.CubeMap)},
            {"DefaultSky",new LoadTextureFromSasOptions("builtin://DefaultSky", TextureType.CubeMap)}
        };

        public UnityBoolEvent OnCanSetSkyChanged;
        public override event Action<bool> CanSetSkyChanged;

        public UnityStringEvent OnSkyChanged;
        public override event Action<string> SkyChanged;

        public void Start()
        {
            // Hook up the event to the Unity event
            CanSetSkyChanged += (canSet) => OnCanSetSkyChanged?.Invoke(canSet);
            SkyChanged += (key) => OnSkyChanged?.Invoke(key);

            RemoteRenderingCoordinator.CoordinatorStateChange += ApplyStateToView;
            ApplyStateToView(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        private void ApplyStateToView(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CanSetSky = true;
                    break;
                default:
                    CanSetSky = false;
                    break;
            }
        }

        public override async void SetSky(string skyKey)
        {
            if (!CanSetSky)
            {
                Debug.Log("Unable to set sky right now");
                return;
            }

            if (AvailableCubemaps.ContainsKey(skyKey))
            {
                Debug.Log("Setting sky to " + skyKey);
                //Load the texture into the session
                var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

                //Apply the texture to the SkyReflectionSettings
                RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
                SkyChanged?.Invoke(skyKey);
            }
            else
            {
                Debug.Log("Invalid sky key");
            }
        }
    }
    ```

    Den viktigaste delen av den här koden är bara några rader:

    ```cs
    //Load the texture into the session
    var texture = await RemoteRenderingCoordinator.CurrentSession.Connection.LoadTextureFromSasAsync(AvailableCubemaps[skyKey]);

    //Apply the texture to the SkyReflectionSettings
    RemoteRenderingCoordinator.CurrentSession.Connection.SkyReflectionSettings.SkyReflectionTexture = texture;
    ```

    Här får vi en referens till texturen som ska användas genom att läsa in den i sessionen från den inbyggda blob-lagringen. Sedan behöver vi bara tilldela den strukturen till sessionen `SkyReflectionTexture` för att tillämpa den.

1. Skapa en tom GameObject i din scen och ge den namnet **SkyLight**.

1. Lägg till **RemoteSky** -skriptet till **SkyLight** -GameObject.

    Det går att växla mellan luft rummets ljus genom att anropa `SetSky` med en av de sträng nycklar som definierats i `AvailableCubemaps` . Den vy som är inbyggd i  **AppMenu** skapar automatiskt knappar och kopplar samman deras händelser så att de anropar `SetSky` sin respektive nyckel.
1. Tryck på Play i Unity-redigeraren och auktorisera en anslutning.
1. När du har anslutit den lokala körnings miljön till en fjärrsession, navigera **AppMenu->-sessionsinställningar – > fjärr-himmelsblå** för att utforska de olika alternativen för luft rummet och se hur de påverkar **TestModel**.

### <a name="scene-lights"></a>Scen lampor

Fjärrscens lampor är: punkt, dekor och riktning. På samma sätt som det klipp som vi skapade ovan är de här scen lamporna fjär entiteter med komponenter kopplade till dem. Ett viktigt övervägande när man försöker att använda ljuset i din lokala scen. Den här strategin är inte alltid möjlig eftersom många Unit-program för HoloLens 2 inte använder fysisk-baserad åter givning för lokalt återgivna objekt. Men till en viss nivå kan vi simulera enhetens enklare standard belysning.

1. Skapa ett nytt skript med namnet **RemoteLight** och ersätt dess kod med koden nedan:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(Light))]
    public class RemoteLight : BaseRemoteLight
    {
        public bool AutomaticallyCreate = true;

        private bool lightReady = false;
        public override bool LightReady 
        {
            get => lightReady;
            set
            {
                lightReady = value;
                LightReadyChanged?.Invoke(lightReady);
            }
        }

        private ObjectType remoteLightType = ObjectType.Invalid;
        public override ObjectType RemoteLightType => remoteLightType;

        public UnityBoolEvent OnLightReadyChanged;

        public override event Action<bool> LightReadyChanged;

        private Light localLight; //Unity Light

        private Entity lightEntity;
        private LightComponentBase remoteLightComponent; //Remote Rendering Light

        private void Awake()
        {
            localLight = GetComponent<Light>();
            switch (localLight.type)
            {
                case LightType.Directional:
                    remoteLightType = ObjectType.DirectionalLightComponent;
                    break;
                case LightType.Point:
                    remoteLightType = ObjectType.PointLightComponent;
                    break;
                case LightType.Spot:
                case LightType.Area:
                    //Not supported in tutorial
                case LightType.Disc:
                    // No direct analog in remote rendering
                    remoteLightType = ObjectType.Invalid;
                    break;
            }
        }

        public void Start()
        {
            // Hook up the event to the Unity event
            LightReadyChanged += (ready) => OnLightReadyChanged?.Invoke(ready);

            RemoteRenderingCoordinator.CoordinatorStateChange += RemoteRenderingCoordinator_CoordinatorStateChange;
            RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        public void OnDestroy()
        {
            lightEntity?.Destroy();
        }

        private void RemoteRenderingCoordinator_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    if (AutomaticallyCreate)
                        CreateLight();
                    break;
                default:
                    DestroyLight();
                    break;
            }
        }

        public override void CreateLight()
        {
            if (remoteLightComponent != null)
                return; //Nothing to do!

            //Create a root object for the light
            if(lightEntity == null)
                lightEntity = RemoteRenderingCoordinator.CurrentSession.Connection.CreateEntity();

            //Bind the remote entity to this game object
            lightEntity.BindToUnityGameObject(this.gameObject);

            //Sync the transform of this object so we can move the light
            var syncComponent = this.gameObject.GetComponent<RemoteEntitySyncObject>();
            syncComponent.SyncEveryFrame = true;

            //Add a light to the entity
            switch (RemoteLightType)
            {
                case ObjectType.DirectionalLightComponent:
                    var remoteDirectional = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.DirectionalLightComponent, lightEntity) as DirectionalLightComponent;
                    //No additional properties
                    remoteLightComponent = remoteDirectional;
                    break;

                case ObjectType.PointLightComponent:
                    var remotePoint = RemoteRenderingCoordinator.CurrentSession.Connection.CreateComponent(ObjectType.PointLightComponent, lightEntity) as PointLightComponent;
                    remotePoint.Radius = 0;
                    remotePoint.Length = localLight.range;
                    //remotePoint.AttenuationCutoff = //No direct analog in Unity legacy lights
                    //remotePoint.ProjectedCubeMap = //No direct analog in Unity legacy lights

                    remoteLightComponent = remotePoint;
                    break;
                default:
                    LightReady = false;
                    return;
            }

            // Set the common values for all light types
            UpdateRemoteLightSettings();

            LightReady = true;
        }

        public override void UpdateRemoteLightSettings()
        {
            remoteLightComponent.Color = localLight.color.toRemote();
            remoteLightComponent.Intensity = localLight.intensity;
        }

        public override void DestroyLight()
        {
            if (remoteLightComponent == null)
                return; //Nothing to do!

            remoteLightComponent.Destroy();
            remoteLightComponent = null;
            LightReady = false;
        }

        [ContextMenu("Sync Remote Light Configuration")]
        public override void RecreateLight()
        {
            DestroyLight();
            CreateLight();
        }

        public override void SetIntensity(float intensity)
        {
            localLight.intensity = Mathf.Clamp(intensity, 0, 1);
            UpdateRemoteLightSettings();
        }

        public override void SetColor(Color color)
        {
            localLight.color = color;
            UpdateRemoteLightSettings();
        }
    }
    ```

    Det här skriptet skapar olika typer av fjärrljus beroende på typen av lokal enhets ljus som skriptet är kopplat till. Fjärrljuset kommer att duplicera det lokala ljuset i dess: position, rotation, färg och intensitet. Där det är möjligt kommer fjärr lampan också att ange ytterligare konfiguration. Detta är inte en perfekt matchning eftersom enhets lamporna inte är PBR-lampor.

1. Hitta **DirectionalLight** -GameObject i din scen. Om du har tagit bort standard **DirectionalLight** från din scen: från den övre meny raden väljer du *GameObject-> Light-> DirectionalLight* för att skapa ett nytt ljus i din scen.

1. Välj **DirectionalLight** -GameObject och Lägg till **RemoteLight** -skriptet med knappen **Lägg till komponent** .

1. Eftersom det här skriptet implementerar Bask Lassen `BaseRemoteLight` kan du använda den tillhandahållna **AppMenu** för att interagera med fjärr ljuset. Navigera till **AppMenu->-sessionsinställningar – > riktad ljus**.

    > [!NOTE]
    > Användar gränssnittet i **AppMenu** har begränsats till ett enda riktat ljus för enkelhetens skull. Men det är fortfarande möjligt och uppmuntras att lägga till punkt lampor och koppla **RemoteLight** -skriptet till dem. Dessa ytterligare ljus kan ändras genom att ändra egenskaperna för enhets indikatorn i redigeraren. Du måste manuellt synkronisera de lokala ändringarna till fjärrljuset med hjälp av snabb menyn **RemoteLight** i kontrollen:
    >
    > ![Fjärr ljus manuell synkronisering](./media/sync-remote-light.png)

1. Tryck på Play i Unity-redigeraren och auktorisera en anslutning.

1. När du har anslutit din körning till en fjärran sluten session kan du placera och rikta din kamera (Använd WASD och högerklicka + flytta) för att Visa kontrollen riktad lampa i vyn. 
1. Använd styrenheten för fjärr ljus visning för att ändra ljusets egenskaper. Använd MRTK hand simulering, ta och rotera (håll ned CTRL för att rotera) det riktade ljuset för att se effekterna på scenens belysning.

    ![Riktat ljus](./media/directional-light-test.png)

## <a name="editing-materials"></a>Redigera material

Fjärranslutna [material](../../../concepts/materials.md) kan ändras för att ge ytterligare visuella effekter, finjustera visuella objekt av återgivna modeller eller ge ytterligare feedback till användare. Det finns många sätt och många skäl att ändra ett material. Här visar vi dig hur du ändrar ett materials albedo färg och ändrar ett PBR-Materials grovhet och metallhet.

> [!NOTE]
> I många fall är det idealiskt att använda den i stället för att ändra materialet om en funktion eller en funktion kan implementeras med hjälp av en **HierarchicalStateOverrideComponent**.

Vi ska skapa ett skript som accepterar en målentitet och konfigurerar ett fåtal `OverrideMaterialProperty` objekt för att ändra egenskaperna för målentiteten. Vi börjar med att hämta målentitetens [**MeshComponent**](../../../concepts/meshes.md#meshcomponent), som innehåller en lista över material som används på nätet. För enkelhetens skull kommer vi bara att använda det första materialet som påträffas. Den här naïve-strategin kan vara mycket enkelt beroende av hur innehållet har skapats, så du skulle förmodligen vilja ta en mer komplex metod för att välja lämpligt material.

Från materialet kan vi komma åt vanliga värden som albedo. Först måste materialet omvandlas till lämplig typ, `PbrMaterial` eller `ColorMaterial` för att hämta sina värden, som visas i **GetMaterialColor** -metoden. När vi har en referens till det önskade materialet, ställer du bara in värdena och ARR hanterar synkroniseringen mellan de lokala material egenskaperna och det fjärranslutna materialet.

1. Skapa ett skript med namnet **EntityMaterialController** och ersätt innehållet med följande kod:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using System.Linq;
    using UnityEngine;
    // to prevent namespace conflicts
    using ARRMaterial = Microsoft.Azure.RemoteRendering.Material;

    public class EntityMaterialController : BaseEntityMaterialController
    {
        public override bool RevertOnEntityChange { get; set; } = true;

        public override OverrideMaterialProperty<Color> ColorOverride { get; set; }
        public override OverrideMaterialProperty<float> RoughnessOverride { get; set; }
        public override OverrideMaterialProperty<float> MetalnessOverride { get; set; }

        private Entity targetEntity;
        public override Entity TargetEntity
        {
            get => targetEntity;
            set
            {
                if (targetEntity != value)
                {
                    if (targetEntity != null && RevertOnEntityChange)
                    {
                        Revert();
                    }

                    targetEntity = value;
                    ConfigureTargetEntity();
                    TargetEntityChanged?.Invoke(value);
                }
            }
        }

        private ARRMaterial targetMaterial;
        private ARRMeshComponent meshComponent;

        public override event Action<Entity> TargetEntityChanged;
        public UnityRemoteEntityEvent OnTargetEntityChanged;

        public void Start()
        {
            // Forward events to Unity events
            TargetEntityChanged += (entity) => OnTargetEntityChanged?.Invoke(entity);

            // If there happens to be a remote RayCaster on this object, assume we should listen for events from it
            if (GetComponent<BaseRemoteRayCastPointerHandler>() != null)
                GetComponent<BaseRemoteRayCastPointerHandler>().RemoteEntityClicked += (entity) => TargetEntity = entity;
        }

        protected override void ConfigureTargetEntity()
        {
            //Get the Unity object, to get the sync object, to get the mesh component, to get the material.
            var targetEntityGameObject = TargetEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

            var localSyncObject = targetEntityGameObject.GetComponent<RemoteEntitySyncObject>();
            meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
            if (meshComponent == null)
            {
                var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
                if (mesh != null)
                {
                    targetEntityGameObject.BindArrComponent<ARRMeshComponent>(mesh);
                    meshComponent = targetEntityGameObject.GetComponent<ARRMeshComponent>();
                }
            }

            meshComponent.enabled = true;

            targetMaterial = meshComponent.RemoteComponent.Mesh.Materials.FirstOrDefault();
            if (targetMaterial == default)
            {
                return;
            }

            ColorOverride = new OverrideMaterialProperty<Color>(
                GetMaterialColor(targetMaterial), //The original value
                targetMaterial, //The target material
                ApplyMaterialColor); //The action to take to apply the override

            //If the material is a PBR material, we can override some additional values
            if (targetMaterial.MaterialSubType == MaterialType.Pbr)
            {
                var firstPBRMaterial = (PbrMaterial)targetMaterial;

                RoughnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Roughness, //The original value
                    targetMaterial, //The target material
                    ApplyRoughnessValue); //The action to take to apply the override

                MetalnessOverride = new OverrideMaterialProperty<float>(
                    firstPBRMaterial.Metalness, //The original value
                    targetMaterial, //The target material
                    ApplyMetalnessValue); //The action to take to apply the override
            }
            else //otherwise, ensure the overrides are cleared out from any previous entity
            {
                RoughnessOverride = null;
                MetalnessOverride = null;
            }
        }

        public override void Revert()
        {
            if (ColorOverride != null)
                ColorOverride.OverrideActive = false;

            if (RoughnessOverride != null)
                RoughnessOverride.OverrideActive = false;

            if (MetalnessOverride != null)
                MetalnessOverride.OverrideActive = false;
        }

        private Color GetMaterialColor(ARRMaterial material)
        {
            if (material == null)
                return default;

            if (material.MaterialSubType == MaterialType.Color)
                return ((ColorMaterial)material).AlbedoColor.toUnity();
            else
                return ((PbrMaterial)material).AlbedoColor.toUnity();
        }

        private void ApplyMaterialColor(ARRMaterial material, Color color)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Color)
                ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
            else
                ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
        }

        private void ApplyRoughnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Roughness
                ((PbrMaterial)material).Roughness = value;
        }

        private void ApplyMetalnessValue(ARRMaterial material, float value)
        {
            if (material == null)
                return;

            if (material.MaterialSubType == MaterialType.Pbr) //Only PBR has Metalness
                ((PbrMaterial)material).Metalness = value;
        }
    }
    ```

`OverrideMaterialProperty`Typen bör vara tillräckligt flexibel för att tillåta att några andra material värden ändras, om så önskas. `OverrideMaterialProperty`Typen spårar status för en åsidosättning, underhåller det gamla och nya värdet och använder ett ombud för att ange åsidosättningen. Du kan till exempel titta på `ColorOverride` :

```cs
ColorOverride = new OverrideMaterialProperty<Color>(
    GetMaterialColor(targetMaterial), //The original value
    targetMaterial, //The target material
    ApplyMaterialColor); //The action to take to apply the override
```

Detta skapar en ny `OverrideMaterialProperty` där åsidosättningen kommer att radbryta typen `Color` . Vi tillhandahåller den aktuella eller ursprungliga färgen vid den tidpunkt då åsidosättningen skapas. Vi ger även det ett ARR-material att agera på. Slutligen anges ett ombud som ska tillämpa åsidosättningen. Delegaten är en metod som accepterar ett ARR-material och den typ som åsidosätter. Den här metoden är den viktigaste delen av att förstå hur ARR justerar material värden.

`ColorOverride`Använder `ApplyMaterialColor` metoden för att utföra sitt arbete:

```cs
private void ApplyMaterialColor(ARRMaterial material, Color color)
{
    if (material.MaterialSubType == MaterialType.Color)
        ((ColorMaterial)material).AlbedoColor = color.toRemoteColor4();
    else
        ((PbrMaterial)material).AlbedoColor = color.toRemoteColor4();
}
```

Den här koden accepterar ett material och en färg. Den kontrollerar vilken typ av material det gör sedan en omvandling av materialet för att tillämpa färgen.

`RoughnessOverride`Och `MetalnessOverride` fungerar på samma sätt som när du `ApplyRoughnessValue` använder `ApplyMetalnessValue` metoderna och för att utföra sitt arbete.

Nu ska vi testa material styrenheten.

1. Lägg till **EntityMaterialController** -skriptet till **TestModel** -GameObject.
1. Tryck på Play i Unity för att starta scenen och ansluta till ARR.
1. När du har anslutit din körning till en fjärrsession och läst in modellen navigerar du till **AppMenu-> modell verktyg – > redigera material**
1. Välj en entitet från modellen med hjälp av de simulerade händerna för att klicka på **TestModel**.
1. Kontrol lera att modulen för materialiserad vy (**AppMenu->modell verktyg->redigera material**) har uppdaterats till den riktade entiteten.
1. Använd kontroll enheten för material för att justera materialet på den riktade entiteten.

Eftersom vi bara ändrar det första materialet i nätet kanske du inte ser material ändringen. Använd den hierarkiska åsidosättande **SeeThrough** för att se om materialet som du ändrar ligger innanför nätet.

![Exempel på material redigering](./media/material-edit-example.png)

## <a name="next-steps"></a>Nästa steg

Grattis! Nu har du implementerat alla kärn funktioner i Azure Remote rendering. I nästa kapitel lär vi dig att skydda din Azure-fjärråter givning och blob-lagring. De är de första stegen för att släppa ett kommersiellt program som använder Azure-fjärrrendering.

> [!div class="nextstepaction"]
> [Nästa: skydda Azure-fjärråter givning och modell lagring](../security/security.md)