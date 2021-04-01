---
title: Finjustera hyperparametrar för modell
titleSuffix: Azure Machine Learning
description: Använd modulen för att finjustera modells parametrar i designern för att utföra en parameter rensning för att justera Hyper-Parameters.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: 2bbf75ba5de4ad20e11261bdcfd1204b1a0b0766
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420639"
---
# <a name="tune-model-hyperparameters"></a>Finjustera hyperparametrar för modell

I den här artikeln beskrivs hur du använder modulen för att finjustera modells parametrar i Azure Machine Learning designer. Målet är att fastställa de optimala egenskaperna för en maskin inlärnings modell. Modulen bygger och testar flera modeller genom att använda olika kombinationer av inställningar. Den jämför mått över alla modeller för att hämta kombinationer av inställningar. 

Villkors *parametern* och den andra *parametern* kan vara förvirrande. Modellens *parametrar* är det du anger i den högra rutan i modulen. I princip utför den här modulen en *parameter rensning* över de angivna parameter inställningarna. Den lär sig en optimal uppsättning av _grundparametrar_, som kan vara olika för varje särskilt besluts träd, data uppsättning eller Regressions metod. Processen för att hitta den optimala konfigurationen kallas ibland *justering*. 

Modulen stöder följande metod för att hitta de optimala inställningarna för en modell: *integrerat tåg och finjustera.* I den här metoden konfigurerar du en uppsättning parametrar som ska användas. Sedan kan du låta modulen iterera över flera kombinationer. Modulen mäter precision tills den hittar en "bästa" modell. Med de flesta lärare-moduler kan du välja vilka parametrar som ska ändras under inlärnings processen och vilka som ska förbli fasta.

Beroende på hur länge du vill att justerings processen ska köras, kan du välja att prova alla kombinationer på ett omfattande sätt. Eller så kan du förkorta processen genom att skapa ett rutnät med parameter kombinationer och testa en slumpmässig del uppsättning av parameter rutnätet.

Den här metoden skapar en utbildad modell som du kan spara för åter användning.  

> [!TIP] 
> Du kan utföra en relaterad uppgift. Innan du börjar justera ska du använda val av funktioner för att fastställa vilka kolumner eller variabler som har det högsta värdet information.

## <a name="how-to-configure-tune-model-hyperparameters"></a>Så här konfigurerar du justerings modellens egenskaper  

Inlärning av de optimala egenskaperna för en maskin inlärnings modell kräver stor användning av pipeliner.

### <a name="train-a-model-by-using-a-parameter-sweep"></a>Träna en modell genom att använda en parameter Svep  

I det här avsnittet beskrivs hur du utför en grundläggande parameter rensning, som tågen en modell med hjälp av modulen för att finjustera modellens standardparametrar.

1.  Lägg till modulen för att finjustera modellens standardparametrar till din pipeline i designern.

2.  Anslut en modell som inte är tränad till intill vänster. 

    > [!NOTE] 
    > Det **går bara** att ansluta till inbyggda Machine Learning algorithm-moduler och stöder inte anpassad modell som skapats i **create python-modellen**.


3.  Lägg till den data uppsättning som du vill använda för utbildning och Anslut den till den mittersta indatan för justerings modellens parametrar.  

    Om du har en taggad data uppsättning kan du ansluta den till porten längst till höger (**valfri verifierings data uppsättning**). På så sätt kan du mäta noggrannhet medan du tränar och justerar.

4.  Välj ett värde för **parameter rensnings läge** i den högra panelen om du vill justera modellens egenskaper. Det här alternativet styr hur parametrarna väljs.

    - **Hela rutnätet**: när du väljer det här alternativet upprepas modulen över ett rutnät som är fördefinierat i systemet, för att testa olika kombinationer och identifiera den bästa bättre eleven. Det här alternativet är användbart när du inte vet vad de bästa parameter inställningarna kan vara och vill testa alla möjliga kombinationer av värden.

    - **Slumpmässig svep**: när du väljer det här alternativet kommer modulen slumpmässigt att välja parameter värden över ett Systemdefinierat intervall. Du måste ange det maximala antalet körningar som du vill att modulen ska köra. Det här alternativet är användbart när du vill öka modell prestandan genom att använda de mått som du väljer, men ändå spara dator resurser.    

5.  För **kolumnen etikett** öppnar du kolumn väljaren för att välja en enskild etikett kolumn.

6.  Välj antal körningar:

    - **Maximalt antal körningar vid slumpmässig svep**: om du väljer en slumpmässig svep kan du ange hur många gånger modellen ska tränas genom att använda en slumpmässig kombination av parameter värden.

7.  I **rangordning** väljer du ett mått som ska användas för att rangordna modellerna.

    När du kör en parameter rensning beräknar modulen alla tillämpliga mått för modell typen och returnerar dem i rapporten **svep resultat** . Modulen använder separata mått för Regressions-och klassificerings modeller.

    Men det mått du väljer avgör hur modellerna rangordnas. Endast den översta modellen, som rangordnas av det valda måttet, är utdata som en utbildad modell som kan användas för poängsättning.

8.  För **slumpmässigt utsäde** anger du ett tal som ska användas för att starta parametern svep. 

9. Skicka pipelinen.

## <a name="results-of-hyperparameter-tuning"></a>Resultat av inställning av min parameter

När inlärningen är klar:

+ Om du vill visa resultaten för rensningen kan du antingen högerklicka på modulen och sedan välja **visualisera**, eller högerklicka på den vänstra utdataporten för modulen för att visualisera.

    **Rensnings resultatet** innehåller alla parameter rensnings-och precisions mått som gäller för modell typen, och det mått som du har valt för rangordning avgör vilken modell som betraktas som "bästa".

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du fliken **utdata + loggar** i den högra panelen i modulen **träna modell** . Välj ikonen **registrera data uppsättning** för att spara modellen som en återanvändbar modul.


## <a name="technical-notes"></a>Tekniska anteckningar

Det här avsnittet innehåller implementerings information och tips.

### <a name="how-a-parameter-sweep-works"></a>Så här fungerar en parameter Svep

När du ställer in en parameter rensning definierar du omfattningen för din sökning. Sökningen kan använda ett begränsat antal parametrar som marker ATS slumpmässigt. Eller så kan det vara en fullständig sökning över ett parameter utrymme som du definierar.

+ **Slumpmässig svep**: det här alternativet tränar en modell genom att använda ett angivet antal iterationer. 

  Du anger ett intervall med värden att iterera över och modulen använder en slumpmässigt vald delmängd av dessa värden. Värdena väljs med ersättning, vilket innebär att de siffror som tidigare valdes slumpmässigt inte tas bort från poolen med tillgängliga nummer. Det innebär att risken för alla värden som väljs förblir samma för alla pass.  

+ **Hela rutnätet**: alternativet att använda hela rutnätet innebär att varje kombination testas. Det här alternativet är det mest grundliga, men det kräver den mest aktuella tiden. 

### <a name="controlling-the-length-and-complexity-of-training"></a>Kontrol lera längden och komplexiteten för utbildningen

Det kan vara tids krävande att iterera över många kombinationer av inställningar, så att modulen ger flera olika sätt att begränsa processen:

+ Begränsa antalet iterationer som används för att testa en modell.
+ Begränsa parameter utrymmet.
+ Begränsa både antalet iterationer och parameter utrymmet.

Vi rekommenderar att du förloppet med inställningarna för att fastställa den mest effektiva metoden för utbildning på en viss data uppsättning och modell.

### <a name="choosing-an-evaluation-metric"></a>Välja ett utvärderings mått

I slutet av testet visar modellen en rapport som innehåller precisionen för varje modell så att du kan granska mått resultatet:

- En enhetlig uppsättning mått används för alla binära klassificerings modeller.
- Noggrannhet används för alla klassificerings modeller med flera klasser.
- En annan uppsättning mått används för Regressions modeller. 

Under utbildningen måste du dock välja ett *enda* mått som ska användas för att rangordna de modeller som genereras under justerings processen. Du kanske upptäcker att det bästa måttet varierar, beroende på ditt företags problem och kostnaden för falska positiva identifieringar och falska negativa negativa.

#### <a name="metrics-used-for-binary-classification"></a>Mått som används för binär klassificering

-   **Noggrannhet** är den andel av sant resultat som det totala antalet fall.  

-   **Precision** är proportionen för sanna resultat till positiva resultat.  

-   **Åter kallelse** är del av alla korrekta resultat över alla resultat.  

-   **F-score** är ett mått som balanserar precision och återkallande.  

-   **AUC** är ett värde som representerar ytan under kurvan när falska positiva identifieringar ritas på x-axeln och sanna positiva resultat ritas på y-axeln.  

-   **Genomsnittlig logg förlust** är skillnaden mellan två sannolikhets fördelningar: den sanna och den som finns i modellen.  

#### <a name="metrics-used-for-regression"></a>Mått som används för regression

-   Medelvärde för **medelvärdet av absoluta fel** beräknar alla fel i modellen, där *felet* betyder avståndet från det förväntade värdet från det sanna värdet. Det är ofta förkortat som *Mae*.  

-   **Roten av medelvärdet i ett kvadratvärde** mäter medelvärdet av fel rutornas kvadrat och använder sedan roten för det värdet. Det är ofta förkortat som *rmse*.  

-   Ett **relativt absolut fel** representerar felet som en procent andel av det sanna värdet.  

-   **Ett relativt kvadratvärde** normaliserar det totala antalet fel i kvadratvärdet genom att dividera med det totala antalet fel i det förväntade värdet.  

-   **Koefficienten för bestämning** är ett enda tal som anger hur bra data passar en modell. Värdet ett innebär att modellen exakt matchar data. Värdet noll innebär att data är slumpmässiga eller på annat sätt inte får plats i modellen. Det kallas ofta *r <sup>2</sup>*, *r <sup>2</sup>* eller *r-kvadratvärdet*.  

### <a name="modules-that-dont-support-a-parameter-sweep"></a>Moduler som inte stöder en parameter-Svep

Nästan alla lär i Azure Machine Learning stöd för kors validering med en integrerad parameter rensning, vilket gör att du kan välja vilka parametrar som ska användas för pipeline. Om eleven inte har stöd för att ange ett värde intervall kan du fortfarande använda det vid kors validering. I det här fallet väljs ett intervall med tillåtna värden för svepet. 


## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

