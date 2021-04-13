---
title: 'Snabb start: skicka och ta emot händelser med go-Azure Event Hubs'
description: 'Snabb start: den här artikeln innehåller en genom gång av hur du skapar ett Go-program som skickar händelser från Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: ededd042dadd6059bd7b2d689cff78fe1bd6af0b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311098"
---
# <a name="quickstart-send-events-to-or-receive-events-from-event-hubs-using-go"></a>Snabb start: skicka händelser till eller ta emot händelser från Event Hubs med go
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här självstudien beskrivs hur du skriver Go-program för att skicka händelser till eller ta emot händelser från en händelsehubben. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien, finns följande förhandskrav:

- Go installeras lokalt. Följ [dessa anvisningar](https://golang.org/doc/install) om det behövs.
- Ett aktivt Azure-konto. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto][] innan du börjar.
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Använd [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md).

## <a name="send-events"></a>Skicka händelser
Det här avsnittet visar hur du skapar ett Go-program för att skicka händelser till en händelsehubben. 

### <a name="install-go-package"></a>Installera go-paket

Hämta go-paketet för Event Hubs med `go get` eller `dep` . Exempel:

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

### <a name="import-packages-in-your-code-file"></a>Importera paket i din kod fil

Om du vill importera go-paketen använder du följande kod exempel:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Skapa ett nytt huvud namn för tjänsten genom att följa anvisningarna i [skapa ett huvud namn för Azure-tjänsten med Azure CLI 2,0](/cli/azure/create-an-azure-service-principal-azure-cli). Spara de angivna autentiseringsuppgifterna i din miljö med följande namn. Både Azure SDK för Go och Event Hubs paket är förkonfigurerade för att leta efter följande variabel namn:

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Nu ska du skapa en autentiseringsprovider för din Event Hubs-klient som använder dessa autentiseringsuppgifter:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="create-event-hubs-client"></a>Skapa Event Hubs-klient

Följande kod skapar en Event Hubs-klient:

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

### <a name="write-code-to-send-messages"></a>Skriv kod för att skicka meddelanden

I följande kodfragment använder du (1) för att skicka meddelanden interaktivt från en terminal eller (2) för att skicka meddelanden i ditt program:

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!"))
```

### <a name="extras"></a>Tillägg

Hämta ID: n för partitionerna i händelsehubben:

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n", info.PartitionIDs)
```

Kör programmet för att skicka händelser till händelsehubben. 

Grattis! Du har nu skickat meddelanden till en händelsehubb.

## <a name="receive-events"></a>Ta emot händelser

### <a name="create-a-storage-account-and-container"></a>Skapa ett lagrings konto och en behållare

Tillstånd som lån på partitioner och kontroll punkter i händelse strömmen delas mellan mottagare som använder en Azure Storage-behållare. Du kan skapa ett lagrings konto och en behållare med go SDK, men du kan också skapa en genom att följa anvisningarna i [om Azure Storage-konton](../storage/common/storage-account-create.md).

Exempel på hur du skapar lagrings artefakter med go SDK finns i [exemplen lagrings platsen](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage) och i exemplet som motsvarar den här självstudien.

### <a name="go-packages"></a>Go-paket

Om du vill ta emot meddelanden hämtar du Go-paketen för Event Hubs med `go get` eller `dep` :

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

### <a name="import-packages-in-your-code-file"></a>Importera paket i din kod fil

Om du vill importera go-paketen använder du följande kod exempel:

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
)
```

### <a name="create-service-principal"></a>Skapa tjänstens huvudnamn

Skapa ett nytt huvud namn för tjänsten genom att följa anvisningarna i [skapa ett huvud namn för Azure-tjänsten med Azure CLI 2,0](/cli/azure/create-an-azure-service-principal-azure-cli). Spara de angivna autentiseringsuppgifterna i din miljö med följande namn: både Azure SDK för Go och Event Hubs paketet är förkonfigurerade för att söka efter dessa variabel namn.

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

Skapa sedan en autentiseringsprovider för din Event Hubs-klient som använder dessa autentiseringsuppgifter:

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

### <a name="get-metadata-struct"></a>Hämta metadata struct

Få en STRUCT med metadata om Azure-miljön med hjälp av Azure go SDK. Senare åtgärder använder den här strukturen för att hitta rätt slut punkter.

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

### <a name="create-credential-helper"></a>Skapa Credential-hjälp 

Skapa en Credential-hjälp som använder tidigare Azure Active Directory-autentiseringsuppgifter (AAD) för att skapa en SAS-autentiseringsuppgift (signatur för delad åtkomst) för lagring. Den sista parametern anger att konstruktorn använder samma miljövariabler som tidigare:

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

### <a name="create-a-check-pointer-and-a-leaser"></a>Skapa en check pekare och en låne 

Skapa en **låne** ansvarig för att leasa en partition till en viss mottagare och en **kontroll pekare** som ansvarar för att skriva kontroll punkter för meddelande strömmen så att andra mottagare kan börja läsa från rätt förskjutning.

För närvarande finns det en enda **StorageLeaserCheckpointer** som använder samma lagrings behållare för att hantera både lån och kontroll punkter. Förutom lagrings kontot och behållar namn måste **StorageLeaserCheckpointer** ha de autentiseringsuppgifter som skapades i föregående steg och Azure-miljön för att få åtkomst till behållaren korrekt.

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

### <a name="construct-event-processor-host"></a>Konstruera värd för händelse bearbetning

Nu har du de bitar som behövs för att skapa en EventProcessorHost, enligt följande. Samma **StorageLeaserCheckpointer** används både som en låne-och kontroll pekare, enligt beskrivningen ovan:

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

### <a name="create-handler"></a>Skapa hanterare 

Nu ska du skapa en hanterare och registrera den med händelse bearbetnings värden. När värden har startats tillämpas den här och andra angivna hanterare i inkommande meddelanden:

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

### <a name="write-code-to-receive-messages"></a>Skriv kod för att ta emot meddelanden

Med allt som har kon figurer ATS kan du starta värddatorn för händelse bearbetning med `Start(context)` och fortsätta att köra den permanent, eller `StartNonBlocking(context)` bara köra så länge meddelanden är tillgängliga.

Den här självstudien startar och körs enligt följande. Se exempel på GitHub med `StartNonBlocking` :

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.yml)


<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[kostnads fritt konto]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
