---
title: Programmerings guide för SCP.NET för storm i Azure HDInsight
description: Lär dig hur du använder SCP.NET för att skapa. NET-baserade Storm-topologier för användning med storm som körs i Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 01/13/2020
ms.openlocfilehash: bd52157e2f0e20e9282d944b07f656c08d9e57da
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932636"
---
# <a name="scp-programming-guide-for-apache-storm-in-azure-hdinsight"></a>Programmerings guide för SCP för Apache Storm i Azure HDInsight

SCP är en plattform för att bygga real tids-, pålitliga, konsekventa och högpresterande data bearbetnings program. Den är byggd ovanpå [Apache Storm](https://storm.incubator.apache.org/), som är ett data ström bearbetnings system som har utformats av program varu grupper med öppen källkod. Nathan Marz skapade storm. Den publicerades som öppen källkod av Twitter. Storm använder [Apache ZooKeeper](https://zookeeper.apache.org/), vilket är ett annat Apache-projekt som möjliggör mycket tillförlitlig distribuerad samordning och tillstånds hantering.

SCP-projektet har inte bara storm i Windows, utan även projekt tillägg och anpassningar för Windows-miljön. Tilläggen inkluderar .NET-utvecklarens miljö och .NET-bibliotek. Anpassningen omfattar Windows-baserad distribution.

Med tilläggen och anpassningen behöver du inte förgrena program vara med öppen källkod. Du kan använda härledda miljöer som byggts ovanpå storm.

## <a name="processing-model"></a>Bearbetar modell

Data i SCP är modellerade som kontinuerliga strömmar av tupler. Normalt är tupler:

1. Flöda till en kö.
1. Hämtas och omvandlas av affärs logik som finns i en Storm-topologi.
1. Antingen har utdata skickas som tupler till ett annat SCP-system, eller så är de allokerade till butiker som distribuerade fil system och databaser som SQL Server.

![Ett diagram över att mata in data i kön för bearbetning, vilket i sin tur matar in ett data lager](./media/apache-storm-scp-programming-guide/queue-feeding-data-to-processing-to-data-store.png)

I storm definierar en Programtopologi ett beräknings diagram. Varje nod i en topologi innehåller bearbetnings logik. Länkar mellan noder indikerar data flödet.

Noder som infogar indata i topologin kallas _kanaler_. Du kan använda dem för att sekvensera data. Indata kan komma från en källa som fil loggar, en transaktions databas eller en system prestanda räknare.

Noder som har både indata och utdata i data flöden kallas _bultar_. De använder faktiska data filtrering, val och sammansättning.

SCP stöder bästa ansträngningar, minst en gång, och exakt en gång för data bearbetning.

I ett program med distribuerad ström bearbetning kan fel inträffa under data bearbetning. Sådana fel omfattar ett nätverks avbrott, ett dator fel eller ett fel i koden. Vid minst en bearbetning säkerställer att alla data bearbetas minst en gång genom att automatiskt spela upp samma data igen när ett fel inträffar.

Minst en bearbetning är enkel och tillförlitlig och passar många program. Men när ett program kräver exakt inventering, är det inte tillräckligt med minst en bearbetning eftersom samma data kan spelas upp i programtopologin. I så fall sker exakt en gång när bearbetningen gör att resultatet är korrekt även när data spelas upp och bearbetas flera gånger.

Med SCP kan .NET-utvecklare skapa data bearbetnings program i real tid när de använder en Java Virtual Machine (JVM) med storm. En JVM-och .NET-kommunikation via TCP-lokala Sockets. Varje kanalen/bult är ett .NET/Java-process par där användar logiken körs i en .NET-process som ett plugin-program.

Följ dessa steg om du vill bygga ett data bearbetnings program ovanpå SCP:

1. Utforma och implementera kanaler för att hämta data från köer.
1. Utforma och implementera bultar som bearbetar indata och sparar dem till externa butiker som en databas.
1. Utforma topologin och skicka och kör den.

Topologin definierar formhörn och de data som flödar mellan dem. SCP tar en topologi och distribuerar den på ett Storm-kluster där varje hörn körs på en logisk nod. Den storm Schemaläggaren tar hand om redundans och skalning.

Den här artikeln använder några enkla exempel för att gå igenom hur du skapar data bearbetnings program med SCP.

## <a name="scp-plug-in-interface"></a>SCP-plugin-gränssnitt

SCP-plugin-program är fristående program. De kan köras i Visual Studio under utvecklingen och vara anslutna till storm-pipelinen efter produktions distributionen.

Att skriva ett SCP-plugin-program är detsamma som att skriva andra Windows-konsolprogram. SCP.NET-plattformen deklarerar vissa gränssnitt för kanalen/bult. Din plugin-kod implementerar dessa gränssnitt. Huvud syftet med den här designen är att du ska kunna fokusera på affärs logiken samtidigt som SCP.NET-plattformen kan hantera andra saker.

Din plugin-kod implementerar något av följande gränssnitt. Vilket gränssnitt som är beroende av om topologin är transaktionell eller inte, och om komponenten är en kanalen eller en bult.

* **ISCPSpout**
* **ISCPBolt**
* **ISCPTxSpout**
* **ISCPBatchBolt**

### <a name="iscpplugin"></a>ISCPPlugin

**ISCPPlugin** är det vanligaste gränssnittet för många plugin-program. För närvarande är det ett dummy-gränssnitt.

```csharp
public interface ISCPPlugin
{
}
```

### <a name="iscpspout"></a>ISCPSpout

**ISCPSpout** är gränssnittet för en kanalen som inte är Transaction.

```csharp
public interface ISCPSpout : ISCPPlugin
{
    void NextTuple(Dictionary<string, Object> parms);
    void Ack(long seqId, Dictionary<string, Object> parms); 
    void Fail(long seqId, Dictionary<string, Object> parms);
}
```

När **NextTuple** anropas kan C#-koden avge en eller flera tupler. Om det inte finns något att skicka, bör den här metoden returnera utan att skicka något.

Metoderna **NextTuple**, **ack** och **failover** anropas i en tätt slinga i en enda tråd i C#-processen. Om det inte finns några tupleer att generera, har du **NextTuple** vilo läge för en kort tids period, till exempel 10 millisekunder. Detta vilo läge hjälper till att undvika CPU-tillgänglighet.

Metoderna **ack** och **failover** anropas bara när en Specifikations fil aktiverar bekräftelse metoden. Parametern *seqId* identifierar den tupel som har bekräftats eller misslyckats. Om bekräftelse är aktiverat i en inaktive rad topologi, ska följande **genereringsområden** användas i en kanalen:

```csharp
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Om en transaktionell topologi inte stöder bekräftelse, kan **ack** och **misslyckanden** lämnas som tomma funktioner.

Indataparametern *parametrar* i dessa funktioner anger en tom ord lista och är reserverad för framtida användning.

### <a name="iscpbolt"></a>ISCPBolt

**ISCPBolt** är gränssnittet för en transaktions bult.

```csharp
public interface ISCPBolt : ISCPPlugin
{
void Execute(SCPTuple tuple);
}
```

När en ny tupel är tillgänglig anropas funktionen **execute** för att bearbeta den.

### <a name="iscptxspout"></a>ISCPTxSpout

**ISCPTxSpout** är gränssnittet för en transaktions kanalen.

```csharp
public interface ISCPTxSpout : ISCPPlugin
{
    void NextTx(out long seqId, Dictionary<string, Object> parms);  
    void Ack(long seqId, Dictionary<string, Object> parms);         
    void Fail(long seqId, Dictionary<string, Object> parms);        
}
```

Precis som deras icke-transaktionella motsvarigheter, **NextTx**, **ack** och **misslyckanden** , anropas i en tätt slinga i en enda tråd i C#-processen. Om det inte finns några tupleer att generera, har du **NextTx** vilo läge för en kort tids period, till exempel 10 millisekunder. Detta vilo läge hjälper till att undvika CPU-tillgänglighet.

När **NextTx** anropas för att starta en ny transaktion identifierar *seqId* output-parametern transaktionen. Transaktionen används också i **ack** och **misslyckad**. **NextTx** -metoden kan generera data till Java-sidan. Data lagras i ZooKeeper för att ge stöd för uppspelning. Eftersom ZooKeeper har begränsad kapacitet ska din kod endast generera metadata och inte Mass data i en transaktions kanalen.

Eftersom Storm automatiskt spelar upp en misslyckad transaktion, anropas vanligt **vis inte.** Men om SCP: n kan kontrol lera de metadata som genereras av en transaktions kanalen, kan det hända att anropet **inte fungerar** om metadata är ogiltiga.

Indataparametern *parametrar* i dessa funktioner anger en tom ord lista och är reserverad för framtida användning.

### <a name="iscpbatchbolt"></a>ISCPBatchBolt

**ISCPBatchBolt** är gränssnittet för en transaktions bult.

```csharp
public interface ISCPBatchBolt : ISCPPlugin
{
    void Execute(SCPTuple tuple);
    void FinishBatch(Dictionary<string, Object> parms);  
}
```

Metoden **execute** anropas när en ny tupel tas emot i bulten. Metoden **FinishBatch** anropas när denna transaktion slutar. Indataparametern *parametrar* är reserverad för framtida användning.

**StormTxAttempt** är en viktig klass för en transaktionell topologi. Den har två medlemmar: **TxId** och **AttemptId**. **TxId** -medlemmen identifierar en speciell transaktion. En transaktion kan provas flera gånger om den Miss lyckas och spelas upp.

SCP.NET skapar ett nytt **ISCPBatchBolt** -objekt för att bearbeta varje **StormTxAttempt** -objekt, precis som vad Storm gör i Java. Syftet med den här designen är att stödja parallell transaktions bearbetning. När ett transaktions försök har slutförts förstörs motsvarande **ISCPBatchBolt** -objekt och skräp insamlas.

## <a name="object-model"></a>Objekt modell

SCP.NET innehåller också en enkel uppsättning viktiga objekt som utvecklare kan använda för att program mera. Objekten är **context**, **StateStore** och **SCPRuntime**. De beskrivs i det här avsnittet.

### <a name="context"></a>Kontext

**Kontext** objekt är en miljö som körs i ett program. Varje **ISCPPlugin** -instans av **ISCPSpout**, **ISCPBolt**, **ISCPTxSpout** eller **ISCPBatchBolt** har en motsvarande **kontext** instans. De funktioner som tillhandahålls av **kontexten** är indelade i följande två delar:

* Den statiska delen, som är tillgänglig i hela C#-processen
* Den dynamiska delen som bara är tillgänglig för den speciella **kontext** instansen

### <a name="static-part"></a>Statisk del

```csharp
public static ILogger Logger = null;
public static SCPPluginType pluginType;
public static Config Config { get; set; }
public static TopologyContext TopologyContext { get; set; }  
```

**Loggnings objekt tillhandahålls** för loggnings syfte.

**PluginType** -objektet anger plugin-typen för C#-processen. Om processen körs i det lokala testläget utan Java är plugin-typen **SCP_NET_LOCAL**.

```csharp
public enum SCPPluginType 
{
    SCP_NET_LOCAL = 0,
    SCP_NET_SPOUT = 1,
    SCP_NET_BOLT = 2,
    SCP_NET_TX_SPOUT = 3,
    SCP_NET_BATCH_BOLT = 4  
    }
```

Egenskapen **config** hämtar konfigurations parametrar från Java-sidan, som skickar dem när ett C#-plugin-program initieras. **Konfigurations** parametrarna är indelade i två delar: **stormConf** och **pluginConf**.

```csharp
public Dictionary<string, Object> stormConf { get; set; }  
public Dictionary<string, Object> pluginConf { get; set; }  
```

**StormConf** -delen är parametrar som definieras av storm och **pluginConf** -delen är parametrar som definierats av SCP. Här är ett exempel:

```csharp
public class Constants
{
    … …

    // constant string for pluginConf
    public static readonly String NONTRANSACTIONAL_ENABLE_ACK = "nontransactional.ack.enabled";  

    // constant string for stormConf
    public static readonly String STORM_ZOOKEEPER_SERVERS = "storm.zookeeper.servers";
    public static readonly String STORM_ZOOKEEPER_PORT = "storm.zookeeper.port";
}
```

**TopologyContext** -typen hämtar Topology-kontexten. Det är mest användbart för flera parallella komponenter. Här är ett exempel:

```csharp
//demo how to get TopologyContext info
if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
{
    Context.Logger.Info("TopologyContext info:");
    TopologyContext topologyContext = Context.TopologyContext;
    Context.Logger.Info("taskId: {0}", topologyContext.GetThisTaskId());
    taskIndex = topologyContext.GetThisTaskIndex();
    Context.Logger.Info("taskIndex: {0}", taskIndex);
    string componentId = topologyContext.GetThisComponentId();
    Context.Logger.Info("componentId: {0}", componentId);
    List<int> componentTasks = topologyContext.GetComponentTasks(componentId);  
    Context.Logger.Info("taskNum: {0}", componentTasks.Count);
}
```

### <a name="dynamic-part"></a>Dynamisk del

Följande gränssnitt är relevanta för en viss **kontext** instans, som skapas av SCP.NET-plattformen och skickas till din kod:

```csharp
// Declare the Output and Input Stream Schemas

public void DeclareComponentSchema(ComponentStreamSchema schema);

// Emit tuple to default stream.
public abstract void Emit(List<object> values);

// Emit tuple to the specific stream.
public abstract void Emit(string streamId, List<object> values);  
```

För en transaktionell kanalen som har stöd för bekräftelse, anges följande metod:

```csharp
// for nontransactional spout that supports ack
public abstract void Emit(string streamId, List<object> values, long seqId);  
```

En transaktions bult som stöder bekräftelse bör uttryckligen anropa **ack** eller **sluta att fungera** med den tupel som tagits emot. Vid sändning av en ny tupel måste bulten också ange tuplens ankare. Följande metoder har angetts:

```csharp
public abstract void Emit(string streamId, IEnumerable<SCPTuple> anchors, List<object> values);
public abstract void Ack(SCPTuple tuple);
public abstract void Fail(SCPTuple tuple);
```

### <a name="statestore"></a>StateStore

**StateStore** -objektet tillhandahåller metadata-tjänster, enkel, enkel sekvensers generering och en väntande samordning. Du kan bygga ut samtidiga samtidiga concurrency-abstraktionar på **StateStore**. Dessa abstraktioner omfattar distribuerade lås, distribuerade köer, barriärer och transaktions tjänster.

SCP-program kan använda **State** -objektet för att serialisera information i [Apache ZooKeeper](https://zookeeper.apache.org/). Den här funktionen är särskilt värdefull för en transaktionell topologi. Om en transaktions kanalen slutar svara och startar om, kan **tillstånd** hämta nödvändig information från ZooKeeper och starta om pipelinen.

**StateStore** -objektet har följande huvud metoder:

```csharp
/// <summary>
/// Static method to retrieve a state store of the given path and connStr 
/// </summary>
/// <param name="storePath">StateStore path</param>
/// <param name="connStr">StateStore address</param>
/// <returns>Instance of StateStore</returns>
public static StateStore Get(string storePath, string connStr);

/// <summary>
/// Create a new state object in this state store instance
/// </summary>
/// <returns>State from StateStore</returns>
public State Create();

/// <summary>
/// Retrieve all states that were previously uncommitted, excluding all exited states
/// </summary>
/// <returns>Uncommitted states</returns>
public IEnumerable<State> GetUnCommitted();

/// <summary>
/// Get all the states in the StateStore
/// </summary>
/// <returns>All the states</returns>
public IEnumerable<State> States();

/// <summary>
/// Get state or registry object
/// </summary>
/// <param name="info">Registry name (registry only)</param>
/// <typeparam name="T">Type, registry or state</typeparam>
/// <returns>Return registry or state</returns>
public T Get<T>(string info = null);

/// <summary>
/// List all the committed states
/// </summary>
/// <returns>Registries containing the committed state </returns>
public IEnumerable<Registry> Committed();

/// <summary>
/// List all the exited states in the StateStore
/// </summary>
/// <returns>Registries containing the exited states</returns>
public IEnumerable<Registry> Aborted();

/// <summary>
/// Retrieve an existing state object from this state store instance 
/// </summary>
/// <returns>State from StateStore</returns>
/// <typeparam name="T">stateId, id of the State</typeparam>
public State GetState(long stateId)
```

Objektet **State** har följande huvud metoder:

```csharp
/// <summary>
/// Set the status of the state object to commit
/// </summary>
public void Commit(bool simpleMode = true);

/// <summary>
/// Set the status of the state object to exit
/// </summary>
public void Abort();

/// <summary>
/// Put an attribute value under the given key
/// </summary>
/// <param name="key">Key</param>
/// <param name="attribute">State attribute</param>
    public void PutAttribute<T>(string key, T attribute);

/// <summary>
/// Get the attribute value associated with the given key
/// </summary>
/// <param name="key">Key</param>
/// <returns>State attribute</returns>
    public T GetAttribute<T>(string key);
```

När **simpleMode** har angetts till **True**, tar metoden **commit** bort motsvarande ZNode i ZooKeeper. Annars tar metoden bort den aktuella ZNode och lägger till en ny nod i den ALLOKERAde \_ sökvägen.

### <a name="scpruntime"></a>SCPRuntime

**SCPRuntime** -klassen innehåller följande två metoder:

```csharp
public static void Initialize();

public static void LaunchPlugin(newSCPPlugin createDelegate);  
```

Metoden **Initialize** initierar körnings miljön för SCP. I den här metoden ansluter C#-processen till Java-sidan för att hämta konfigurations parametrar och topologi-kontext.

**LaunchPlugin** -metoden startar meddelande bearbetnings slingan. I den här slingan tar C#-plugin-programmet emot meddelanden från Java-sidan. Dessa meddelanden innehåller tupler och kontroll signaler. Plugin-programmet bearbetar sedan meddelandena, t. ex. genom att anropa den gränssnitts metod som anges i din kod.

Indataparametern för **LaunchPlugin** är ett ombud. Metoden kan returnera ett objekt som implementerar **ISCPSpout**-, **ISCPBolt**-, **ISCPTxSpout**-eller **ISCPBatchBolt** -gränssnittet.

```csharp
public delegate ISCPPlugin newSCPPlugin(Context ctx, Dictionary<string, Object> parms);
```

För **ISCPBatchBolt** kan du hämta ett **StormTxAttempt** -objekt från parametern *parametrar* och använda det för att bedöma om försöket är ett omspelat försök. Det görs ofta en kontroll av att ett omuppspelnings försök görs i commit-blixten. HelloWorldTx-exemplet längre fram i den här artikeln visar den här kontrollen.

SCP-plugin-program kan vanligt vis köras i två lägen: lokalt test läge och normalt läge.

#### <a name="local-test-mode"></a>Lokalt testläge

I det här läget körs SCP-plugin-programmen i C#-koden i Visual Studio under utvecklings fasen. Du kan använda **ILocalContext** -gränssnittet i det här läget. Gränssnittet innehåller metoder för att serialisera de utgivna tupelarna till lokala filer och läsa tillbaka dem till RAM-minnet.

```csharp
public interface ILocalContext
{
    List<SCPTuple> RecvFromMsgQueue();
    void WriteMsgQueueToFile(string filepath, bool append = false);  
    void ReadFromFileToMsgQueue(string filepath);
}
```

#### <a name="regular-mode"></a>Normalt läge

I det här läget Kör Storm Java-processen SCP-plugin-programmen. Här är ett exempel:

```csharp
namespace Scp.App.HelloWorld
{
public class Generator : ISCPSpout
{
    … …
    public static Generator Get(Context ctx, Dictionary<string, Object> parms)
    {
    return new Generator(ctx);
    }
}

class HelloWorld
{
    static void Main(string[] args)
    {
    /* Setting the environment variable here can change the log file name */
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "HelloWorld");

    SCPRuntime.Initialize();
    SCPRuntime.LaunchPlugin(new newSCPPlugin(Generator.Get));
    }
}
}
```

## <a name="topology-specification-language"></a>Språk för topologi specifikation

Specifikationen av SCP-topologin är ett domänbaserat språk (DSL) för att beskriva och konfigurera SCP-topologier. Den baseras på [Storms CLOJURE DSL](https://storm.incubator.apache.org/documentation/Clojure-DSL.html) och utökas av SCP.

Du kan skicka Topology-specifikationer direkt till ett Storm-kluster för körning via **runSpec** -kommandot.

SCP.NET har lagt till följande funktioner för att definiera transaktionella topologier:

| Ny funktion | Parametrar | Description |
| --- | --- | --- |
| **TX-topolopy** |*topologi-namn*<br />*kanalen – mappa*<br />*bult-karta* |Definierar en transaktionell topologi med topologins namn, kanaler definitions karta och bult. |
| **SCP-TX-kanalen** |*exec-namn*<br />*args*<br />*fält* |Definierar en transaktionell kanalen. Funktionen kör programmet som anges av *exec-Name* och använder *argument*.<br /><br />*Fält* parametern anger utmatnings fälten för kanalen. |
| **SCP-TX-batch-bult** |*exec-namn*<br />*args*<br />*fält* |Definierar en transaktionell batch-bult. Funktionen kör programmet som anges av *exec-Name* och använder *argument.*<br /><br />Parametern *Fields* anger utmatnings fälten för bulten. |
| **SCP-TX-commit-bult** |*exec-namn*<br />*args*<br />*fält* |Definierar en transaktionell commit-bult. Funktionen kör programmet som anges av *exec-Name* och använder *argument*.<br /><br />Parametern *Fields* anger utmatnings fälten för bulten. |
| **nontx-topologi** |*topologi-namn*<br />*kanalen – mappa*<br />*bult-karta* |Definierar en transaktionell topologi med topologins namn, kanaler definitions karta och bult. |
| **SCP – kanalen** |*exec-namn*<br />*args*<br />*fält*<br />*komponentparametrar* |Definierar en kanalen som inte är en transaktion. Funktionen kör programmet som anges av *exec-Name* och använder *argument*.<br /><br />*Fält* parametern anger utmatnings fälten för kanalen.<br /><br />Parametern  Parameters är valfri. Använd den för att ange parametrar som "inte transaktions-ack. enabled". |
| **SCP-bult** |*exec-namn*<br />*args*<br />*fält*<br />*komponentparametrar* |Definierar en inte transaktions bult. Funktionen kör programmet som anges av *exec-Name* och använder *argument*.<br /><br />*Fält* parametern anger utmatnings fälten för bulten<br /><br />Parametern  Parameters är valfri. Använd den för att ange parametrar som "inte transaktions-ack. enabled". |

SCP.NET definierar följande nyckelord:

| Följt | Description |
| --- | --- |
| **: namn** |Topology-namn |
| **: topologi** |Topologin med hjälp av funktionerna i föregående tabell och inbyggda funktioner |
| **:p** |Parallel-tips för varje kanalen eller bult |
| **: config** |Om du vill konfigurera parametrar eller uppdatera befintliga |
| **: schema** |Data strömmens schema |

SCP.NET definierar även följande parametrar som används ofta:

| Parameter | Beskrivning |
| --- | --- |
| "plugin.name" |. Exe-filnamn för C#-plugin-programmet |
| "plugin. args" |Plugin-argumenten |
| "output. schema" |Schemat för utdata |
| "transaktionell. ack. enabled" |Om bekräftelse har Aktiver ATS för en transaktionell topologi |

Kommandot **runSpec** distribueras tillsammans med bitarna. Här är kommando användningen:

```csharp
.\bin\runSpec.cmd
usage: runSpec [spec-file target-dir [resource-dir] [-cp classpath]]
ex: runSpec examples\HelloWorld\HelloWorld.spec specs examples\HelloWorld\Target
```

Parametern *Resource-dir* är valfri. Ange den när du vill ansluta ett C#-program. Den angivna katalogen innehåller programmet, beroendena och konfigurationerna.

*Classpath* -parametern är också valfri. Den anger Java-classpath om Specifikations filen innehåller en Java-kanalen eller en bult.

## <a name="miscellaneous-features"></a>Diverse funktioner

### <a name="input-and-output-schema-declarations"></a>Schema deklarationer för indata och utdata

C#-processerna kan generera tupler. För att göra det, kan plattformen serialisera tupler till **byte []** -objekt och överföra objekten till Java-sidan. Storm överför sedan dessa tupler till målen.

I efterföljande komponenter bearbetar C# de tar emot tupler tillbaka från Java-sidan och konverterar dem till plattformens ursprungliga typer. Alla dessa åtgärder döljs av plattformen.

För att stödja serialisering och deserialisering måste din kod deklarera schemat för indata och utdata. Schemat definieras som en ord lista. Stream-ID: t är ord listans nyckel. Nyckelvärdet är kolumnernas typer. En komponent kan deklarera flera strömmar.

```csharp
public class ComponentStreamSchema
{
    public Dictionary<string, List<Type>> InputStreamSchema { get; set; }
    public Dictionary<string, List<Type>> OutputStreamSchema { get; set; }
    public ComponentStreamSchema(Dictionary<string, List<Type>> input, Dictionary<string, List<Type>> output)
    {
        InputStreamSchema = input;
        OutputStreamSchema = output;
    }
}
```

Följande funktion läggs till i ett **kontext** objekt:

```csharp
public void DeclareComponentSchema(ComponentStreamSchema schema)
```

Utvecklare måste se till att de utgivna tuppeln följer schemat som definierats för en data ström. Annars kommer systemet att utlösa ett körnings undantag.

### <a name="multistream-support"></a>Stöd för multistream

SCP låter din kod skicka till eller ta emot från flera distinkta strömmar samtidigt. **Context** -objektet visar det här stödet som en valfri Stream-ID-parameter för **generering** metoden.

Två metoder i SCP.NET- **kontextens** objekt har lagts till. De genererar en eller flera tupler till vissa strömmar. Parametern *streamId* är en sträng. Dess värde måste vara detsamma i både C#-koden och i specifikationen för topologi-definition.

```csharp
/* Emit tuple to the specific stream. */
public abstract void Emit(string streamId, List<object> values);

/* for nontransactional spout only */
public abstract void Emit(string streamId, List<object> values, long seqId);
```

Vid sändning till en icke-existerande data ström orsakar körnings undantag.

### <a name="fields-grouping"></a>Fält grupper

De inbyggda fälten gruppering i storm fungerar inte korrekt i SCP.NET. På sidan Java-proxy är data typen för alla fält faktiskt **byte []**. Fält grupper använder hash-koden **byte []** för objekt för att gruppera. Hash-koden är adressen till det här objektet i RAM-minnet. Grupperingen kommer därför att vara fel för multibyte-objekt som delar samma innehåll men inte samma adress.

SCP.NET lägger till en anpassad grupperingstyp och använder innehållet i objektet **byte []** för gruppering. I en Specifikations fil ser syntaxen ut som i det här exemplet:

```csharp
(bolt-spec
    {
        "spout_test" (scp-field-group :non-tx [0,1])
    }
    …
)
```

I föregående Specifikations fil:

* `scp-field-group` anger att gruppering är en anpassad fält grupp som implementeras av SCP.
* `:tx` eller `:non-tx` anger om topologin är transaktionell. Du behöver den här informationen eftersom start indexet skiljer sig åt mellan transaktionella och inte transaktionella topologier.
* `[0,1]` anger en hash-uppsättning för fält-ID: n som börjar med noll.

### <a name="hybrid-topology"></a>Hybrid topologi

Inbyggd Storm-kod är skriven i Java. SCP.NET har förbättrat Storm så att du kan skriva C#-kod för att hantera affärs logiken. Men SCP.NET stöder även hybrid topologier som inte bara innehåller C#-kanaler/-bultar, utan även Java-kanaler/bultar.

### <a name="specify-java-spoutbolt-in-a-specification-file"></a>Ange Java-kanalen/bult i en Specifikations fil

Du kan använda **SCP-kanalen** och **SCP-bult** i en Specifikations fil för att ange Java-kanaler och bultar. Här är ett exempel:

```csharp
(spout-spec 
  (microsoft.scp.example.HybridTopology.Generator.)
  :p 1)
```

Här `microsoft.scp.example.HybridTopology.Generator` är namnet på Java kanalen-klassen.

### <a name="specify-the-java-classpath-in-a-runspec-command"></a>Ange Java-classpath i ett runSpec-kommando

Om du vill skicka topologi som innehåller Java-kanaler eller bultar ska du först kompilera dem för att skapa JAR-filer. Ange sedan den Java-classpath som innehåller JAR-filerna när du skickar topologi. Här är ett exempel:

```csharp
bin\runSpec.cmd examples\HybridTopology\HybridTopology.spec specs examples\HybridTopology\net\Target -cp examples\HybridTopology\java\target\*
```

Här `examples\HybridTopology\java\target\` är mappen som innehåller jar-filen för Java-kanalen/bult.

### <a name="serialization-and-deserialization-between-java-and-c"></a>Serialisering och deserialisering mellan Java och C #

En SCP-komponent inkluderar Java-sidan och C#-sidan. För att interagera med ursprungliga Java-kanaler/-bultar måste serialisering och deserialisering ske mellan Java-sidan och C#-sidan, vilket visas i följande diagram:

![Diagram över Java-komponenten som skickar till SCP-komponenten, som sedan skickar till en annan Java-komponent](./media/apache-storm-scp-programming-guide/java-compent-sending-to-scp-component-sending-to-java-component.png)

#### <a name="serialization-in-the-java-side-and-deserialization-in-the-c-side"></a>Serialisering i Java-sidan och deserialiseringen i C#-Sidan

Börja med att ange standard implementeringen för serialisering i Java-sidan och deserialiseringen på C#-sidan.

Ange serialiserings metoden för Java-sidan i en Specifikations fil.

```csharp
(scp-bolt
    {
        "plugin.name" "HybridTopology.exe"
        "plugin.args" ["displayer"]
        "output.schema" {}
        "customized.java.serializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer"]
    })
```

Ange C#-sidans avserialiserings metod i C#-koden.

```csharp
Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
inputSchema.Add("default", new List<Type>() { typeof(Person) });
this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, null));
this.ctx.DeclareCustomizedDeserializer(new CustomizedInteropJSONDeserializer());
```  

Om data typen inte är för komplex, bör den här standard implementeringen hantera de flesta fall. Här är de fall där du kan ansluta din egen implementering:

* Data typen är för komplex för standard implementeringen.
* Prestandan för din standard implementering uppfyller inte dina krav.

Serialiserings gränssnittet på Java-sidan definieras som:

```csharp
public interface ICustomizedInteropJavaSerializer {
    public void prepare(String[] args);
    public List<ByteBuffer> serialize(List<Object> objectList);
}
```

Avserialiserings gränssnittet på C#-sidan definieras som:

```csharp
public interface ICustomizedInteropCSharpDeserializer
{
    List<Object> Deserialize(List<byte[]> dataList, List<Type> targetTypes);
}
```

#### <a name="serialization-in-the-c-side-and-deserialization-in-the-java-side"></a>Serialisering i C#-sidan och deserialiseringen på Java-Sidan

Ange metoden för att serialisera C#-sidan i C#-koden.

```csharp
this.ctx.DeclareCustomizedSerializer(new CustomizedInteropJSONSerializer()); 
```

Ange Java-sidans avserialiserings metod i en Specifikations fil.

```csharp
(scp-spout
   {
     "plugin.name" "HybridTopology.exe"
     "plugin.args" ["generator"]
     "output.schema" {"default" ["person"]}
     "customized.java.deserializer" ["microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer" "microsoft.scp.example.HybridTopology.Person"]
   }
)
```

Här `"microsoft.scp.storm.multilang.CustomizedInteropJSONDeserializer"` är namnet på deserialiseraren och `"microsoft.scp.example.HybridTopology.Person"` är mål klassen som data avserialiseras till.

Du kan också ansluta din egen implementering av en C#-serialisering och en Java-deserialiserare.

Den här koden är gränssnittet för C#-serialiseraren:

```csharp
public interface ICustomizedInteropCSharpSerializer
{
    List<byte[]> Serialize(List<object> dataList);
}
```

Den här koden är gränssnittet för Java-deserialiseraren:

```csharp
public interface ICustomizedInteropJavaDeserializer {
    public void prepare(String[] targetClassNames);
    public List<Object> Deserialize(List<ByteBuffer> dataList);
}
```

## <a name="scp-host-mode"></a>Värd läge för SCP

I det här läget kan du kompilera din kod som en DLL och använda SCPHost.exe som anges av SCP för att skicka en topologi. En Specifikations fil ser ut som den här koden:

```csharp
(scp-spout
  {
    "plugin.name" "SCPHost.exe"
    "plugin.args" ["HelloWorld.dll" "Scp.App.HelloWorld.Generator" "Get"]
    "output.schema" {"default" ["sentence"]}
  })
```

Här `"plugin.name"` anges som `"SCPHost.exe"` , som tillhandahålls av SCP SDK. SCPHost.exe accepterar tre parametrar i följande ordning:

1. DLL-namnet, som finns `"HelloWorld.dll"` i det här exemplet.
1. Klass namnet, som är `"Scp.App.HelloWorld.Generator"` i det här exemplet.
1. Namnet på en offentlig statisk metod som kan anropas för att hämta en instans av **ISCPPlugin**.

I värd läge kompilerar du koden som en DLL för anrop av SCP-plattformen. Eftersom plattformen sedan kan få fullständig kontroll över hela bearbetnings logiken rekommenderar vi att du skickar topologi i värd läge för SCP. Detta fören klar utvecklings upplevelsen. Det ger dig också större flexibilitet och bättre bakåtkompatibilitet för senare versioner.

## <a name="scp-programming-examples"></a>Programmerings exempel för SCP

### <a name="helloworld"></a>Projektet

I följande enkla HelloWorld-exempel visas en smak på SCP.NET. Den använder en intransaktionskö-topologi med en kanalen som kallas **Generator** och två bultar som kallas **delare** och **räknare**. **Generator** -kanalen skapar slumpmässigt meningar och genererar dessa meningar till **delare**. **Delnings** blixten delar upp meningarna i ord och genererar dessa ord till **räknar** bulten. **Counters** bult använder en ord lista för att registrera förekomst av varje ord.

Det här exemplet har två Specification-filer: HelloWorld. spec och HelloWorld \_ EnableAck. spec. C#-koden kan ta reda på om bekräftelsen har Aktiver ATS genom `pluginConf` att hämta objektet från Java-sidan.

```csharp
/* demo how to get pluginConf info */
if (Context.Config.pluginConf.ContainsKey(Constants.NONTRANSACTIONAL_ENABLE_ACK))
{
    enableAck = (bool)(Context.Config.pluginConf[Constants.NONTRANSACTIONAL_ENABLE_ACK]);
}
Context.Logger.Info("enableAck: {0}", enableAck);
```

Om bekräftelse är aktiverat i kanalen cachelagrar en ord lista de tupler som inte har bekräftats. Om `Fail` anropas spelas den felaktiga tuppeln upp.

```csharp
public void Fail(long seqId, Dictionary<string, Object> parms)
{
    Context.Logger.Info("Fail, seqId: {0}", seqId);
    if (cachedTuples.ContainsKey(seqId))
    {
        /* get the cached tuple */
        string sentence = cachedTuples[seqId];

        /* replay the failed tuple */
        Context.Logger.Info("Re-Emit: {0}, seqId: {1}", sentence, seqId);
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), seqId);
    }
    else
    {
        Context.Logger.Warn("Fail(), can't find cached tuple for seqId {0}!", seqId);
    }
}
```

### <a name="helloworldtx"></a>HelloWorldTx

Följande HelloWorldTx-exempel visar hur du implementerar en transaktionell topologi. Exemplet har en kanalen som kallas **Generator**, en batch-bult som kallas för **partiellt antal** och en commit bult som heter **Count-sum**. Exemplet innehåller också tre befintliga textfiler: DataSource0.txt, DataSource1.txt och DataSource2.txt.

I varje transaktion väljer **Generator** -kanalen slumpmässigt två filer från de befintliga tre filerna och genererar de två fil namnen till en **avdelare** bult. Den **partiella Count-** bult:

1. Hämtar ett fil namn från den mottagna tuppeln.
1. Öppnar motsvarande fil.
1. Räknar antalet ord i filen.
1. Genererar ordet Count till **Count-sum-** bult.

**Count-sum-** blixten sammanfattar det totala antalet.

Om du vill uppnå exakt en semantik måste du bedöma om det är en omspelad transaktion genom att köra **Count-sum-** incheckning. I det här exemplet har den följande statiska medlems variabel:

```csharp
public static long lastCommittedTxId = -1; 
```

När en **ISCPBatchBolt** -instans skapas hämtas värdet för `txAttempt` objektet från indataparametrarna.

```csharp
public static CountSum Get(Context ctx, Dictionary<string, Object> parms)
{
    /* for transactional topology, we can get txAttempt from the input parms */
    if (parms.ContainsKey(Constants.STORM_TX_ATTEMPT))
    {
        StormTxAttempt txAttempt = (StormTxAttempt)parms[Constants.STORM_TX_ATTEMPT];
        return new CountSum(ctx, txAttempt);
    }
    else
    {
        throw new Exception("null txAttempt");
    }
}
```

När `FinishBatch` anropas, `lastCommittedTxId` uppdateras om det inte är en omspelad transaktion.

```csharp
public void FinishBatch(Dictionary<string, Object> parms)
{
    /* judge whether it is a replayed transaction */
    bool replay = (this.txAttempt.TxId <= lastCommittedTxId);

    if (!replay)
    {
        /* If it is not replayed, update the totalCount and lastCommittedTxId value */
        totalCount = totalCount + this.count;
        lastCommittedTxId = this.txAttempt.TxId;
    }
    … …
}
```

### <a name="hybridtopology"></a>HybridTopology

Den här topologin innehåller en Java-kanalen och en C#-bult. Den använder den standardinställda serialisering och deserialiserings-implementering som tillhandahålls av SCP-plattformen. Se filen HybridTopology. spec i \\ mappen exempel HybridTopology för information om Specifikations filen. Se även SubmitTopology.bat för hur du anger Java-classpath.

### <a name="scphostdemo"></a>SCPHostDemo

Det här exemplet är i grunden samma som HelloWorld. Den enda skillnaden är att din kod kompileras som en DLL och att topologin skickas med hjälp av SCPHost.exe. I avsnittet SCP-värd läge finns en mer detaljerad förklaring.

## <a name="next-steps"></a>Nästa steg

Exempel på Apache Storm topologier som skapats med SCP finns i följande artiklar:

* [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md)
* [Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md)
* [Bearbeta fordons sensor data från Event Hubs med Apache Storm i HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/IotExample)
* [Extrahera, transformera och läsa in (ETL) från Azure Event Hubs till Apache HBase](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample)
