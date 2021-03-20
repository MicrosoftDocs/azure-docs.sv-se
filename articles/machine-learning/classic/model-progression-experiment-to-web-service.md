---
title: 'ML Studio (klassisk): hur en modell blir en webb tjänst – Azure'
description: En översikt över Mechanics för hur din Azure Machine Learning Studio (klassiska) modell förloppet från ett utvecklings experiment till en webb tjänst.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 4e0f5786047977a319825aae9f3c7b89c0aa118b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518631"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Hur en Machine Learning Studio (klassisk) modell går från ett experiment till en webb tjänst

**gäller för:** ![ Detta är en bock markering, vilket innebär att den här artikeln gäller Machine Learning Studio (klassisk). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ Detta är ett X, vilket innebär att den här artikeln gäller för Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Azure Machine Learning Studio (klassisk) tillhandahåller en interaktiv arbets yta som gör att du kan utveckla, köra, testa och iterera ett ***experiment*** som representerar en förutsägelse analys modell. Det finns en mängd olika moduler som kan:

* Mata in data i experimentet
* Ändra data
* Träna en modell med Machine Learning-algoritmer
* Poängsätta modellen
* Utvärdera resultatet
* Utgående slutgiltiga värden

När du är nöjd med experimentet kan du distribuera det som en ***klassisk Azure Machine Learning-webbtjänst** _ eller en _ *_ny Azure Machine Learning-webbtjänst_** så att användarna kan skicka nya data och ta emot tillbaka resultatet.

I den här artikeln ger vi en översikt över Mechanics för hur din Machine Learning modell fortlöper från ett utvecklings experiment till en drifts webb tjänst.

> [!NOTE]
> Det finns andra sätt att utveckla och distribuera Machine Learning-modeller, men den här artikeln fokuserar på hur du använder Machine Learning Studio (klassisk). Om du till exempel vill läsa en beskrivning av hur du skapar en klassisk förutsägbar webb tjänst med R, se blogg inlägget [Build & distribuera förutsägande Web Apps med RStudio och Azure Machine Learning Studio](/archive/blogs/machinelearning/build-deploy-predictive-web-apps-using-rstudio-and-azure-ml).
>
>

Medan Azure Machine Learning Studio (klassisk) är utformat för att hjälpa dig att utveckla och distribuera en *förutsägelse analys modell*, är det möjligt att använda Studio (klassisk) för att utveckla ett experiment som inte innehåller en förutsägelse analys modell. Till exempel kan ett experiment bara mata in data, manipulera det och sedan spara resultatet. Precis som ett experiment med förutsägelse analys kan du distribuera detta icke-förutsägande experiment som en webb tjänst, men det är en enklare process eftersom experimentet inte tränar eller beräknar en maskin inlärnings modell. Det är inte vanligt att använda Studio (klassisk) på det här sättet, men vi inkluderar dem i diskussionen så att vi kan ge en fullständig förklaring av hur Studio (klassisk) fungerar.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Utveckla och distribuera en förutsägbar webb tjänst
Här följer de steg som en typisk lösning följer när du utvecklar och distribuerar den med hjälp av Machine Learning Studio (klassisk):

![Distributions flöde](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*Figur 1 – faser i en typisk förutsägelse analys modell*

### <a name="the-training-experiment"></a>Utbildnings experimentet
***Övnings experimentet*** är den inledande fasen av att utveckla din webb tjänst i Machine Learning Studio (klassisk). Syftet med övnings experimentet är att ge dig en plats för att utveckla, testa, iterera och slutligen träna en maskin inlärnings modell. Du kan till och med träna flera modeller samtidigt när du söker efter den bästa lösningen, men när du är klar med experimentet väljer du en enda utbildad modell och tar bort resten från experimentet. Ett exempel på att utveckla ett förutsägelse analys experiment finns i [utveckla en förutsägelse analys lösning för kredit riskbedömning i Azure Machine Learning Studio (klassisk)](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>Förutsägande experiment
När du har en tränad modell i ditt utbildnings experiment klickar du på **Konfigurera webb tjänsten** och väljer **förutsägbar webb tjänst** i Machine Learning Studio (klassisk) för att starta processen med att konvertera ditt utbildnings experiment till ett **_förutsägelse experiment_**. Syftet med förutsägande experiment är att använda din tränade modell för att ta fram nya data, med målet att bli drifts dugligd som en Azure-webbtjänst.

Den här omvandlingen görs med hjälp av följande steg:

* Konvertera den uppsättning moduler som används för utbildning i en enda modul och spara den som en utbildad modell
* Eliminera eventuella främmande moduler som inte är relaterade till Poäng
* Lägg till indata-och utgående portar som den andra webb tjänsten kommer att använda

Det kan finnas fler ändringar som du vill göra för att få ett förutsägelse experiment klart att distribuera som en webb tjänst. Om du till exempel vill att webb tjänsten endast ska mata ut en delmängd av resultaten kan du lägga till en-filtrerings modul före utdataporten.

I den här konverterings processen ignoreras inte inlärnings experimentet. När processen är klar har du två flikar i Studio (klassisk): en för övnings experimentet och en för det förutsägande experimentet. På så sätt kan du göra ändringar i övnings experimentet innan du distribuerar din webb tjänst och återskapa det förutsägande experimentet. Eller så kan du spara en kopia av utbildnings experimentet för att starta en annan rad experimentering.

> [!NOTE]
> När du klickar på **förutsägbar webb tjänst** startar du en automatisk process för att konvertera ditt utbildnings experiment till ett förutsägelse experiment, och det fungerar bra i de flesta fall. Om övnings experimentet är komplicerat (till exempel om du har flera sökvägar för utbildning som du kopplar ihop) kan du överväga att göra konverteringen manuellt. Mer information finns i [förbereda din modell för distribution i Azure Machine Learning Studio (klassisk)](deploy-a-machine-learning-web-service.md).
>
>

### <a name="the-web-service"></a>Webb tjänsten
När du är nöjd med att ditt förutsägelse experiment är klart kan du distribuera tjänsten som en klassisk webb tjänst eller en ny webb tjänst som baseras på Azure Resource Manager. Om du vill operationalisera din modell genom att distribuera den som en *klassisk Machine Learning-webbtjänst* klickar du på **distribuera webb tjänst** och väljer **distribuera webb tjänst [klassisk]**. Om du vill distribuera som *ny Machine Learning-webbtjänst* klickar du på **distribuera webb tjänst** och väljer **distribuera webb tjänst [ny]**. Användarna kan nu skicka data till din modell med hjälp av webb tjänsten REST API och få tillbaka resultatet. Mer information finns i [Använda Azure Machine Learning-webbtjänster](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Icke-typiskt fall: skapa en icke-förutsägbar webb tjänst
Om experimentet inte tränar en förutsägelse analys modell behöver du inte skapa både ett utbildnings experiment och ett Poäng experiment – det finns bara ett experiment och du kan distribuera det som en webb tjänst. Machine Learning Studio (klassisk) identifierar om experimentet innehåller en förutsägelse modell genom att analysera de moduler som du har använt.

När du har itererat över ditt experiment och är nöjd med det:

1. Klicka på **Konfigurera webb tjänsten** och välj **retraining Web Service** – indata och utdata-noder läggs till automatiskt
2. Klicka på **Kör**
3. Klicka på **distribuera webb tjänst** och välj **distribuera webb tjänst [klassisk]** eller **distribuera webb tjänst [ny]** beroende på vilken miljö som du vill distribuera.

Din webb tjänst distribueras nu och du kan komma åt och hantera den precis som en förutsägbar webb tjänst.

## <a name="updating-your-web-service"></a>Uppdatera din webb tjänst
Nu när du har distribuerat experimentet som en webb tjänst, vad gör du om du behöver uppdatera den?

Det beror på vad du behöver uppdatera:

**Du vill ändra indata eller utdata eller ändra hur webb tjänsten ska manipulera data**

Om du inte ändrar modellen, men bara ändrar hur webb tjänsten ska hantera data, kan du redigera förutsägelse experimentet och klicka på **distribuera webb** tjänst **(klassisk)** eller **distribuera webb tjänst [ny]** igen. Webb tjänsten har stoppats, det uppdaterade förutsägande experimentet distribueras och webb tjänsten startas om.

Här är ett exempel: Antag att ditt förutsägelse experiment returnerar hela raden med indata med det förväntade resultatet. Du kan bestämma att du vill att webb tjänsten bara ska returnera resultatet. Så du kan lägga till en modul för **projekt kolumner** i förutsägande experiment, precis före utdataporten, för att utesluta andra kolumner än resultatet. När du klickar på **distribuera webb tjänst** och väljer **distribuera webb tjänst [klassisk]** eller **distribuera webb tjänst [ny]** igen, uppdateras webb tjänsten.

**Du vill träna om modellen med nya data**

Om du vill behålla din Machine Learning-modell, men du vill träna den med nya data, har du två alternativ:

1. **Träna om modellen medan webb tjänsten körs** – om du vill träna modellen medan den förutsägbara webb tjänsten körs, kan du göra detta genom att göra några ändringar i övnings experimentet och göra det till en **_omskolning av experimentet_*_. sedan kan du distribuera det som en _*_retraining-_ webbtjänst**. Instruktioner för hur du gör detta finns i [omträna Machine Learning modeller program mässigt](./retrain-machine-learning-model.md).
2. **Gå tillbaka till det ursprungliga inlärnings experimentet och Använd olika tränings data för att utveckla din modell** – ditt förutsägelse experiment är länkat till webb tjänsten, men utbildnings experimentet är inte direkt länkat på det här sättet. Om du ändrar det ursprungliga inlärnings experimentet och klickar på **Konfigurera webb tjänsten**, kommer det att skapa ett *nytt*     förutsägelse experiment som, när det distribueras, kommer att skapa en *ny* webb tjänst. Den ursprungliga webb tjänsten uppdateras inte bara.

   Om du behöver ändra inlärnings experimentet öppnar du det och klickar på **Spara som** för att göra en kopia. Detta lämnar det ursprungliga inlärnings experimentet, förebyggande experimentet och webb tjänsten. Nu kan du skapa en ny webb tjänst med dina ändringar. När du har distribuerat den nya webb tjänsten kan du bestämma om du vill stoppa den tidigare webb tjänsten eller låta den köras bredvid den nya.

**Du vill träna en annan modell**

Om du vill göra ändringar i ditt ursprungliga förutsägelse experiment, till exempel om du väljer en annan algoritm för maskin inlärning, försöker med en annan utbildnings metod osv., måste du följa den andra proceduren som beskrivs ovan för att träna om din modell: öppna övnings experimentet, klicka på **Spara som** för att göra en kopia och starta sedan den nya sökvägen till utveckla din modell, skapa förutsägande experiment och distribuera webb tjänsten. Detta skapar en ny webb tjänst som inte är relaterad till den ursprungliga, som du kan välja vilken eller båda, som ska fortsätta köras.

## <a name="next-steps"></a>Nästa steg
Mer information om processen för att utveckla och experimentera finns i följande artiklar:

* konvertera experimentet – [förbereda din modell för distribution i Azure Machine Learning Studio (klassisk)](deploy-a-machine-learning-web-service.md)
* distribuera webb tjänsten – [distribuera en Azure Machine Learning-webbtjänst](deploy-a-machine-learning-web-service.md)
* öva på att träna modell [Machine Learning modeller program mässigt](./retrain-machine-learning-model.md)

Exempel på hela processen finns i:

* [Själv studie kurs om Machine Learning: skapa ditt första experiment i Azure Machine Learning Studio (klassisk)](create-experiment.md)
* [Genom gång: utveckla en förutsägelse analys lösning för kredit riskbedömning i Azure Machine Learning](tutorial-part1-credit-risk.md)