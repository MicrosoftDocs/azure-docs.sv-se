---
title: 'ML Studio (klassisk): Distribuera en webb tjänst – Azure'
description: Så här konverterar du ett utbildnings experiment till ett förutsägelse experiment, förbereder det för distribution och distribuerar det som en Azure Machine Learning Studio (klassisk)-webb tjänst.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 018e292abc7050f5d71c6bf9065618fecf8b5668
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100520450"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Distribuera en Azure Machine Learning Studio (klassisk)-webb tjänst

**gäller för:** ![ Detta är en bock markering, vilket innebär att den här artikeln gäller Machine Learning Studio (klassisk). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ Detta är ett X, vilket innebär att den här artikeln gäller för Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Azure Machine Learning Studio (klassisk) gör det möjligt att bygga och testa en förutsägelse analys lösning. Sedan kan du distribuera lösningen som en webb tjänst.

Machine Learning Studio (klassiska) webb tjänster tillhandahåller ett gränssnitt mellan ett program och en Machine Learning Studio (klassisk) beräknings modell för arbets flöde. Ett externt program kan kommunicera med en Machine Learning Studio (klassisk) beräknings modell för arbets flöde i real tid. Ett anrop till en Machine Learning Studio (klassisk)-webb tjänst returnerar förutsägelse resultat till ett externt program. Om du vill göra ett anrop till en webbtjänst skickar du en API-nyckel som skapades när du distribuerade webbtjänsten. En Machine Learning Studio (klassisk)-webb tjänst baseras på REST, ett populärt arkitektur val för webb program projekt.

Azure Machine Learning Studio (klassisk) har två typer av webb tjänster:

* Request-Response tjänst (RR): en mycket skalbar tjänst med låg fördröjning som tar en enda data post.
* BES (batch execution service): en asynkron tjänst som utvärderar en batch med data poster.

Indata för BES är som de indata som används av RRS. Den största skillnaden är att BES läser ett block med poster från olika källor, till exempel Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (hive-fråga) och HTTP-källor.

Du kan distribuera din modell i tre steg från en överblick på hög nivå:

* **[Skapa ett utbildnings experiment]** – i Studio (klassisk) kan du träna och testa en förutsägelse analys modell med hjälp av utbildnings data som du tillhandahåller med hjälp av en stor uppsättning inbyggda Machine Learning-algoritmer.
* **[Konvertera det till ett förutsägelse experiment]** – när din modell har tränats med befintliga data och du är redo att använda den för att skapa nya data, förbereder du och effektiviserar experimentet med förutsägelser.
* **Distribuera** den som en **[ny webb tjänst]** eller en **[klassisk webb tjänst]** – när du distribuerar ditt förutsägelse experiment som en Azure-webbtjänst kan användarna skicka data till din modell och ta emot modellens förutsägelser.

## <a name="create-a-training-experiment"></a>Skapa ett utbildnings experiment

För att träna en förutsägelse analys modell använder du Azure Machine Learning Studio (klassisk) för att skapa ett utbildnings experiment där du inkluderar olika moduler för att läsa in tränings data, förbereda data vid behov, använda Machine Learning-algoritmer och utvärdera resultatet. Du kan iterera vidare till ett experiment och prova olika Machine Learning-algoritmer för att jämföra och utvärdera resultatet.

Processen för att skapa och hantera utbildnings experiment omfattas mer noggrant. Mer information finns i de här artiklarna:

* [Skapa ett enkelt experiment i Azure Machine Learning Studio (klassisk)](create-experiment.md)
* [Utveckla en förutsägelse lösning med Azure Machine Learning Studio (klassisk)](tutorial-part1-credit-risk.md)
* [Importera dina utbildnings data till Azure Machine Learning Studio (klassisk)](import-data.md)
* [Hantera experiment upprepningar i Azure Machine Learning Studio (klassisk)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Konvertera träningsexperimentet till ett förutsägelseexperiment

När du har tränat din modell är du redo att konvertera ditt utbildnings experiment till ett förutsägelse experiment för att få nya data.

Genom att konvertera till ett förutsägelse experiment får du en utbildad modell som är redo att distribueras som en bedömnings webb tjänst. Användare av webb tjänsten kan skicka indata till din modell och din modell skickar tillbaka förutsägelse resultatet. När du konverterar till ett förutsägelse experiment bör du tänka på hur du förväntar dig att modellen ska användas av andra.

Processen med att konvertera ett utbildnings experiment till ett förutsägelse experiment innefattar tre steg:

1. Ersätt modulerna för Machine Learning-algoritm med din tränaa modell.
2. Trimma experimentet till endast de moduler som behövs för att kunna ta poäng. Ett utbildnings experiment innehåller ett antal moduler som är nödvändiga för utbildning, men som inte behövs när modellen har tränats.
3. Definiera hur din modell ska ta emot data från webb tjänst användaren och vilka data som ska returneras.

> [!TIP]
> I ditt utbildnings experiment har du lärt dig att träna och göra en bedömning av din modell med dina egna data. Men när de har distribuerats skickar användarna nya data till din modell så att de returnerar förutsägelse resultat. Så när du konverterar ditt utbildnings experiment till ett förutsägelse experiment för att förbereda den för distribution, bör du tänka på hur modellen kommer att användas av andra.

![Konvertera till poängsättnings experiment](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>Knappen Konfigurera webb tjänst
När du har kört experimentet (klicka på **Kör** längst ned i experiment arbets ytan) klickar du på knappen **Konfigurera webb tjänst** (Välj alternativet för **förutsägelse webb tjänst** ). **Konfigurera webb tjänsten** utför för dig de tre stegen för att konvertera ditt utbildnings experiment till ett förutsägande experiment:

1. Den sparar din tränade modell i avsnittet **tränade modeller** i modulen modul (till vänster om experimentets arbets yta). Den ersätter sedan Machine Learning-algoritmen och [tränar modell][train-model] moduler med den sparade tränade modellen.
2. Det analyserar experimentet och tar bort moduler som används tydligt för utbildning och som inte längre behövs.
3. Den infogar moduler för indata och _utdata_ för _webb tjänst_ på standard platserna i experimentet (dessa moduler accepterar och returnerar användar data).

Följande experimenterar exempelvis en modell med två klasser som har förbättrats i besluts fattandet med exempel på inventerings data:

![Utbildnings experiment](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Modulerna i det här experimentet utför i princip fyra olika funktioner:

![Modul funktioner](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

När du konverterar det här inlärnings experimentet till ett förutsägelse experiment behövs inte längre några av de här modulerna, eller så har de nu ett annat syfte:

* **Data** -data i den här exempel data uppsättningen används inte under poängsättningen – användaren av webb tjänsten kommer att ange de data som ska poängas. Men metadata från den här data uppsättningen, till exempel data typer, används av den tränade modellen. Därför måste du behålla data uppsättningen i förutsägande experiment så att den kan tillhandahålla dessa metadata.

* **Prep** – beroende på vilka användar data som kommer att skickas för att komma fram kan dessa moduler vara nödvändiga för att bearbeta inkommande data. Knappen **Konfigurera webb tjänst** vidrör inte dessa – du måste bestämma hur du vill hantera dem.
  
    I det här exemplet kan exempel data uppsättningen ha saknade värden, så en [rensad][clean-missing-data] datamodul som saknas inkluderades för att hantera dem. Dessutom innehåller exempel data uppsättningen kolumner som inte behövs för att träna modellen. Därför inkluderades en [Välj kolumner i data uppsättnings][select-columns] modulen för att utesluta de extra kolumnerna från data flödet. Om du vet att de data som ska skickas för poängsättning via webb tjänsten saknar värden som saknas kan du ta bort modulen [Rensa data som saknas][clean-missing-data] . Eftersom modulen [Välj kolumner i data uppsättning][select-columns] hjälper till att definiera kolumner med data som den tränade modellen förväntar sig, måste modulen vara kvar.

* **Träna** – dessa moduler används för att träna modellen. När du klickar på **Konfigurera webb tjänsten** ersätts dessa moduler med en enda modul som innehåller den modell som du har tränat. Den här nya modulen sparas i avsnittet **tränade modeller** i modulen modul.

* **Score** – i det här exemplet används modulen [dela data][split] för att dela upp data strömmen i test data och tränings data. I det förutsägande experimentet har vi inte längre utbildning, så [delade data][split] kan tas bort. På samma sätt används den andra [Poäng modell][score-model] modulen och modulen [utvärdera modell][evaluate-model] för att jämföra resultat från test data, så dessa moduler behövs inte i förutsägelse experimentet. Den återstående [poängen modell][score-model] -modulen behövs dock för att returnera ett resultat resultat via webb tjänsten.

Så här ser vårt exempel ut efter att du har klickat på **Konfigurera webb tjänsten**:

![Konverterat förutsägelse experiment](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Arbetet som du utför genom att **Konfigurera webb tjänsten** kan vara tillräckligt för att förbereda ditt experiment som ska distribueras som en webb tjänst. Men du kanske vill göra ytterligare arbete som är speciellt för experimentet.

#### <a name="adjust-input-and-output-modules"></a>Justera moduler för indata och utdata
I ditt utbildnings experiment har du använt en uppsättning tränings data och gjorde bearbetningen för att hämta data i ett formulär som Machine Learning-algoritmen behövde. Om de data som du förväntar dig via webb tjänsten inte behöver den här bearbetningen kan du kringgå det: Anslut utdata från **webb tjänstens inmatnings modul** till en annan modul i experimentet. Användarens data kommer nu att tas med i modellen på den här platsen.

Som standard lägger du till **webb** tjänstens **inmatnings** modul överst i ditt data flöde, som du ser i bilden ovan. Men vi kan manuellt placera **webb tjänstens indata** förbi modulerna för data bearbetning:

![Flytta webb tjänsten inmatade](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

De indata som tillhandahålls via webb tjänsten skickas nu direkt till modulen Poäng modell utan att Förbearbeta.

Som standard lägger **webb** tjänsten i webb tjänstens output-modul längst ned i ditt data flöde. I det här exemplet återgår webb tjänsten till användaren utdata från modulen [Poäng modell][score-model] , som innehåller den fullständiga inmatnings data vektorn plus resultat resultaten.
Men om du föredrar att returnera något annat kan du lägga till ytterligare moduler innan du skapar **webb tjänstens output-** modul. 

Om du till exempel bara vill returnera resultat resultaten och inte hela vektorn för indata lägger du till en [Välj kolumner i data uppsättnings][select-columns] modulen för att undanta alla kolumner förutom resultat resultaten. Flytta sedan **webb tjänstens output-** modul till utdata från modulen [Välj kolumner i data uppsättning][select-columns] . Experimentet ser ut så här:

![Flytta webb tjänstens utdata](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>Lägg till eller ta bort ytterligare data bearbetnings moduler
Om det finns fler moduler i experimentet som du vet inte kommer att behövas under poängsättningen kan du ta bort dem. Till exempel, eftersom vi flyttade **webb tjänstens inmatnings** modul till en punkt efter data bearbetnings modulerna, kan vi ta bort modulen [Rensa data som saknas][clean-missing-data] från förutsägelse experimentet.

Vårt förutsägande experiment ser nu ut så här:

![Tar bort ytterligare modul](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>Lägg till valfria webb tjänst parametrar
I vissa fall kanske du vill tillåta att användaren av webb tjänsten ändrar beteendet för moduler när tjänsten nås. Med *webb tjänst parametrar* kan du göra detta.

Ett vanligt exempel är att skapa en modul för att [Importera data][import-data] så att användaren av den distribuerade webb tjänsten kan ange en annan data källa när webb tjänsten nås. Eller konfigurera en modul för att [Exportera data][export-data] så att du kan ange ett annat mål.

Du kan definiera webb tjänst parametrar och associera dem med en eller flera parametrar för modulen, och du kan ange om de är obligatoriska eller valfria. Användaren av webb tjänsten tillhandahåller värden för dessa parametrar när tjänsten används och modulerna ändras därefter.

Mer information om vilka webb tjänst parametrar som är och hur de används finns i [använda Azure Machine Learning webb tjänst parametrar][webserviceparameters].

Följande steg beskriver hur du distribuerar ett förutsägelse experiment som en ny webb tjänst. Du kan också distribuera experimentet som den klassiska webb tjänsten.

## <a name="deploy-it-as-a-new-web-service"></a>Distribuera den som en ny webb tjänst

Nu när det förutsägande experimentet har förberetts kan du distribuera det som en ny (Resource Manager-baserad) Azure-webbtjänst. Med hjälp av webb tjänsten kan användarna skicka data till din modell och modellen returnerar sina förutsägelser.

Om du vill distribuera ett förutsägelse experiment klickar du på **Kör** längst ned i experiment arbets ytan. När experimentet har körts klickar du på **distribuera webb tjänst** och väljer **distribuera webb tjänst [ny]**.  Sidan distribution på webb tjänst portalen Machine Learning Studio (klassisk) öppnas.

> [!NOTE] 
> Om du vill distribuera en ny webb tjänst måste du ha tillräcklig behörighet i den prenumeration som du distribuerar webb tjänsten till. Mer information finns i [hantera en webb tjänst med hjälp av Azure Machine Learning Web Services-portalen](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Webb tjänst Portal för att distribuera experiment Sidan

På sidan distribuera experiment anger du ett namn för webb tjänsten.
Välj en pris sättnings plan. Om du har en befintlig pris plan kan du välja den, annars måste du skapa en ny pris plan för tjänsten.

1. Välj en befintlig plan i list rutan **pris plan** eller Välj alternativet **Välj ny plan** .
2. I **planerat namn** skriver du ett namn som identifierar planen på din faktura.
3. Välj en av de **månatliga plan nivåerna**. Planen får som standard nivåerna för planer i din standardregion och din webbtjänst distribueras till den regionen.

Klicka på **distribuera** och **snabb starts** sidan för din webb tjänst öppnas.

På sidan snabb start för webb tjänst får du till gång till och vägledning om de vanligaste åtgärderna som du kan utföra när du har skapat en webb tjänst. Härifrån kan du enkelt komma åt både test sidan och förbruknings sidan.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Testa din nya webb tjänst

Testa din nya webb tjänst genom att klicka på **test-webbtjänst** under vanliga uppgifter. På sidan test kan du testa webb tjänsten som en Request-Response tjänst (RR) eller en batch execution service (BES).

På test sidan för resurs poster visas indata, utdata och eventuella globala parametrar som du har definierat för experimentet. Om du vill testa webb tjänsten kan du manuellt ange lämpliga värden för indata eller ange en fil med kommaavgränsade värden (CSV) som innehåller test värden.

Om du vill testa att använda resurs namn, går du till listvyns visnings läge och anger lämpliga värden för indata och klickar på **test Request-Response**. Dina förutsägelse resultat visas i kolumnen utdata till vänster.

![Ange lämpliga värden för att testa din webb tjänst](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Om du vill testa din BES klickar du på **batch**. På sidan batch-test klickar du på Bläddra under dina indata och väljer en CSV-fil som innehåller lämpliga exempel värden. Om du inte har en CSV-fil och du har skapat ditt förutsägelse experiment med Machine Learning Studio (klassisk) kan du hämta data uppsättningen för ditt förutsägelse experiment och använda den.

Öppna Machine Learning Studio (klassisk) om du vill hämta data uppsättningen. Öppna ditt förutsägelse experiment och högerklicka på indatamängden för experimentet. Välj **data uppsättning** på snabb menyn och välj sedan **Ladda ned**.

![Hämta data uppsättningen från Studio-arbetsytan (klassisk)](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klicka på **testa**. Status för batch-körningen visas till höger under **testa batch-jobb**.

![Testa batch-körningen med webb tjänst portalen](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

På sidan **konfiguration** kan du ändra beskrivningen, rubriken, uppdatera lagrings konto nyckeln och aktivera exempel data för webb tjänsten.

![Konfigurera din webb tjänst](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Få åtkomst till din nya webb tjänst

När du har distribuerat din webb tjänst från Machine Learning Studio (klassisk) kan du skicka data till tjänsten och ta emot svar program mässigt.

Sidan **förbrukning** innehåller all information som du behöver för att komma åt din webb tjänst. API-nyckeln tillhandahålls till exempel för att tillåta behörig åtkomst till tjänsten.

Mer information om hur du kommer åt en Machine Learning Studio (klassisk)-webb tjänst finns i [så här använder du en Azure Machine Learning Studio (klassisk)-webb tjänst](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Hantera din nya webb tjänst

Du kan hantera dina nya webb tjänster med hjälp av Machine Learning Studio (klassiska) webb tjänst portalen. Från [huvud Portal sidan](https://services.azureml.net/)klickar du på **webb tjänster**. På sidan webb tjänster kan du ta bort eller kopiera en tjänst. Om du vill övervaka en enskild tjänst klickar du på tjänsten och sedan på **instrument panel**. Om du vill övervaka batch-jobb som är associerade med webb tjänsten klickar du på **batch Request log**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> Distribuera din nya webb tjänst till flera regioner

Du kan enkelt distribuera en ny webb tjänst till flera regioner utan att behöva flera prenumerationer eller arbets ytor.

Priser är landsspecifika, så du måste definiera en fakturerings plan för varje region där du ska distribuera webb tjänsten.

#### <a name="create-a-plan-in-another-region"></a>Skapa en plan i en annan region

1. Logga in på [Microsoft Azure Machine Learning-webbtjänster](https://services.azureml.net/).
2. Klicka på meny alternativet **planer** .
3. På sidan planer över vy klickar du på **ny**.
4. I list rutan **prenumeration** väljer du den prenumeration som den nya planen kommer att finnas i.
5. I list rutan **region** väljer du en region för den nya planen. Plan alternativen för den valda regionen visas i avsnittet **plan alternativ** på sidan.
6. I list rutan **resurs grupp** väljer du en resurs grupp för planen. Mer information om resurs grupper finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).
7. I **planerat namn** skriver du namnet på planen.
8. Under **plan alternativ** klickar du på fakturerings nivå för den nya planen.
9. Klicka på **Skapa**.

#### <a name="deploy-the-web-service-to-another-region"></a>Distribuera webb tjänsten till en annan region

1. På sidan Microsoft Azure Machine Learning webb tjänster klickar du på meny alternativet **webb tjänster** .
2. Välj den webb tjänst som du distribuerar till en ny region.
3. Klicka på **Kopiera**.
4. I **webb tjänstens namn** skriver du ett nytt namn för webb tjänsten.
5. I **Beskrivning av webb tjänst** anger du en beskrivning av webb tjänsten.
6. I list rutan **prenumeration** väljer du den prenumeration som den nya webb tjänsten kommer att finnas i.
7. I list rutan **resurs grupp** väljer du en resurs grupp för webb tjänsten. Mer information om resurs grupper finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).
8. I list rutan **region** väljer du den region där du vill distribuera webb tjänsten.
9. I list rutan **lagrings konto** väljer du ett lagrings konto där webb tjänsten ska lagras.
10. Välj en plan i den region som du valde i steg 8 i list rutan **pris plan** .
11. Klicka på **Kopiera**.

## <a name="deploy-it-as-a-classic-web-service"></a>Distribuera den som en klassisk webb tjänst

Nu när det förutsägande experimentet har varit tillräckligt för berett kan du distribuera det som en klassisk Azure-webbtjänst. Med hjälp av webb tjänsten kan användarna skicka data till din modell och modellen returnerar sina förutsägelser.

Om du vill distribuera ett förutsägelse experiment klickar du på **Kör** längst ned i experiment arbets ytan och klickar sedan på **distribuera webb tjänst**. Webb tjänsten har kon figurer ATS och du placeras på instrument panelen för webb tjänster.

![Distribuera din webb tjänst från Studio (klassisk)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testa din klassiska webb tjänst

Du kan testa webb tjänsten antingen på den Machine Learning Studio (klassiska) webb tjänst portalen eller Machine Learning Studio (klassisk).

Testa webb tjänsten Request Response genom att klicka på knappen **test** på instrument panelen för webb tjänster. En dialog ruta öppnas där du uppmanas att ange indata för tjänsten. Detta är de kolumner som förväntas av poängsättnings experimentet. Ange en uppsättning data och klicka sedan på **OK**. De resultat som genereras av webb tjänsten visas längst ned på instrument panelen.

Du kan klicka på länken för att **testa** för hands versionen för att testa tjänsten på den Azure Machine Learning Studio (klassiska) webb tjänst portalen som tidigare visades i avsnittet ny webb tjänst.

Om du vill testa batch-körningen klickar du på **testa** för hands versions länk. På sidan batch-test klickar du på Bläddra under dina indata och väljer en CSV-fil som innehåller lämpliga exempel värden. Om du inte har en CSV-fil och du har skapat ditt förutsägelse experiment med Machine Learning Studio (klassisk) kan du hämta data uppsättningen för ditt förutsägelse experiment och använda den.

![Testa webbtjänsten](./media/publish-a-machine-learning-web-service/figure-3.png)

På sidan **konfiguration** kan du ändra visnings namnet för tjänsten och ge den en beskrivning. Namnet och beskrivningen visas i [Azure Portal](https://portal.azure.com/) där du hanterar dina webb tjänster.

Du kan ange en beskrivning av dina indata, utdata och webb tjänst parametrar genom att ange en sträng för varje kolumn under **inmatnings schema**, **utdata schema** och **webb tjänst parameter**. Dessa beskrivningar används i den exempel kod dokumentation som tillhandahålls för webb tjänsten.

Du kan aktivera loggning för att diagnostisera eventuella fel som du ser när din webb tjänst nås. Mer information finns i [Aktivera loggning för Machine Learning Studio (klassiska) webb tjänster](web-services-logging.md).

![Aktivera loggning i webb tjänst portalen](./media/publish-a-machine-learning-web-service/figure-4.png)

Du kan också konfigurera slut punkterna för webb tjänsten i Azure Machine Learning Web Services-portalen på samma sätt som i den nya webb tjänstens avsnitt. Alternativen skiljer sig åt, du kan lägga till eller ändra tjänst beskrivningen, aktivera loggning och aktivera exempel data för testning.

### <a name="access-your-classic-web-service"></a>Åtkomst till din klassiska webb tjänst

När du har distribuerat din webb tjänst från Azure Machine Learning Studio (klassisk) kan du skicka data till tjänsten och ta emot svar program mässigt.

Instrument panelen innehåller all information du behöver för att komma åt din webb tjänst. API-nyckeln tillhandahålls till exempel för att tillåta åtkomst till tjänsten, och API-hjälp sidor finns för att hjälpa dig att komma igång med att skriva din kod.

Mer information om hur du kommer åt en Machine Learning Studio (klassisk)-webb tjänst finns i [så här använder du en Azure Machine Learning Studio (klassisk)-webb tjänst](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Hantera din klassiska webb tjänst

Det finns olika åtgärder som du kan utföra för att övervaka en webb tjänst. Du kan uppdatera den och ta bort den. Du kan också lägga till ytterligare slut punkter till en klassisk webb tjänst förutom standard slut punkten som skapas när du distribuerar den.

Mer information finns i [hantera en Azure Machine Learning Studio (klassisk) arbets yta](manage-workspace.md) och [hantera en webb tjänst med hjälp av webb tjänst portalen för Azure Machine Learning Studio (klassisk)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Uppdatera webb tjänsten
Du kan göra ändringar i webb tjänsten, till exempel uppdatera modellen med ytterligare utbildnings data, och distribuera den igen, och skriva över den ursprungliga webb tjänsten.

Om du vill uppdatera webb tjänsten öppnar du det ursprungliga förutsägelse experiment som du använde för att distribuera webb tjänsten och gör en redigerbar kopia genom att klicka på **Spara som**. Gör ändringarna och klicka sedan på **distribuera webb tjänst**.

Eftersom du har distribuerat det här experimentet tidigare tillfrågas du om du vill skriva över (klassisk webb tjänst) eller uppdatera (ny webb tjänst) den befintliga tjänsten. Om du klickar på **Ja** eller **Uppdatera** stoppas den befintliga webb tjänsten och det nya förutsägande experimentet distribueras på sitt ställe.

> [!NOTE]
> Om du har gjort konfigurations ändringar i den ursprungliga webb tjänsten, t. ex. genom att ange ett nytt visnings namn eller en beskrivning, måste du ange dessa värden igen.

Ett alternativ för att uppdatera din webb tjänst är att träna modellen program mässigt. Mer information finns i [omträna Machine Learning Studio (klassiska) modeller program mässigt](./retrain-machine-learning-model.md).

## <a name="next-steps"></a>Nästa steg

* Mer teknisk information om hur distribution fungerar finns i [hur en Machine Learning Studio (klassisk) modell går från ett experiment till en drifts webb tjänst](model-progression-experiment-to-web-service.md).

* Mer information om hur du förbereder din modell för distribution finns [i förbereda din modell för distribution i Azure Machine Learning Studio (klassisk)](deploy-a-machine-learning-web-service.md).

* Du kan använda REST-API:et och komma åt webbtjänsten på flera sätt. Se [hur du använder en Azure Machine Learning Studio (klassisk)-webb tjänst](consume-web-services.md).

<!-- internal links -->
[Skapa ett utbildnings experiment]: #create-a-training-experiment
[Konvertera det till ett förutsägande experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Ny webb tjänst]: #deploy-it-as-a-new-web-service
[Klassisk webb tjänst]: #deploy-it-as-a-classic-web-service
[Nyårs]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data