---
title: U-SQL User Defined Extractor programmerings guide för Azure Data Lake
description: Läs om U-SQL-UDO programmerings guide – användardefinierad Extractor.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: ad7f6336753903533771033de21aec8262425a61
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97608025"
---
# <a name="use-user-defined-extractor"></a>Använda användardefinierad extraktor

## <a name="u-sql-udo-user-defined-extractor"></a>U-SQL-UDO: användardefinierad Extractor
Med U-SQL kan du importera externa data med hjälp av en EXTRACT-instruktion. En EXTRACT-instruktion kan använda inbyggda UDO-Extraherare:  

* *Reextractrs. text ()*: tillhandahåller extrahering från avgränsade textfiler med olika kodningar.

* *Extractors.Csv ()*: tillhandahåller extrahering från CSV-filer (kommaavgränsade värden) med olika kodningar.

* *Reextractrs. tsv ()*: tillhandahåller extrahering av TSV-filer (Tabbavgränsade värden) med olika kodningar.

Det kan vara användbart att utveckla en anpassad Extractor. Detta kan vara till hjälp vid data import om vi vill utföra någon av följande uppgifter:

* Ändra indata genom att dela upp kolumner och ändra enskilda värden. PROCESSOR funktionerna är bättre för att kombinera kolumner.
* Parsa ostrukturerade data, till exempel webb sidor och e-postmeddelanden eller delvis ostrukturerade data, till exempel XML/JSON.
* Parsa data i kodning som inte stöds.

## <a name="how-to-define-and-use-user-defined-extractor"></a>Definiera och använda användardefinierad Extractor
Vi måste skapa ett gränssnitt för att definiera en användardefinierad Extractor-eller OMMANDOT-Beskrivning `IExtractor` . Alla indataparametrar till Extractor, till exempel kolumn-och rad avgränsare och kodning, måste definieras i klassens konstruktor. `IExtractor`Gränssnittet bör också innehålla en definition för `IEnumerable<IRow>` åsidosättningen enligt följande:

```csharp
[SqlUserDefinedExtractor]
public class SampleExtractor : IExtractor
{
     public SampleExtractor(string row_delimiter, char col_delimiter)
     { … }

     public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
     { … }
}
```

Attributet **SqlUserDefinedExtractor** anger att typen ska registreras som en användardefinierad Extractor. Det går inte att ärva den här klassen.

SqlUserDefinedExtractor är ett valfritt attribut för OMMANDOT-definitionen. Den används för att definiera egenskapen AtomicFileProcessing för objektet OMMANDOT.

* bool AtomicFileProcessing   

* **True** = anger att denna Extractor kräver Atom-indatafiler (JSON, XML,...)
* **false** = anger att denna Extractor kan hantera delade/distribuerade filer (CSV, seq,...)

De huvudsakliga OMMANDOT-objekten är **indata** och **utdata**. Indata-objektet används för att räkna upp indata som `IUnstructuredReader` . Objektet utdata används för att ange utdata som ett resultat av aktiviteten Extractor.

Indata nås via `System.IO.Stream` och `System.IO.StreamReader` .

För uppräkning av indata-kolumner delar vi först upp indataströmmen med hjälp av en rad avgränsare.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
}
```

Sedan kan du ytterligare dela upp indatamängden i kolumn delar.

```csharp
foreach (Stream current in input.Split(my_row_delimiter))
{
…
    string[] parts = line.Split(my_column_delimiter);
    foreach (string part in parts)
    { … }
}
```

Vi använder-metoden för att ange utdata `output.Set` .

Det är viktigt att förstå att den anpassade extraheringen bara matar ut kolumner och värden som har definierats med utdata. Ange metod anrop.

```csharp
output.Set<string>(count, part);
```

Det faktiska extraherings resultatet utlöses genom att anropa `yield return output.AsReadOnly();` .

Följande är exempel på Extractor:

```csharp
[SqlUserDefinedExtractor(AtomicFileProcessing = true)]
public class FullDescriptionExtractor : IExtractor
{
     private Encoding _encoding;
     private byte[] _row_delim;
     private char _col_delim;

    public FullDescriptionExtractor(Encoding encoding, string row_delim = "\r\n", char col_delim = '\t')
    {
         this._encoding = ((encoding == null) ? Encoding.UTF8 : encoding);
         this._row_delim = this._encoding.GetBytes(row_delim);
         this._col_delim = col_delim;

    }

    public override IEnumerable<IRow> Extract(IUnstructuredReader input, IUpdatableRow output)
    {
         string line;
         //Read the input line by line
         foreach (Stream current in input.Split(_encoding.GetBytes("\r\n")))
         {
        using (System.IO.StreamReader streamReader = new StreamReader(current, this._encoding))
         {
             line = streamReader.ReadToEnd().Trim();
             //Split the input by the column delimiter
             string[] parts = line.Split(this._col_delim);
             int count = 0; // start with first column
             foreach (string part in parts)
             {
    if (count == 0)
             {  // for column “guid”, re-generated guid
                 Guid new_guid = Guid.NewGuid();
                 output.Set<Guid>(count, new_guid);
             }
             else if (count == 2)
             {
                 // for column “user”, convert to UPPER case
                 output.Set<string>(count, part.ToUpper());

             }
             else
             {
                 // keep the rest of the columns as-is
                 output.Set<string>(count, part);
             }
             count += 1;
             }

         }
         yield return output.AsReadOnly();
         }
         yield break;
     }
}
```

I det här användnings fallet genererar extraheringen GUID för kolumnen "GUID" och konverterar värdena för kolumnen "användare" till versaler. Anpassade extraktioner kan producera mer komplicerade resultat genom att parsa indata och ändra dem.

Följande är grundläggande U-SQL-skript som använder en anpassad Extractor:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid Guid,
        dt String,
        user String,
        des String
    FROM @input_file
        USING new USQL_Programmability.FullDescriptionExtractor(Encoding.UTF8);

OUTPUT @rs0 TO @output_file USING Outputters.Text();
```


## <a name="next-steps"></a>Nästa steg
* [Guide för U-SQL-programmering – översikt](data-lake-analytics-u-sql-programmability-guide.md)
* [U-SQL programmerings guide – UDT och UDAGG](data-lake-analytics-u-sql-programmability-guide-UDT-AGG.md)