---
title: 'K-innebär klustring: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen K-betyder kluster i Azure Machine Learning för att träna kluster modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 08/04/2020
ms.openlocfilehash: 7e0b61c1ca6ae30044e4c9d4705bdce01eac1942
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93323697"
---
# <a name="module-k-means-clustering"></a>Modul: K-innebär klustring

Den här artikeln beskriver hur du använder modulen *k-: kluster* modul i Azure Machine Learning designer för att skapa en modell modell med en modell som inte är tränad k-: 
 
K-betyder en av de enklaste och bästa kända, ej *övervakade* inlärnings algoritmerna. Du kan använda algoritmen för en mängd olika Machine Learning-uppgifter, till exempel: 

* [Identifiera onormala data](/archive/msdn-magazine/2013/february/data-clustering-detecting-abnormal-data-using-k-means-clustering).
* Klustring av text dokument.
* Analysera data uppsättningar innan du använder andra klassificerings-eller Regressions metoder. 

För att skapa en kluster modell:

* Lägg till den här modulen i din pipeline.
* Anslut en data uppsättning.
* Ange parametrar, till exempel antalet kluster som du förväntar dig, det avstånds mått som ska användas för att skapa klustren och så vidare. 
  
När du har konfigurerat modulens egenskaper ansluter du den avtränade modellen till [träna kluster modellen](train-clustering-model.md). Eftersom den N:te algoritmen är en oövervakad inlärnings metod är en etikett kolumn valfri. 

+ Om dina data innehåller en etikett kan du använda etikett värden för att välja mellan kluster och optimera modellen. 

+ Om dina data saknar etikett skapar algoritmen kluster som representerar möjliga kategorier, baserat på enbart data.  

##  <a name="understand-k-means-clustering"></a>Förstå K-innebär klustring
 
I allmänhet använder kluster iterativa tekniker för att gruppera ärenden i en data uppsättning i kluster som har liknande egenskaper. Dessa grupperingar är användbara för att utforska data, identifiera avvikelser i data och slutligen för att göra förutsägelser. Kluster modeller kan också hjälpa dig att identifiera relationer i en data uppsättning som du kanske inte logiskt kan härleda genom att söka eller göra enkla observationer. Av dessa orsaker används ofta klustring i de tidiga faserna av Machine Learning-aktiviteter, för att utforska data och upptäcka oväntade korrelationer.  
  
 När du konfigurerar en kluster modell med hjälp av metoden K-Methods måste du ange ett Target Number *K* som anger antalet *centroids* som du vill ha i modellen. Centroid är en punkt som är representativ för varje kluster. Algoritmen K-betyder tilldelar varje inkommande data punkt till ett av klustren genom att minimera kvadratsumman i klustret. 
 
När den bearbetar tränings data börjar algoritmen för K-medelvärdet med en inledande uppsättning slumpmässigt valda centroids. Centroids fungerar som start punkter för klustren och använder sedan Lloyd ' s algorithm för att förfina deras platser iterativt. Den N:te algoritmen stoppar skapandet och raffineringen av kluster när den uppfyller ett eller flera av följande villkor:  
  
-   Centroids stabiliseras, vilket innebär att kluster tilldelningarna för enskilda punkter inte längre ändras och algoritmen har konvergerat till en lösning.  
  
-   Algoritmen slutförde körningen av det angivna antalet iterationer.  
  
 När du har slutfört tränings fasen använder du modulen [tilldela data till kluster](assign-data-to-clusters.md) för att tilldela nya ärenden till ett av de kluster som du har hittat med hjälp av algoritmen K-betyder. Du utför kluster tilldelning genom att beräkna avståndet mellan det nya ärendet och centroid för varje kluster. Varje nytt ärende tilldelas till klustret med närmaste centroid.  

## <a name="configure-the-k-means-clustering-module"></a>Konfigurera modulen K-betyder kluster
  
1.  Lägg till modulen **K-: klustring** i din pipeline.  
  
2.  Om du vill ange hur modellen ska tränas väljer du alternativet **skapa utbildare läge** .  
  
    -   **Enskild parameter**: om du känner till de exakta parametrar som du vill använda i kluster modellen, kan du ange en viss uppsättning värden som argument.  
  
3.  För **antal centroids** anger du det antal kluster som du vill att algoritmen ska börja med.  
  
     Modellen är inte garanterat att producera exakt det här antalet kluster. Algoritmen börjar med det här antalet data punkter och upprepas för att hitta den optimala konfigurationen. Du kan referera till [käll koden för sklearn](https://github.com/scikit-learn/scikit-learn/blob/fd237278e/sklearn/cluster/_kmeans.py#L1069).
  
4.  Egenskaps **initieringen** används för att ange algoritmen som används för att definiera den inledande kluster konfigurationen.  
  
    -   **Första N**: vissa inledande antal data punkter väljs från data uppsättningen och används som första metod. 
    
         Den här metoden kallas även för *förfalsknings metoden*.  
  
    -   **Slumpmässig**: algoritmen placerar en data punkt slumpmässigt i ett kluster och sedan beräknar det inledande medelvärdet som centroid för klustrets slumpvis tilldelade punkter. 

         Den här metoden kallas även för den *slumpmässiga partitionsfunktionen* .  
  
    -   **K-betyder + +**: det här är standard metoden för att initiera kluster.  
  
         **K-betyder + +** -algoritmen föreslogs i 2007 av David Arthur och Sergei Vassilvitskii för att undvika dåligt klustring av standardalgoritmen för K-=. **K-betyder + +** förbättrar standard K-metoden genom att använda en annan metod för att välja de första kluster centret.  
  
    
5.  För **slumpmässigt antal frön** kan du ange ett värde som ska användas som start för kluster initieringen. Det här värdet kan ha en betydande inverkan på kluster valet.  
  
6.  För **mått** väljer du den funktion som ska användas för att mäta avståndet mellan kluster vektorer eller mellan nya data punkter och den slumpmässigt valda centroid. Azure Machine Learning stöder följande kluster avstånds mått:  
  
    -   **Euclidean**: Euclidean-avståndet används ofta som ett mått på kluster spridning för K-innebär klustring. Detta mått är att föredra eftersom det minimerar avståndet mellan punkter och centroids.
  
7.  För **iterationer** anger du hur många gånger algoritmen ska iterera över tränings data innan den slutför valet av centroids.  
  
     Du kan justera den här parametern om du vill utjämna precisionen mot inlärnings tiden.  
  
8.  För **tilldela etikett läge** väljer du ett alternativ som anger hur en etikett kolumn, om den finns i data uppsättningen, ska hanteras.  
  
     Eftersom K-betyder klustring är en oövervakad Machine Learning-metod, är etiketter valfria. Men om din data uppsättning redan har en etikett kolumn kan du använda dessa värden för att vägleda valet av kluster, eller så kan du ange att värdena ska ignoreras.  
  
    -   **Ignorera etikett kolumn**: värdena i kolumnen Label ignoreras och används inte för att skapa modellen.
  
    -   **Fyll i saknade värden**: etikett kolumn värden används som funktioner för att bygga klustren. Om en etikett saknas i några rader tilldelas värdet med hjälp av andra funktioner.  
  
    -   **Skriv över från närmaste till Center**: etikett kolumnens värden ersätts med förväntade etikett värden med hjälp av etiketten för den punkt som är närmast den aktuella centroid.  

8.  Välj alternativet **normaliserings funktioner** om du vill normalisera funktioner innan du tränar.
  
     Om du använder normalisering före träning normaliseras data punkterna till `[0,1]` av MinMaxNormalizer.

10. Träna modellen.  
  
    -   Om du ställer in **skapa utbildare** för en **parameter** lägger du till en taggad data uppsättning och tränar modellen med hjälp av modulen [träna kluster modell](train-clustering-model.md) .  
  
## <a name="results"></a>Resultat

När du har konfigurerat och tränat modellen har du en modell som du kan använda för att generera poäng. Det finns dock flera sätt att träna modellen på och flera sätt att visa och använda resultaten: 

### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Avbilda en ögonblicks bild av modellen i din arbets yta

Om du använde modulen [träna klustring modell](train-clustering-model.md) :

1. Välj modulen **träna klustring modell** och öppna den högra panelen.

2. Välj fliken **utdata** . Välj ikonen **registrera data uppsättning** för att spara en kopia av den tränade modellen.

Den sparade modellen representerar tränings data vid den tidpunkt då du sparade modellen. Om du senare uppdaterar de tränings data som används i pipelinen, uppdateras inte den sparade modellen. 

### <a name="see-the-clustering-result-dataset"></a>Se data uppsättningen för kluster resultatet 

Om du använde modulen [träna klustring modell](train-clustering-model.md) :

1. Högerklicka på modulen **träna klustring modell** .

2. Välj **visualisera**.

### <a name="tips-for-generating-the-best-clustering-model"></a>Tips för att skapa den bästa kluster modellen  

Det är känt att *initierings* processen som används under klustringen kan påverka modellen betydligt. Seeding innebär den inledande placeringen av punkter i potentiella centroids.
 
Om data uppsättningen till exempel innehåller många avvikande värden och en avvikare har valts för att dirigera klustren, så skulle inga andra data punkter bli bra med klustret, och klustret kan vara en singleton. Det kan ha en enda punkt.  
  
Du kan undvika det här problemet på ett par olika sätt:  
  
-   Ändra antalet centroids och försök med flera värden för dirigering.  
  
-   Skapa flera modeller, varierande mått eller iterera mer.  
  
I allmänhet, med kluster modeller, är det möjligt att alla angivna konfigurationer resulterar i en lokalt optimerad uppsättning kluster. Med andra ord motsvarar den uppsättning kluster som returneras av modellen bara de aktuella data punkterna och kan inte generaliseras till andra data. Om du använder en annan inledande konfiguration kan metoden K-betyder att hitta en annan, överlägsen och konfiguration. 

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.