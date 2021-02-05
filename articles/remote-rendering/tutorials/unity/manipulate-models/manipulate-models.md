---
title: Manipulera modeller
description: Ändra fjärranslutna modeller genom att flytta, rotera skalning med mera
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: cec97134173cfc7879baf1d914d8f224a0736430
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593052"
---
# <a name="tutorial-manipulating-models"></a>Självstudie: ändra modeller

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Lägg till visuella objekt och Manipulerings gränser runt fjärranslutna modeller
> * Flytta, rotera och skala
> * Raycast med spatiala frågor
> * Lägg till enkla animeringar för fjärrrenderade objekt

## <a name="prerequisites"></a>Förutsättningar

* Den här självstudien bygger på [Självstudier: gränssnitt och anpassade modeller](../custom-models/custom-models.md).

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>Fråga Fjärrobjektets gränser och tillämpa på lokala gränser

För att interagera med fjärrobjekt behöver vi en lokal representation för att interagera med det första. [Objekt gränserna](../../../concepts/object-bounds.md) är användbara för snabb manipulering av ett fjärrobjekt. Fjär gränserna kan frågas från ARR, med den lokala entiteten som referens. Gränserna efter frågas när modellen har lästs in i fjärrsessionen.

Gränserna för en modell definieras av den ruta som innehåller hela modellen, precis som enhetens [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html), som har ett centrum och en storlek som definierats för x-, y-och z-axlarna. I själva verket använder vi enhetens **BoxCollider** för att representera gränserna för fjärrmodellen.

1. Skapa ett nytt skript i samma katalog som **RemoteRenderedModel** och ge den namnet **RemoteBounds**.
1. Ersätt innehållet i skriptet med följande kod:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create an async query using the model entity
        async private void QueryBounds()
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Om du ser ett fel i *funktionen "X" i Visual Studio är inte tillgängligt i C# 6. Använd språk version 7,0 eller senare*. det här felet kan ignoreras på ett säkert sätt. Detta är relaterat till lösning och projekt skapande.

    Det här skriptet ska läggas till i samma GameObject som skriptet som implementerar  **BaseRemoteRenderedModel**. I det här fallet innebär det **RemoteRenderedModel**. På samma sätt som för tidigare skript, hanterar den här inledande koden alla status ändringar, händelser och data som är relaterade till fjärrvärden.

    Det finns bara en metod kvar att implementera: **QueryBounds**. **QueryBounds** hämtar gränserna asynkront, tar resultatet av frågan och tillämpar den på den lokala **BoxCollider**.

    Metoden **QueryBounds** är enkel: skicka en fråga till fjärrrendering-sessionen och vänta på resultatet.

1. Ersätt **QueryBounds** -metoden med följande slutförda metod:

    ```cs
    // Create a query using the model entity
    async private void QueryBounds()
    {
        remoteBoundsQuery = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        await remoteBounds;

        if (remoteBounds.IsCompleted)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

    Vi kontrollerar frågeresultaten för att se om det lyckades. Om ja, konvertera och Använd de returnerade gränserna i ett format som **BoxCollider** kan acceptera.

Nu när **RemoteBounds** -skriptet läggs till i samma spel objekt som **RemoteRenderedModel**, läggs en **BoxCollider** om det behövs och när modellen når sitt `Loaded` tillstånd, kommer gränserna automatiskt att frågas och tillämpas på **BoxCollider**.

1. Lägg till **RemoteBounds** -komponenten med **TestModel** -GameObject som skapades tidigare.
1. Bekräfta att skriptet har lagts till.

     ![Lägg till RemoteBounds-komponent](./media/remote-bounds-script.png)

1. Kör programmet igen. Strax efter att modellen har lästs in visas gränserna för fjärrobjektet. Du ser något som liknar värdena nedan:

     ![Skärm bild som visar exempel på fjär objekt gränser.](./media/updated-bounds.png)

Nu har vi en lokal **BoxCollider** som kon figurer ATS med korrekta gränser på Unity-objektet. Gränserna tillåter visualisering och interaktion med samma strategier som vi använder för ett lokalt återgivet objekt. Till exempel skript som ändrar transformeringen, fysiken och mer.

## <a name="move-rotate-and-scale"></a>Flytta, rotera och skala  

Flyttning, rotation och skalning av fjärranslutna objekt fungerar på samma sätt som andra Unity-objekt. **RemoteRenderingCoordinator** anropar i dess `LateUpdate` metod `Update` den aktuella aktiva sessionen. En del av vad `Update` synkroniserar den lokala modell enheten omvandlas till sina fjärrmotparter. Om du vill flytta, rotera eller skala en fjärrrenderad modell behöver du bara flytta, rotera eller skala transformeringen för GameObject som representerar fjärrmodellen. Här ska vi ändra transformeringen för den överordnade GameObject som har **RemoteRenderedModel** -skriptet kopplat till det.

I den här självstudien används MRTK för objekt interaktion. De flesta av de MRTK-speciella implementeringarna för att flytta, rotera och skala ett objekt ligger utanför omfånget för den här självstudien. Det finns en modell visnings hanterare som är förkonfigurerad i **AppMenu** i menyn **modell verktyg** .

1. Se till att **TestModel** -GameObject som skapades tidigare finns i scenen.
1. Se till att **AppMenu** -Prefab finns i scenen.
1. Tryck på enhetens uppspelnings knapp för att spela upp scenen och öppna menyn **modell verktyg** i **AppMenu**.
![Visa kontrollant](./media/model-with-view-controller.png)

**AppMenu** har en undermeny **modell verktyg** som implementerar en visnings kontroll enhet för bindning med modellen. När GameObject innehåller en **RemoteBounds** -komponent lägger View Controller till en [**BoundingBox**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html) -komponent, som är en MRTK-komponent som återger en avgränsnings ruta runt ett objekt med en **BoxCollider**. En [**ObjectManipulator**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.5.1/api/Microsoft.MixedReality.Toolkit.UI.ObjectManipulator.html)som ansvarar för handinteraktioner. Med de här skripten kan vi flytta, rotera och skala den fjärranslutna modellen.

1. Flytta musen till spel panelen och klicka i den för att fokusera på den.
1. Tryck på och håll ned den vänstra Skift-tangenten med [MRTK hand simulering](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation).
1. Styr den simulerade handen så att hand Ray pekar på test modellen.

    ![Uddig hand Ray](./media/handray-engine.png)

1. Håll kvar genom att klicka och dra modellen för att flytta den.

Du bör se flyttningen av fjärranslutna innehåll tillsammans med markerings rutan. Du kan märka en fördröjning eller fördröjning mellan markerings rutan och fjärrinnehållet. Den här fördröjningen beror på din Internet fördröjning och bandbredd.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>Ray-Cast och spatiala frågor för fjärranslutna modeller

En ruta som krockar runt modeller är lämplig för att interagera med hela modellen, men inte tillräckligt detaljerad för att interagera med enskilda delar av en modell. Vi kan lösa detta genom att använda [databyte](../../../overview/features/spatial-queries.md#ray-casts). Databyte är ett API som tillhandahålls av Azures fjärrrendering för att skicka strålar till den fjärranslutna scenen och returnera träff resultat lokalt. Den här tekniken kan användas för att välja underordnade entiteter i en stor modell eller få resultat information som position, yta, normal och avstånd.

Test modellen har ett antal underordnade entiteter som kan frågas och väljas. För tillfället kommer valet att mata ut namnet på den valda entiteten till Unity-konsolen. Kontrol lera kapitlet [material, belysning och effekt](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining) för att markera den valda entiteten.

Börja med att skapa en statisk omslutning runt mikroray-frågorna. Det här skriptet accepterar en position och riktning i Unity Space, konverterar det till de data typer som accepteras av Remote Ray-Casten och returnerar resultatet. Skriptet kommer att använda `RayCastQueryAsync` API: et.

1. Skapa ett nytt skript med namnet **RemoteRayCaster** och ersätt innehållet med följande kod:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                var result = await RemoteRenderingCoordinator.CurrentSession.Connection.RayCastQueryAsync(rayCast);
                return result.Hits;
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > Unity har en klass med namnet [**RaycastHit**](https://docs.unity3d.com/ScriptReference/RaycastHit.html)och Azure-fjärrrendering har en klass med namnet [**RaycastHit**](/dotnet/api/microsoft.azure.remoterendering.raycasthit). Versalerna **C** är en viktig skillnad för att undvika kompileringsfel.

    **RemoteRayCaster** tillhandahåller en gemensam åtkomst punkt för att omvandla fjärrdatorer till den aktuella sessionen. För att vara mer speciell implementerar vi en MRTK pekare hanterare härnäst. Skriptet implementerar `IMixedRealityPointerHandler` gränssnittet, vilket anger MRTK som vi vill att det här skriptet ska lyssna efter händelser i [Mixad verklighet](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html) .

1. Skapa ett nytt skript med namnet **RemoteRayCastPointerHandler** och Ersätt koden med följande kod:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

**RemoteRayCastPointerHandler** `OnPointerClicked` -metoden anropas av MRTK när en pekare klickar på en krocker, till exempel i en ruta. Efter det `PointerDataToRemoteRayCast` kallas den för att omvandla visarens resultat till en punkt och riktning. Den punkten och riktningen används sedan för att omvandla en fjärran sluten Ray i fjärrsessionen.

![Gränser har uppdaterats](./media/raycast-local-remote.png)

Sändning av begäran om Ray-databyte vid klickning är en effektiv strategi för att skicka frågor till fjärrobjekt. Det är dock inte en bra användar upplevelse eftersom markören kolliderar med rutan kolliderar, inte själva modellen.

Du kan också skapa en ny MRTK-pekare som kastar om sina strålar i fjärrsessionen oftare. Även om detta är en mer komplex metod, är användar upplevelsen bättre. Den här strategin ligger utanför omfånget för den här själv studie kursen, men ett exempel på den här metoden finns i den sammanvisande appen, som finns i [lagrings platsen för arr-exempel](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/Showcase).

När en Ray-Cast har slutförts korrekt i **RemoteRayCastPointerHandler** genereras träffen `Entity` från `OnRemoteEntityClicked` Unity-händelsen. För att svara på händelsen skapar vi ett hjälp skript som godkänner `Entity` och utför en åtgärd på den. Vi börjar med att hämta skriptet för att skriva ut namnet på `Entity` till fel söknings loggen.

1. Skapa ett nytt skript med namnet **RemoteEntityHelper** och ersätt innehållet med följande:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. Lägg till både **RemoteRayCastPointerHandler** -komponenten och **RemoteEntityHelper** -komponenten på **TestModel** -GameObject som skapades tidigare.
1. Tilldela `EntityToDebugLog` metoden till `OnRemoteEntityClicked` händelsen. När händelsens Utdatatyp och metod stämmer, kan vi använda enhetens dynamiska Event-Anslut, som automatiskt skickar händelse värdet till-metoden.
    1. Skapa ett nytt återanrops fält ![ Lägg till motringning](./media/add-callback-remote-entity-clicked.png)
    1. Dra komponenten **Fjärrentitets hjälp** till fältet objekt för att referera till det överordnade GameObject ![ tilldela-objektet](./media/assign-object.png)
    1. Tilldela `EntityToDebugLog` som motringning ![ till motringning](./media/remote-entity-event.png)
1. Tryck på Play i Unity-redigeraren för att starta scenen, Anslut till en fjärrsession och Läs in test modellen.
1. Använd MRTK för manuell simulering och håll vänster SKIFT-tangenten nedtryckt.
1. Styr den simulerade handen så att hand Ray pekar på test modellen.
1. Klicka länge för att simulera en Lufts knackning och köra `OnPointerClicked` händelsen.
1. Observera enhets konsolen för ett logg meddelande med namnet på den underordnade entiteten som valts. Exempel: ![ exempel på underordnad entitet](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>Synkronisera objektet i fjärrobjektet i Union-hierarkin

Hittills har vi bara sett en enda lokal GameObject som representerar hela modellen. Detta fungerar bra för åter givning och manipulering av hela modellen. Om vi vill tillämpa effekter eller ändra vissa underordnade entiteter måste du dock skapa lokala GameObjects för att representera dessa entiteter. Först kan vi utforska manuellt i test modellen.

1. Starta scenen och Läs in test modellen.
1. Expandera de underordnade objekten till **TestModel** -GameObject i enhetens hierarki och välj **TestModel_Entity** GameObject.
1. I kontrollen klickar du på knappen *Visa underordnade* .
![Visa underordnade](./media/show-remote-children.png)
1. Fortsätt att expandera underordnade i hierarkin och klicka på *Visa underordnade* tills en stor lista över underordnade visas.
![Alla underordnade](./media/test-model-children.png)

En lista med dussin tals entiteter fyller nu i hierarkin. Om du väljer en av dem visas- `Transform` och- `RemoteEntitySyncObject` komponenterna i kontrollanten. Som standard synkroniseras inte varje entitet automatiskt varje ram, så lokala ändringar i den `Transform` synkroniseras inte på servern. Du kan kontrol lera *synkronisera varje ram* och sedan flytta, skala eller rotera transformeringen i vyn scen. du kommer inte att se den åter givnings modellen i vyn scen, titta på spel-vyn för att se modellens position och rotation visuellt uppdatering.

Samma process kan utföras program mässigt och är det första steget med att ändra vissa fjär entiteter.

1. Ändra **RemoteEntityHelper** -skriptet så att det också innehåller följande metod:

    ```cs
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. Lägg till ytterligare en motringning till **RemoteRayCastPointerHandler** `OnRemoteEntityClicked` -händelsen och Ställ in den på `MakeSyncedGameObject` .
![Ytterligare motringning](./media/additional-callback.png)
1. Använd MRTK för manuell simulering och håll vänster SKIFT-tangenten nedtryckt.
1. Styr den simulerade handen så att hand Ray pekar på test modellen.
1. Klicka länge för att simulera en Lufts knackning och köra `OnPointerClicked` händelsen.
1. Markera och expandera hierarkin om du vill se ett nytt underordnat objekt som representerar den klickade entiteten.
![GameObject-representation](./media/gameobject-representing-entity.png)
1. När du har testat tar du bort återanropet för `MakeSyncedGameObject` , eftersom vi införlivar detta som en del av andra effekter senare.

> [!NOTE]
> Synkronisering av varje ram krävs bara när du behöver synkronisera Transformations data. Det finns vissa kostnader för synkronisering av transformeringar, så det bör användas sparsamt.

Det första steget med att ändra underordnade entiteter är att skapa en lokal instans och göra den automatiskt synkroniserad. Samma tekniker som vi har använt för att ändra modellen som helhet kan även användas på underentiteter. När du till exempel har skapat en synkroniserad lokal instans av en entitet kan du fråga dess gränser och lägga till modifierings hanterare som gör det möjligt att flytta runt användarens hand strålar.

## <a name="next-steps"></a>Nästa steg

Du kan nu ändra och interagera med dina fjärranslutna modeller! I nästa självstudie får vi se hur du ändrar material, ändrar belysningen och tillämpar effekter på fjärranslutna modeller.

> [!div class="nextstepaction"]
> [Nästa: förfina material, belysning och effekter](../materials-lighting-effects/materials-lighting-effects.md)