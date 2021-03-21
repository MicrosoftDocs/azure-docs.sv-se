---
title: Programmerings guide för U-SQL UDT och UDAGG för Azure Data Lake
description: Läs om U-SQL UDT-och UDAGG-programmeringen i Azure Data Lake Analytics så att du kan skapa ett lämpligt USQL-skript.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 06/30/2017
ms.openlocfilehash: 10fcce9a667d9a08318f5adab804f482387052ff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606665"
---
# <a name="u-sql-programmability-guide---udt-and-udagg"></a>U-SQL programmerings guide – UDT och UDAGG



## <a name="use-user-defined-types-udt"></a>Använd användardefinierade typer: UDT
Användardefinierade typer, eller UDT, är en annan programmerings funktion i U-SQL. U-SQL UDT fungerar som en vanlig C#-användardefinierad typ. C# är ett starkt skrivet språk som gör det möjligt att använda inbyggda och anpassade användardefinierade typer.

U-SQL kan inte implicit serialisera eller deserialisera godtyckliga UDTs när UDT skickas mellan formhörn i rad uppsättningar. Det innebär att användaren måste ange en explicit formatering med hjälp av IFormatter-gränssnittet. Detta ger U-SQL med metoderna serialisera och deserialisera för UDT.

> [!NOTE]
> U-SQL: s inbyggda Extraherare och utvinnare kan för närvarande inte serialisera eller deserialisera UDT-data till eller från filer även med IFormatter-uppsättningen. När du skriver UDT-data till en fil med OUTPUT-instruktionen eller läser den med en Extractor måste du skicka den som en sträng eller en byte mat ris. Sedan anropar du serialisering och avserialiserings kod (det vill säga UDT: s ToString ()-metod) explicit. Användardefinierade avgränsare och utvinnare, å andra sidan, kan läsa och skriva UDTs.

Om vi försöker använda UDT i EXTRACTOR eller ett uttrycks värde (utanför föregående val), som du ser här:

```usql
@rs1 =
    SELECT 
        MyNameSpace.Myfunction_Returning_UDT(filed1) AS myfield
    FROM @rs0;

OUTPUT @rs1 
    TO @output_file 
    USING Outputters.Text();
```

Vi får följande fel meddelande:

```output
Error   1   E_CSC_USER_INVALIDTYPEINOUTPUTTER: Outputters.Text was used to output column myfield of type
MyNameSpace.Myfunction_Returning_UDT.

Description:

Outputters.Text only supports built-in types.

Resolution:

Implement a custom outputter that knows how to serialize this type, or call a serialization method on the type in
the preceding SELECT.   C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\
USQL-Programmability\Types.usql 52  1   USQL-Programmability
```

För att kunna arbeta med UDT i en utlösare måste du antingen serialisera den till en sträng med metoden ToString () eller skapa en anpassad utlösare.

UDTs kan för närvarande inte användas i GROUP BY. Om UDT används i GROUP BY, genereras följande fel:

```output
Error   1   E_CSC_USER_INVALIDTYPEINCLAUSE: GROUP BY doesn't support type MyNameSpace.Myfunction_Returning_UDT
for column myfield

Description:

GROUP BY doesn't support UDT or Complex types.

Resolution:

Add a SELECT statement where you can project a scalar column that you want to use with GROUP BY.
C:\Users\sergeypu\Documents\Visual Studio 2013\Projects\USQL-Programmability\USQL-Programmability\Types.usql
62  5   USQL-Programmability
```

För att definiera en UDT måste vi:

1. Lägg till följande namn rymder:

```csharp
using Microsoft.Analytics.Interfaces
using System.IO;
```

2. Lägg till `Microsoft.Analytics.Interfaces` , vilket krävs för UDT-gränssnitten. `System.IO`Du kan också behöva definiera IFormatter-gränssnittet.

3. Definiera en Använd definierad typ med attributet SqlUserDefinedType.

**SqlUserDefinedType** används för att markera en typ definition i en sammansättning som en användardefinierad typ (UDT) i U-SQL. Egenskaperna för attributet reflekterar de fysiska egenskaperna för UDT. Det går inte att ärva den här klassen.

SqlUserDefinedType är ett obligatoriskt attribut för UDT-definitionen.

Klassens konstruktor:  

* SqlUserDefinedTypeAttribute (typ Formatter)

* Typ Formatter: obligatorisk parameter för att definiera en UDT-formatering – särskilt måste typ av `IFormatter` gränssnitt skickas här.

```csharp
[SqlUserDefinedType(typeof(MyTypeFormatter))]
public class MyType
{ … }
```

* Typisk UDT kräver också definition av IFormatter-gränssnittet, som visas i följande exempel:

```csharp
public class MyTypeFormatter : IFormatter<MyType>
{
    public void Serialize(MyType instance, IColumnWriter writer, ISerializationContext context)
    { … }

    public MyType Deserialize(IColumnReader reader, ISerializationContext context)
    { … }
}
```

`IFormatter`Gränssnittet serialiserar och deserialiserar ett objekt diagram med rot typen \<typeparamref name="T"> .

\<typeparam name="T">Objekt diagramets rottyp för att serialisera och deserialisera.

* **Deserialisering**: deserialiserar data på den angivna strömmen och utbildar grafen över objekt.

* **Serialisera**: serialiserar ett objekt eller diagram med objekt, med den angivna roten till den angivna data strömmen.

`MyType` instans: instans av typen.  
`IColumnWriter` skribent/ `IColumnReader` läsare: den underliggande kolumn data strömmen.  
`ISerializationContext` Context: Enum som definierar en uppsättning flaggor som anger käll-eller mål kontexten för data strömmen under serialisering.

* **Mellan**: anger att käll-eller mål kontexten inte är ett beständigt arkiv.

* **Persistence**: anger att käll-eller mål kontexten är en sparad lagrings plats.

Som en vanlig C#-typ kan en U-SQL UDT-definition innehålla åsidosättningar för operatorer som +/= =/! =. Den kan även innehålla statiska metoder. Om vi till exempel ska använda denna UDT som en parameter till en f-SQL minsta mängd funktion, måste vi definiera < operatörs åsidosättning.

Tidigare i den här hand boken demonstrerade vi ett exempel för räkenskaps period identifiering från det angivna datumet i formatet `Qn:Pn (Q1:P10)` . I följande exempel visas hur du definierar en anpassad typ för värden för räkenskaps perioden.

Följande är ett exempel på ett avsnitt med bakomliggande kod med anpassade UDT-och IFormatter-gränssnitt:

```csharp
[SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
public struct FiscalPeriod
{
    public int Quarter { get; private set; }

    public int Month { get; private set; }

    public FiscalPeriod(int quarter, int month):this()
    {
    this.Quarter = quarter;
    this.Month = month;
    }

    public override bool Equals(object obj)
    {
    if (ReferenceEquals(null, obj))
    {
        return false;
    }

    return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
    }

    public bool Equals(FiscalPeriod other)
    {
return this.Quarter.Equals(other.Quarter) && this.Month.Equals(other.Month);
    }

    public bool GreaterThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
    }

    public bool LessThan(FiscalPeriod other)
    {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
    }

    public override int GetHashCode()
    {
    unchecked
    {
        return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
    }
    }

    public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
    {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
    }

    public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.Equals(c2);
    }

    public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
    {
    return !c1.Equals(c2);
    }
    public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.GreaterThan(c2);
    }
    public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
    {
    return c1.LessThan(c2);
    }
    public override string ToString()
    {
    return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
    }

}

public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
{
    public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
    {
    using (var binaryWriter = new BinaryWriter(writer.BaseStream))
    {
        binaryWriter.Write(instance.Quarter);
        binaryWriter.Write(instance.Month);
        binaryWriter.Flush();
    }
    }

    public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
    {
    using (var binaryReader = new BinaryReader(reader.BaseStream))
    {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
        return result;
    }
    }
}
```

Den definierade typen innehåller två siffror: kvartal och månad. Operatorer `==/!=/>/<` och statiska metoder `ToString()` definieras här.

Som nämnts tidigare kan UDT användas i SELECT-uttryck, men det går inte att använda den i utrycke/EXTRACTOR utan anpassad serialisering. Den måste antingen serialiseras som en sträng med `ToString()` eller använda med en anpassad utskickare/Extractor.

Nu ska vi diskutera användningen av UDT. I ett avsnitt med bakomliggande kod ändrade vi vår GetFiscalPeriod-funktion till följande:

```csharp
public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
{
    int FiscalMonth = 0;
    if (dt.Month < 7)
    {
    FiscalMonth = dt.Month + 6;
    }
    else
    {
    FiscalMonth = dt.Month - 6;
    }

    int FiscalQuarter = 0;
    if (FiscalMonth >= 1 && FiscalMonth <= 3)
    {
    FiscalQuarter = 1;
    }
    if (FiscalMonth >= 4 && FiscalMonth <= 6)
    {
    FiscalQuarter = 2;
    }
    if (FiscalMonth >= 7 && FiscalMonth <= 9)
    {
    FiscalQuarter = 3;
    }
    if (FiscalMonth >= 10 && FiscalMonth <= 12)
    {
    FiscalQuarter = 4;
    }

    return new FiscalPeriod(FiscalQuarter, FiscalMonth);
}
```

Som du kan se returneras värdet för vår FiscalPeriod-typ.

Här ger vi ett exempel på hur du kan använda det ytterligare i grundläggande skript i U-SQL. Det här exemplet visar olika former av UDT-anrop från U-SQL-skript.

```usql
DECLARE @input_file string = @"c:\work\cosmos\usql-programmability\input_file.tsv";
DECLARE @output_file string = @"c:\work\cosmos\usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
        guid string,
        dt DateTime,
        user String,
        des String
    FROM @input_file USING Extractors.Tsv();

@rs1 =
    SELECT 
        guid AS start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Quarter AS fiscalquarter,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).Month AS fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt) + new USQL_Programmability.CustomFunctions.FiscalPeriod(1,7) AS fiscalperiod_adjusted,
        user,
        des
    FROM @rs0;

@rs2 =
    SELECT 
        start_id,
        dt,
        DateTime.Now.ToString("M/d/yyyy") AS Nowdate,
        fiscalquarter,
        fiscalmonth,
        USQL_Programmability.CustomFunctions.GetFiscalPeriodWithCustomType(dt).ToString() AS fiscalperiod,

       // This user-defined type was created in the prior SELECT.  Passing the UDT to this subsequent SELECT would have failed if the UDT was not annotated with an IFormatter.
           fiscalperiod_adjusted.ToString() AS fiscalperiod_adjusted,
           user,
           des
    FROM @rs1;

OUTPUT @rs2 
    TO @output_file 
    USING Outputters.Text();
```

Här är ett exempel på ett fullständigt bakomliggande kod avsnitt:

```csharp
using Microsoft.Analytics.Interfaces;
using Microsoft.Analytics.Types.Sql;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.IO;

namespace USQL_Programmability
{
    public class CustomFunctions
    {
        static public DateTime? ToDateTime(string dt)
        {
            DateTime dtValue;

            if (!DateTime.TryParse(dt, out dtValue))
                return Convert.ToDateTime(dt);
            else
                return null;
        }

        public static FiscalPeriod GetFiscalPeriodWithCustomType(DateTime dt)
        {
            int FiscalMonth = 0;
            if (dt.Month < 7)
            {
                FiscalMonth = dt.Month + 6;
            }
            else
            {
                FiscalMonth = dt.Month - 6;
            }

            int FiscalQuarter = 0;
            if (FiscalMonth >= 1 && FiscalMonth <= 3)
            {
                FiscalQuarter = 1;
            }
            if (FiscalMonth >= 4 && FiscalMonth <= 6)
            {
                FiscalQuarter = 2;
            }
            if (FiscalMonth >= 7 && FiscalMonth <= 9)
            {
                FiscalQuarter = 3;
            }
            if (FiscalMonth >= 10 && FiscalMonth <= 12)
            {
                FiscalQuarter = 4;
            }

            return new FiscalPeriod(FiscalQuarter, FiscalMonth);
        }        [SqlUserDefinedType(typeof(FiscalPeriodFormatter))]
        public struct FiscalPeriod
        {
            public int Quarter { get; private set; }

            public int Month { get; private set; }

            public FiscalPeriod(int quarter, int month):this()
            {
                this.Quarter = quarter;
                this.Month = month;
            }

            public override bool Equals(object obj)
            {
                if (ReferenceEquals(null, obj))
                {
                    return false;
                }

                return obj is FiscalPeriod && Equals((FiscalPeriod)obj);
            }

            public bool Equals(FiscalPeriod other)
            {
return this.Quarter.Equals(other.Quarter) &&    this.Month.Equals(other.Month);
            }

            public bool GreaterThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) > 0 || this.Month.CompareTo(other.Month) > 0;
            }

            public bool LessThan(FiscalPeriod other)
            {
return this.Quarter.CompareTo(other.Quarter) < 0 || this.Month.CompareTo(other.Month) < 0;
            }

            public override int GetHashCode()
            {
                unchecked
                {
                    return (this.Quarter.GetHashCode() * 397) ^ this.Month.GetHashCode();
                }
            }

            public static FiscalPeriod operator +(FiscalPeriod c1, FiscalPeriod c2)
            {
return new FiscalPeriod((c1.Quarter + c2.Quarter) > 4 ? (c1.Quarter + c2.Quarter)-4 : (c1.Quarter + c2.Quarter), (c1.Month + c2.Month) > 12 ? (c1.Month + c2.Month) - 12 : (c1.Month + c2.Month));
            }

            public static bool operator ==(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.Equals(c2);
            }

            public static bool operator !=(FiscalPeriod c1, FiscalPeriod c2)
            {
                return !c1.Equals(c2);
            }
            public static bool operator >(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.GreaterThan(c2);
            }
            public static bool operator <(FiscalPeriod c1, FiscalPeriod c2)
            {
                return c1.LessThan(c2);
            }
            public override string ToString()
            {
                return (String.Format("Q{0}:P{1}", this.Quarter, this.Month));
            }

        }

        public class FiscalPeriodFormatter : IFormatter<FiscalPeriod>
        {
public void Serialize(FiscalPeriod instance, IColumnWriter writer, ISerializationContext context)
            {
                using (var binaryWriter = new BinaryWriter(writer.BaseStream))
                {
                    binaryWriter.Write(instance.Quarter);
                    binaryWriter.Write(instance.Month);
                    binaryWriter.Flush();
                }
            }

public FiscalPeriod Deserialize(IColumnReader reader, ISerializationContext context)
            {
                using (var binaryReader = new BinaryReader(reader.BaseStream))
                {
var result = new FiscalPeriod(binaryReader.ReadInt16(), binaryReader.ReadInt16());
                    return result;
                }
            }
        }
    }
}
```

## <a name="use-user-defined-aggregates-udagg"></a>Använd användardefinierade agg regeringar: UDAGG
Användardefinierade agg regeringar är alla agg regerings funktioner som inte levereras direkt med U-SQL. Exemplet kan vara en agg regering för att utföra anpassade matematiska beräkningar, sträng sammanfogningar, modifieringar med strängar och så vidare.

Den användardefinierade agg regerings Bask lass definitionen är följande:

```csharp
    [SqlUserDefinedAggregate]
    public abstract class IAggregate<T1, T2, TResult> : IAggregate
    {
        protected IAggregate();

        public abstract void Accumulate(T1 t1, T2 t2);
        public abstract void Init();
        public abstract TResult Terminate();
    }
```

**SqlUserDefinedAggregate** anger att typen ska registreras som en användardefinierad mängd. Det går inte att ärva den här klassen.

Attributet SqlUserDefinedType är **valfritt** för UDAGG-definitionen.


Med Bask Lassen kan du skicka tre abstrakta parametrar: två som indataparametrar och en som resultat. Data typerna är variabla och bör definieras under klass arvet.

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    { … }

    public override void Accumulate(string guid, string user)
    { … }

    public override string Terminate()
    { … }
}
```

* **Init** anropar en gång för varje grupp under beräkning. Den innehåller en initierings rutin för varje agg regerings grupp.  
* **Ackumulerad** utförs en gång för varje värde. Den tillhandahåller huvud funktionerna för agg regerings algoritmen. Den kan användas för att aggregera värden med olika data typer som definieras vid klass arvet. Den kan acceptera två parametrar för variabla data typer.
* **Avsluta** utförs en gång per sammansättnings grupp i slutet av bearbetningen för att generera resultatet för varje grupp.

Om du vill deklarera rätt data typer för indata och utdata använder du klass definitionen enligt följande:

```csharp
public abstract class IAggregate<T1, T2, TResult> : IAggregate
```

* T1: första parametern som ska ackumuleras
* T2: den andra parametern som ska samlas
* TResult: retur typen Avbryt

Exempel:

```csharp
public class GuidAggregate : IAggregate<string, int, int>
```

eller

```csharp
public class GuidAggregate : IAggregate<string, string, string>
```

### <a name="use-udagg-in-u-sql"></a>Använd UDAGG i U-SQL
Om du vill använda UDAGG ska du först definiera den i bakomliggande eller referera till den från den befintliga programmerings-DLL: en som beskrivs ovan.

Använd sedan följande syntax:

```csharp
AGG<UDAGG_functionname>(param1,param2)
```

Här är ett exempel på UDAGG:

```csharp
public class GuidAggregate : IAggregate<string, string, string>
{
    string guid_agg;

    public override void Init()
    {
        guid_agg = "";
    }

    public override void Accumulate(string guid, string user)
    {
        if (user.ToUpper()== "USER1")
        {
        guid_agg += "{" + guid + "}";
        }
    }

    public override string Terminate()
    {
        return guid_agg;
    }

}
```

Och basera U-SQL-skript:

```usql
DECLARE @input_file string = @"\usql-programmability\input_file.tsv";
DECLARE @output_file string = @" \usql-programmability\output_file.tsv";

@rs0 =
    EXTRACT
            guid string,
        dt DateTime,
            user String,
            des String
    FROM @input_file 
    USING Extractors.Tsv();

@rs1 =
    SELECT
        user,
        AGG<USQL_Programmability.GuidAggregate>(guid,user) AS guid_list
    FROM @rs0
    GROUP BY user;

OUTPUT @rs1 TO @output_file USING Outputters.Text();
```

I det här användnings fallet sammanfogar vi klass-GUID för de aktuella användarna.

## <a name="next-steps"></a>Nästa steg
* [Guide för U-SQL-programmering – översikt](data-lake-analytics-u-sql-programmability-guide.md)
* [Guide för U-SQL-programmering – UDO](data-lake-analytics-u-sql-programmability-guide-UDO.md)
