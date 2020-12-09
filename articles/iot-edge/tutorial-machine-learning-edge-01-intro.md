---
title: 'Självstudie: detaljerad genom gång av Machine Learning på Azure IoT Edge'
description: En självstudie på hög nivå som vägleder dig genom de olika aktiviteterna som behövs för att skapa en slut punkt till slut punkt, maskin inlärning i gräns scenariot.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b23324a7226d4b3de4908bd78a8f19c799e59f06
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932191"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Självstudie: en lösning från slut punkt till slut punkt med hjälp av Azure Machine Learning och IoT Edge

IoT-program vill ofta dra nytta av det intelligenta molnet och den intelligenta gränsen. I den här självstudien vägleder vi dig genom utbildningen av en maskin inlärnings modell med data som samlas in från IoT-enheter i molnet, distribution av modellen till IoT Edge och underhåll och raffinering av modellen med jämna mellanrum.

Det främsta syftet med den här självstudien är att introducera bearbetningen av IoT-data med Machine Learning, specifikt på gränsen. Medan vi vidrör många aspekter av ett allmänt Machine Learning-arbetsflöde är den här självstudien inte avsedd som en djupgående introduktion till Machine Learning. Som ett litet exempel försöker vi inte skapa en mycket optimerad modell för användnings fallet – vi räcker bara för att illustrera processen med att skapa och använda en praktisk modell för IoT-databearbetning.

Det här avsnittet i själv studie kursen beskriver:

> [!div class="checklist"]
>
> * Kraven för att slutföra de efterföljande delarna av självstudien.
> * Den aktuella mål gruppen för självstudien.
> * Användnings fall som självstudien simulerar.
> * Den övergripande processen som vägledningen följer för att uppfylla användnings fallet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

För att slutföra självstudien måste du ha åtkomst till en Azure-prenumeration där du har behörighet att skapa resurser. Flera av de tjänster som används i den här självstudien debiteras Azure-avgifter. Om du inte redan har en Azure-prenumeration kan du komma igång med ett [kostnads fritt Azure-konto](https://azure.microsoft.com/offers/ms-azr-0044p/).

Du behöver också en dator med PowerShell installerat där du kan köra skript för att konfigurera en virtuell Azure-dator som utvecklings dator.

I det här dokumentet använder vi följande uppsättning verktyg:

* En Azure IoT Hub för data insamling

* Azure Notebooks som vår huvud klient för förberedelse av data och maskin inlärnings experiment. Att köra python-kod i en bärbar dator på en delmängd av exempel data är ett bra sätt att få snabb och interaktiv data behandling under förberedelse av data. Jupyter-anteckningsböcker kan också användas för att förbereda skript som ska köras i stor skala i en beräknings Server del.

* Azure Machine Learning som Server del för maskin inlärning i skala och för generering av Machine Learning-avbildningar. Vi driver Azure Machine Learning Server del med skript som är beredda och testade i Jupyter Notebooks.

* Azure IoT Edge för program utanför molnet för en Machine Learning-avbildning

Det finns andra tillgängliga alternativ. I vissa fall kan exempelvis IoT Central användas som ett alternativ utan kod för att samla in initiala tränings data från IoT-enheter.

## <a name="target-audience-and-roles"></a>Mål grupp och roller

Den här uppsättningen med artiklar är avsedd för utvecklare utan tidigare erfarenhet av IoT-utveckling eller maskin inlärning. Att distribuera maskin inlärning i Edge kräver kunskaper om hur du ansluter en mängd olika tekniker. Därför täcker den här självstudien en hel del-till-slutpunkt-scenario som visar ett sätt att ansluta till dessa tekniker tillsammans för en IoT-lösning. I en verklig miljö kan dessa uppgifter distribueras mellan flera personer med olika specialiseringar. Utvecklare skulle till exempel kunna fokusera på antingen enhets-eller moln kod, medan data experter utformade analys modeller. För att göra det möjligt för en enskild utvecklare att slutföra den här självstudien har vi tillhandahållit kompletterande vägledning med insikter och länkar till mer information som vi hoppas räcker för att förstå vad som är klart, samt varför.

Alternativt kan du samar beta med kollegor med olika roller för att följa självstudien tillsammans, och få din fullständiga expertis att stå och lära sig som ett lag som passar ihop.

I båda fallen visas användarens roll i varje artikel i den här självstudien för att orientera läsarna. Dessa roller omfattar:

* Moln utveckling (inklusive en molnbaserad utvecklare som arbetar i en DevOps-kapacitet)
* Datanalys

## <a name="use-case-predictive-maintenance"></a>Användnings fall: förutsägande underhåll

Vi bygger det här scenariot på ett användnings fall som presenteras i konferensen om Prognostics och Health Management (PHM08) i 2008. Målet är att förutse återstående livs längd (RUL) för en uppsättning turbofan flyg Plans motorer. Den här informationen genererades med C-MAPS, den kommersiella versionen av MAPS (program vara för modulär Aero-Propulsion system simulering). Den här program varan tillhandahåller en flexibel miljö för turbofan motor simulering som gör det enkelt att simulera hälso-, kontroll-och motor parametrarna.

De data som används i den här självstudien hämtas från [data uppsättningen för turbofan-motorns degraderinging-simulering](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan).

Från Readme-filen:

***Experimentellt scenario** _

_Data mängder består av flera multivarierad tids serier. Varje data uppsättning är ytterligare indelad i utbildning och test del mängder. Varje tids serie är från en annan motor, d.v.s. data kan anses vara från en flotta av motorer av samma typ. Varje motor börjar med olika grader av inledande slitage och tillverknings variation som är okänt för användaren. Detta slitage betraktas som vanligt, d.v.s. det anses inte vara ett fel tillstånd. Det finns tre operativa inställningar som har en betydande effekt på motorns prestanda. Dessa inställningar ingår också i datan. Data är förorenade av sensor brus. *

*Motorn fungerar normalt i början av varje tids serie och utvecklar ett fel vid någon tidpunkt under serien. I inlärnings uppsättningen växer felet i storleksordning tills systemet havererar. I test uppsättningen går tids serien ut en stund innan systemet kraschar. Målet med tävlingen är att förutsäga antalet återstående operativa cykler innan det uppstår problem i test uppsättningen, d.v.s. antalet operativa cykler efter den senaste cykeln som motorn fortsätter att fungera. Tillhandahöll också en Vector med sanna återstående användbara livs längd värden (RUL) för test data.*

Eftersom data har publicerats för en tävling har flera metoder för att härleda maskin inlärnings modeller publicerats oberoende av varandra. Vi hittade att inlärnings exempel är användbart för att förstå processen och orsaken till att en speciell Machine Learning-modell skapas. Se till exempel:

[Modell för att förutsäga flyg Plans motorn](https://github.com/jancervenka/turbofan_failure) av GitHub User jancervenka.

[Turbofan-motorn försämras](https://github.com/hankroark/Turbofan-Engine-Degradation) av GitHub User hankroark.

## <a name="process"></a>Process

Bilden nedan illustrerar de grova steg som vi följer i den här självstudien:

![Arkitektur diagram för process steg](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Samla in utbildnings data**: processen börjar genom att samla in tränings data. I vissa fall har data redan samlats in och är tillgängliga i en databas eller i form av datafiler. I andra fall, särskilt för IoT-scenarier, måste data samlas in från IoT-enheter och sensorer och lagras i molnet.

   Vi förutsätter att du inte har en samling av turbofan-motorer, så att projektfilerna innehåller en enkel enhets simulator som skickar NASA enhets data till molnet.

1. **Förbered data**. I de flesta fall kräver rå data som samlas in från enheter och sensorer förberedelser för maskin inlärning. Det här steget kan innebära att data rensas, att data formateras om eller förbearbetas för att mata in ytterligare information Machine Learning kan ligga ut.

   För maskin data i flyg Plans motorn innebär data förberedelse att du beräknar explicita tids-till-feltider för varje data punkt i exemplet baserat på de faktiska observationerna av data. Med den här informationen kan Machine Learning-algoritmen hitta korrelationer mellan faktiska sensor data mönster och den förväntade återstående livs längden för motorn. Det här steget är mycket domänanslutet.

1. **Bygg en Machine Learning-modell**. Utifrån de för beredda data kan vi nu experimentera med olika Machine Learning-algoritmer och parameterizations för att träna modeller och jämföra resultatet till varandra.

   I det här fallet jämför vi det förväntade resultatet som beräknas av modellen med det faktiska resultatet i en uppsättning motorer. I Azure Machine Learning kan vi hantera olika iterationer av modeller som vi skapar i ett modell register.

1. **Distribuera modellen**. Vi kan gå vidare till distributionen när vi har en modell som uppfyller våra framgångar-kriterier. Det innefattar att omsluta modellen till en webbapp som kan matas med data med hjälp av REST-anrop och returnera analys resultat. Webbtjänst-appen paketeras sedan i en Docker-behållare, som i sin tur kan distribueras antingen i molnet eller som en IoT Edge modul. I det här exemplet fokuserar vi på distribution till IoT Edge.

1. **Underhålla och förfina modellen**. Vårt arbete utförs inte när modellen har distribuerats. I många fall vill vi fortsätta samla in data och regelbundet överföra dessa data till molnet. Vi kan sedan använda dessa data för att omträna och förfina vår modell, som vi sedan kan distribuera till IoT Edge.

## <a name="clean-up-resources"></a>Rensa resurser

Den här självstudien är en del av en uppsättning där varje artikel bygger på det arbete som utförts i föregående avsnitt. Vänta tills du är klar med att rensa alla resurser tills du är klar med den sista självstudien.

## <a name="next-steps"></a>Nästa steg

Den här självstudien är indelad i följande avsnitt:

1. Konfigurera din utvecklings dator och Azure-tjänster.
2. Generera utbildnings data för Machine Learning-modulen.
3. Träna och distribuera Machine Learning-modulen.
4. Konfigurera en IoT Edge-enhet så att den fungerar som en transparent Gateway.
5. Skapa och distribuera IoT Edge moduler.
6. Skicka data till din IoT Edge-enhet.

Fortsätt till nästa artikel för att konfigurera en utvecklings dator och etablera Azure-resurser.

> [!div class="nextstepaction"]
> [Konfigurera en miljö för maskin inlärning på IoT Edge](tutorial-machine-learning-edge-02-prepare-environment.md)