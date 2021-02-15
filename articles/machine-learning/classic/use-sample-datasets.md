---
title: 'ML Studio (klassisk): Använd exempel data uppsättningarna – Azure'
description: Beskrivningar av de data uppsättningar som används i exempel modeller som ingår i Machine Learning Studio (klassisk). Du kan använda dessa exempel data uppsättningar för dina experiment.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: sample
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 8a848f6589902c2eabf5b4e14a4d000dc4ed081e
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518444"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Använd exempel data uppsättningarna i Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


[top]: #machine-learning-sample-datasets

När du skapar en ny arbets yta i Azure Machine Learning Studio (klassisk) ingår ett antal exempel data uppsättningar och experiment som standard. Många av dessa exempel data uppsättningar används av exempel modellerna i [Azure AI Gallery](https://gallery.azure.ai/). Andra inkluderas som exempel på olika typer av data som vanligt vis används i Machine Learning.

Några av dessa data uppsättningar är tillgängliga i Azure Blob Storage. I följande tabell finns en direkt länk för de här data uppsättningarna. Du kan använda dessa data uppsättningar i experimenten med hjälp av modulen [Importera data][import-data] .

Resten av dessa exempel data uppsättningar är tillgängliga i din arbets yta under **sparade data uppsättningar**. Du hittar det här i modulen modul till vänster om arbets ytan experiment i Machine Learning Studio (klassisk).
Du kan använda någon av dessa data uppsättningar i ditt eget experiment genom att dra den till arbets ytan för experimentet.

## <a name="datasets"></a>Datauppsättningar

<table>

<tr>
  <th>Namn på datauppsättning</th>
  <th>Beskrivning av data uppsättning</th>
</tr>

<tr>
  <td>Data uppsättning för binära sammanräknings inkomst</td>
  <td>
En delmängd av databasen för 1994-inventering, med hjälp av att arbeta vuxna under 16 med ett justerat inkomst index på > 100.
<p></p>
<b>Användning:</b> Klassificera personer som använder demografiska till att förutsäga om en person får över 50 000 per år.
<p></p>
<b>Relaterad forskning:</b> Kohavi, R., Becker, B., (1996). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap </td>
</tr>

<tr>
  <td>Data uppsättning för flyg plats koder</td>
  <td>
Amerikanska flyg plats koder.
<p></p>
Den här data uppsättningen innehåller en rad för varje amerikansk flyg plats, vilket ger flyg platsens ID-nummer och namn tillsammans med platsens ort och delstat.
  </td>
</tr>

<tr>
  <td>Bil pris data (RAW)</td>
  <td>
Information om mobila enheter med märke och modeller, inklusive pris, funktioner, till exempel antalet cylindrar och MPG, samt en försäkrings risk poäng.
<p></p>
Risk poängen är inlednings vis kopplad till automatiskt pris. Den justeras sedan för faktisk risk i en process som är känd för aktuarier som symbol. Värdet + 3 anger att det automatiska är riskabelt och att värdet-3 förmodligen är säkert.
<p></p>
<b>Användning:</b> Förutsäg risk poängen med hjälp av funktioner, regression eller multivarierad klassificering. 
<p></p>
<b>Relaterad forskning:</b> Schlimmer, J.C. (1987). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap </td>
</tr>

<tr>
  <td>Bil-datauppsättning för cykel hyra</td>
  <td>
DataStore Bike hyr-datauppsättning som baseras på verkliga data från kapital Bikeshares företag som underhåller ett cykel uthyrnings nätverk i Washington DC.
<p></p>
Data uppsättningen har en rad för varje timme på varje dag i 2011 och 2012, för totalt 17 379 rader. Intervallet för cykel uthyrning per timme är mellan 1 och 977.

  </td>
</tr>

<tr>
  <td>Bild av Bill Gates RGB</td>
  <td>
Offentligt tillgänglig bildfil konverterad till CSV-data.
<p></p>
Koden för att konvertera avbildningen finns på informations sidan om <strong>färg-kvantifieringsfel med hjälp av</strong> sidan för kluster modell information.
  </td>
</tr>

<tr>
  <td>Blod donations data</td>
  <td>
En delmängd av data från blod donator databasen för blod transfusions tjänst Center för Hsin-Chu City, Taiwan.
<p></p>
Donator data inkluderar de månader sedan senaste donationen) och frekvens, eller det totala antalet donationer, tid sedan senaste donation och mängden blod som donerats.
<p></p>
<b>Användning:</b> Målet är att förutsäga över klassificeringen om givaren donerade blod i mars 2007, där 1 anger en donator under mål perioden och 0 en icke-donator. 
<p></p>
<b>Relaterad forskning:</b> Yeh, I.C., (2008). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap
<p></p>
Yeh, I-Cheng, Yang, King-Jang och, tagg-mobilnät, "kunskaps identifiering på RFM-modell med Bernoulli Sequence", expert system med program, 2008, <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Bröst cancer data</td>
  <td>
En av tre cancer data uppsättningar som tillhandahålls av Oncology-Institutet som visas ofta i Machine Learning-dokumentationen. Kombinerar diagnostikinformation med funktioner från laboratorie analys av cirka 300 vävnads exempel.
<p></p>
<b>Användning:</b> Klassificera typ av cancer, baserat på 9 attribut, varav vissa är linjära och vissa är kategoriska. 
<p></p>
<b>Relaterad forskning:</b> Wohlberg, W.H., gata, W.N., & Mangasarian, O.L. (1995). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap </td>
</tr>

<tr>
  <td>Bröst cancer funktioner <td>
Data uppsättningen innehåller information för 102K-misstänkta regioner (kandidater) av X-Ray-bilder, var och en beskrivs av 117-funktionerna. Funktionerna är tillverkarspecifika och deras innebörd visas inte av data uppsättnings skapare (Siemens-hälsovården). 
  </td>
</tr>

<tr>
  <td>Bröst cancer information</td>
  <td>
Data uppsättningen innehåller ytterligare information för varje misstänkt region med X-ray-avbildning. I varje exempel finns information (t. ex. etikett, patient-ID, koordinater för korrigering i förhållande till hela bilden) om motsvarande rad nummer i data uppsättningen bröst cancer-funktioner. Varje patient har ett antal exempel. För patienter som har en cancer är vissa exempel positiva och några är negativa. För patienter som inte har någon cancer är alla exempel negativa. Data uppsättningen har 102K-exempel. Data uppsättningen är prioriterad, 0,6% av punkterna är positiv, resten är negativa. Data uppsättningen gjordes tillgänglig av Siemens-hälsovården.
  </td>
</tr>

<tr>
  <td>CRM begär-etiketter delade</td>
  <td>
Etiketter från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. labels</a>).
  </td>
</tr>

<tr>
  <td>CRM omsättnings etiketter delade</td>
  <td>
Etiketter från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. labels</a>).
  </td>
</tr>

<tr>
  <td>CRM-datauppsättning delad</td>
  <td>
Dessa data kommer från KDD-bägaren 2009 kund Relations förutsägelse kontroll (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train.data.zip</a>).
<p></p>
Data uppsättningen innehåller 50 000-kunder från det franska telekom-företaget orange. Varje kund har 230 anonymiserats-funktioner, men 190 av som är numeriska och 40 är kategoriska. Funktionerna är mycket glesa.
  </td>
</tr>

<tr>
  <td>CRM försäljar etiketter delade</td>
  <td>
Etiketter från KDD-bägaren 2009-förfrågan om kund Relations förutsägelse (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. labels</a>).
  </td>
</tr>

<tr>
  <td>Energy-Efficiency Regressions data</td>
  <td>
En samling simulerade energi profiler, baserat på 12 olika byggnads former. Byggnaderna skiljs åt av åtta funktioner. Detta omfattar glas rutor, distribution av glas områden och orientering.
<p></p>
<b>Användning:</b> Använd antingen regression eller klassificering för att förutsäga energi effektivitets betyget baserat på ett av två faktiska värde svar. För klassificering i flera klasser, är processvariabeln Response till närmaste heltal. 
<p></p>
<b>Relaterad forskning:</b> Xifara, A. & Tsanas, A. (2012). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap </td>
</tr>

<tr>
  <td>Data för flyg fördröjning</td>
  <td>
Prestanda data för passagerar flygning i tid som tagits från TranStats-datainsamlingen för USA: s transport (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">i tid</a>).
<p></p>
Data uppsättningen täcker tids perioden April-October 2013. Innan du överför till Azure Machine Learning Studio (klassisk) bearbetades data uppsättningen på följande sätt:
<ul>
  <li>Data uppsättningen har filtrerats för att endast avse 70 vanligaste-flyg platserna i kontinental USA</li>
  <li>Annullerade flygningar har märkts som fördröjda med mer än 15 minuter</li>
  <li>Avlästa flygningar filtrerades bort</li>
  <li>Följande kolumner valdes: Year, Month, DayofMonth, DayOfWeek, Carrier, OriginAirportID, DestAirportID, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, avbruten</li>
</ul>
</td>
</tr>

<tr>
  <td>Prestanda för Flight i tid (RAW)</td>
  <td>
Register över flyg Plans införslar och avgångser i USA från oktober 2011.
<p></p>
<b>Användning:</b> Förutsäg fördröjningar i flygningen. 
<p></p>
<b>Relaterad forskning:</b> Från amerikanska <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a> avd.
  </td>
</tr>

<tr>
  <td>Skog utlöser data</td>
  <td>
Innehåller väder data, till exempel temperatur-och fuktighets index och vridnings hastighet. Data hämtas från ett arean av nordöstra Portugal, kombinerat med poster i skogs utlöses.
<p></p>
<b>Användning:</b> Detta är en svår Regressions uppgift där målet är att förutsäga den brända arean i skogs bränder. 
<p></p>
<b>Relaterad forskning:</b> Cortez, P., & Morais, A. (2008). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap
<p></p>
[Cortez och Morais, 2007] P. Cortez och en. Morais. En data utvinnings metod för att förutsäga skogen utlöses med hjälp av meteorologiska data. I J. Neves, M. F. Santos och J. Machado EDS., nya trender i artificiell intelligens, åtgärder hos 13 EPIA 2007-portugisiska-konferensen om artificiell intelligens, december, Guimarães, Portugal, s. 512-523, 2007. APPIA, ISBN-13 978-989-95618-0-9. Tillgängligt på: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a> .
  </td>
</tr>

<tr>
  <td>German-datauppsättning för tyska kredit kort</td>
  <td>
Den Statlog (tyska kredit kort) data uppsättningen (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + tyska + kredit + data</a>), med hjälp av den tyska. data filen.
<p></p>
Data uppsättningen klassificerar personer, som beskrivs av en uppsättning attribut, som små eller stora kredit risker. Varje exempel representerar en person. Det finns 20 funktioner, både numeriska och kategoriska och en binär etikett (kredit risk värde). Stora kredit risk poster har etikett = 2, låg kredit risk poster har etikett = 1. Kostnaden för att inte klassificera ett litet risk exempel som hög är 1, medan kostnaden för att klassificera ett högt risk exempel som låg är 5.
  </td>
</tr>

<tr>
  <td>Film titlar för IMDB</td>
  <td>
Data uppsättningen innehåller information om filmer som har klassificerats i Twitter-tweets: IMDB Movie ID, Movie Name, genre och Product Year. Det finns 17K filmer i data uppsättningen. Data uppsättningen introducerades i papperet "S. Dooms, T. ex. de Pessemier och L. Martens. MovieTweetings: en film betygs uppsättning som samlats in från Twitter. Workshop om gemensamt skapade och mänsklig beräkning för rekommenderade system, CrowdRec på RecSys 2013. "
  </td>
</tr>

<tr>
  <td>Iris, två klass data</td>
  <td>
Detta är kanske den bästa kända databasen som finns i dokumentationen för mönster igenkänning. Data uppsättningen är relativt liten, som innehåller 50 exempel på olika sorters mått från tre Iris.
<p></p>
<b>Användning:</b> Förutsäg Iris-typen från mätningarna.  
<p></p>
<b>Relaterad forskning:</b> Fisher, R.A. (1988). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap </td>
</tr>

<tr>
  <td>Film tweets</td>
  <td>
Data uppsättningen är en utökad version av data uppsättningen för film tweeter. Data uppsättningen har 170K klassificeringar för filmer som extraheras från välstrukturerade tweets på Twitter. Varje instans representerar en tweet och är en tupel: användar-ID, IMDB-film-ID, klassificering, tidsstämpel, antal favoriter för den här tweeten och antalet retweetar för den här tweeten. Data uppsättningen gjordes tillgänglig av A. S. Dooms, B. Loni och D. Tikk för rekommenderade system Challenge 2014.
  </td>
</tr>

<tr>
  <td>MPG-data för olika mobila enheter</td>
  <td>
Den här data uppsättningen är en något modifierad version av den data uppsättning som tillhandahålls av StatLib-biblioteket för Carnegie Mellon University. Data uppsättningen användes i den 1983 amerikanska amerikanska statistiska kopplings hand boken.
<p></p>
Data visar bränsle förbrukning för olika bilenheter i km per liter. Den innehåller också information om t. ex. antalet cylindrar, motor förskjutning, häst krafter, total vikt och acceleration.
<p></p>
<b>Användning:</b> Förutsäga bränsle ekonomin baserat på tre multivärdeiga diskreta attribut och fem kontinuerliga attribut. 
<p></p>
<b>Relaterad forskning:</b> StatLib, Carnegie Mellon University, (1993). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap </td>
</tr>

<tr>
  <td>Pima Indians diabetes Binary klassificering data uppsättning</td>
  <td>
En delmängd av data från National Institute of diabetes och databasen för sammandrag av eller njure sjukdomar. Data uppsättningen filtrerades för att fokusera på kvinnlig patienter från Pima indiska kultur arvet. Data innehåller medicinska data, till exempel glukos och insulin nivåer, samt livsstils faktorer.
<p></p>
<b>Användning:</b> Förutsäg om ämnet har diabetes (binära klassificering). 
<p></p>
<b>Relaterad forskning:</b> Sigillito, V. (1990). Machine Learning-lagringsplats <a href="https://archive.ics.uci.edu/ml"> https://archive.ics.uci.edu/ml för</a>. Irvine, CA: University of Kalifornien, skol information och dator vetenskap </td>
</tr>

<tr>
  <td>Restaurang kund information</td>
  <td>
En uppsättning metadata om kunder, inklusive demografiska och preferenser.
<p></p>
<b>Användning:</b> Använd den här data uppsättningen, i kombination med de andra två restaurang data uppsättningarna, för att träna och testa ett rekommenderat system. 
<p></p>
<b>Relaterad forskning:</b> Bache, K. och Lichman, M. (2013). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap.
  </td>
</tr>

<tr>
  <td>Restaurang funktions data</td>
  <td>
En uppsättning metadata om restauranger och deras funktioner, till exempel livsmedels typ, matsal och plats.
<p></p>
<b>Användning:</b> Använd den här data uppsättningen, i kombination med de andra två restaurang data uppsättningarna, för att träna och testa ett rekommenderat system. 
<p></p>
<b>Relaterad forskning:</b> Bache, K. och Lichman, M. (2013). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap.
  </td>
</tr>

<tr>
  <td>Restaurang värderingar</td>
  <td>
Innehåller omdömen från användare till restauranger på en skala från 0 till 2.
<p></p>
<b>Användning:</b> Använd den här data uppsättningen, i kombination med de andra två restaurang data uppsättningarna, för att träna och testa ett rekommenderat system. 
<p></p>
<b>Relaterad forskning:</b> Bache, K. och Lichman, M. (2013). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap.
  </td>
</tr>

<tr>
  <td>Anlöpning för flera klasser i stål</td>
  <td>
Den här data uppsättningen innehåller en serie poster från stål anlöpning-tester. Den innehåller de fysiska attributen (bredd, tjocklek, typ (spole, ark osv.) för de resulterande stål typerna.
<p></p>
<b>Användning:</b> Förutsäga alla två numeriska klass-attribut; hårdhet eller styrka. Du kan också analysera korrelationer mellan attribut.
<p></p>
Stål betyg följer en angiven standard som definieras av SAE och andra organisationer. Du letar efter en speciell klass (klass variabel) och vill förstå de värden som behövs. 
<p></p>
<b>Relaterad forskning:</b> Sterling, D. & Buntine, W. (NA). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information och dator vetenskap
<p></p>
Du hittar en användbar guide till stål betyg här: <a href="https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf">https://www.steamforum.com/pictures/Outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Information om Telescope</td>
  <td>
Registrera hög Energis törningar med hög strömförbrukning och bakgrunds brus, som båda simuleras med en Monte Carlo-process.
<p></p>
Syftet med simuleringen var att förbättra noggrannheten för markbaserade atmosfäriska Cherenkov gamma-scope. Detta görs med hjälp av statistiska metoder för att skilja mellan den önskade signalen (Cherenkov-strålnings dusch) och bakgrunds brus (hadronic duschar som initierats av Cosmic strålar i den övre atmosfärn).
<p></p>
Data har bearbetats för att skapa ett överdrivet kluster med den långa axeln riktas mot kamera centret. Egenskaperna för den här ellipsen (kallas ofta Hill)-parametrar är bland de avbildnings parametrar som kan användas för diskriminering.
<p></p>
<b>Användning:</b> Förutse om bilden av ett barn visar signal-eller bakgrunds brus.
<p></p>
<b>Anteckningar:</b> Enkel klassificerings noggrannhet är inte meningsfullt för dessa data, eftersom en bakgrunds händelse klassificeras som signal än om man klassificerar en signal händelse som bakgrund. För jämförelse av olika klassificerare ska ROC-grafen användas. Sannolikheten för att acceptera en bakgrunds händelse som signal måste vara under något av följande tröskelvärden: 0,01, 0,02, 0,05, 0,1 eller 0,2.
<p></p>
Observera också att antalet bakgrunds händelser (h, för hadronic duschar) är uppskattat. I verkliga mätningar representerar h-eller brus klassen majoriteten av händelserna. 
<p></p>
<b>Relaterad forskning:</b> Bock, R.K. (1995). Machine Learning-lagringsplats för <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a> . Irvine, CA: University of Kalifornien, skol information </td>
</tr>

<tr>
  <td>Data uppsättning väder</td>
  <td>
Varje timmes markbaserade väderleks observationer från NOAA (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">sammanfogade data från 201304 till 201310</a>).
<p></p>
Väder data täcker observationer som gjorts från flyg platsens väder stationer, som omfattar tids perioden April-October 2013. Innan du överför till Azure Machine Learning Studio (klassisk) bearbetades data uppsättningen på följande sätt:
<ul>
  <li>Väder Stations-ID: n mappades till motsvarande flyg plats-ID</li>
  <li>Väder stationer som inte är kopplade till 70 vanligaste-flyg platserna filtrerades bort</li>
  <li>Datum kolumnen delades upp i separata kolumner för år, månad och dag</li>
  <li>Följande kolumner valdes: AirportID, Year, Month, Day, Time, TimeZone, SkyCondition, visibility, WeatherType, DryBulbFarenheit, DryBulbCelsius, WetBulbFarenheit, WetBulbCelsius, DewPointFarenheit, DewPointCelsius, RelativeHumidity, WindSpeed, WindDirection, ValueForWindCharacter, StationPressure, PressureTendency, PressureChange, SeaLevelPressure, RecordType, HourlyPrecip, altimeter,,,,</li>
</ul>
  </td>
</tr>

<tr>
  <td>Wikipedia SP 500-datauppsättning</td>
  <td>
Data härleds från Wikipedia ( <a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a> ) baserat på artiklar i varje S&P 500-företag, lagrade som XML-data.
<p></p>
Innan du överför till Azure Machine Learning Studio (klassisk) bearbetades data uppsättningen på följande sätt:
<ul>
  <li>Extrahera text innehåll för varje enskilt företag</li>
  <li>Ta bort wiki-formatering</li>
  <li>Ta bort icke-alfanumeriska tecken</li>
  <li>Konvertera all text till gemener</li>
  <li>Kända företags kategorier har lagts till</li>
</ul>
<p></p>
Observera att för vissa företag gick det inte att hitta en artikel, så antalet poster är mindre än 500.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td>
Data uppsättningen innehåller kund information och uppgifter om deras svar på en direkt kopplings kampanj. Varje rad representerar en kund. Data uppsättningen innehåller nio funktioner för användar demografiskhet och tidigare beteende och tre etikett kolumner (besök, konvertering och spendera).  Besök är en binär kolumn som visar att en kund besökt efter marknadsförings kampanjen. Omvandlingen innebär att en kund har köpt något. Spenderat är det belopp som spenderades.  Data uppsättningen har gjorts tillgänglig av Kevin Hillstrom för MineThatData e-post analys och data utvinnings utmaning.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td>
Funktioner i test exempel i RCV1-v2 Reuters News-datauppsättningen. Data uppsättningen har 781K nyhets artiklar tillsammans med deras ID (första kolumnen i data uppsättningen). Varje artikel är token, stopworded och påfylld. Data uppsättningen gjordes tillgänglig av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td>
Funktioner i övnings exempel i RCV1-v2 Reuters News-datauppsättningen. Data uppsättningen har 23K nyhets artiklar tillsammans med deras ID (första kolumnen i data uppsättningen). Varje artikel är token, stopworded och påfylld. Data uppsättningen gjordes tillgänglig av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td>
Data uppsättning från KDD-bägaren 1999 kunskaps identifiering och data utvinnings verktyg tävling (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99.html</a>).
<p></p>
Data uppsättningen har laddats ned och lagrats i Azure Blob Storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a>) och omfattar både utbildning och testning av data uppsättningar. Data uppsättningen för träning har ungefär 126K rader och 43 kolumner, inklusive etiketter. Tre kolumner är en del av etikett informationen och 40 kolumner, som består av numeriska och sträng/kategoriska funktioner, är tillgängliga för att träna modellen. Test data har ungefär 22.5 K test-exempel med samma 43-kolumner som i tränings data.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td>
Ämnes tilldelningar för nyhets artiklar i RCV1-v2 Reuters News-datauppsättningen. En nyhets artikel kan tilldelas till flera ämnen. Formatet på varje rad är " &lt; ämnes namn &gt; &lt; dokument-ID &gt; 1". Data uppsättningen innehåller ämnes tilldelningar 2.6 M. Data uppsättningen gjordes tillgänglig av David. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td>
Dessa data kommer från KDD-bägaren 2010 student prestanda utvärderings utmaning (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">student Performance Evaluation</a>). De data som används är Algebra_2008_2009 inlärnings uppsättning (Stamper, J., Niculescu-Mizil, A., Ritter, S., Gordon, G.J., & Koedinger, K.R. (2010). Algebra I 2008-2009. Utmanings data uppsättning från KDD-bägare 2010 utbildnings data utvinnings utmaning. Hitta den på <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">downloads.jsp</a>.
<p></p>
Data uppsättningen har laddats ned och lagrats i Azure Blob Storage (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a>) och innehåller loggfiler från ett student Utlärnings-system. De tillhandahållna funktionerna inkluderar problem-ID och dess kort beskrivning, Student-ID, tidsstämpel och hur många försök student som gjorts innan problemet löses på rätt sätt. Den ursprungliga data uppsättningen innehåller 8,9 M-poster; den här data uppsättningen har inaktiverats för de första 100 000 raderna. Data uppsättningen har 23 tabbavgränsade kolumner av olika typer: numeric, kategoriska och timestamp.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kickstart dina experiment med exempel](sample-experiments.md)

<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data