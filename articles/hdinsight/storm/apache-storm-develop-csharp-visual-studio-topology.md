---
title: Apache Storm topologier med Visual Studio och C# – Azure HDInsight
description: Lär dig hur du skapar Storm-topologier i C#. Skapa en topologi för ord räkning i Visual Studio med hjälp av Hadoop-verktygen för Visual Studio.
ROBOTS: NOINDEX
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a81f2b21545a5362168482f3f0a65fbbbf381c10
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929165"
---
# <a name="develop-c-topologies-for-apache-storm-by-using-the-data-lake-tools-for-visual-studio"></a>Utveckla C#-topologier för Apache Storm med hjälp av Data Lake verktyg för Visual Studio

Lär dig hur du skapar en C#-Apache Storm topologi med hjälp av Azure Data Lake (Apache Hadoop) verktyg för Visual Studio. Det här dokumentet vägleder dig genom processen att skapa ett Storm-projekt i Visual Studio, testa det lokalt och distribuera det till en Apache Storm på Azure HDInsight-kluster.

Du lär dig också hur du skapar hybrid topologier som använder C#-och Java-komponenter.

C#-topologier använder .NET 4,5 och använder mono för att köra i HDInsight-klustret. Information om möjliga inkompatibiliteter finns i [mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/). Om du vill använda en C#-topologi måste du uppdatera `Microsoft.SCP.Net.SDK` NuGet-paketet som används av projektet till version 0.10.0.6 eller senare. Paketversionen måste även matcha huvudversionen av Storm som är installerad på HDInsight.

| HDInsight-version | Apache Storm version | SCP.NET-version | Standard version av mono |
|:-----------------:|:-------------:|:---------------:|:--------------------:|
| 3.4 | 0.10.0. x | 0.10.0. x | 3.2.8 |
| 3.5 | 1.0.2. x | 1.0.0. x | 4.2.1 |
| 3,6 | 1.1.0. x | 1.0.0. x | 4.2.8 |

## <a name="prerequisite"></a>Förutsättning

Ett Apache Storm kluster i HDInsight. Se [skapa Apache Hadoop kluster med Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **kluster typ**.

## <a name="install-visual-studio"></a>Installera Visual Studio

Du kan utveckla C#-topologier med SCP.NET med hjälp av [Visual Studio](https://visualstudio.microsoft.com/downloads/). Anvisningarna här använder Visual Studio 2019, men du kan också använda tidigare versioner av Visual Studio.

## <a name="install-data-lake-tools-for-visual-studio"></a>Installera Data Lake verktyg för Visual Studio

Om du vill installera Data Lake verktyg för Visual Studio följer du stegen i [Kom igång med data Lake verktyg för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md#install-data-lake-tools-for-visual-studio).

## <a name="install-java"></a>Installera Java

När du skickar en Storm-topologi från Visual Studio genererar SCP.NET en zip-fil som innehåller topologin och beroenden. Java används för att skapa dessa zip-filer, eftersom den använder ett format som är mer kompatibelt med Linux-baserade kluster.

1. Installera Java Developer Kit (JDK) 7 eller senare i utvecklings miljön. Du kan hämta Oracle-JDK från [Oracle](https://openjdk.java.net/). Du kan också använda [andra Java-distributioner](/java/azure/jdk/).

2. Ange `JAVA_HOME` miljövariabeln till den katalog som innehåller Java.

3. Ange `PATH` miljövariabeln som ska innehålla `%JAVA_HOME%\bin` katalogen.

Du kan skapa och köra följande C#-konsol program för att kontrol lera att Java och JDK är korrekt installerade:

```csharp
using System;
using System.IO;
namespace ConsoleApplication2
{
   class Program
   {
       static void Main(string[] args)
       {
           string javaHome = Environment.GetEnvironmentVariable("JAVA_HOME");
           if (!string.IsNullOrEmpty(javaHome))
           {
               string jarExe = Path.Combine(javaHome + @"\bin", "jar.exe");
               if (File.Exists(jarExe))
               {
                   Console.WriteLine("JAVA Is Installed properly");
                    return;
               }
               else
               {
                   Console.WriteLine("A valid JAVA JDK is not found. Looks like JRE is installed instead of JDK.");
               }
           }
           else
           {
             Console.WriteLine("A valid JAVA JDK is not found. JAVA_HOME environment variable is not set.");
           }
       }  
   }
}
```

## <a name="apache-storm-templates"></a>Apache Storm mallar

Data Lake Tools för Visual Studio tillhandahåller följande mallar:

| Projekttyp | Demonstrationer |
| --- | --- |
| Storm-program |Ett tomt Storm Topology-projekt. |
| Exempel på Storm Azure SQL Writer |Så här skriver du till Azure SQL Database. |
| Exempel på Storm Azure Cosmos DB Reader |Läsa från Azure Cosmos DB. |
| Exempel på Storm Azure Cosmos DB Writer |Så här skriver du till Azure Cosmos DB. |
| Exempel på Storm EventHub-läsare |Läsa från Azure Event Hubs. |
| Exempel på Storm EventHub-skrivare |Så här skriver du till Azure Event Hubs. |
| Exempel på Storm HBase-läsare |Läsa från HBase i HDInsight-kluster. |
| Exempel på Storm HBase Writer |Så här skriver du till HBase i HDInsight-kluster. |
| Storm hybrid-exempel |Så här använder du en Java-komponent. |
| Storm-exempel |En grundläggande topologi för ord räkning. |

> [!WARNING]  
> Alla mallar fungerar inte med Linux-baserade HDInsight. NuGet-paket som används av mallarna kanske inte är kompatibla med mono. För att identifiera potentiella problem, se [mono-kompatibilitet](https://www.mono-project.com/docs/about-mono/compatibility/) och Använd .net- [portbaserad analys](../hdinsight-hadoop-migrate-dotnet-to-linux.md#automated-portability-analysis).

I stegen i det här dokumentet använder du projekt typen Basic Storm Application för att skapa en topologi.

### <a name="apache-hbase-templates"></a>Apache HBase-mallar

HBase Reader-och Writer-mallar använder HBase-REST API, inte HBase Java API, för att kommunicera med en HBase på HDInsight-kluster.

### <a name="eventhub-templates"></a>EventHub-mallar

> [!IMPORTANT]  
> Den Java-baserade EventHub kanalen-komponenten som ingår i EventHub-läsar mal len fungerar kanske inte med storm på HDInsight version 3,5 eller senare. En uppdaterad version av den här komponenten finns på [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/HDI3.5/lib).

Ett exempel på en topologi som använder den här komponenten och fungerar med storm på HDInsight 3,5 finns i [GitHub](https://github.com/Azure-Samples/hdinsight-dotnet-java-storm-eventhub).

## <a name="create-a-c-topology"></a>Skapa en C#-topologi

Så här skapar du ett C#-Topology-projekt i Visual Studio:

1. Öppna Visual Studio.

1. I fönstret **Starta** väljer du **skapa ett nytt projekt**.

1. I fönstret **skapa ett nytt projekt** rullar du till och väljer **Storm-program** och väljer sedan **Nästa**.

1. I fönstret **Konfigurera ditt nya projekt** anger du ett **projekt namn** för *WORDCOUNT*, går till eller skapar en **plats** katalog Sök väg för projektet och väljer sedan **skapa**.

    ![Storm-program, konfigurera ditt nya projekt dialog ruta, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/apache-storm-new-project.png)

När du har skapat projektet bör du ha följande filer:

* *Program.cs*: definitions miljön för ditt projekt. En standardtopologi som består av en kanalen och en bult skapas som standard.

* *Spout.cs*: ett exempel på kanalen som avger slumpmässiga tal.

* *Bolt.cs*: en exempels bult som innehåller antalet tal som skickas av kanalen.

När du skapar projektet laddar NuGet ned det senaste [SCP.net-paketet](https://www.nuget.org/packages/Microsoft.SCP.Net.SDK/).

### <a name="implement-the-spout"></a>Implementera kanalen

Lägg sedan till koden för kanalen, som används för att läsa data i en topologi från en extern källa. Den här kanalen avger slumpmässigt en mening i topologin.

1. Öppna *Spout.cs*. Huvud komponenterna för en kanalen är:

   * `NextTuple`: Anropas av storm när kanalen får generera nya tupler.

   * `Ack` (endast transaktionell topologi): hanterar bekräftelser som har startats av andra komponenter i topologin för tupler som skickats från kanalen. Genom att bekräfta en tupel vet kanalen att det har bearbetats av underordnade komponenter.

   * `Fail` (endast transaktionell topologi): hanterar tupler som inte kan bearbeta andra komponenter i topologin. `Fail`Genom att implementera en metod kan du skapa en ny tupel så att den kan bearbetas igen.

2. Ersätt innehållet i `Spout` klassen med följande text:

    ```csharp
    private Context ctx;
    private Random r = new Random();
    string[] sentences = new string[] {
        "the cow jumped over the moon",
        "an apple a day keeps the doctor away",
        "four score and seven years ago",
        "snow white and the seven dwarfs",
        "i am at two with nature"
    };

    public Spout(Context ctx)
    {
        // Set the instance context
        this.ctx = ctx;

        Context.Logger.Info("Generator constructor called");

        // Declare Output schema
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // The schema for the default output stream is
        // a tuple that contains a string field
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(null, outputSchema));
    }

    // Get an instance of the spout
    public static Spout Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Spout(ctx);
    }

    public void NextTuple(Dictionary<string, Object> parms)
    {
        Context.Logger.Info("NextTuple enter");
        // The sentence to be emitted
        string sentence;

        // Get a random sentence
        sentence = sentences[r.Next(0, sentences.Length - 1)];
        Context.Logger.Info("Emit: {0}", sentence);
        // Emit it
        this.ctx.Emit(new Values(sentence));

        Context.Logger.Info("NextTuple exit");
    }

    public void Ack(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }

    public void Fail(long seqId, Dictionary<string, Object> parms)
    {
        // Only used for transactional topologies
    }
    ```

### <a name="implement-the-bolts"></a>Implementera bultarna

Nu ska du skapa två Storm-bultar i det här exemplet:

1. Ta bort den befintliga *Bolt.cs* -filen från projektet.

2. I **Solution Explorer** högerklickar du på projektet och väljer **Lägg till**  >  **nytt objekt**. I listan väljer du **Storm bult** och anger *splitter.cs* som namn. I den nya filens kod ändrar du namn områdets namn till `WordCount` . Upprepa sedan processen för att skapa en andra bult med namnet *Counter.cs*.

   * *Splitter.cs*: implementerar en bult som delar upp meningar i enskilda ord och avger en ny data ström.

   * *Counter.cs*: implementerar en bult som räknar varje ord och utvärderar en ny ström med ord och antalet för varje ord.

     > [!NOTE]  
     > Dessa bultar läser och skriver till strömmar, men du kan också använda en bult för att kommunicera med källor som en databas eller tjänst.

3. Öppna *splitter.cs*. Den har bara en metod som standard: `Execute` . `Execute`Metoden anropas när bulten tar emot en tupel för bearbetning. Här kan du läsa och bearbeta inkommande tupler och generera utgående tupler.

4. Ersätt innehållet i `Splitter` klassen med följande kod:

    ```csharp
    private Context ctx;

    // Constructor
    public Splitter(Context ctx)
    {
        Context.Logger.Info("Splitter constructor called");
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // Input contains a tuple with a string field (the sentence)
        inputSchema.Add("default", new List<Type>() { typeof(string) });
        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // Outbound contains a tuple with a string field (the word)
        outputSchema.Add("default", new List<Type>() { typeof(string) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance of the bolt
    public static Splitter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Splitter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the sentence from the tuple
        string sentence = tuple.GetString(0);
        // Split at space characters
        foreach (string word in sentence.Split(' '))
        {
            Context.Logger.Info("Emit: {0}", word);
            //Emit each word
            this.ctx.Emit(new Values(word));
        }

        Context.Logger.Info("Execute exit");
    }
    ```

5. Öppna *Counter.cs* och ersätt klass innehållet med följande kod:

    ```csharp
    private Context ctx;

    // Dictionary for holding words and counts
    private Dictionary<string, int> counts = new Dictionary<string, int>();

    // Constructor
    public Counter(Context ctx)
    {
        Context.Logger.Info("Counter constructor called");
        // Set instance context
        this.ctx = ctx;

        // Declare Input and Output schemas
        Dictionary<string, List<Type>> inputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string field - the word
        inputSchema.Add("default", new List<Type>() { typeof(string) });

        Dictionary<string, List<Type>> outputSchema = new Dictionary<string, List<Type>>();
        // A tuple containing a string and integer field - the word and the word count
        outputSchema.Add("default", new List<Type>() { typeof(string), typeof(int) });
        this.ctx.DeclareComponentSchema(new ComponentStreamSchema(inputSchema, outputSchema));
    }

    // Get a new instance
    public static Counter Get(Context ctx, Dictionary<string, Object> parms)
    {
        return new Counter(ctx);
    }

    // Called when a new tuple is available
    public void Execute(SCPTuple tuple)
    {
        Context.Logger.Info("Execute enter");

        // Get the word from the tuple
        string word = tuple.GetString(0);
        // Do we already have an entry for the word in the dictionary?
        // If no, create one with a count of 0
        int count = counts.ContainsKey(word) ? counts[word] : 0;
        // Increment the count
        count++;
        // Update the count in the dictionary
        counts[word] = count;

        Context.Logger.Info("Emit: {0}, count: {1}", word, count);
        // Emit the word and count information
        this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new List<SCPTuple> { tuple }, new Values(word, count));
        Context.Logger.Info("Execute exit");
    }
    ```

### <a name="define-the-topology"></a>Definiera topologin

Kanaler och bultar är ordnade i ett diagram, vilket definierar hur data flödar mellan komponenter. I den här topologin är grafen följande:

![Kanalen-och bult-komponent, Storm-topologi](./media/apache-storm-develop-csharp-visual-studio-topology/word-count-topology1.png)

Kanalen avger meningar som distribueras till förekomster av delnings blixten. Delnings bulten delar upp meningarna i ord som distribueras till räknar bulten.

Eftersom räknar instansen innehåller ord antalet lokalt, vill du se till att vissa ord flödar till samma Counter bult-instans. Varje instans håller reda på vissa ord. Eftersom delnings blixten inte har något tillstånd spelar det ingen roll vilken instans av delnings listen som tar emot vilken mening.

Öppna *Program.cs*. Den viktiga metoden är `GetTopologyBuilder` , som används för att definiera topologin som skickas till storm. Ersätt innehållet i `GetTopologyBuilder` med följande kod för att implementera topologin som beskrivs ovan:

```csharp
// Create a new topology named 'WordCount'
TopologyBuilder topologyBuilder = new TopologyBuilder(
    "WordCount" + DateTime.Now.ToString("yyyyMMddHHmmss"));

// Add the spout to the topology.
// Name the component 'sentences'
// Name the field that is emitted as 'sentence'
topologyBuilder.SetSpout(
    "sentences",
    Spout.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"sentence"}}
    },
    1);
// Add the splitter bolt to the topology.
// Name the component 'splitter'
// Name the field that is emitted 'word'
// Use suffleGrouping to distribute incoming tuples
//   from the 'sentences' spout across instances
//   of the splitter
topologyBuilder.SetBolt(
    "splitter",
    Splitter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word"}}
    },
    1).shuffleGrouping("sentences");

// Add the counter bolt to the topology.
// Name the component 'counter'
// Name the fields that are emitted 'word' and 'count'
// Use fieldsGrouping to ensure that tuples are routed
//   to counter instances based on the contents of field
//   position 0 (the word). This could also have been
//   List<string>(){"word"}.
//   This ensures that the word 'jumped', for example, will always
//   go to the same instance
topologyBuilder.SetBolt(
    "counter",
    Counter.Get,
    new Dictionary<string, List<string>>()
    {
        {Constants.DEFAULT_STREAM_ID, new List<string>(){"word", "count"}}
    },
    1).fieldsGrouping("splitter", new List<int>() { 0 });

// Add topology config
topologyBuilder.SetTopologyConfig(new Dictionary<string, string>()
{
    {"topology.kryo.register","[\"[B\"]"}
});

return topologyBuilder;
```

## <a name="submit-the-topology"></a>Skicka topologin

Du är nu redo att skicka topologin till ditt HDInsight-kluster.

1. Gå till **Visa**  >  **Server Explorer**.

1. Högerklicka på **Azure**, Välj **Anslut till Microsoft Azure prenumeration...** och slutför inloggnings processen.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Skicka till storm på HDInsight**.

1. I dialog rutan **sändning Topology** , under List rutan **Storm-kluster** väljer du storm på HDInsight-kluster och väljer sedan **Skicka**. Du kan kontrol lera om överföringen lyckas genom att visa fönstret **utdata** .

    När topologin har skickats visas fönstret Storm- **topologier** för klustret. Välj **WORDCOUNT** -topologin i listan om du vill visa information om topologi som körs.

    ![Visnings fönster för Storm-topologi, HDInsight-kluster, Visual Studio](./media/apache-storm-develop-csharp-visual-studio-topology/storm-topology-view.png)

    > [!NOTE]  
    > Du kan också visa **Storm-topologier** från **Server Explorer**. Expandera **Azure**  >  **HDInsight**, högerklicka på en storm i HDInsight-kluster och välj sedan **Visa Storm-topologier**.

    Om du vill visa information om komponenterna i topologin väljer du en komponent i diagrammet.

1. I avsnittet **topologi Sammanfattning** väljer du **Kill** för att stoppa topologin.

    > [!NOTE]  
    > Storm-topologier fortsätter att köras tills de har inaktiverats eller också tas klustret bort.

## <a name="transactional-topology"></a>Transaktionell topologi

Föregående topologi är inte transaktionell. Komponenterna i topologin implementerar inte funktioner för att spela upp meddelanden. Ett exempel på en transaktionell topologi är att skapa ett projekt och välja Storm- **exempel** som projekt typ.

Transaktionella topologier implementerar följande för att ge stöd för uppspelning av data:

* **Cachelagring av metadata**: kanalen måste lagra metadata om de data som skickas, så att data kan hämtas och genereras igen om ett fel uppstår. Eftersom de data som genereras av exemplet är små lagras rå data för varje tupel i en ord lista för uppspelning.

* **Ack**: varje bult i topologin kan anropa `this.ctx.Ack(tuple)` för att bekräfta att den har bearbetat en tupel. När alla bultar har godkänt tuppeln `Ack` anropas metoden för kanalen. `Ack`Metoden tillåter att kanalen tar bort data som cachelagrats för uppspelning.

* **Misslyckades**: varje bult kan anropa `this.ctx.Fail(tuple)` för att indikera att bearbetningen misslyckades för en tupel. Felen sprids till `Fail` metoden i kanalen, där tuppeln kan spelas upp med hjälp av cachelagrade metadata.

* **Sekvens-ID**: när du avger en tupel kan du ange ett unikt sekvens-ID. Värdet identifierar tuppeln för uppspelnings `Ack` bearbetning (och `Fail` ). Till exempel använder kanalen i storm- **exempelprojektet** följande metod anrop vid sändning av data:

  `this.ctx.Emit(Constants.DEFAULT_STREAM_ID, new Values(sentence), lastSeqId);`

  Den här koden ger en tupel som innehåller en mening till standard strömmen, med det sekvens-ID-värde som finns i `lastSeqId` . I det här exemplet `lastSeqId` ökas varje tupel.

Som det visas i **Storm-exempelprojektet** , om en komponent är transaktionell, kan ställas in vid körning baserat på konfiguration.

## <a name="hybrid-topology-with-c-and-java"></a>Hybrid topologi med C# och Java

Du kan också använda Data Lake verktyg för Visual Studio för att skapa hybrid topologier, där vissa komponenter är C# och andra är Java.

Ett exempel på en hybrid topologi är att skapa ett projekt och välja **Storm hybrid-exempel**. Den här exempel typen visar följande begrepp:

* **Java-kanalen** och **C#-bult**: definieras i `HybridTopology_javaSpout_csharpBolt` klassen.

  En transaktionell version definieras i- `HybridTopologyTx_javaSpout_csharpBolt` klassen.

* **C# kanalen** och **Java-bult**: definieras i `HybridTopology_csharpSpout_javaBolt` klassen.

  En transaktionell version definieras i- `HybridTopologyTx_csharpSpout_javaBolt` klassen.

  > [!NOTE]  
  > Den här versionen visar också hur du använder clojure-kod från en textfil som en Java-komponent.

Om du vill byta topologi som används när projektet skickas flyttar du `[Active(true)]` instruktionen till den topologi du vill använda innan du skickar den till klustret.

> [!NOTE]  
> Alla Java-filer som krävs ingår som en del av det här projektet i mappen *JavaDependency* .

Tänk på följande när du skapar och skickar en hybrid topologi:

* Används `JavaComponentConstructor` för att skapa en instans av Java-klassen för en kanalen eller en bult.

* Används `microsoft.scp.storm.multilang.CustomizedInteropJSONSerializer` för att serialisera data till eller från Java-komponenter från Java-objekt till JSON.

* När du skickar topologin till servern måste du använda alternativet **ytterligare konfigurationer** för att ange **sökvägar till Java-filen**. Den angivna sökvägen ska vara den katalog som har JAR-filerna som innehåller dina Java-klasser.

### <a name="azure-event-hubs"></a>Azure Event Hubs

SCP.NET version 0.9.4.203 introducerar en ny klass och metod för att arbeta med Event Hub-kanalen (ett Java-kanalen som läser från Event Hubs). När du skapar en topologi som använder en Event Hub-kanalen (till exempel med hjälp av exempel mal len **Storm EventHub-läsare** ) använder du följande API: er:

* `EventHubSpoutConfig` klass: skapar ett objekt som innehåller konfigurationen för kanalen-komponenten.

* `TopologyBuilder.SetEventHubSpout` metod: lägger till Event Hub kanalen-komponenten i topologin.

> [!NOTE]  
> Du måste fortfarande använda `CustomizedInteropJSONSerializer` för att serialisera data som produceras av kanalen.

## <a name="use-configurationmanager"></a>Använd ConfigurationManager

Använd inte **ConfigurationManager** för att hämta konfigurations värden från bult-och kanalen-komponenter. Detta kan orsaka ett undantag för null-pekare. Skicka i stället konfigurationen för ditt projekt till storm-topologin som nyckel-och värdepar i topologins kontext. Varje komponent som förlitar sig på konfigurations värden måste hämta dem från kontexten under initieringen.

Följande kod visar hur du hämtar dessa värden:

```csharp
public class MyComponent : ISCPBolt
{
    // To hold configuration information loaded from context
    Configuration configuration;
    ...
    public MyComponent(Context ctx, Dictionary<string, Object> parms)
    {
        // Save a copy of the context for this component instance
        this.ctx = ctx;
        // If it exists, load the configuration for the component
        if(parms.ContainsKey(Constants.USER_CONFIG))
        {
            this.configuration = parms[Constants.USER_CONFIG] 
                as System.Configuration.Configuration;
        }
        // Retrieve the value of "Foo" from configuration
        var foo = this.configuration.AppSettings.Settings["Foo"].Value;
    }
    ...
}
```

Om du använder en- `Get` metod för att returnera en instans av-komponenten måste du se till att den passerar `Context` både `Dictionary<string, Object>` parametrarna och för-konstruktorn. Följande exempel är en grundläggande `Get` metod som skickar dessa värden korrekt:

```csharp
public static MyComponent Get(Context ctx, Dictionary<string, Object> parms)
{
    return new MyComponent(ctx, parms);
}
```

## <a name="how-to-update-scpnet"></a>Så här uppdaterar du SCP.NET

De senaste versionerna av SCP.NET-uppgraderingen av support paketet via NuGet. När en ny uppdatering är tillgänglig får du ett uppgraderings meddelande. Följ dessa steg om du vill söka efter en uppgradering manuellt:

1. Högerklicka på projektet i **Solution Explorer** och välj **Hantera NuGet-paket**.

2. Välj **uppdateringar** från paket hanteraren. Om det finns en uppdatering för SCP.NET-stödpaketet visas det i listan. Välj **Uppdatera** för paketet och välj sedan **OK** i dialog rutan för hands versioner av **ändringar** .

> [!IMPORTANT]  
> Om projektet har skapats med en tidigare version av SCP.NET som inte använde NuGet, måste du utföra följande steg för att uppdatera till en nyare version:
>
> 1. Högerklicka på projektet i **Solution Explorer** och välj **Hantera NuGet-paket**.
> 2. Använd **Sök** fältet och Sök efter och Lägg sedan till `Microsoft.SCP.Net.SDK` i projektet.

## <a name="troubleshoot-common-issues-with-topologies"></a>Felsöka vanliga problem med topologier

### <a name="null-pointer-exceptions"></a>Undantag för null-pekare

När du använder en C#-topologi med ett Linux-baserat HDInsight-kluster kan bult-och kanalen-komponenter som använder **ConfigurationManager** för att läsa konfigurations inställningar vid körning returnera null-pekare.

Konfigurationen för ditt projekt överförs till storm-topologin som nyckel-och värdepar i topologins kontext. Den kan hämtas från det Dictionary-objekt som skickas till dina komponenter när de har initierats.

Mer information finns i avsnittet [använda ConfigurationManager](#use-configurationmanager) i det här dokumentet.

### <a name="systemtypeloadexception"></a>System. TypeLoadException

När du använder en C#-topologi med ett Linux-baserat HDInsight-kluster kan du komma över följande fel:

`System.TypeLoadException: Failure has occurred while loading a type.`

Det här felet uppstår när du använder en binärfil som inte är kompatibel med den version av .NET som stöds av mono.

För Linux-baserade HDInsight-kluster ser du till att ditt projekt använder binärfiler som kompilerats för .NET 4,5.

### <a name="test-a-topology-locally"></a>Testa en topologi lokalt

Även om det är enkelt att distribuera en topologi till ett kluster kan du i vissa fall behöva testa en topologi lokalt. Använd följande steg för att köra och testa exempel sto pol Ogin i den här artikeln lokalt i utvecklings miljön.

> [!WARNING]  
> Lokal testning fungerar bara för topologier med endast C#. Du kan inte använda lokal testning för Hybrid topologier eller topologier som använder flera strömmar.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Egenskaper**. I projekt egenskaperna. Ändra sedan **utdatatypen** till **konsol programmet**.

   ![HDInsight Storm-program, projekt egenskaper, Utdatatyp](./media/apache-storm-develop-csharp-visual-studio-topology/hdi-output-type-window.png)

   > [!NOTE]
   > Kom ihåg att ändra **utdatatypen tillbaka till** **klass biblioteket** innan du distribuerar topologin till ett kluster.

1. I **Solution Explorer** högerklickar du på projektet och väljer sedan **Lägg till**  >  **nytt objekt**. Välj **klass** och ange *LocalTest.cs* som klass namn. Välj slutligen **Lägg till**.

1. Öppna *LocalTest.cs* och Lägg till följande- `using` instruktion högst upp:

    ```csharp
    using Microsoft.SCP;
    ```

1. Använd följande kod som innehållet i `LocalTest` klassen:

    ```csharp
    // Drives the topology components
    public void RunTestCase()
    {
        // An empty dictionary for use when creating components
        Dictionary<string, Object> emptyDictionary = new Dictionary<string, object>();

        #region Test the spout
        {
            Console.WriteLine("Starting spout");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext spoutCtx = LocalContext.Get();
            // Get a new instance of the spout, using the local context
            Spout sentences = Spout.Get(spoutCtx, emptyDictionary);

            // Emit 10 tuples
            for (int i = 0; i < 10; i++)
            {
                sentences.NextTuple(emptyDictionary);
            }
            // Use LocalContext to persist the data stream to file
            spoutCtx.WriteMsgQueueToFile("sentences.txt");
            Console.WriteLine("Spout finished");
        }
        #endregion

        #region Test the splitter bolt
        {
            Console.WriteLine("Starting splitter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext splitterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Splitter splitter = Splitter.Get(splitterCtx, emptyDictionary);

            // Set the data stream to the data created by the spout
            splitterCtx.ReadFromFileToMsgQueue("sentences.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = splitterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                splitter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            splitterCtx.WriteMsgQueueToFile("splitter.txt");
            Console.WriteLine("Splitter bolt finished");
        }
        #endregion

        #region Test the counter bolt
        {
            Console.WriteLine("Starting counter bolt");
            // LocalContext is a local-mode context that can be used to initialize
            // components in the development environment.
            LocalContext counterCtx = LocalContext.Get();
            // Get a new instance of the bolt
            Counter counter = Counter.Get(counterCtx, emptyDictionary);

            // Set the data stream to the data created by splitter bolt
            counterCtx.ReadFromFileToMsgQueue("splitter.txt");
            // Get a batch of tuples from the stream
            List<SCPTuple> batch = counterCtx.RecvFromMsgQueue();
            // Process each tuple in the batch
            foreach (SCPTuple tuple in batch)
            {
                counter.Execute(tuple);
            }
            // Use LocalContext to persist the data stream to file
            counterCtx.WriteMsgQueueToFile("counter.txt");
            Console.WriteLine("Counter bolt finished");
        }
        #endregion
    }
    ```

    Ta en stund att läsa igenom kod kommentarerna. Den här koden används `LocalContext` för att köra komponenterna i utvecklings miljön. Den behåller data strömmen mellan komponenter till textfiler på den lokala enheten.

1. Öppna *program.cs* och Lägg till följande kod i- `Main` metoden:

    ```csharp
    Console.WriteLine("Starting tests");
    System.Environment.SetEnvironmentVariable("microsoft.scp.logPrefix", "WordCount-LocalTest");
    // Initialize the runtime
    SCPRuntime.Initialize();

    //If we are not running under the local context, throw an error
    if (Context.pluginType != SCPPluginType.SCP_NET_LOCAL)
    {
        throw new Exception(string.Format("unexpected pluginType: {0}", Context.pluginType));
    }
    // Create test instance
    LocalTest tests = new LocalTest();
    // Run tests
    tests.RunTestCase();
    Console.WriteLine("Tests finished");
    Console.ReadKey();
    ```

1. Spara ändringarna och välj sedan **F5** eller Välj **Felsök**  >  **Starta fel sökning** för att starta projektet. Ett konsol fönster bör visas och logg status visas som test förlopp. När `Tests finished` visas väljer du en tangent för att stänga fönstret.

1. Använd **Utforskaren** för att hitta den katalog som innehåller ditt projekt. (Till exempel: *C: \\ användare \\ \<your_user_name> \\ Source \\ databaser \\ WORDCOUNT \\ WORDCOUNT*.) I den här katalogen öppnar du sedan *bin* och väljer sedan *Felsök*. Du bör se de textfiler som skapades när testerna kördes: *sentences.txt*, *counter.txt* och *splitter.txt*. Öppna varje textfil och granska data.

   > [!NOTE]  
   > Sträng data sparas som en matris med decimal värden i de här filerna. `[[97,103,111]]`I **splitter.txt** -filen representerar till exempel ordet *sedan*.

> [!NOTE]  
> Se till att ange **projekt typen** tillbaka till **klass biblioteket** i projekt egenskaperna innan du distribuerar till en storm på HDInsight-kluster.

### <a name="log-information"></a>Logg information

Du kan enkelt logga information från dina Topology-komponenter med hjälp av `Context.Logger` . Följande kommando skapar till exempel en informations logg post:

`Context.Logger.Info("Component started");`

Loggad information kan visas från **Hadoop-Tjänsteloggen**, som finns i **Server Explorer**. Expandera posten för din storm i HDInsight-klustret och expandera sedan **Hadoop-Tjänsteloggen**. Välj slutligen logg filen som du vill visa.

> [!NOTE]  
> Loggarna lagras på det Azure Storage-konto som används av klustret. Om du vill visa loggarna i Visual Studio måste du logga in på den Azure-prenumeration som äger lagrings kontot.

### <a name="view-error-information"></a>Visa fel information

Använd följande steg för att visa fel som har inträffat i en topologi som körs:

1. Från **Server Explorer** högerklickar du på HDInsight-klustret och väljer **Visa Storm-topologier**.

   I **kanalen** och **bultarna** innehåller den **senaste fel** kolumnen information om det senaste felet.

2. Välj **kanalen-ID** eller **bult-ID** för komponenten som innehåller ett fel som visas. Sidan information visar ytterligare fel information i avsnittet **fel** längst ned på sidan.

3. Om du vill ha mer information väljer du en **port** från avsnittet **körningar** på sidan för att se Storm Worker-loggen under de senaste minuterna.

### <a name="errors-submitting-topologies"></a>Fel vid sändning av topologier

Om du kommer över fel som skickar en topologi till HDInsight kan du hitta loggar för Server sidans komponenter som hanterar topologin som skickas i HDInsight-klustret. Använd följande kommando från en kommando rad för att hämta loggarna:

```cmd
scp sshuser@clustername-ssh.azurehdinsight.net:/var/log/hdinsight-scpwebapi/hdinsight-scpwebapi.out .
```

Ersätt *sshuser* med SSH-användarkontot för klustret. Ersätt *kluster* namn med namnet på HDInsight-klustret. Mer information om hur `scp` du använder och `ssh` med HDInsight finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Det går inte att skicka bidrag av flera orsaker:

* JDK är inte installerat eller så finns den inte i sökvägen.
* Nödvändiga Java-beroenden ingår inte i överföringen.
* Beroenden är inkompatibla.
* Namn på topologier är dubbletter.

Om *HDInsight-scpwebapi. out* -loggfilen innehåller ett `FileNotFoundException` , kan undantaget orsakas av följande:

* JDK finns inte i sökvägen till utvecklings miljön. Kontrol lera att JDK har installerats i utvecklings miljön och att det `%JAVA_HOME%/bin` finns i sökvägen.
* Du saknar ett Java-beroende. Se till att du inkluderar alla obligatoriska. jar-filer som en del av överföringen.

## <a name="next-steps"></a>Nästa steg

Ett exempel på att bearbeta data från Event Hubs finns i [process händelser från Azure Event Hubs med storm på HDInsight](apache-storm-develop-csharp-event-hub-topology.md).

Ett exempel på en C#-topologi som delar upp data strömmar i flera strömmar finns i [C# Storm-exempel](https://github.com/Blackmist/csharp-storm-example).

Mer information om hur du skapar C#-topologier finns i [GitHub](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/SCPNet-GettingStarted.md).

Fler sätt att arbeta med HDInsight och mer storm på HDInsight-exempel finns i följande dokument:

**Microsoft-SCP.NET**

* [Programmerings guide för SCP för Apache Storm i Azure HDInsight](apache-storm-scp-programming-guide.md)

**Apache Storm på HDInsight**

* [Distribuera och hantera Apache Storm topologier på Azure HDInsight](apache-storm-deploy-monitor-topology-linux.md)
* [Exempel Apache Storm topologier i Azure HDInsight](apache-storm-example-topology.md)

**Apache Hadoop på HDInsight**

* [Vad är Apache Hive och HiveQL på Azure HDInsight?](../hadoop/hdinsight-use-hive.md)
* [Använda MapReduce i Apache Hadoop på HDInsight](../hadoop/hdinsight-use-mapreduce.md)

**Apache HBase på HDInsight**

* [Använda Apache HBase i Azure HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md)
