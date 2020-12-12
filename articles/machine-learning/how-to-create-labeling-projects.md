---
title: Skapa ett data etiketts projekt
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och kör etiketter för projekt för att tagga data för Machine Learning.  Använd ML-etikettering eller mänsklig i slingan för att hjälpa till med uppgiften.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: 5abb222a6211e2f3f92c002a49987ac54edc78e2
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346664"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Skapa ett projekt med data etiketter och exportera etiketter 



Att märka Voluminous-data i Machine Learning-projekt är ofta ett problem. Projekt som har en dator vision komponent, till exempel bild klassificering eller objekt identifiering, kräver vanligt vis etiketter för tusentals avbildningar.
 
Med [Azure Machine Learning](https://ml.azure.com/) data etiketter får du en central plats för att skapa, hantera och övervaka projekt etiketter. Använd den för att koordinera data, etiketter och team medlemmar för att effektivt hantera etikett uppgifter. Machine Learning stöder bild klassificering, antingen flera etiketter eller flera klasser och objekt identifiering med gränser rutor.

Data etiketter spårar förloppet och underhåller kön med ofullständiga etikett uppgifter.

Du kan starta och stoppa projektet och styra etikettens förlopp. Du kan granska etiketterade data och exportera etiketterade i COCO-format eller som en Azure Machine Learning data uppsättning.

> [!Important]
> Det finns för närvarande stöd för att märka projekt med bild klassificering och objekt identifiering. Dessutom måste data avbildningarna vara tillgängliga i ett Azure Blob-datalager. (Om du inte har ett befintligt data lager kan du ladda upp bilder när projektet skapas.)

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Skapa ett projekt
> * Ange projektets data och struktur
> * Köra och övervaka projektet
> * Exportera etiketterna


## <a name="prerequisites"></a>Förutsättningar

* De data som du vill märka, antingen i lokala filer eller i Azure Blob Storage.
* Den uppsättning etiketter som du vill använda.
* Anvisningarna för att märka.
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar.
* En Machine Learning-arbetsyta. Se [skapa en Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Skapa ett etikett projekt

Att märka projekt administreras från Azure Machine Learning. Du kan använda sidan **Märk projekt** för att hantera dina projekt.

Om dina data redan finns i Azure Blob Storage bör du göra det tillgängligt som ett data lager innan du skapar ett etikettande projekt. Ett exempel på hur du använder ett data lager finns i [Självstudier: skapa ditt första bild klassificerings projekt](tutorial-labeling.md).

Välj **Lägg till projekt** om du vill skapa ett projekt. Ge projektet ett lämpligt namn och välj **uppgifts typ för etiketter**.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Guiden skapa etikett för projekt":::

* Välj **bild klassificering flera klasser** för projekt när du bara vill använda en *enda etikett* från en uppsättning etiketter till en bild.
* Välj **bild klassificering med flera** etiketter för projekt om du vill tillämpa *en eller flera* etiketter från en uppsättning etiketter till en bild. Till exempel kan ett foto av en hund vara märkt med både *hund* och *dagtid*.
* Välj **objekt identifiering (markerings ram)** för projekt när du vill tilldela en etikett och en avgränsnings ruta till varje objekt i en bild.

Välj **Nästa** när du är redo att fortsätta.

## <a name="specify-the-data-to-label"></a>Ange de data som ska etiketteras

Om du redan har skapat en data uppsättning som innehåller dina data väljer du den från List rutan **Välj en befintlig data uppsättning** . Eller Välj **skapa en data uppsättning** för att använda ett befintligt Azure-datalager eller för att ladda upp lokala filer.

> [!NOTE]
> Ett projekt får inte innehålla fler än 500 000 bilder.  Om din data uppsättning har fler kommer endast de första 500 000 bilderna att läsas in.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Skapa en data uppsättning från ett Azure-datalager

I många fall är det bra att bara ladda upp lokala filer. Men [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ger ett snabbare och mer robust sätt att överföra en stor mängd data. Vi rekommenderar Storage Explorer som standard sätt att flytta filer.

Så här skapar du en data uppsättning från data som du redan har lagrat i Azure Blob Storage:

1. Välj **skapa en data uppsättning**  >  **från data lagret**.
1. Tilldela ett **namn** till din data uppsättning.
1. Välj **fil** som **data uppsättnings typ**.  Endast fil data uppsättnings typer stöds.
1. Välj data lagret.
1. Om dina data finns i en undermapp i Blob Storage väljer du **Bläddra** för att välja sökvägen.
    * Lägg till "/* *" i sökvägen om du vill inkludera alla filer i undermappar för den valda sökvägen.
    * Lägg till "* */* . *" för att inkludera alla data i den aktuella behållaren och dess undermappar.
1. Ange en beskrivning för din data uppsättning.
1. Välj **Nästa**.
1. Bekräfta informationen. Välj **tillbaka** om du vill ändra inställningarna eller **skapa** för att skapa data uppsättningen.


### <a name="create-a-dataset-from-uploaded-data"></a>Skapa en data uppsättning från överförda data

Så här överför du dina data direkt:

1. Välj **skapa en data uppsättning**  >  **från lokala filer**.
1. Tilldela ett **namn** till din data uppsättning.
1. Välj "Arkiv" som **data uppsättnings typ**.
1. *Valfritt:* Välj **Avancerade inställningar** för att anpassa data lagret, behållaren och sökvägen till dina data.
1. Välj **Bläddra** för att välja de lokala filer som ska laddas upp.
1. Ange en beskrivning av din data uppsättning.
1. Välj **Nästa**.
1. Bekräfta informationen. Välj **tillbaka** om du vill ändra inställningarna eller **skapa** för att skapa data uppsättningen.

Data överförs till standard-BLOB-arkivet ("workspaceblobstore") på din Machine Learning-arbetsyta.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a> Konfigurera stegvis uppdatering

Om du planerar att lägga till nya avbildningar i data uppsättningen använder du stegvis uppdatering för att lägga till dessa nya bilder i projektet.   När **stegvis uppdatering** har Aktiver ATS kontrol leras data uppsättningen regelbundet för nya avbildningar som ska läggas till i ett projekt, baserat på slut för ande frekvensen för etiketter.   Sök efter nya data stoppas när projektet innehåller maximalt 500 000 bilder.

Om du vill lägga till fler avbildningar i projektet använder [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ladda upp till lämplig mapp i blob-lagringen. 

Markera kryss rutan om du vill **Aktivera stegvis uppdatering** när du vill att projektet ska övervakas kontinuerligt för nya data i data lagret. Den här informationen kommer att hämtas till projektet en gång om dagen när den är aktive rad.

Avmarkera den här kryss rutan om du inte vill att nya avbildningar som visas i data lagret ska läggas till i projektet.

Du kan hitta tidsstämpeln för den senaste uppdateringen i avsnittet **stegvis uppdatering** på fliken **information** för ditt projekt.


## <a name="specify-label-classes"></a>Ange etikett klasser

På sidan **etikett klasser** anger du uppsättningen klasser för att kategorisera dina data. Gör detta noggrant eftersom etiketternas precision och hastighet kommer att påverkas av deras möjlighet att välja bland klasserna. I stället för att bokstavera hela släktet och arterna för växter eller djur använder du exempelvis en fältkod eller förkorta släktet.

Ange en etikett per rad. Använd **+** knappen för att lägga till en ny rad. Om du har fler än 3 eller 4 etiketter men färre än 10 kanske du vill lägga till prefixet i namnen med siffror ("1:", "2:") så att etiketterna kan använda sig av siffer tangenterna för att påskynda sitt arbete.

## <a name="describe-the-labeling-task"></a>Beskriv etikettens uppgift

Det är viktigt att tydligt förklara etikettens uppgift. På sidan **etiketting-instruktioner** kan du lägga till en länk till en extern plats för etikett instruktioner eller ange instruktioner i redigerings rutan på sidan. Se till att instruktionerna är orienterade och lämpligt för mål gruppen. Tänk på följande frågor:

* Vilka etiketter ser de ut och hur kommer de att välja mellan dem? Finns det en referens text att referera till?
* Vad ska de göra om ingen etikett verkar vara lämplig?
* Vad ska de göra om flera etiketter förefaller lämpliga?
* Vilket konfidens tröskelvärde bör gälla för en etikett? Vill du ha deras "bästa gissning" om de inte är säkra?
* Vad ska de göra med delvis Occluded eller överlappande objekt av intresse?
* Vad ska de göra om ett objekt av intresse klipps av bildens kant?
* Vad ska de göra när de skickar en etikett om de tror att de gör ett misstag?

För avgränsnings rutor är viktiga frågor:

* Hur definieras avgränsnings rutan för den här uppgiften? Ska det vara helt på insidan av objektet eller ska det vara på utsidan? Bör den beskäras så tätt som möjligt, eller är vissa godkännanden godtagbara?
* Vilken nivå av vård och konsekvens förväntar du dig att etiketterna ska tillämpas i definitions rutor?
* Hur gör jag för att märka objektet som visas delvis i bilden? 
* Hur gör jag för att märka objektet som delvis täcks av andra objekt?

>[!NOTE]
> Observera att etiketterna kan välja de första 9 etiketterna genom att använda siffer nycklar 1-9.

## <a name="use-ml-assisted-labeling"></a>Använd ML-etikettering

Med hjälp av sidan **ml-märkning** kan du utlösa automatiska maskin inlärnings modeller för att påskynda etiketten. I början av ditt projekt som du har märkt, sorteras bilderna i en slumpmässig ordning för att minska potentiell kompensation. Men eventuella förskjutningar som förekommer i data uppsättningen visas i den tränade modellen. Om till exempel 80% av dina avbildningar är av en enda klass kommer cirka 80% av de data som används för att träna modellen att vara av klassen. Den här utbildningen omfattar inte aktiv inlärning.

Välj *Enable ml-etikettering* och ange en GPU för att aktivera assisterad märkning, som består av två faser:
* Klustring
* För markering

Det exakta antalet etiketterade bilder som krävs för att kunna starta assisterad etikettering är inte ett fast nummer.  Detta kan variera markant från ett etikett projekt till ett annat. För vissa projekt är det ibland möjligt att se förmärknings-eller kluster aktiviteter efter att 300-bilder har märkts manuellt. ML-märkning använder en teknik som kallas *överförings Inlärning*, som använder en förtränad modell för att komma igång med inlärnings processen. Om din data uppsättnings klasser liknar dem i den förtränade modellen kan det finnas etiketter som är tillgängliga efter några hundra manuellt märkta bilder. Om din data uppsättning skiljer sig avsevärt från de data som används för att förträna modellen, kan det ta mycket längre tid.

Eftersom de slutliga etiketterna fortfarande är beroende av inmatade Labeler, kallas den här tekniken ibland *mänsklig i slingan* .

> [!NOTE]
> ML data märkning för att hjälpa dig stöder inte standard lagrings konton som skyddas bakom ett [virtuellt nätverk](how-to-network-security-overview.md). Du måste använda ett lagrings konto som inte är standard för data märkning med ML-stöd. Lagrings kontot som inte är standard kan skyddas bakom det virtuella nätverket. 

### <a name="clustering"></a>Klustring

När ett visst antal etiketter har skickats börjar Machine Learning-modellen för bild klassificering att gruppera liknande bilder.  Dessa liknande bilder presenteras för etiketter på samma skärm för att påskynda manuell taggning. Klustring är särskilt användbart när Labeler visar ett rutnät med 4, 6 eller 9 bilder. 

När en maskin inlärnings modell har tränats på dina manuellt märkta data, trunkeras modellen till det sista fullständigt anslutna lagret. Omärkta bilder skickas sedan genom den trunkerade modellen i en process som kallas "inbäddning" eller "funktionalisering". Detta bäddar in varje bild i ett högt dimensionellt utrymme som definieras av detta modell lager. Bilder som är närmsta grannar i utrymmet används för kluster uppgifter. 

Kluster fasen visas inte för objekt identifierings modeller.

### <a name="prelabeling"></a>För markering

När tillräckligt med bild etiketter har skickats används en klassificerings modell för att förutsäga bild taggar. Eller en objekt identifierings modell används för att förutsäga avgränsnings rutor. Labeler ser nu sidor som innehåller förväntade etiketter som redan finns på varje bild. För objekt identifiering visas även de förväntade rutorna. Uppgiften granskar sedan dessa förutsägelser och korrigerar eventuella felmärkta bilder innan sidan skickas.  

När en maskin inlärnings modell har tränats på dina manuellt märkta data, utvärderas modellen i en test uppsättning med manuellt märkta bilder för att fastställa dess exakthet på en rad olika konfidens trösklar. Den här utvärderings processen används för att fastställa ett konfidens tröskelvärde över vilket modellen är tillräckligt korrekt för att Visa före-etiketter. Modellen utvärderas sedan mot omärkta data. Bilder med förutsägelser mer tryggare än det här tröskelvärdet används för för märkning.

## <a name="initialize-the-labeling-project"></a>Initiera ett etikettande projekt

När du har initierat projektet är vissa delar av projektet oföränderliga. Du kan inte ändra aktivitets typen eller data uppsättningen. Du *kan* ändra etiketter och URL: en för uppgifts beskrivningen. Granska inställningarna noggrant innan du skapar projektet. När du har skickat projektet kommer du tillbaka till start sidan för **data etiketter** som visar projektet som **initieras**.

> [!NOTE]
> Den här sidan kanske inte uppdateras automatiskt. Efter en paus uppdaterar du sidan manuellt för att se projektets status som **skapats**.

## <a name="run-and-monitor-the-project"></a>Köra och övervaka projektet
När du har initierat projektet börjar Azure att köra det. Välj projektet på huvud sidan för **data etiketter** för att se information om projektet

Om du vill pausa eller starta om projektet kan du växla **körnings** status längst upp till höger. Du kan bara märka data när projektet körs.

### <a name="dashboard"></a>Instrumentpanel

Fliken **instrument panel** visar förloppet för etikett uppgiften.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Instrument panel för data etiketter":::

I förlopps diagrammet visas hur många objekt som har märkts och hur många som ännu inte har gjorts.  Objekt som väntar kan vara:

* Har ännu inte lagts till i en aktivitet
* Ingår i en uppgift som har tilldelats en Labeler men ännu inte slutförts 
* I en kö med aktiviteter som ännu ska tilldelas

I området i mitten visas en kö med aktiviteter som ännu inte har tilldelats. När ML-etiketter är inaktiverat visar det här avsnittet antalet manuella uppgifter som ska tilldelas. När ML-etikettering är på visas även följande:

* Uppgifter som innehåller klustrade objekt i kön
* Uppgifter som innehåller förmärkta objekt i kön

När ML-etikettering är aktive rad visas dessutom en liten förlopps indikator när nästa övnings körning sker.  Avsnittet experiment innehåller länkar för var och en av Machine Learning-körningarna.

* Öva – tågen en modell för att förutsäga etiketterna
* Verifiering – bestämmer om den här modellens förutsägelse ska användas för att före etikettera objekten 
* Härledning – förutsägelse körning för nya objekt
* Funktionalisering-kluster objekt (endast för bild klassificerings projekt)

På den högra sidan finns en fördelning av etiketterna för de aktiviteter som är slutförda.  Kom ihåg att i vissa projekt typer kan ett objekt ha flera etiketter, vilket innebär att det totala antalet etiketter kan vara större än det totala antalet objekt.

### <a name="data-tab"></a>Fliken data

På fliken **data** kan du se din data uppsättning och granska etiketterade data. Om du ser felaktigt märkta data markerar du den och väljer **avvisa**, vilket tar bort etiketterna och sätter tillbaka dem i den omärkta kön.

### <a name="details-tab"></a>Fliken information

Visa information om projektet.  På den här fliken kan du:

* Visa projekt information och indata-datauppsättningar
* Aktivera stegvis uppdatering
* Visa information om den lagrings behållare som används för att lagra etiketterade utdata i projektet
* Lägg till etiketter i projektet
* Redigera instruktioner som du ger dina etiketter
* Redigera information om ML-etiketter, inklusive aktivera/inaktivera

### <a name="access-for-labelers"></a>Åtkomst för etiketter

Alla som har åtkomst till din arbets yta kan märka data i projektet.  Du kan också anpassa behörigheterna för dina etiketter så att du kan komma åt etiketter, men inte andra delar av arbets ytan eller att märka projektet.  Mer information finns i [Hantera åtkomst till en Azure Machine Learning-arbetsyta](how-to-assign-roles.md)och lär dig hur du skapar den [anpassade rollen Labeler](how-to-assign-roles.md#labeler).

## <a name="add-new-label-class-to-a-project"></a>Lägg till en ny etikett klass i ett projekt

Under märkningen kanske du upptäcker att ytterligare etiketter behövs för att klassificera dina avbildningar.  Du kanske exempelvis vill lägga till etiketten "okänd" eller "annan" för att ange förvirrande bilder.

Följ dessa steg om du vill lägga till en eller flera etiketter i ett projekt:

1. Välj projektet på huvud sidan för **data etiketter** .
1. Längst upp till höger på sidan växlar du **igång** till **pausad** för att stoppa etiketter från deras aktivitet.
1. Välj fliken **Information**.
1. I listan till vänster väljer du **etikett klasser**.
1. Överst i listan väljer du **+ Lägg till etiketter** ![ Lägg till en etikett](media/how-to-create-labeling-projects/add-label.png)
1. I formuläret lägger du till din nya etikett och väljer hur du vill fortsätta.  Eftersom du har ändrat de tillgängliga etiketterna för en bild väljer du hur du vill behandla redan märkta data:
    * Börja om och ta bort alla befintliga etiketter.  Välj det här alternativet om du vill starta etiketter från början med den nya fullständiga uppsättningen etiketter. 
    * Börja om och Behåll alla befintliga etiketter.  Välj det här alternativet om du vill markera alla data som omärkta, men Behåll de befintliga etiketterna som en standardtagg för bilder som tidigare har märkts.
    * Fortsätt och Behåll alla befintliga etiketter. Välj det här alternativet om du vill behålla alla data som redan är märkta som de är och börja använda den nya etiketten för data som ännu inte har märkts.
1. Ändra sidan instruktioner efter behov för de nya etiketterna.
1. När du har lagt till alla nya etiketter visas **överst till höger på sid växlingen** till **Kör** för att starta om projektet.  

## <a name="export-the-labels"></a>Exportera etiketterna

Du kan när som helst exportera etikett data för Machine Learning experimentering. Bild etiketter kan exporteras i [Coco-format](http://cocodataset.org/#format-data) eller som en [Azure Machine Learning data uppsättning med etiketter](how-to-use-labeled-dataset.md). Använd knappen **Exportera** på sidan **projekt information** i ditt projekt med etiketter.

COCO-filen skapas i standard-BLOB-arkivet för Azure Machine Learning arbets ytan i en mapp i *export-/Coco*. Du kan komma åt den exporterade Azure Machine Learning data uppsättningen i avsnittet **data uppsättningar** i Machine Learning. På sidan data uppsättnings information finns också exempel kod för att få åtkomst till dina etiketter från python.

![Exporterad data uppsättning](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Nästa steg

* [Självstudie: skapa din första bild klassificerings etikett för projekt](tutorial-labeling.md).
* Etikett bilder för [bild klassificering eller objekt identifiering](how-to-label-images.md)
* Läs mer om [Azure Machine Learning och Machine Learning Studio (klassisk)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
