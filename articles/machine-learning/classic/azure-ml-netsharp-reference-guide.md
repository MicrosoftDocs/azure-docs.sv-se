---
title: 'ML Studio (klassisk): net # Custom neurala Networks – Azure'
description: 'Syntax guide för net # neurala Networks Specification-språk. Lär dig hur du skapar anpassade neurala-nätverks modeller i Azure Machine Learning Studio (klassisk).'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: 5137b633f66088efbee41b96ba715eb3b18961dc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519260"
---
# <a name="guide-to-net-neural-network-specification-language-for-machine-learning-studio-classic"></a>Guide till net # neurala Network Specification language för Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

NET # är ett språk som utvecklats av Microsoft och som används för att definiera komplexa neurala-nätverks arkitekturer som djup neurala nätverk eller convolutions för godtyckliga dimensioner. Du kan använda komplexa strukturer för att förbättra inlärningen på data, till exempel bild, video eller ljud.

Du kan använda en net # Architecture-specifikation i följande kontexter:

+ Alla neurala i Microsoft Azure Machine Learning Studio (klassisk): [neurala nätverk](/azure/machine-learning/studio-module-reference/multiclass-neural-network)i flera klasser, [två neurala nätverk](/azure/machine-learning/studio-module-reference/two-class-neural-network)och [neurala nätverks regression](/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neurala Network Functions in Microsoft ML Server: [NeuralNet](/machine-learning-server/r-reference/microsoftml/neuralnet) och [rxNeuralNet](/machine-learning-server/r-reference/microsoftml/rxneuralnet)för R-språket och [rx_neural_network](/machine-learning-server/python-reference/microsoftml/rx-neural-network) för python.


I den här artikeln beskrivs de grundläggande begreppen och syntaxen som behövs för att utveckla ett anpassat neurala-nätverk med net #:

+ Neurala nätverks krav och hur du definierar de primära komponenterna
+ Syntaxen och nyckelorden för net # Specification-språket
+ Exempel på anpassade neurala-nätverk som skapats med net #



## <a name="neural-network-basics"></a>Neurala Network-grunder

En neurala nätverks struktur består av noder som är ordnade i lager och viktade anslutningar (eller kanter) mellan noderna. Anslutningarna är riktade och varje anslutning har en källnod och en målnod.

Varje skiktat lager (ett dolt eller ett utmatnings lager) har ett eller flera **anslutnings paket**. Ett anslutnings paket består av ett käll skikt och en specifikation av anslutningarna från det käll skiktet. Alla anslutningar i en specifik paket resurs källa och mål lager. I net # betraktas ett anslutnings paket som tillhör paketets mål lager.

NET # stöder olika typer av anslutnings paket, vilket gör att du kan anpassa hur indata mappas till dolda lager och mappas till utdata.

Standard-eller standard-paketet är ett **fullständigt paket** där varje nod i käll skiktet är ansluten till varje nod i mål lagret.

NET # stöder dessutom följande fyra typer av avancerade anslutnings paket:

+ **Filtrerade paket**. Du kan definiera ett predikat genom att använda platserna för noden käll skikt och noden mål lager. Noderna är anslutna när predikatet är sant.

+ **(Convolutional-paket**. Du kan definiera små platser med noder i käll skiktet. Varje nod i mål lagret är ansluten till ett noder i käll skiktet.

+ **Samlings paket och** **svars normaliserings paket**. Dessa liknar (convolutional-paket i som användaren definierar små noder i käll skiktet. Skillnaden är att kanternas vikt i dessa paket inte kan tränas. I stället tillämpas en fördefinierad funktion på Källnoden för att fastställa värdet för målnoden.


## <a name="supported-customizations"></a>Anpassningar som stöds

Arkitekturen i neurala-nätverks modeller som du skapar i Azure Machine Learning Studio (klassisk) kan anpassas i stor utsträckning med hjälp av net #. Du kan:

+ Skapa dolda lager och kontrol lera antalet noder i varje lager.
+ Ange hur lager ska vara anslutna till varandra.
+ Definiera särskilda anslutnings strukturer, till exempel convolutions och vikt delnings paket.
+ Ange olika aktiverings funktioner.

Mer information om Specification-språksyntaxen finns i [Structure Specification](#structure-specifications).

Exempel på hur du definierar neurala-nätverk för några vanliga Machine Learning-uppgifter, från simplex till komplex, finns i [exempel](#examples-of-net-usage).

## <a name="general-requirements"></a>Allmänna krav

+ Det måste finnas exakt ett utmatnings lager, minst ett inmatnings lager och noll eller flera dolda lager.
+ Varje lager har ett fast antal noder som är konceptuellt ordnade i en rektangulär matris med godtyckliga dimensioner.
+ Inmatnings lager har inga tillhör ande utbildade parametrar och representerar den punkt där instans data går in i nätverket.
+ De skikt bara skikten (dolda och utgående lager) har associerade, utbildade parametrar, som kallas vikter och bias.
+ Käll-och mål-noderna måste finnas i separata lager.
+ Anslutningarna måste vara acykliska; med andra ord får det inte finnas en kedja av anslutningar som leder tillbaka till den ursprungliga Källnoden.
+ Utmatnings lagret får inte vara ett käll skikt i ett anslutnings paket.

## <a name="structure-specifications"></a>Specifikationer för struktur

En neurala nätverks struktur specifikation består av tre delar: **konstant deklaration**, **lager deklaration**, **anslutnings deklarationen**. Det finns även ett valfritt **resurs deklarations** avsnitt. Avsnitten kan anges i vilken ordning som helst.

## <a name="constant-declaration"></a>Konstant deklaration

En konstant deklaration är valfri. Det är ett sätt att definiera värden som används någon annan stans i neurala-nätverks definitionen. Deklarations instruktionen består av en identifierare följt av ett likhets tecken och ett värde uttryck.

Följande uttryck definierar till exempel en konstant `x` :

`Const X = 28;`

Om du vill definiera två eller flera konstanter samtidigt, omger du Identifierarens namn och värden inom klammerparenteser och avgränsar dem med hjälp av semikolon. Exempel:

`Const { X = 28; Y = 4; }`

Höger sida av varje tilldelnings uttryck kan vara ett heltal, ett reellt tal, ett booleskt värde (sant eller falskt) eller ett matematiskt uttryck. Exempel:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Lager deklaration

Lager deklarationen krävs. Den definierar lagrets storlek och källa, inklusive dess anslutnings paket och attribut. Deklarations instruktionen börjar med namnet på lagret (indata, dold eller utdata) följt av lagrets mått (en tupel med positiva heltal). Exempel:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Dimensionens produkt är antalet noder i lagret. I det här exemplet finns det två dimensioner [5, 20], vilket innebär att det finns 100 noder i skiktet.
+ Lagren kan deklareras i vilken ordning som helst, med ett undantag: om fler än ett inmatnings lager har definierats måste den ordning som de deklareras i vara samma som ordningen på funktionerna i indata.

Om du vill ange att antalet noder i ett lager ska fastställas automatiskt använder du `auto` nyckelordet. `auto`Nyckelordet har olika effekter, beroende på lagret:

+ I en deklaration för inmatnings lager är antalet noder antalet funktioner i indata.
+ I en deklaration för en dold lager är antalet noder det tal som anges av parametervärdet för **antalet dolda noder**.
+ I en deklaration för utmatnings skikt är antalet noder 2 för klassificering med två klasser, 1 för regression och lika med antalet utdata-noder för klassificering av flera klasser.

Följande nätverks definition tillåter till exempel att storleken på alla skikt bestäms automatiskt:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

En lager deklaration för ett skiktat lager (dolda eller utgående lager) kan alternativt ta med funktionen output (kallas även en aktiverings funktion) som standardvärdet för **sigmoid** för klassificerings modeller och **linjära** för Regressions modeller. Även om du använder standard kan du uttryckligen ange aktiverings funktionen om du vill ha klarhet.

Följande utdata-funktioner stöds:

+ sigmoid
+ Line
+ softmax
+ rlinear
+ Square
+ sqrt
+ srlinear
+ ABS
+ tanh
+ brlinear

Följande deklaration använder till exempel funktionen **SOFTMAX** :

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Anslutnings deklaration

Direkt efter att du har definierat det tågens skiktet måste du deklarera anslutningar mellan de lager som du har definierat. Deklarationen för anslutnings paket börjar med nyckelordet `from` följt av namnet på paketets käll skikt och vilken typ av anslutnings paket som ska skapas.

För närvarande stöds fem typer av anslutnings paket:

+ **Fullständiga** paket som anges av nyckelordet `all`
+ **Filtrerade** paket som anges av nyckelordet `where` följt av ett predikat-uttryck
+ **(Convolutional** paket som anges av nyckelordet `convolve` följt av convolution-attributen
+ **Samlings paket,** angivna med nyckelorden **Max pool** eller **genomsnitts pool**
+ Paket för **svars normalisering** som anges av nyckelordet **Response norm**

## <a name="full-bundles"></a>Fullständiga paket

Ett fullständigt anslutnings paket innehåller en anslutning från varje nod i käll skiktet till varje nod i mål lagret. Detta är standard typen av nätverks anslutning.

## <a name="filtered-bundles"></a>Filtrerade paket

En filtrerad specifikation för anslutnings paket innehåller ett predikat, uttryckt syntaktiskt, ungefär som ett C# lambda-uttryck. I följande exempel definieras två filtrerade paket:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ I predikatet för `ByRow` `s` är en parameter som representerar ett index i den rektangulära matrisen av noder i indatanivå, `Pixels` och `d` är en parameter som representerar ett index i matrisen med noder i det dolda lagret `ByRow` . Typen för båda `s` och `d` är en tupel av heltal med längden två. Konceptuellt, `s` intervall över alla par heltal med och och `0 <= s[0] < 10` `0 <= s[1] < 20` `d` intervall över alla par med heltal, med `0 <= d[0] < 10` och `0 <= d[1] < 12` .

+ På den högra sidan av predikatet uttryck finns det ett villkor. I det här exemplet finns det `s` `d` en gräns från käll skiktets nod till mål skiktet för varje värde av och så att villkoret är uppfyllt. Detta filter uttryck anger därför att paketet innehåller en anslutning från noden som definieras av `s` till den nod som definieras i `d` i samtliga fall där s [0] är lika med d [0].

Alternativt kan du ange en uppsättning vikter för ett filtrerat paket. Värdet för attributet **viktning** måste vara en tupel av flytt ALS värden med en längd som matchar antalet anslutningar som definieras av paketet. Som standard genereras vikter slumpmässigt.

Viktnings värden grupperas efter målets index. Det vill säga om den första målnoden är ansluten till K-Källnoden, är de första `K` elementen i tuplen **vikter** för den första målnoden i käll index ordning. Samma gäller för de återstående målnoden.

Det är möjligt att ange vikter direkt som konstanta värden. Om du till exempel har lärt vikten tidigare kan du ange dem som konstanter med följande syntax:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>(Convolutional-paket

När tränings data har en homogen struktur, används (convolutional-anslutningar ofta för att lära dig mer avancerade funktioner i data. I bild-, ljud-eller video data kan det till exempel vara ganska enhetligt att ha en enhetlig eller temporal dimension.

(Convolutional-paket använder rektangulära **kärnor** som är slid genom dimensionerna. I grunden definierar varje kernel en uppsättning vikter som används i lokala områden, vilket kallas för **kernel-program**. Varje kernel-program motsvarar en nod i käll skiktet, som kallas för den **centrala noden**. Vikten av en kernel delas mellan många anslutningar. I ett (convolutional-paket är varje kernel rektangulär och alla kernel-program har samma storlek.

(Convolutional-paket stöder följande attribut:

**InputShape** definierar skiktets dimensionalitet i (convolutional-paketets syfte. Värdet måste vara en tupel av positiva heltal. Heltals produkten måste vara lika med antalet noder i käll skiktet, men annars behöver den inte matcha den dimensionalitet som har deklarerats för käll skiktet. Den här tupelens längd blir **aritet** -värdet för (convolutional-paketet. Aritet refererar vanligt vis till antalet argument eller operander som en funktion kan utföra.

Om du vill definiera form och platser för kerneln använder du attributen **KernelShape**, **kliv**, **utfyllnad**, **LowerPad** och **UpperPad**:

+ **KernelShape**: (obligatoriskt) definierar dimensionalheten för varje kernel för (convolutional-paketet. Värdet måste vara en tupel av positiva heltal med en längd som är lika med aritet för paketet. Varje komponent i denna tupel får inte vara större än motsvarande komponent i **InputShape**.

+ **Kliv**: (valfritt) definierar de glidande steg storlekarna för convolution (en steg storlek för varje dimension), det vill säga avståndet mellan de centrala noderna. Värdet måste vara en tupel av positiva heltal med en längd som är aritet för paketet. Varje komponent i denna tupel får inte vara större än motsvarande komponent i **KernelShape**. Standardvärdet är en tupel med alla komponenter som är lika med ett.

+ **Delning**: (valfritt) definierar vikt delning för varje dimension i convolution. Värdet kan vara ett enstaka booleskt värde eller en tupel av booleska värden med en längd som är aritet för paketet. Ett enda booleskt värde utökas till att vara en tupel av rätt längd med alla komponenter som är lika med det angivna värdet. Standardvärdet är en tupel som består av alla sanna värden.

+ **MapCount**: (valfritt) definierar antalet funktions mappningar för (convolutional-paketet. Värdet kan vara ett positivt heltal eller en tupel med positiva heltal med en längd som är aritet för paketet. Ett enda heltals värde utökas till att vara en tupel av rätt längd med de första komponenterna som är lika med det angivna värdet och alla återstående komponenter som är lika med ett. Standardvärdet är ett. Det totala antalet funktions kartor är produkten av komponenterna i tuppeln. Det totala antalet i alla komponenter bestämmer hur värdet för funktions kartan grupperas i målnoden.

+ **Vikter**: (valfritt) definierar paketets initiala vikt. Värdet måste vara en tupel av flytt ALS värden med en längd som är antalet kärnor gånger antalet vikter per kernel, enligt definitionen längre fram i den här artikeln. Standard vikterna genereras slumpmässigt.

Det finns två uppsättningar egenskaper som styr utfyllnaden. egenskaperna är ömsesidigt uteslutande:

+ **Utfyllnad**: (valfritt) avgör om indatatypen ska fyllas i med ett **standardfyllnads schema**. Värdet kan vara ett enskilt booleskt värde, eller så kan det vara en tupel av booleska värden med en längd som är aritet för paketet.

    Ett enda booleskt värde utökas till att vara en tupel av rätt längd med alla komponenter som är lika med det angivna värdet.

    Om värdet för en dimension är sant fylls källan logiskt i med noll-värdes celler för att stödja ytterligare kernel-program, så att de centrala noderna i den första och den sista kärnan i dimensionen är de första och sista noderna i dimensionen i käll skiktet. Därför fastställs antalet "provdocka"-noder i varje dimension automatiskt för att passa exakt `(InputShape[d] - 1) / Stride[d] + 1` kernel i det utfyllda käll skiktet.

    Om värdet för en dimension är falskt definieras kernelerna så att antalet noder på varje sida som lämnas ut är detsamma (upp till en skillnad på 1). Standardvärdet för det här attributet är en tupel med alla komponenter som är lika med falskt.

+ **UpperPad** och **LowerPad**: (valfritt) ger bättre kontroll över mängden utfyllnad som ska användas. **Viktigt:** Dessa attribut kan definieras om och endast om egenskapen **utfyllnad** ovan **_inte_ är *_ definierad. Värdena ska vara heltals-värdes par med längd som är aritet för paketet. När de här attributen anges läggs "provdocka"-noder till i de nedre och övre ändarna i varje dimension i Indatakällan. Antalet noder som läggs till i de nedre och övre ändarna i varje dimension bestäms av _* LowerPad**[i] respektive **UpperPad**[i].

    För att säkerställa att kärnan endast motsvarar "verkliga" noder och inte till "dummy"-noder måste följande villkor vara uppfyllda:
  - Varje komponent i **LowerPad** måste vara strikt mindre än `KernelShape[d]/2` .
  - Varje komponent i **UpperPad** får inte vara större än `KernelShape[d]/2` .
  - Standardvärdet för dessa attribut är en tupel med alla komponenter som är lika med 0.

    Inställningen **padding** = True tillåter lika många utfyllnad som behövs för att hålla kärnan i kärnan i "Real"-indatamängden. Detta ändrar det matematiska värdet för att beräkna storleken på utdata. Normalt beräknas utmatnings storleken *D* som `D = (I - K) / S + 1` , där är storleken på `I` indata, `K` är kernel-storleken, `S` är klivet och är en `/` heltals Division (avrunda mot noll). Om du anger UpperPad = [1, 1], är storleken på indatamängden på `I` ett effektivt sätt 29 och därmed `D = (29 - 5) / 2 + 1 = 13` . Men när **utfyllnaden** = True, kommer i själva verket att bli `I` instötande av `K - 1` ; därför `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14` . Genom att ange värden för **UpperPad** och **LowerPad** får du mycket mer kontroll över utfyllnaden än om du bara ställer in **utfyllnad** = True.

Mer information om (convolutional-nätverk och deras program finns i följande artiklar:

+ [http://d2l.ai/chapter_convolutional-neural-networks/lenet.html ](http://d2l.ai/chapter_convolutional-neural-networks/lenet.html )
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Samlings paket

Ett **pooling-paket** använder en geometri som liknar (convolutional-anslutning, men använder fördefinierade funktioner för att härleda målnoden. Därför har pooling-paket inte något tåg tillstånd (vikt eller bias). Pooling-paket stöder alla (convolutional-attribut förutom **delning**, **MapCount** och **vikter**.

Normalt överlappas inte de kärnor som sammanfattas av intilliggande pooling-enheter. Om kliv [d] är lika med KernelShape [d] i varje dimension är det lager som erhålls det traditionella lokala skikt lagret, som vanligt vis används i (convolutional neurala-nätverk. Varje målnod beräknar det maximala antalet eller medelvärdet för dess kernels aktiviteter i käll skiktet.

I följande exempel illustreras en samling paket:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ Paketets aritet är 3: det vill säga längden på tuppeln `InputShape` , `KernelShape` och `Stride` .
+ Antalet noder i käll skiktet är `5 * 24 * 24 = 2880` .
+ Detta är ett traditionellt lokalt skikt för pooler eftersom **KernelShape** och **kliv** är lika.
+ Antalet noder i mål lagret är `5 * 12 * 12 = 1440` .

Mer information om pool lager finns i följande artiklar:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Avsnitt 3,4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Paket för svars normalisering

**Normalisering av svar** är ett lokalt normaliserings schema som först introducerades av Geoffrey Hinton, et al i pappers [ImageNet klassificering med djup (convolutional neurala-nätverk](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Normalisering av svar används för att under lätta generaliseringen i neurala-näten. När en neuron startar på en mycket hög aktiverings nivå, undertrycker ett lokalt normaliserings lager på aktiverings nivån för den omgivande neurons. Detta görs med hjälp av tre parametrar ( `α` , `β` , och `k` ) och en (convolutional-struktur (eller form). Varje neuron i mål skiktet **y** motsvarar ett neuron **x** i käll skiktet. Aktiverings nivån för **y** anges av följande formel, där `f` är aktiverings nivån för en neuron och `Nx` är kärnan (eller uppsättningen som innehåller neurons i **x**), som definieras av följande (convolutional-struktur:

![formel för (convolutional-struktur](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Paket för normalisering av svar har stöd för alla (convolutional-attribut förutom **delning**, **MapCount** och **vikter**.

+ Om kärnan innehåller neurons i samma karta som ***x** _, kallas normaliserings schema _ * samma mappnings-normalisering * *. Om du vill definiera samma mappnings-normalisering måste den första koordinaten i **InputShape** ha värdet 1.

+ Om kärnan innehåller neurons i samma spatialdata som ***x** _, men neurons finns i andra Maps, kallas normaliserings schema _ * över Maps-normalisering * *. Den här typen av svars normalisering implementerar en form av lateralt hämmande som inspireras av den typ som påträffades i verklig neurons, vilket skapar en tävling för stor aktiverings nivå bland neuron utdata som beräknas på olika kartor. För att definiera över Maps-normalisering måste den första koordinaten vara ett heltal som är större än ett och inte större än antalet mappningar, och resten av koordinaterna måste ha värdet 1.

Eftersom paket för normalisering av svar tillämpar en fördefinierad funktion för Källnoden för att fastställa värdet för målnoden, har de inte något tåg tillstånd (vikt eller bias).

> [!NOTE]
> Noderna i mål lagret motsvarar neurons som är centrala noder i kernelerna. Om är till exempel `KernelShape[d]` udda, `KernelShape[d]/2` motsvarar den centrala kernel-noden. Om `KernelShape[d]` är ens är den centrala noden på `KernelShape[d]/2 - 1` . Om `Padding[d]` är false har den första och de sista `KernelShape[d]/2` noderna därför inte motsvarande noder i mål lagret. Undvik den här situationen genom att definiera **utfyllnad** som [True, true,..., True].

Förutom de fyra attribut som beskrivits tidigare, stöder svars normaliserings paket även följande attribut:

+ **Alfa**: (obligatoriskt) anger ett flytt ALS värde som motsvarar `α` i föregående formel.
+ **Beta**: (obligatoriskt) anger ett flytt ALS värde som motsvarar `β` i föregående formel.
+ **Offset**: (valfritt) anger ett flyt punkts värde som motsvarar `k` i föregående formel. Standardvärdet är 1.

I följande exempel definieras ett paket för normalisering av svar med följande attribut:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ Käll skiktet innehåller fem Maps, var och en med AOF-dimensionen 12x12, totalt antal 1440-noder.
+ Värdet för **KernelShape** anger att det här är ett likadant mappnings-normaliserings lager där området är en 3x3-rektangel.
+ Standardvärdet för **utfyllnad** är false, vilket innebär att mål lagret bara har 10 noder i varje dimension. Om du vill inkludera en nod i mål lagret som motsvarar varje nod i käll skiktet lägger du till utfyllnad = [True, true, True]; och ändra storleken på RN1 till [5, 12, 12].

## <a name="share-declaration"></a>Dela deklaration

NET # kan användas för att definiera flera buntar med delade vikter. Vikterna för två paket kan delas om deras strukturer är desamma. Följande syntax definierar paket med delade vikter:

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

Följande resurs deklaration anger till exempel lager namnen, vilket anger att både vikter och kompensationer ska delas:

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ Inmatade funktioner partitioneras i två lika stora ingångs lager.
+ De dolda lagren kan sedan beräkna funktioner på högre nivå på de två indatavärdena.
+ Den delade-deklarationen anger att *H1* och *H2* måste beräknas på samma sätt från deras respektive indata.

Alternativt kan detta anges med två separata resurs deklarationer på följande sätt:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Du kan bara använda det korta formatet när lagren innehåller ett enda paket. I allmänhet är delning möjlig endast när den relevanta strukturen är identisk, vilket innebär att de har samma storlek, samma (convolutional-geometri och så vidare.

## <a name="examples-of-net-usage"></a>Exempel på net-användning

Det här avsnittet innehåller några exempel på hur du kan använda net # för att lägga till dolda lager, definiera hur dolda lager ska interagera med andra lager och skapa (convolutional-nätverk.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Definiera ett enkelt anpassat neurala-nätverk: "Hello World" exempel

Det här enkla exemplet visar hur du skapar en neurala-nätverks modell som har ett enda dolt lager.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

Exemplet illustrerar några grundläggande kommandon på följande sätt:

+ Den första raden definierar Indatakällan (Named `Data` ). När du använder  `auto` nyckelordet, innehåller neurala-nätverket automatiskt alla funktions kolumner i inmatade exempel.
+ Den andra raden skapar det dolda lagret. Namnet `H` tilldelas det dolda lagret, som har 200 noder. Det här lagret är fullständigt anslutet till indataströmmen.
+ Den tredje raden definierar utmatnings skiktet (Named `Out` ), som innehåller 10 utdata-noder. Om neurala-nätverket används för klassificering, finns det en utmatnings nod per klass. Nyckelordet **sigmoid** anger att output-funktionen används på output-lagret.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definiera flera dolda lager: exempel på en dator vision

Följande exempel visar hur du definierar ett något mer komplext neurala-nätverk med flera anpassade dolda lager.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

Det här exemplet illustrerar flera funktioner i språket neurala Networks Specification:

+ Strukturen har två ingångs lager `Pixels` och `MetaData` .
+ `Pixels`Lagret är ett käll skikt för två anslutnings paket, med mål lager `ByRow` och `ByCol` .
+ Lagren `Gather` och `Result` är mål lager i flera anslutnings paket.
+ Utmatnings lagret, `Result` är ett mål lager i två anslutnings paket, ett med det dolda lagret på den andra nivån `Gather` som ett mål lager och det andra med inmatnings lagret `MetaData` som ett mål lager.
+ De dolda lagren `ByRow` och `ByCol` anger filtrerad anslutning med hjälp av predikat-uttryck. Mer exakt är noden i `ByRow` [x, y] kopplad till noderna i `Pixels` som har den första index koordinaten som är lika med nodens första koordinat, x. På samma sätt är noden i `ByCol` [x, y] kopplad till noderna i `Pixels` som har den andra index koordinaten i en av nodens andra koordinat, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Definiera ett (convolutional-nätverk för klassificering av multiklasser: tal igenkännings exempel

Definitionen av följande nätverk är utformad för att identifiera siffror, och det illustrerar vissa avancerade tekniker för att anpassa ett neurala-nätverk.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ Strukturen har ett enda indatamängds lager `Image` .
+ Nyckelordet `convolve` anger att lagren med namnet `Conv1` och `Conv2` är (convolutional lager. Var och en av dessa lager deklarationer följs av en lista med convolution-attribut.
+ Nätet har ett tredje dolt lager, `Hid3` som är fullt anslutet till det andra dolda lagret `Conv2` .
+ Utmatnings lagret, `Digit` , är bara anslutet till det tredje dolda lagret, `Hid3` . Nyckelordet `all` anger att output-lagret är fullständigt anslutet till `Hid3` .
+ Aritet för convolution är tre: längden på tupelarna `InputShape` , `KernelShape` , `Stride` och `Sharing` .
+ Antalet vikter per kernel är `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26` . Eller `26 * 50 = 1300` .
+ Du kan beräkna noderna i varje dolt lager enligt följande:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Det totala antalet noder kan beräknas med hjälp av skiktets deklarerade dimensionalitet, [50, 5, 5], enligt följande: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Eftersom `Sharing[d]` bara är falskt för `d == 0` är antalet kärnor `MapCount * NodeCount\[0] = 10 * 5 = 50` .

## <a name="acknowledgements"></a>Erkännanden

NET #-språket för anpassning av arkitekturen i neurala-nätverk utvecklades på Microsoft av Shon Katzenberger (arkitekt, Machine Learning) och Alexey Kamenev (program varu tekniker, Microsoft Research). Den används internt för maskin inlärnings projekt och program som sträcker sig från bild identifiering till text analys. Mer information finns i [neurala Garns i Azure Machine Learning Studio – introduktion till net #](/archive/blogs/machinelearning/neural-nets-in-azure-ml-introduction-to-net)