---
title: Starta och stoppa klusternoder
description: Lär dig hur du använder fel inmatning för att testa ett Service Fabric program genom att starta och stoppa klusternoder.
author: LMWF
ms.topic: conceptual
ms.date: 6/12/2017
ms.author: lemai
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c31040ec13084f9e4b08bbc9a347e4ad44975bf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89021263"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>Ersätta startnoden och stoppa Node-API: er med nodens över gångs-API

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>Vad gör API-noden och start-nodens API: er?

API för att stoppa noden (hanterade: [StopNodeAsync ()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps]) stoppar en Service Fabric nod.  En Service Fabric nod är process, inte en virtuell dator eller dator – den virtuella datorn eller datorn kommer fortfarande att köras.  För resten av dokumentet "Node" betyder det att Service Fabric nod.  Om du *stoppar en nod* försätts den i ett *stoppat* tillstånd där den inte är medlem i klustret och kan därför inte hantera tjänster, vilket simulerar en nod.  Detta är användbart för att mata in fel i systemet för att testa ditt program.  Startnodens API (hanterat: [StartNodeAsync ()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]]) kastar om API för att stoppa noden, vilket gör att noden återgår till ett normalt tillstånd.

## <a name="why-are-we-replacing-these"></a>Varför ersätter vi dessa?

Som tidigare beskrivits är en *stoppad* Service Fabric nod en nod som avsiktligen riktar sig mot att använda API: et för att stoppa noden.  En *nod är inte tillgänglig för* andra orsaker (till exempel att den virtuella datorn eller datorn är avstängd).  Med API för att stoppa noden, visar systemet inte information för att skilja mellan *stoppade* *noder och noder* .

Dessutom är vissa fel som returneras av dessa API: er inte så beskrivande som de kan vara.  Om du t. ex. anropar API: et för att stoppa noden på en redan *stoppad* nod returneras felet *InvalidAddress*.  Den här upplevelsen kan förbättras.

Varaktigheten för en nod stoppas också för "oändlig" tills startnodens API anropas.  Vi har hittat detta kan orsaka problem och kan vara fel känsligt.  Vi har till exempel sett problem där en användare anropade API för att stoppa noden på en nod och sedan glömmer bort det.  Senare *var det* oklart om noden *stoppades eller stoppades*.


## <a name="introducing-the-node-transition-apis"></a>Introducera API: erna för noden över gångar

Vi har åtgärdat problemen ovan i en ny uppsättning API: er.  Den nya noden över gångs-API (hanterade: [StartNodeTransitionAsync ()][snt]) kan användas för att överföra en Service Fabric-nod till ett *stoppat* tillstånd, eller för att överföra den från ett *stoppat* tillstånd till ett vanligt läge.  Observera att "Start" i namnet på API: et inte refererar till att starta en nod.  Det syftar på att starta en asynkron åtgärd som systemet ska köra för att överföra noden till antingen *stoppad* eller startad.

**Användning**

Om nodens över gångs-API inte genererar ett undantag när det anropas, har systemet godkänt den asynkrona åtgärden och kör den.  Ett lyckat anrop innebär inte att åtgärden har slutförts ännu.  Om du vill hämta information om åtgärdens aktuella tillstånd anropar du nodens förlopps-API (hanterad: [GetNodeTransitionProgressAsync ()][gntp]) med det GUID som användes när du anropar nodens över gångs-API för den här åtgärden.  Nodens över gångs förlopps-API returnerar ett NodeTransitionProgress-objekt.  Egenskapen State för det här objektet anger åtgärdens aktuella tillstånd.  Om status är "körs" körs åtgärden.  Om det är klart slutförs åtgärden utan fel.  Om det är fel har det uppstått ett problem med att köra åtgärden.  Resultat egenskapens undantags egenskap indikerar vad problemet var.  Se https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate för mer information om egenskapen State och avsnittet "exempel användning" nedan för kod exempel.


**Differentiering mellan en stoppad nod och en nod** Om en nod *stoppas* med hjälp av noden över gångs-API, visar utdata för en Node-fråga (hanterad: [GetNodeListAsync ()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps]) att den här noden har ett värde för egenskapen *IsStopped* True.  Observera att detta skiljer sig från värdet för egenskapen *NodeStatus* , som kommer att säga *nedåt*.  Om egenskapen *NodeStatus* har värdet *Down*, men *IsStopped* är false, stoppades inte noden med nodens över gångs-API och är *avstängd* på grund av någon annan orsak.  Om egenskapen *IsStopped* är true och egenskapen *NodeStatus* är *nere* stoppades den med nodens över gångs-API.

Om du startar en *stoppad* nod med nodens över gångs-API kommer den att fungera som en normal medlem i klustret igen.  Utdata från API: t för Node-frågor visar *IsStopped* som falskt och *NodeStatus* som något som inte är nere (till exempel upp).


**Begränsad varaktighet** När du använder noden över gångs-API för att stoppa en nod, representerar en av de obligatoriska parametrarna, *stopNodeDurationInSeconds*, hur lång tid i sekunder som noden *stoppas*.  Värdet måste vara inom det tillåtna intervallet, som har minst 600 och högst 14400.  När den här tiden går ut startar noden om automatiskt i upp-läge.  Se exempel 1 nedan för ett exempel på användning.

> [!WARNING]
> Undvik att blanda nodens över gångs-API: er och noden stoppa och starta Node API: er.  Rekommendationen är att endast använda nodens över gångs-API.  > om en nod redan har stoppats med hjälp av API: et för att stoppa Node, ska den startas med hjälp av startnodens API först innan du använder API: erna för över gångar > i noden.

> [!WARNING]
> Det går inte att utföra flera noder över gångs-API-anrop på samma nod parallellt.  I den här situationen > noden över gångs-API: et för nodens över gångs-API ett FabricException med värdet NodeTransitionInProgress för ErrorCode.  När en nod över gång på en speciell nod har > startats, bör du vänta tills åtgärden når ett Terminal-tillstånd (slutfört, trasigt eller ForceCancelled) innan du startar en > ny över gång på samma nod.  Parallella nods över gångs anrop på olika noder tillåts.


#### <a name="sample-usage"></a>Exempelanvändning


**Exempel 1** – följande exempel använder nodens över gångs-API för att stoppa en nod.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Exempel 2** – följande exempel startar en *stoppad* nod.  Den använder vissa hjälp metoder från det första exemplet.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**Exempel 3** – följande exempel visar felaktig användning.  Den här användningen är felaktig eftersom den *stopDurationInSeconds* som den tillhandahåller är större än det tillåtna intervallet.  Eftersom StartNodeTransitionAsync () Miss fungerar med ett allvarligt fel, accepterades inte åtgärden och förlopps-API: t ska inte anropas.  I det här exemplet används vissa hjälp metoder från det första exemplet.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**Exempel 4** – följande exempel visar den fel information som kommer att returneras från nodens förlopps-API när åtgärden som initierades av nodens över gångs-API accepteras, men Miss lyckas senare vid körning.  I så fall Miss lyckas det eftersom nodens över gångs-API försöker starta en nod som inte finns.  I det här exemplet används vissa hjälp metoder från det första exemplet.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[stopnodeps]: /previous-versions/azure/mt125982(v=azure.100)
[startnode]: /dotnet/api/system.fabric.fabricclient.faultmanagementclient
[startnodeps]: /previous-versions/azure/mt163520(v=azure.100)
[nodequery]: /dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: /powershell/module/servicefabric/get-servicefabricnode
[snt]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: /dotnet/api/system.fabric.fabricclient.testmanagementclient
