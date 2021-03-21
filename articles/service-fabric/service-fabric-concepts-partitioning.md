---
title: Partitionera Service Fabric tjänster
description: Lär dig att partitionera Service Fabric tillstånds lösa och tillstånds känsliga tjänster
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 199ae9d9844149c1931da638633110f717fe0517
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97915903"
---
# <a name="partition-service-fabric-reliable-services"></a>Partitionera tillförlitliga Service Fabric-tjänster
Den här artikeln innehåller en introduktion till de grundläggande begreppen för partitionering av Azure Service Fabric Reliable Services. Partitionering möjliggör data lagring på de lokala datorerna så att data och data bearbetning kan skalas tillsammans.

> [!TIP]
> Ett [fullständigt exempel](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions) på koden i den här artikeln finns på GitHub.

## <a name="partitioning"></a>Partitionering
Partitionering är inte unikt för Service Fabric. I själva verket är det ett kärn mönster för att skapa skalbara tjänster. I en större mening kan vi tänka på partitionering som ett koncept för att dela upp tillstånd (data) och beräkna i mindre tillgängliga enheter för att förbättra skalbarhet och prestanda. En välkänd form av partitionering är [data partitionering][wikipartition], även kallat horisontell partitionering.

### <a name="partition-service-fabric-stateless-services"></a>Partitionera Service Fabric tillstånds lösa tjänster
För tillstånds lösa tjänster kan du tänka på att en partition är en logisk enhet som innehåller en eller flera instanser av en tjänst. Bild 1 visar en tillstånds lös tjänst med fem instanser som distribueras över ett kluster med en partition.

![Tillstånds lös tjänst](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Det finns i själva verket två typer av tillstånds lösa tjänst lösningar. Den första är en tjänst som behåller sitt tillstånd externt, till exempel i en databas i Azure SQL Database (t. ex. en webbplats som lagrar sessionsinformation och data). Den andra är endast för beräknings tjänster (t. ex. en kalkylator eller bild miniatyr) som inte hanterar något beständigt tillstånd.

I båda fallen är partitionering av en tillstånds lös tjänst ett mycket sällsynt scenario – skalbarhet och tillgänglighet uppnås normalt genom att lägga till fler instanser. Den enda gången du vill överväga att använda flera partitioner för tillstånds lösa tjänst instanser är när du behöver möta särskilda Dirigerings begär Anden.

Som exempel bör du tänka på ett fall där användare med ID i ett visst intervall endast ska hanteras av en viss tjänst instans. Ett annat exempel på när du kan partitionera en tillstånds lös tjänst är om du har en helt partitionerad Server del (t. ex. en shardade-databas i SQL Database) och du vill styra vilken tjänst instans som ska skrivas till databasen Shard – eller utföra andra förberedelser i den tillstånds lösa tjänsten som kräver samma partitionerings information som används i Server delen. Dessa typer av scenarier kan också lösas på olika sätt och kräver inte nödvändigt vis tjänst partitionering.

Resten av den här genom gången fokuserar på tillstånds känsliga tjänster.

### <a name="partition-service-fabric-stateful-services"></a>Partitionera Service Fabric tillstånds känsliga tjänster
Service Fabric är det enkelt att utveckla skalbara tillstånds känsliga tjänster genom att erbjuda ett första klass sätt att partitionera tillstånd (data). Konceptuellt kan du tänka på en partition av en tillstånds känslig tjänst som en skalnings enhet som är mycket tillförlitlig via [repliker](service-fabric-availability-services.md) som distribueras och bal anse ras över noderna i ett kluster.

Partitionering i samband med Service Fabric tillstånds känsliga tjänster syftar på att fastställa att en viss tjänstmall är ansvarig för en del av tjänstens fullständiga status. (Som tidigare nämnts är en partition en uppsättning [repliker](service-fabric-availability-services.md)). En bra sak om Service Fabric är att partitionerna placeras på olika noder. Detta gör det möjligt för dem att växa till en nods resurs gräns. När data behöver växa, växer partitionerna och Service Fabric ombalansera partitioner mellan noder. Detta säkerställer den fortsatta effektiva användningen av maskin varu resurser.

Anta att du börjar med ett kluster med fem noder och en tjänst som har kon figurer ATS för att ha 10 partitioner och ett mål av tre repliker. I det här fallet balanserar och distribuerar Service Fabric replikerna i klustret – och du får två primära [repliker](service-fabric-availability-services.md) per nod.
Om du nu behöver skala ut klustret till 10 noder, skulle Service Fabric debiteras de primära [replikerna](service-fabric-availability-services.md) på alla 10 noder. Om du skalade tillbaka till 5 noder, kan Service Fabric på samma sätt balansera om alla repliker över 5 noderna.  

Bild 2 visar fördelningen av 10 partitioner före och efter skalning av klustret.

![Tillstånds känslig tjänst](./media/service-fabric-concepts-partitioning/partitions.png)

Därför uppnås utskalning eftersom begär Anden från klienter distribueras mellan datorer, den övergripande prestandan för programmet förbättras och konkurrens om åtkomsten till data segmenten minskas.

## <a name="plan-for-partitioning"></a>Planera för partitionering
Innan du implementerar en tjänst bör du alltid fundera över vilken partitionerings strategi som krävs för att skala ut. Det finns olika sätt, men alla fokuserar på vad programmet behöver för att uppnå. Vi är medveten om några av de viktiga aspekterna av den här artikeln.

Ett bättre tillvägagångs sätt är att tänka på strukturen för det tillstånd som behöver partitioneras, som det första steget.

Låt oss ta ett enkelt exempel. Om du vill skapa en tjänst för en delmängds avsökning kan du skapa en partition för varje stad i regionen. Sedan kan du lagra rösterna för varje person i staden i den partition som motsvarar den staden. Bild 3 visar en uppsättning personer och den ort där de finns.

![Enkel partition](./media/service-fabric-concepts-partitioning/cities.png)

Eftersom populationen av städer varierar mycket, kan det hända att du får en del partitioner som innehåller mycket data (t. ex. Seattle) och andra partitioner med mycket litet tillstånd (t. ex. Kirkland). Så vad är effekten av att ha partitioner med ojämna mängder av tillstånd?

Om du tror att du ser exemplet igen kan du enkelt se att partitionen som innehåller rösterna för Seattle får mer trafik än Kirkland en. Service Fabric ser som standard till att det finns ungefär samma antal primära och sekundära repliker på varje nod. Det innebär att du kan få noder som innehåller repliker som hanterar mer trafik och andra som betjänar mindre trafik. Du vill helst undvika frekventa och kalla fläckar som i ett kluster.

För att undvika detta bör du göra två saker, från en partitionering plats i vyn:

* Försök att partitionera statusen så att den är jämnt fördelad på alla partitioner.
* Rapport belastning från var och en av-replikerna för tjänsten. (Mer information om hur finns i den här artikeln om [mått och belastning](service-fabric-cluster-resource-manager-metrics.md)). Service Fabric ger möjlighet att rapportera belastning som används av tjänster, till exempel mängden minne eller antalet poster. Baserat på de mått som rapporter ATS identifierar Service Fabric att vissa partitioner betjänar högre belastningar än andra och balanserar klustret genom att flytta repliker till mer lämpliga noder, så att ingen nod överbelastas.

Ibland vet du inte hur mycket data som finns i en specifik partition. En allmän rekommendation är att göra både och först genom att anta en partitionerings strategi som sprider data jämnt över partitionerna och andra, genom att använda rapporterings belastning.  Den första metoden förhindrar situationer som beskrivs i röstnings exemplet, medan den andra hjälper till att utjämna temporära skillnader i åtkomst eller belastning över tid.

En annan aspekt av att planera partitionen är att välja rätt antal partitioner att börja med.
Från ett Service Fabric perspektiv finns det inget som hindrar dig från att starta med ett högre antal partitioner än förväntat för ditt scenario.
I själva verket är det ett giltigt tillvägagångs sätt att anta att det maximala antalet partitioner är giltigt.

I sällsynta fall kan du behöva fler partitioner än du först har valt. Eftersom du inte kan ändra antalet partitioner efter faktumet måste du tillämpa några metoder för avancerad partition, till exempel skapa en ny tjänst instans av samma typ av tjänst. Du måste också implementera en del trafik på klient sidan som skickar begär anden till rätt tjänst instans, baserat på information på klient sidan som din klient kod måste underhålla.

Ett annat övervägande för partitionerings planering är tillgängliga dator resurser. När tillstånd måste nås och lagras, är du kopplad till följande:

* Begränsningar för nätverks bandbredd
* System minnes gränser
* Disk lagrings gränser

Vad händer om du kör i resurs begränsningar i ett kluster som körs? Svaret är att du enkelt kan skala ut klustret så att det passar de nya kraven.

[I kapacitets planerings guiden](service-fabric-capacity-planning.md) får du vägledning för hur du avgör hur många noder klustret behöver.

## <a name="get-started-with-partitioning"></a>Kom igång med partitionering
I det här avsnittet beskrivs hur du kommer igång med att partitionera din tjänst.

Service Fabric erbjuder ett val av tre partition scheman:

* Intervall partitionering (kallas även UniformInt64Partition).
* Namngiven partitionering. Program som använder den här modellen har vanligt vis data som kan vara i Bucket, inom en begränsad mängd. Några vanliga exempel på data fält som används som namngivna partitionsnyckel är regioner, post nummer, kund grupper eller andra affärs gränser.
* Singleton-partitionering. Singleton-partitioner används vanligt vis när tjänsten inte kräver ytterligare routning. Till exempel kan tillstånds lösa tjänster använda detta partitionerings schema som standard.

Namngivna och singleton-partitionerings scheman är särskilda former av intervallbaserade partitioner. Som standard är Visual Studio-mallarna för Service Fabric använda mellanliggande partitionering, eftersom det är den vanligaste och användbara. Resten av den här artikeln fokuserar på det intervallbaserade partitionerings schemat.

### <a name="ranged-partitioning-scheme"></a>Intervall för partitionerings scheman
Detta används för att ange ett heltals intervall (identifieras av en låg nyckel och hög nyckel) och ett antal partitioner (n). Den skapar n partitioner, som var och en ansvarar för ett under intervall som inte överlappar under intervallet för den totala partitionsnyckel. Till exempel ett intervall för partitionering med en låg nyckel på 0, en hög nyckel på 99 och antalet 4 skulle skapa fyra partitioner, som du ser nedan.

![Intervall partitionering](./media/service-fabric-concepts-partitioning/range-partitioning.png)

En vanlig metod är att skapa en hash baserat på en unik nyckel i data uppsättningen. Några vanliga exempel på nycklar är ett fordons identifierings nummer (VIN), ett medarbetar-ID eller en unik sträng. Genom att använda den här unika nyckeln genererar du en hash-kod, Modulus nyckel intervallet, som ska användas som nyckel. Du kan ange de övre och nedre gränserna för det tillåtna nyckel intervallet.

### <a name="select-a-hash-algorithm"></a>Välj en hash-algoritm
En viktig del av hashing är att välja din hash-algoritm. Ett övervägande är om målet är att gruppera liknande nycklar nära varandra (lokal känslig hashing)--eller om en aktivitet ska distribueras brett över alla partitioner (distributions-hashing), vilket är vanligare.

Egenskaperna för en bra distributions-hash-algoritm är att det är enkelt att beräkna, det har några kollisioner och distribuerar nycklar jämnt. Ett bra exempel på en effektiv hash-algoritm är [FNV-1-](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) hashalgoritmen.

En lämplig resurs för allmänna alternativ för hash-kod är [Wikipedia-sidan på hash-funktioner](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Bygg en tillstånds känslig tjänst med flera partitioner
Nu ska vi skapa din första pålitliga tillstånds känsliga tjänst med flera partitioner. I det här exemplet ska du skapa ett mycket enkelt program där du vill lagra alla efter namn som börjar med samma bokstav i samma partition.

Innan du skriver någon kod måste du tänka på partitionerna och partitionernas nycklar. Du behöver 26 partitioner (en för varje bokstav i alfabetet), men vad gäller för låga och höga nycklar?
Eftersom vi verkligen vill ha en partition per bokstav kan vi använda 0 som låg nyckel och 25 som hög nyckel, eftersom varje bokstav är en egen nyckel.

> [!NOTE]
> Detta är ett förenklat scenario, som i verkligheten skulle distributionen bli ojämn. Efter namn som börjar med bokstäverna "S" eller "M" är vanligare än de som börjar med "X" eller "Y".
> 
> 

1. Öppna **Visual Studio**-  >  **filen**  >  **nytt**  >  **projekt**.
2. I dialog rutan **nytt projekt** väljer du programmet Service Fabric.
3. Anropa projektet "AlphabetPartitions".
4. I dialog rutan **skapa en tjänst** väljer du **tillstånds känslig** tjänst och anropar den "alfabetet. bearbetar".
5. Ange antalet partitioner. Öppna den Applicationmanifest.xml-fil som finns i mappen ApplicationPackageRoot i AlphabetPartitions-projektet och uppdatera parametern Processing_PartitionCount till 26 som visas nedan.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    Du måste också uppdatera egenskaperna LowKey och HighKey för StatefulService-elementet i ApplicationManifest.xml som visas nedan.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. För att tjänsten ska kunna nås öppnar du en slut punkt på en port genom att lägga till slut punkts elementet för ServiceManifest.xml (finns i mappen PackageRoot) för alfabetet. bearbetar tjänst enligt nedan:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    Tjänsten är nu konfigurerad för att lyssna på en intern slut punkt med 26 partitioner.
7. Sedan måste du åsidosätta `CreateServiceReplicaListeners()` metoden för bearbetnings klassen.
   
   > [!NOTE]
   > För det här exemplet förutsätter vi att du använder en enkel HttpCommunicationListener. Mer information om tillförlitlig tjänst kommunikation finns i [den tillförlitliga tjänst kommunikations modellen](service-fabric-reliable-services-communication.md).
   > 
   > 
8. Ett rekommenderat mönster för den URL som en replik lyssnar på är följande format: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}` .
    Så du vill konfigurera din kommunikations lyssnare så att den lyssnar på rätt slut punkter och med det här mönstret.
   
    Flera repliker av den här tjänsten kanske finns på samma dator, så den här adressen måste vara unik för repliken. Detta är anledningen till att partitions-ID + replik-ID finns i URL: en. HttpListener kan lyssna på flera adresser på samma port så länge URL-prefixet är unikt.
   
    Extra GUID finns där för ett avancerat fall där sekundära repliker också lyssnar efter skrivskyddade begär Anden. I så fall vill du se till att en ny unik adress används vid över gång från primär till sekundär för att tvinga klienter att matcha adressen igen. "+" används som adress här så att repliken lyssnar på alla tillgängliga värdar (IP, FQDN, localhost osv.) I koden nedan visas ett exempel.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Det är också värt att notera att den publicerade URL: en skiljer sig något från lyssnings-URL-prefixet.
    Lyssnings-URL: en har fått till HttpListener. Den publicerade URL: en är den URL som publiceras till Service Fabric Naming Service, som används för tjänst identifiering. Klienterna kommer att fråga efter den här adressen genom den identifierings tjänsten. Den adress som klienterna behöver för att ha den faktiska IP-adressen eller FQDN för noden för att kunna ansluta. Du måste ersätta "+" med nodens IP eller FQDN enligt ovan.
9. Det sista steget är att lägga till bearbetnings logiken till tjänsten som visas nedan.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` läser värdena för frågesträngparametern som används för att anropa partitionen och anropen för att `AddUserAsync` lägga till LastName i den tillförlitliga ord listan `dictionary` .
10. Nu ska vi lägga till en tillstånds lös tjänst i projektet för att se hur du kan anropa en viss partition.
    
    Den här tjänsten fungerar som ett enkelt webb gränssnitt som accepterar LastName som en frågesträngparametern, fastställer partitionsnyckel och skickar den till den alfabetiska. bearbetnings tjänsten för bearbetning.
11. I dialog rutan **skapa en tjänst** väljer du **tillstånds lös** tjänst och anropar den "alfabete. Web" som visas nedan.
    
    ![Skärm bild för tillstånds lös tjänst](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Uppdatera slut punkts informationen i ServiceManifest.xml av tjänsten alfabet. WebApi för att öppna en port som visas nedan.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. Du måste returnera en samling ServiceInstanceListeners i-klass webben. Återigen kan du välja att implementera en enkel HttpCommunicationListener.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Nu måste du implementera bearbetnings logiken. HttpCommunicationListener anropas `ProcessInputRequest` när en förfrågan kommer in. Nu ska vi gå vidare och lägga till koden nedan.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Låt oss gå igenom det steg för steg. Koden läser den första bokstaven i frågesträngparametern `lastname` i ett tecken. Sedan fastställer den partitionsnyckel för den här bokstaven genom att subtrahera det hexadecimala värdet `A` från det hexadecimala värdet för de senaste namnen: s första bokstav.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Kom ihåg att vi använder 26 partitioner med en partitionsnyckel per partition i det här exemplet.
    Därefter hämtar vi tjänstepartitionen `partition` för den här nyckeln genom att använda- `ResolveAsync` metoden på `servicePartitionResolver` objektet. `servicePartitionResolver` definieras som
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    `ResolveAsync`Metoden tar tjänst-URI: n, partitionsnyckel och en token token som parametrar. Tjänst-URI: n för bearbetnings tjänsten är `fabric:/AlphabetPartitions/Processing` . Sedan hämtar vi slut punkten för partitionen.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Slutligen skapar vi slut punkts-URL plus QueryString och anropar bearbetnings tjänsten.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    När bearbetningen är färdig skriver vi tillbaka utdata.
15. Det sista steget är att testa tjänsten. Visual Studio använder program parametrar för lokal distribution och moln distribution. Om du vill testa tjänsten med 26 partitioner lokalt måste du uppdatera `Local.xml` filen i mappen ApplicationParameters i AlphabetPartitions-projektet enligt nedan:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. När du har slutfört distributionen kan du kontrol lera tjänsten och alla dess partitioner i Service Fabric Explorer.
    
    ![Service Fabric Explorer skärm bild](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. I en webbläsare kan du testa partitionerings logiken genom att ange `http://localhost:8081/?lastname=somename` . Du kommer att se att varje efter namn som börjar med samma bokstav lagras på samma partition.
    
    ![Webbläsarens skärm bild](./media/service-fabric-concepts-partitioning/samplerunning.png)

Den fullständiga lösningen av koden som används i den här artikeln finns här: https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions .

## <a name="next-steps"></a>Nästa steg
Läs mer om Service Fabric Services:

* [Anslut och kommunicera med tjänster i Service Fabric](service-fabric-connect-and-communicate-with-services.md)
* [Tillgänglighet för Service Fabric tjänster](service-fabric-availability-services.md)
* [Skalbarhet för Service Fabric tjänster](service-fabric-concepts-scalability.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
