---
title: 'ML Studio (klassisk): skapa & distribuera anpassade R-moduler – Azure'
description: Lär dig hur du skapar och distribuerar anpassade R-moduler i ML Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: d44f2cfa72bd53b01da073fca31ca698eb42720d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520484"
---
# <a name="define-custom-r-modules-for-machine-learning-studio-classic"></a>Definiera anpassade R-moduler för Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

I det här avsnittet beskrivs hur du skapar och distribuerar en anpassad R Studio (klassisk). Det förklarar vilka anpassade R-moduler som är och vilka filer som används för att definiera dem. Den illustrerar hur du skapar filerna som definierar en modul och hur du registrerar modulen för distribution i en Machine Learning-arbetsyta. De element och attribut som används i definitionen av den anpassade modulen beskrivs sedan i detalj. Information om hur du använder extra funktioner och filer och flera utdata beskrivs också. 

En **anpassad modul** är en användardefinierad modul som kan överföras till din arbets yta och köras som en del av den Azure Machine Learning Studio (klassiska) experimentet. En **anpassad r-modul** är en anpassad modul som kör en användardefinierad R-funktion. **R** är ett programmeringsspråk för statistisk data behandling och grafik som används ofta av statistiker och data experter för att implementera algoritmer. För närvarande är R det enda språk som stöds i anpassade moduler, men stöd för ytterligare språk schemaläggs för framtida versioner.

Anpassade moduler har **status i första klass** i Azure Machine Learning Studio (klassisk) i den mening att de kan användas precis som andra moduler. De kan köras med andra moduler, som ingår i publicerade experiment eller i visualiseringar. Du har kontroll över algoritmen som implementeras av modulen, vilka indata-och utdataports portar som ska användas, modell parametrar och andra olika körnings beteenden. Ett experiment som innehåller anpassade moduler kan också publiceras i Azure AI Gallery för enkel delning.

## <a name="files-in-a-custom-r-module"></a>Filer i en anpassad R-modul
En anpassad R-modul definieras av en. zip-fil som innehåller minst två filer:

* En **käll fil** som implementerar R-funktionen som exponeras av modulen
* En **XML-definitions fil** som beskriver gränssnittet för anpassad modul

Ytterligare tilläggsfiler kan också ingå i. zip-filen som innehåller funktioner som kan nås från den anpassade modulen. Det här alternativet beskrivs i **argument** delen av **elementen i referens avsnittet i XML-definitions filen** efter snabb starts exemplet.

## <a name="quickstart-example-define-package-and-register-a-custom-r-module"></a>Snabb starts exempel: definiera, paketera och registrera en anpassad R-modul
Det här exemplet illustrerar hur du skapar de filer som krävs av en anpassad R-modul, paketerar dem i en zip-fil och sedan registrerar modulen i din Machine Learning-arbetsyta. Zip-paketet och exempelfilerna kan laddas ned från [Ladda ned CustomAddRows.zip-filen](https://go.microsoft.com/fwlink/?LinkID=524916&clcid=0x409).

## <a name="the-source-file"></a>Käll filen
Överväg exemplet på en anpassad modul för att **lägga till rader** som ändrar standard implementeringen av modulen **Lägg till rader** som används för att sammanfoga rader (observationer) från två data uppsättningar (data ramar). I modulen standard **Lägg till rader** läggs raderna i den andra data uppsättningen till i slutet av den första data uppsättningen med `rbind` algoritmen. Den anpassade `CustomAddRows` funktionen godkänner två data uppsättningar, men accepterar även en boolesk växlings parameter som ytterligare indata. Om växlings parametern har angetts till **false** returneras samma data uppsättning som standard implementeringen. Men om växlings parametern är **True** lägger funktionen till rader i första data uppsättningen i slutet av den andra data uppsättningen i stället. Den CustomAddRows. R-fil som innehåller implementeringen av R- `CustomAddRows` funktionen som exponeras av modulen för **anpassade Lägg till rader** har följande R-kod.

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) 
{
    if (swap)
    {
        return (rbind(dataset2, dataset1));
    }
    else
    {
        return (rbind(dataset1, dataset2));
    } 
} 
```

### <a name="the-xml-definition-file"></a>XML-definitions filen
Om du vill visa den här `CustomAddRows` funktionen som den Azure Machine Learning Studio (klassiska) modulen, måste du skapa en XML-definitions fil för att ange hur den **anpassade modulen Lägg till rader** ska se ut och bete sig. 

```xml
<!-- Defined a module using an R Script -->
<Module name="Custom Add Rows">
    <Owner>Microsoft Corporation</Owner>
    <Description>Appends one dataset to another. Dataset 2 is concatenated to Dataset 1 when Swap is FALSE, and vice versa when Swap is TRUE.</Description>

<!-- Specify the base language, script file and R function to use for this module. -->        
    <Language name="R" 
        sourceFile="CustomAddRows.R" 
        entryPoint="CustomAddRows" />  

<!-- Define module input and output ports -->
<!-- Note: The values of the id attributes in the Input and Arg elements must match the parameter names in the R Function CustomAddRows defined in CustomAddRows.R. -->
    <Ports>
        <Input id="dataset1" name="Dataset 1" type="DataTable">
            <Description>First input dataset</Description>
        </Input>
        <Input id="dataset2" name="Dataset 2" type="DataTable">
            <Description>Second input dataset</Description>
        </Input>
        <Output id="dataset" name="Dataset" type="DataTable">
            <Description>The combined dataset</Description>
        </Output>
    </Ports>

<!-- Define module parameters -->
    <Arguments>
        <Arg id="swap" name="Swap" type="bool" >
            <Description>Swap input datasets.</Description>
        </Arg>
    </Arguments>
</Module>
```

Det är viktigt att Observera att värdet för **ID-** attributen för **indata** -och **arg** -element i XML-filen måste matcha R-kodens funktions parameter namn i CustomAddRows. R-filen exakt: (*DataSet1*, *DataSet2* och *swap* i exemplet). På samma sätt måste värdet för attributet **entryPoint** i **språk** elementet matcha namnet på funktionen i R-skriptet exakt: (*CustomAddRows* i exemplet). 

Däremot motsvarar **ID-** attributet för **output** -elementet inte några variabler i R-skriptet. Om fler än en utmatning krävs behöver du bara returnera en lista från R-funktionen med resultat som har placerats *i samma ordning* som **utmatnings** elementen deklareras i XML-filen.

### <a name="package-and-register-the-module"></a>Paketera och registrera modulen
Spara de här två filerna som *CustomAddRows. R* och *CustomAddRows.xml* och zippa sedan samman de två filerna i en *CustomAddRows.zip* fil.

Om du vill registrera dem på arbets ytan Machine Learning går du till din arbets yta i Azure Machine Learning Studio (klassisk), klickar på knappen **+ ny** längst ned och väljer **modul-> från zip-paket** för att ladda upp den nya **anpassade modulen Lägg till rader** .

![Ladda upp zip](./media/custom-r-modules/upload-from-zip-package.png)

Den **anpassade modulen Lägg till rader** är nu redo att användas av dina Machine Learning experiment.

## <a name="elements-in-the-xml-definition-file"></a>Element i XML-definitions filen
### <a name="module-elements"></a>Modulens element
**Module** -elementet används för att definiera en anpassad modul i XML-filen. Flera moduler kan definieras i en XML-fil med hjälp av flera **modul** -element. Varje modul i arbets ytan måste ha ett unikt namn. Registrera en anpassad modul med samma namn som en befintlig anpassad modul och den ersätter den befintliga modulen med den nya. Anpassade moduler kan dock registreras med samma namn som en befintlig Azure Machine Learning Studio-modul (klassisk). I så fall visas de i den **anpassade** kategorin för modulen.

```xml
<Module name="Custom Add Rows" isDeterministic="false"> 
    <Owner>Microsoft Corporation</Owner>
    <Description>Appends one dataset to another...</Description>/> 
```

I elementet **module** kan du ange två ytterligare valfria element:

* ett **ägar** element som är inbäddat i modulen  
* ett **beskrivnings** element som innehåller text som visas i snabb hjälpen för modulen och när du hovrar över modulen i Machine Learning användar gränssnitt.

Regler för tecken gränser i modulens element:

* Värdet **för namnattributet i** **module** -elementet får inte vara längre än 64 tecken. 
* Innehållet i **Description** -elementet får inte vara längre än 128 tecken.
* Innehållet i **ägar** elementet får inte vara längre än 32 tecken.

En moduls resultat kan vara deterministiska eller icke deterministiska. * * som standard anses alla moduler vara deterministiska. Det innebär att om du har en oförändrad uppsättning av indataparametrar och data, ska modulen returnera samma resultat eacRAND eller en funktions tid som körs. Med det här beteendet Azure Machine Learning Studio (klassisk) endast omförsök för moduler som marker ATS som deterministiska om en parameter eller indata har ändrats. Att returnera de cachelagrade resultaten ger också mycket snabbare körning av experiment.

Det finns funktioner som är icke-deterministiska, t. ex. RAND eller en funktion som returnerar aktuellt datum och aktuell tid. Om modulen använder en icke-deterministisk funktion kan du ange att modulen är icke-deterministisk genom att ange det valfria **isDeterministic** -attributet till **false**. Detta säkerställer att modulen körs igen när experimentet körs, även om modulernas indatatyper och parametrar inte har ändrats. 

### <a name="language-definition"></a>Definition av språk
**Språk** elementet i XML-definitions filen används för att ange det anpassade modulens språk. R är för närvarande det enda språk som stöds. Värdet för attributet **sourceFile** måste vara namnet på R-filen som innehåller den funktion som ska anropas när modulen körs. Den här filen måste vara en del av zip-paketet. Värdet för attributet **entryPoint** är namnet på funktionen som anropas och måste matcha en giltig funktion som definierats med i käll filen.

```xml
<Language name="R" sourceFile="CustomAddRows.R" entryPoint="CustomAddRows" />
```

### <a name="ports"></a>Portar
Portarna för indata och utdata för en anpassad modul anges i underordnade element i avsnittet **ports** i XML-definitions filen. Ordningen på dessa element bestämmer layouten som erfarna (UX) av användare. De första underordnade **indata** eller **utdata** som anges i elementet **ports** i XML-filen blir den vänstra-mest angivna porten i Machine Learning UX.
Varje indata-och utdataport kan ha ett valfritt underordnat **beskrivnings** element som anger den text som visas när du hovrar mus markören över porten i Machine Learning gränssnittet.

**Port regler**:

* Maximalt antal **indata-och utgående portar** är 8 för var och en.

### <a name="input-elements"></a>Inmatade element
Med indataportar kan du skicka data till din R-funktion och-arbets yta. De **data typer** som stöds för indataportar är följande: 

**DataTable:** Den här typen skickas till R-funktionen som data. Frame. I själva verket konverteras alla typer (t. ex. CSV-filer eller ARFF-filer) som stöds av Machine Learning och som är kompatibla med **DataTable** till data. Frame automatiskt. 

```xml
<Input id="dataset1" name="Input 1" type="DataTable" isOptional="false">
    <Description>Input Dataset 1</Description>
</Input>
```

Det **ID-** attribut som är associerat med varje **DataTable** -dataport måste ha ett unikt värde och det här värdet måste matcha motsvarande namngivna parameter i R-funktionen.
Valfria **DataTable** -portar som inte överförs som inloggade i ett experiment har värdet **Null** som skickas till R-funktionen och valfria zip-portar ignoreras om indatamängden inte är ansluten. Attributet **isOptional** är valfritt för både **DataTable** -och **zip** -typerna och är *falskt* som standard.

**Zip:** Anpassade moduler kan acceptera en zip-fil som indata. Den här indatamängden packas upp i R Work-katalogen i din funktion

```xml
<Input id="zippedData" name="Zip Input" type="Zip" IsOptional="false">
    <Description>Zip files to be extracted to the R working directory.</Description>
</Input>
```

För anpassade R-moduler behöver inte ID: t för en zip-port matcha några parametrar för R-funktionen. Detta beror på att zip-filen automatiskt extraheras till R-arbetskatalogen.

**Ingångs regler:**

* Värdet för attributet ID i **indatavärdet** måste vara ett giltigt R **-** variabel namn.
* Värdet för attributet **ID** i **indatamängden** får inte vara längre än 64 tecken.
* Värdet **för namnattributet för** **inmatat** element får inte vara längre än 64 tecken.
* Innehållet i **Description** -elementet får inte vara längre än 128 tecken
* Värdet **för Typattributet för** elementet **indatavärdet** måste vara *zip* eller *DataTable*.
* Värdet för **isOptional** -attributet för **indatamängden** är inte obligatoriskt (och är *falskt* som standard om inget anges). men om det har angetts måste det vara *Sant* eller *falskt*.

### <a name="output-elements"></a>Utmatnings element
**Standardutdata-portar:** Utgående portar mappas till retur värden från R-funktionen, som sedan kan användas av efterföljande moduler. *DataTable* är den enda standard typen av utgående port som stöds för närvarande. (Support för *Lär* och *transformeringar* är kommande.) *DataTable* -utdata definieras som:

```xml
<Output id="dataset" name="Dataset" type="DataTable">
    <Description>Combined dataset</Description>
</Output>
```

För utdata i anpassade R-moduler behöver inte värdet för **ID-** attributet motsvara vad som finns i R-skriptet, men det måste vara unikt. För utdata i en enda modul måste returvärdet från R-funktionen vara en *data. Frame*. För att kunna skriva ut fler än ett objekt av en datatyp som stöds måste rätt utgående portar anges i XML-definitions filen och objekten måste returneras som en lista. Utgående objekt tilldelas till utgående portar från vänster till höger, vilket återspeglar i vilken ordning objekten placeras i den returnerade listan.

Om du till exempel vill ändra den anpassade modulen **Lägg till rader** för att mata ut de ursprungliga två data uppsättningarna, *DataSet1* och *DataSet2*, förutom den nya sammanfogade data uppsättningen, *data uppsättningen*, (i en ordning, från vänster till höger, som: *data uppsättning*, *DataSet1*, *DataSet2*) definierar du Utdataportarna i CustomAddRows.xml-filen på följande sätt:

```xml
<Ports> 
    <Output id="dataset" name="Dataset Out" type="DataTable"> 
        <Description>New Dataset</Description> 
    </Output> 
    <Output id="dataset1_out" name="Dataset 1 Out" type="DataTable"> 
        <Description>First Dataset</Description> 
    </Output> 
    <Output id="dataset2_out" name="Dataset 2 Out" type="DataTable"> 
        <Description>Second Dataset</Description> 
    </Output> 
    <Input id="dataset1" name="Dataset 1" type="DataTable"> 
        <Description>First Input Table</Description>
    </Input> 
    <Input id="dataset2" name="Dataset 2" type="DataTable"> 
        <Description>Second Input Table</Description> 
    </Input> 
</Ports> 
```

Och returnerar listan över objekt i en lista i rätt ordning i ' CustomAddRows. R ':

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) { 
    if (swap) { dataset <- rbind(dataset2, dataset1)) } 
    else { dataset <- rbind(dataset1, dataset2)) 
    } 
    return (list(dataset, dataset1, dataset2)) 
} 
```

**Visualisering av utdata:** Du kan också ange en utdataport av typen *visualisering*, som visar utdata från R Graphics-enheten och konsolens utdata. Den här porten är inte en del av utdata för R-funktionen och påverkar inte ordningen för andra typer av utgående portar. Om du vill lägga till en visualiserings port i de anpassade modulerna lägger du till ett **utdata** -element med värdet *visualisering* för dess **typ** -attribut:

```xml
<Output id="deviceOutput" name="View Port" type="Visualization">
    <Description>View the R console graphics device output.</Description>
</Output>
```

**Utmatnings regler:**

* Värdet för attributet ID i elementet **output** måste vara ett giltigt R **-** variabel namn.
* Värdet för attributet ID i **output** **-** elementet får inte vara längre än 32 tecken.
* Värdet **för namnattributet för** elementet **output** får inte vara längre än 64 tecken.
* Värdet **för Typattributet för** elementet **output** måste vara *visualisering*.

### <a name="arguments"></a>Argument
Ytterligare data kan skickas till R-funktionen via modul parametrar som definieras i elementet **arguments** . Dessa parametrar visas i rutan Egenskaper för längst till höger i Machine Learning gränssnittet när modulen väljs. Argument kan vara vilken som helst av de typer som stöds, eller så kan du skapa en anpassad uppräkning när det behövs. På samma sätt som **portens** element kan **argument** element ha ett valfritt **beskrivnings** element som anger texten som visas när du hovrar med musen över parameter namnet.
Valfria egenskaper för en modul, till exempel defaultValue, minValue och maxValue, kan läggas till i argument som attribut till ett **egenskaps** element. Giltiga egenskaper för **egenskaps** elementet är beroende av argument typen och beskrivs med argument typerna som stöds i nästa avsnitt. Argument med egenskapen **isOptional** inställt på **"true"** kräver inte att användaren anger ett värde. Om inget värde anges för argumentet skickas inte argumentet till Start punkt funktionen. Argument för den Start punkts funktion som är valfria behöver hanteras explicit av funktionen, t. ex. ett standardvärdet NULL-värde i Start punkts funktions definitionen. Ett valfritt argument tvingar bara de andra argument begränsningarna, dvs. min eller Max, om ett värde anges av användaren.
Precis som med indata och utdata är det viktigt att var och en av parametrarna har unika ID-värden kopplade till sig. I vårt snabb starts exempel har tillhör ande ID/parameter *växlats*.

### <a name="arg-element"></a>Arg-element
En modul-parameter definieras med det underordnade elementet **arg** i **argument** avsnittet i XML-definitions filen. Precis som med underordnade element i avsnittet **portar** definierar ordningen för parametrar i avsnittet **argument** den layout som påträffas i UX. Parametrarna visas uppifrån och ned i användar gränssnittet i samma ordning som de är definierade i XML-filen. De typer som stöds av Machine Learning för parametrar visas här. 

**int** – en heltals typ (32-bitars).

```xml
<Arg id="intValue1" name="Int Param" type="int">
    <Properties min="0" max="100" default="0" />
    <Description>Integer Parameter</Description>
</Arg>
```

* *Valfria egenskaper*: **min**, **Max**, **standard** och **isOptional**

**Double** – en dubbel typ parameter.

```xml
<Arg id="doubleValue1" name="Double Param" type="double">
    <Properties min="0.000" max="0.999" default="0.3" />
    <Description>Double Parameter</Description>
</Arg>
```

* *Valfria egenskaper*: **min**, **Max**, **standard** och **isOptional**

**bool** – en boolesk parameter som representeras av en kryss ruta i UX.

```xml
<Arg id="boolValue1" name="Boolean Param" type="bool">
    <Properties default="true" />
    <Description>Boolean Parameter</Description>
</Arg>
```

* *Valfria egenskaper*: **standard** -falskt om inget anges

**sträng**: en standard sträng

```xml
<Arg id="stringValue1" name="My string Param" type="string">
    <Properties isOptional="true" />
    <Description>String Parameter 1</Description>
</Arg>    
```

* *Valfria egenskaper*: **standard** och **isOptional**

**ColumnPicker**: en kolumn vals parameter. Den här typen återges i UX som en kolumn väljare. **Egenskaps** elementet används här för att ange ID: t för den port från vilken kolumner väljs, där mål Port typen måste vara *DataTable*. Resultatet av kolumn urvalet skickas till R-funktionen som en lista med strängar som innehåller de valda kolumn namnen. 

```xml
<Arg id="colset" name="Column set" type="ColumnPicker">      
    <Properties portId="datasetIn1" allowedTypes="Numeric" default="NumericAll"/>
    <Description>Column set</Description>
</Arg>
```

* *Obligatoriska egenskaper*: **PORTID** – matchar ID för ett inmatat element med typen *DataTable*.
* *Valfria egenskaper*:
  
  * **allowedTypes** – filtrerar kolumn typerna som du kan välja mellan. Giltiga värden är: 
    
    * Numerisk
    * Boolesk
    * Kategoriska
    * Sträng
    * Etikett
    * Funktion
    * Poäng
    * Alla
  * **standard** – giltiga standard val för kolumn väljaren är: 
    
    * Inget
    * NumericFeature
    * NumericLabel
    * NumericScore
    * Numeriskt
    * BooleanFeature
    * BooleanLabel
    * BooleanScore
    * BooleanAll
    * CategoricalFeature
    * CategoricalLabel
    * CategoricalScore
    * CategoricalAll
    * StringFeature
    * StringLabel
    * StringScore
    * StringAll
    * AllLabel
    * AllFeature
    * AllScore
    * Alla

**Listruta**: en användardefinierad lista med uppräknade användare. List Rute elementen anges i elementet **Properties** med ett **objekt** element. **ID:** t för varje **objekt** måste vara unikt och en giltig R-variabel. Värdet för **namnet** på ett **objekt** fungerar som både den text som visas och värdet som skickas till R-funktionen.

```xml
<Arg id="color" name="Color" type="DropDown">
    <Properties default="red">
        <Item id="red" name="Red Value"/>
        <Item id="green" name="Green Value"/>
        <Item id="blue" name="Blue Value"/>
    </Properties>
    <Description>Select a color.</Description>
</Arg>    
```

* *Valfria egenskaper*:
  * **standard** -värdet för standard egenskapen måste motsvara ett ID-värde från ett av **objekt** elementen.

### <a name="auxiliary-files"></a>Tilläggsfiler
Alla filer som placeras i din ZIP-fil för anpassade moduler kommer att vara tillgängliga för användning under körnings tiden. Alla katalog strukturer finns kvar. Det innebär att filkälla fungerar likadant lokalt och i den Azure Machine Learning Studio (klassiska) körningen. 

> [!NOTE]
> Observera att alla filer extraheras till "src"-katalogen så att alla sökvägar ska ha prefixet "src/".
> 
> 

Anta till exempel att du vill ta bort alla rader med NAs från data uppsättningen och även ta bort eventuella dubblettrader innan du lägger till dem i CustomAddRows och att du redan har skrivit en R-funktion som gör det i en fil RemoveDupNARows. R:

```r
RemoveDupNARows <- function(dataFrame) {
    #Remove Duplicate Rows:
    dataFrame <- unique(dataFrame)
    #Remove Rows with NAs:
    finalDataFrame <- dataFrame[complete.cases(dataFrame),]
    return(finalDataFrame)
}
```

Du kan källa till tilläggs filen RemoveDupNARows. R i CustomAddRows-funktionen:

```r
CustomAddRows <- function(dataset1, dataset2, swap=FALSE) {
    source("src/RemoveDupNARows.R")
        if (swap) { 
            dataset <- rbind(dataset2, dataset1))
        } else { 
            dataset <- rbind(dataset1, dataset2)) 
        } 
    dataset <- removeDupNARows(dataset)
    return (dataset)
}
```

Sedan laddar du upp en zip-fil som innehåller "CustomAddRows. R", "CustomAddRows.xml" och "RemoveDupNARows. R" som en anpassad R-modul.

## <a name="execution-environment"></a>Körnings miljö
Körnings miljön för R-skriptet använder samma version av R som modulen **köra R-skript** och kan använda samma standard paket. Du kan också lägga till ytterligare R-paket i din anpassade modul genom att inkludera dem i zip-paketet för den anpassade modulen. Läs bara in dem i ditt R-skript som du skulle göra i din egen R-miljö. 

**Begränsningar i körnings miljön** är:

* Icke-beständigt fil system: filer som skrivs när den anpassade modulen körs behålls inte över flera körningar av samma modul.
* Ingen nätverks åtkomst