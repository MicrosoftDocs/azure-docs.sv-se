---
title: Programmerings guide för U-SQL-användardefinierad programmerings handbok för Azure Data Lake
description: Lär dig mer om den UDO programmerings guiden för U-SQL-.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 56b104b5cc8f8923445455c71fe2418e39539b8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96512635"
---
# <a name="use-user-defined-outputter"></a>Använda användardefinierad utdatafunktion

## <a name="u-sql-udo-user-defined-outputter"></a>U-SQL-UDO: användardefinierad utsparatillfil
Användardefinierad utskickare är en annan U-SQL-UDO som gör att du kan utöka de inbyggda U-SQL-funktionerna. Precis som i Extractor finns det flera inbyggda uppslags grupper.

* *Sparatillfils. text ()*: skriver data till avgränsade textfiler med olika kodningar.
* *Outputters.Csv ()*: skriver data till filer med kommaavgränsade värden (CSV) med olika kodningar.
* *Sparatillfils. tsv ()*: skriver data till TSV-filer (tabbavgränsad värde) med olika kodningar.

Med anpassad utmatnings fil kan du skriva data i ett anpassat definierat format. Detta kan vara användbart för följande uppgifter:

* Skriva data till halv strukturerade eller ostrukturerade filer.
* Skrivning av data stöder inte kodningar som stöds.
* Ändra utdata eller lägga till anpassade attribut.

## <a name="how-to-define-and-use-user-defined-outputter"></a>Definiera och använda användardefinierad utsparatillfil
Vi måste skapa gränssnittet för att definiera en användardefinierad utsparatillfile `IOutputter` .

Följande är Bask `IOutputter` lass implementeringen:

```csharp
public abstract class IOutputter : IUserDefinedOperator
{
    protected IOutputter();

    public virtual void Close();
    public abstract void Output(IRow input, IUnstructuredWriter output);
}
```

Alla indataparametrar till utdataporten, till exempel kolumn-och rad avgränsare, kodning och så vidare, måste definieras i klassens konstruktor. `IOutputter`Gränssnittet bör också innehålla en definition för `void Output` åsidosättning. Attributet `[SqlUserDefinedOutputter(AtomicFileProcessing = true)` kan alternativt anges för atomisk fil bearbetning. Mer information finns i följande information.

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class MyOutputter : IOutputter
{

    public MyOutputter(myparam1, myparam2)
    {
      …
    }

    public override void Close()
    {
      …
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
      …
    }
}
```

* `Output` anropas för varje indataparameter. Den returnerar `IUnstructuredWriter output` rad uppsättningen.
* Klassen konstruktor används för att skicka parametrar till den användardefinierade uppslags listan.
* `Close` används för att åsidosätta kostsamma tillstånd eller fastställa när den sista raden skrevs.

Attributet **SqlUserDefinedOutputter** anger att typen ska registreras som en användardefinierad Utskicks text. Det går inte att ärva den här klassen.

SqlUserDefinedOutputter är ett valfritt attribut för en användardefinierad utuppsättnings definition. Den används för att definiera egenskapen AtomicFileProcessing.

* bool AtomicFileProcessing   

* **True** = anger att den här utmatnings filen kräver Atom-utdatafiler (JSON, XML,...)
* **false** = anger att den här utlösaren kan hantera delade/distribuerade filer (CSV, seq,...)

Huvud programmerings objekt är **rad** och **utdata**. **Row** -objektet används för att räkna upp utdata som `IRow` gränssnitt. **Utdata** används för att ange utdata för mål filen.

Utdata nås via `IRow` gränssnittet. Utdata skickas till en rad i taget.

De enskilda värdena räknas upp genom anrop av Get-metoden för IRow-gränssnittet:

```csharp
row.Get<string>("column_name")
```

Enskilda kolumn namn kan fastställas genom att anropa `row.Schema` :

```csharp
ISchema schema = row.Schema;
var col = schema[i];
string val = row.Get<string>(col.Name)
```

Med den här metoden kan du bygga en flexibel utvinnare för alla metadata-scheman.

Utdata skrivs till filen med hjälp av `System.IO.StreamWriter` . Data Ströms parametern anges `output.BaseStream` som en del av `IUnstructuredWriter output` .

Observera att det är viktigt att tömma databufferten till filen efter varje rad iteration. Dessutom `StreamWriter` måste objektet användas med attributet disponibelt aktiverat (standard) och med nyckelordet **using** :

```csharp
using (StreamWriter streamWriter = new StreamWriter(output.BaseStream, this._encoding))
{
…
}
```

Annars anropar du flush ()-metoden direkt efter varje iteration. Vi visar detta i följande exempel.

### <a name="set-headers-and-footers-for-user-defined-outputter"></a>Ange sidhuvud och sidfot för användardefinierad uttrycks List
Om du vill ange ett sidhuvud använder du ett enda iteration körnings flöde.

```csharp
public override void Output(IRow row, IUnstructuredWriter output)
{
 …
if (isHeaderRow)
{
    …                
}

 …
if (isHeaderRow)
{
    isHeaderRow = false;
}
 …
}
}
```

Koden i det första `if (isHeaderRow)` blocket körs bara en gång.

Använd referensen till instansen av `System.IO.Stream` objektet () för sidfoten `output.BaseStream` . Skriv sidfoten i Close ()-metoden för `IOutputter` gränssnittet.  (Mer information finns i följande exempel.)

Följande är ett exempel på en användardefinierad utsparatillfil:

```csharp
[SqlUserDefinedOutputter(AtomicFileProcessing = true)]
public class HTMLOutputter : IOutputter
{
    // Local variables initialization
    private string row_delimiter;
    private char col_delimiter;
    private bool isHeaderRow;
    private Encoding encoding;
    private bool IsTableHeader = true;
    private Stream g_writer;

    // Parameters definition            
    public HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    this.isHeaderRow = isHeader;
    this.encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
    }

    // The Close method is used to write the footer to the file. It's executed only once, after all rows
    public override void Close()
    {
    //Reference to IO.Stream object - g_writer
    StreamWriter streamWriter = new StreamWriter(g_writer, this.encoding);
    streamWriter.Write("</table>");
    streamWriter.Flush();
    streamWriter.Close();
    }

    public override void Output(IRow row, IUnstructuredWriter output)
    {
    System.IO.StreamWriter streamWriter = new StreamWriter(output.BaseStream, this.encoding);

    // Metadata schema initialization to enumerate column names
    ISchema schema = row.Schema;

    // This is a data-independent header--HTML table definition
    if (IsTableHeader)
    {
        streamWriter.Write("<table border=1>");
        IsTableHeader = false;
    }

    // HTML table attributes
    string header_wrapper_on = "<th>";
    string header_wrapper_off = "</th>";
    string data_wrapper_on = "<td>";
    string data_wrapper_off = "</td>";

    // Header row output--runs only once
    if (isHeaderRow)
    {
        streamWriter.Write("<tr>");
        for (int i = 0; i < schema.Count(); i++)
        {
        var col = schema[i];
        streamWriter.Write(header_wrapper_on + col.Name + header_wrapper_off);
        }
        streamWriter.Write("</tr>");
    }

    // Data row output
    streamWriter.Write("<tr>");                
    for (int i = 0; i < schema.Count(); i++)
    {
        var col = schema[i];
        string val = "";
        try
        {
        // Data type enumeration--required to match the distinct list of types from OUTPUT statement
        switch (col.Type.Name.ToString().ToLower())
        {
            case "string": val = row.Get<string>(col.Name).ToString(); break;
            case "guid": val = row.Get<Guid>(col.Name).ToString(); break;
            default: break;
        }
        }
        // Handling NULL values--keeping them empty
        catch (System.NullReferenceException)
        {
        }
        streamWriter.Write(data_wrapper_on + val + data_wrapper_off);
    }
    streamWriter.Write("</tr>");

    if (isHeaderRow)
    {
        isHeaderRow = false;
    }
    // Reference to the instance of the IO.Stream object for footer generation
    g_writer = output.BaseStream;
    streamWriter.Flush();
    }
}

// Define the factory classes
public static class Factory
{
    public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
    {
    return new HTMLOutputter(isHeader, encoding);
    }
}
```

Och U-SQL Base-skript:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.html";

@rs0 =
    EXTRACT
            guid Guid,
        dt String,
            user String,
            des String
         FROM @input_file
         USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 
    TO @output_file 
    USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

Detta är en HTML-fil som skapar en HTML-fil med tabell data.

### <a name="call-outputter-from-u-sql-base-script"></a>Anropa upscriptor från U-SQL Base-skriptet
Om du vill anropa en anpassad utbildare från det grundläggande U-SQL-skriptet, måste den nya instansen av ett utstils objekt skapas.

```sql
OUTPUT @rs0 TO @output_file USING new USQL_Programmability.HTMLOutputter(isHeader: true);
```

För att undvika att skapa en instans av objektet i bas skriptet, kan vi skapa ett funktions tecken, som du ser i vårt tidigare exempel:

```csharp
        // Define the factory classes
        public static class Factory
        {
            public static HTMLOutputter HTMLOutputter(bool isHeader = false, Encoding encoding = null)
            {
                return new HTMLOutputter(isHeader, encoding);
            }
        }
```

I det här fallet ser det ursprungliga anropet ut så här:

```usql
OUTPUT @rs0 
TO @output_file 
USING USQL_Programmability.Factory.HTMLOutputter(isHeader: true);
```

## <a name="next-steps"></a>Nästa steg
* [Guide för U-SQL-programmering – översikt](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programmerings guide – UDT och UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)