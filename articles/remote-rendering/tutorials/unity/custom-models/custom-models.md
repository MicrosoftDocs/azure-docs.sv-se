---
title: Gränssnitt och anpassade modeller
description: Lägg till vyer och anpassade modeller som ska återges av Azure fjärrrendering
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a278b6e725488d6107e6b0819e002d1dafe4774
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99591671"
---
# <a name="tutorial-interfaces-and-custom-models"></a>Självstudie: gränssnitt och anpassade modeller

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Lägg till verktyg för Mixad verklighet i projektet
> * Hantera modell tillstånd
> * Konfigurera Azure-Blob Storage för modell inmatning
> * Ladda upp och bearbeta modeller för åter givning

## <a name="prerequisites"></a>Förutsättningar

* Den här självstudien bygger på [Självstudier: Visa en fjärrrenderad modell](../view-remote-models/view-remote-models.md).

## <a name="get-started-with-the-mixed-reality-toolkit-mrtk"></a>Kom igång med MRTK (Mixed Reality Toolkit)

MRTK (Mixed Reality Toolkit) är en plattforms oberoende verktygs uppsättning för att skapa blandade verklighets upplevelser. Vi använder MRTK 2.5.1 för dess interaktions-och visualiserings funktioner.

Om du vill lägga till MRTK följer du de [steg](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#required) som anges i [installations guiden för MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html).

Dessa steg är:
 - [Hämta de senaste MRTK-Unit-paketen](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#1-get-the-latest-mrtk-unity-packages)
     - Även om det står "senaste", använder du version 2.5.1 från sidan MRTK release.
     - Vi använder bara *Foundation* -paketet i den här självstudien. Paketen för *tillägg*, *verktyg* och *exempel* krävs inte.
 - [Importera MRTK-paket till ditt Unity-projekt](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#2-import-mrtk-packages-into-your-unity-project)
 - [Ändra ditt Unity-projekt till mål plattformen](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#3-switch-your-unity-project-to-the-target-platform)
     - Du bör ha gjort det här steget redan i det första kapitlet, men nu är det en stund att kontrol lera!
 - [Lägg till MRTK till en ny scen eller nytt projekt](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/Documentation/Installation.html#4-add-and-configure-mrtk-with-a-new-scene)
     - Du kan lägga till MRTK i en ny scen och lägga till koordinatorn och modell objekt/skript igen, eller så kan du lägga till MRTK i din befintliga scen med hjälp av *verktyget Mixed Reality-> lägga till i scen och konfigurera* meny kommando.

## <a name="import-assets-used-by-this-tutorial"></a>Importera till gångar som används i den här självstudien

Från och med det här kapitlet implementerar vi ett enkelt [modell-Visa-kontrollant-mönster](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) för mycket av det material som täcks. *Modell* delen av mönstret är den Azure Remote rerendering-kod och den tillstånds hantering som är relaterad till Azure Remote rendering. Mönstrets *visnings* -och *styrenhets* delar implementeras med hjälp av MRTK-tillgångar och vissa anpassade skript. Du kan använda *modellen* i den här självstudien utan att *Visa kontrollanten* som implementeras här. Den här separationen gör att du enkelt kan integrera koden som finns i den här självstudien i ditt eget program där det tar över *vyns kontrollant* del av design mönstret.

Med introduktionen av MRTK finns ett antal skript, prefabs och till gångar som nu kan läggas till i projektet för att stödja interaktioner och visuell feedback. Dessa till gångar, som kallas för **självstudier**, paketeras i ett [Unity Asset-paket](https://docs.unity3d.com/Manual/AssetPackages.html), som ingår i [GitHub för Azure-fjärråter givning](https://github.com/Azure/azure-remote-rendering) i "\Unity\TutorialAssets\TutorialAssets.unitypackage".

1. Klona eller hämta Azure- [Fjärrrenderingen](https://github.com/Azure/azure-remote-rendering)för git-lagringsplatsen, om hämtningen extraherar zip-filen till en känd plats.
1. I ditt Unity-projekt väljer du *till gångar-> importera paket-> anpassade paketet*.
1. I Utforskaren navigerar du till den katalog där du klonade eller zippade Azure-databasen för fjärrrendering. Välj sedan den. unitypackage som finns i **Unity-> TutorialAssets-> TutorialAssets. unitypackage**
1. Klicka på **Importera** om du vill importera paketets innehåll till projektet.
1. I Unity-redigeraren väljer du verktyg för *Mixad verklighet-> verktyg-> uppgradera MRTK standard Shader for Lightweight rendera pipelines* från den övre meny raden och följer anvisningarna för att uppgradera skuggningen.

När MRTK och självstudiernas till gångar ingår i projektet ska vi byta MRTK-profilen till en lämpligare för självstudien.

1. Välj **MixedRealityToolkit** -GameObject i scen-hierarkin.
1. I kontrollanten går du till **MixedRealityToolkit** -komponenten och byter konfigurations profilen till *ARRMixedRealityToolkitConfigurationProfile*.
1. Tryck på *CTRL + S* för att spara ändringarna.

Detta konfigurerar MRTK, i huvudsak, med standard-HoloLens 2-profilerna. De angivna profilerna är förkonfigurerade på följande sätt:
 - Stäng av profileraren (tryck på 9 för att aktivera/inaktivera eller säg "Visa/Dölj profiler" på enheten).
 - Stäng av ögon blick markören.
 - Aktivera uppdelnings bara mus klickningar, så du kan klicka på MRTK UI-element med musen i stället för den simulerade handen.

## <a name="add-the-app-menu"></a>Lägg till app-menyn

De flesta av visnings kontrol Lanterna i den här självstudien använder sig av abstrakta bas klasser i stället för i konkreta klasser. Det här mönstret ger större flexibilitet och gör det möjligt för oss att tillhandahålla visnings styrenheter åt dig, samtidigt som du får lära dig om Azures kod för fjärråter givning. För enkelhetens skull har **RemoteRenderingCoordinator** -klassen ingen abstrakt klass och dess visnings hanterare fungerar direkt mot den konkreta klassen.

Nu kan du lägga till Prefab- **AppMenu** till scenen för visuell feedback för det aktuella sessionstillståndet. Den här vyns kontrollant kommer att "låsa upp" fler undermenyn Visa kontrollanter när vi implementerar och integrerar fler ARR-funktioner i scenen. För närvarande kommer **AppMenu** att ha en visuell indikation på arr-tillstånd och presentera den modala panel som användaren använder för att ge programmet behörighet att ansluta till arr.

1. Leta upp **AppMenu** -Prefab i *assets/RemoteRenderingTutorial/Prefabs/AppMenu*
1. Dra **AppMenu** -Prefab till scenen.
1. Du ser förmodligen en dialog ruta för **tmp-import**, eftersom det är första gången vi inkluderar *text nät Pro* -tillgångar i scenen. Följ anvisningarna för att **Importera tmp Essentials**. Stäng sedan import dialog rutan, exemplen och tillägg behövs inte.
1. **AppMenu** har kon figurer ATS för att automatiskt ansluta och tillhandahålla spärren för att kunna ansluta till en session, så att vi kan ta bort den tidigare placerade. På **RemoteRenderingCoordinator** -GameObject tar du bort bypass för auktorisering som vi har implementerat tidigare genom att trycka på knappen "-" på **begäran om auktorisation** .
 ![Ta bort förbikoppling ](./media/remove-bypass-event.png) .
1. Testa visnings styrenheten genom att trycka på **spela upp** i Unity-redigeraren.
1. Nu när MRTK har kon figurer ATS i redigerings programmet kan du använda WASD-nycklarna för att ändra placeringen av vyn och placera den högra mus knappen + Flytta musen för att ändra din visnings riktning. Prova att "köra" runt scenen en bit för att få en känsla för kontrollerna.
1. På enhet kan du skapa en Palm-enhet för att kalla **AppMenu**, i Unity-redigeraren, använder du snabb tangenten '.
1. Om du har förlorat menyns blick trycker du på "m"-tangenten för att kalla menyn. Menyn placeras nära kameran för enkel interaktion.
1. Auktoriseringen visas nu som en begäran till höger om **AppMenu**, från och med nu kan du använda den för att auktorisera appen för att hantera sessioner för fjärrrendering.
 ![UI-auktorisering](./media/authorize-request-ui.png)
1. Stoppa enhets omslutning för att fortsätta med självstudien.

## <a name="manage-model-state"></a>Hantera modell tillstånd

Nu ska vi implementera ett nytt skript, **RemoteRenderedModel** som är till för att spåra status, svara på händelser, utlösa händelser och konfiguration. **RemoteRenderedModel** lagrar i princip fjärrsökvägen för modell data i `modelPath` . Den lyssnar efter tillstånds ändringar i **RemoteRenderingCoordinator** för att se om den automatiskt ska läsa in eller ta bort den modell som den definierar. Den GameObject som har den **RemoteRenderedModel** som är kopplad till den är den lokala överordnade för fjärrinnehållet.

Observera att **RemoteRenderedModel** -skriptet implementerar **BaseRemoteRenderedModel**, som ingår i **självstudiernas till gångar**. Detta gör att styrenheten för fjärrmodellens vy kan bindas till ditt skript.

1. Skapa ett nytt skript med namnet **RemoteRenderedModel** i samma mapp som **RemoteRenderingCoordinator**. Ersätt hela innehållet med följande kod:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;
    using UnityEngine.Events;

    public class RemoteRenderedModel : BaseRemoteRenderedModel
    {
        public bool AutomaticallyLoad = true;

        private ModelState currentModelState = ModelState.NotReady;

        [SerializeField]
        [Tooltip("The friendly name for this model")]
        private string modelDisplayName;
        public override string ModelDisplayName { get => modelDisplayName; set => modelDisplayName = value; }

        [SerializeField]
        [Tooltip("The URI for this model")]
        private string modelPath;
        public override string ModelPath
        {
            get => modelPath.Trim();
            set => modelPath = value;
        }

        public override ModelState CurrentModelState
        {
            get => currentModelState;
            protected set
            {
                if (currentModelState != value)
                {
                    currentModelState = value;
                    ModelStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<ModelState> ModelStateChange;
        public override event Action<float> LoadProgress;
        public override Entity ModelEntity { get; protected set; }

        public UnityEvent OnModelNotReady = new UnityEvent();
        public UnityEvent OnModelReady = new UnityEvent();
        public UnityEvent OnStartLoading = new UnityEvent();
        public UnityEvent OnModelLoaded = new UnityEvent();
        public UnityEvent OnModelUnloading = new UnityEvent();

        public UnityFloatEvent OnLoadProgress = new UnityFloatEvent();

        public void Awake()
        {
            // Hook up the event to the Unity event
            LoadProgress += (progress) => OnLoadProgress?.Invoke(progress);

            ModelStateChange += HandleUnityStateEvents;
        }

        private void HandleUnityStateEvents(ModelState modelState)
        {
            switch (modelState)
            {
                case ModelState.NotReady:  OnModelNotReady?.Invoke();  break;
                case ModelState.Ready:     OnModelReady?.Invoke();     break;
                case ModelState.Loading:   OnStartLoading?.Invoke();   break;
                case ModelState.Loaded:    OnModelLoaded?.Invoke();    break;
                case ModelState.Unloading: OnModelUnloading?.Invoke(); break;
            }
        }

        private void Start()
        {
            //Attach to and initialize current state (in case we're attaching late)
            RemoteRenderingCoordinator.CoordinatorStateChange += Instance_CoordinatorStateChange;
            Instance_CoordinatorStateChange(RemoteRenderingCoordinator.instance.CurrentCoordinatorState);
        }

        /// <summary>
        /// Listen for state changes on the coordinator, clean up this model's remote objects if we're no longer connected.
        /// Automatically load if required
        /// </summary>
        private void Instance_CoordinatorStateChange(RemoteRenderingCoordinator.RemoteRenderingState state)
        {
            switch (state)
            {
                case RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected:
                    CurrentModelState = ModelState.Ready;
                    if (AutomaticallyLoad)
                        LoadModel();
                    break;
                default:
                    UnloadModel();
                    break;
            }
        }

        private void OnDestroy()
        {
            RemoteRenderingCoordinator.CoordinatorStateChange -= Instance_CoordinatorStateChange;
            UnloadModel();
        }

        /// <summary>
        /// Asks the coordinator to create a model entity and listens for coordinator state changes
        /// </summary>
        [ContextMenu("Load Model")]
        public override async void LoadModel()
        {
            if (CurrentModelState != ModelState.Ready)
                return; //We're already loaded, currently loading, or not ready to load

            CurrentModelState = ModelState.Loading;

            ModelEntity = await RemoteRenderingCoordinator.instance?.LoadModel(ModelPath, this.transform, SetLoadingProgress);

            if (ModelEntity != null)
                CurrentModelState = ModelState.Loaded;
            else
                CurrentModelState = ModelState.Error;
        }

        /// <summary>
        /// Clean up the local model instances
        /// </summary>
        [ContextMenu("Unload Model")]
        public override void UnloadModel()
        {
            CurrentModelState = ModelState.Unloading;

            if (ModelEntity != null)
            {
                var modelGameObject = ModelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
                Destroy(modelGameObject);
                ModelEntity.Destroy();
                ModelEntity = null;
            }

            if (RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
                CurrentModelState = ModelState.Ready;
            else
                CurrentModelState = ModelState.NotReady;
        }

        /// <summary>
        /// Update the Unity progress event
        /// </summary>
        /// <param name="progressValue"></param>
        public override void SetLoadingProgress(float progressValue)
        {
            LoadProgress?.Invoke(progressValue);
        }
    }
    ```

I de mest grundläggande villkoren innehåller **RemoteRenderedModel** de data som behövs för att läsa in en modell (i det här fallet SAS-eller *Builtin://* -URI) och spårar fjärrmodellens tillstånd. När det är dags att läsa in, `LoadModel` anropas metoden på **RemoteRenderingCoordinator** och entiteten som innehåller modellen returneras för referens och borttagning.

## <a name="load-the-test-model"></a>Läs in test modellen

Nu ska vi testa det nya skriptet genom att läsa in test modellen igen. Vi ska skapa ett spel objekt som innehåller skriptet och vara överordnat test modellen.

1. Skapa ett nytt tomt spel objekt i scenen och ge det namnet **TestModel**.
1. Lägg till *RemoteRenderedModel* -skriptet i **TestModel**.
![Lägg till RemoteRenderedModel-komponent](./media/add-remote-rendered-model-script.png)
1. Fyll i `Model Display Name` och `Model Path` med "*TestModel*" respektive "*Builtin://Engine*".
![Ange modell information](./media/add-model-script.png)
1. Placera **TestModel** -objektet framför kameran, vid position **x = 0, y = 0, z = 3**.
![Placera objekt](./media/test-model-position.png)
1. Se till att **AutomaticallyLoad** har Aktiver ATS.
1. Tryck på **Play** i Unity-redigeraren för att testa programmet.
1. Bevilja auktorisering genom att klicka på knappen *Anslut* för att tillåta appen att skapa en session och ansluta till en session och läsa in modellen automatiskt.

Titta på konsolen när programmet fortskrider genom dess tillstånd. Tänk på att det kan ta en stund att slutföra vissa tillstånd och inte Visa förloppet. Slutligen kommer du att se loggarna från inläsningen av modellen och sedan återges test modellen i scenen.

Prova att flytta och rotera **TestModel** -GameObject via transformeringen i kontrollanten eller i vyn scen. Du ser att modellen flyttas och roterar den i spel läge.

![Unions logg](./media/unity-loading-log.png)

## <a name="provision-blob-storage-in-azure-and-custom-model-ingestion"></a>Etablera Blob Storage i Azure och med anpassad modell inmatning

Nu kan vi prova att läsa in din egen modell. Om du vill göra det måste du konfigurera Blob Storage och på Azure, ladda upp och konvertera en modell och sedan läsa in modellen med **RemoteRenderedModel** -skriptet. De anpassade stegen för att läsa in modeller kan hoppas över på ett säkert sätt om du inte har en egen modell som kan läsas in just nu.

Följ stegen som anges i [snabb starten: konvertera en modell för åter givning](../../../quickstarts/convert-model.md). Hoppa över avsnittet **Infoga ny modell i exempel programmet för snabb start** i den här självstudien. När du har den inmatade modellens SAS-URI *(signatur för delad åtkomst)* fortsätter du till nästa steg nedan.

## <a name="load-and-rendering-a-custom-model"></a>Läs in och återge en anpassad modell

1. Skapa en ny tom GameObject i scenen och ge den samma namn som din anpassade modell.
1. Lägg till *RemoteRenderedModel* -skriptet till den nya GameObject.
 ![Lägg till RemoteRenderedModel-komponent](./media/add-remote-rendered-model-script.png)
1. Fyll i `Model Display Name` med ett lämpligt namn för din modell.
1. Fyll i `Model Path` med modellens *SAS-URI (signatur för delad åtkomst)* som du skapade i stegen ovan.
1. Placera GameObject framför kameran, vid position **x = 0, y = 0, z = 3.**
1. Se till att **AutomaticallyLoad** har Aktiver ATS.
1. Tryck på **Play** i Unity-redigeraren för att testa programmet.

    Du kommer att se att-konsolen börjar fylla i med det aktuella läget och till och med att förloppet för modell inläsning pågår. Din anpassade modell kommer sedan att läsas in i scenen.

1. Ta bort det anpassade modell objektet från scenen. Den bästa upplevelsen av den här självstudien kommer att använda test modellen. Även om flera modeller stöds i ARR, skrevs den här självstudien till bästa stöd för en enda fjärrmodell i taget.

## <a name="next-steps"></a>Nästa steg

Nu kan du läsa in egna modeller i Azures fjärrrendering och visa dem i ditt program! Sedan vägleder vi dig genom att ändra dina modeller.

> [!div class="nextstepaction"]
> [Nästa: manipulera modeller](../manipulate-models/manipulate-models.md)
