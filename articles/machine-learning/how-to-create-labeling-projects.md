---
title: Skapa ett projekt för etikettering av data
titleSuffix: Azure Machine Learning
description: Lär dig hur du skapar och kör etiketteringsprojekt för att tagga data för maskininlärning. Använd ML-assisterad märkning, eller mänsklig i loopetiketter, för att underlätta uppgiften.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: ff1783dd31b8139940e56d24ae82866b428838b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861165"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Skapa ett dataetikettprojekt och exportera etiketter 

Lär dig hur du skapar och kör dataetiketteringsprojekt för att tagga data i Azure Machine Learning.  Använd maskininlärningsassisterad dataetikettering eller märkning av människor i slingan för att underlätta uppgiften.


## <a name="data-labeling-capabilities"></a>Funktioner för dataetikettering

> [!Important]
> Dataavbildningar måste vara tillgängliga i ett Azure-blobdatalager. (Om du inte har ett befintligt datalager kan du ladda upp bilder när projektet skapas.)

Azure Machine Learning är en central plats för att skapa, hantera och övervaka etiketteringsprojekt:
 - Samordna data, etiketter och teammedlemmar för att effektivt hantera etiketteringsuppgifter. 
 - Spårar förlopp och underhåller kön med ofullständiga etiketteringsuppgifter.
 - Starta och stoppa projektet och kontrollera etikettförloppet.
 - Granska etiketterade data och exportera som är märkta iFORMAT ELLER som en Azure Machine Learning datauppsättning.

## <a name="prerequisites"></a>Förutsättningar

* De data som du vill märka, antingen i lokala filer eller i Azure Blob Storage.
* Den uppsättning etiketter som du vill använda.
* Anvisningar för etikettering.
* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://aka.ms/AMLFree) innan du börjar.
* En Machine Learning arbetsyta. Se [Skapa en Azure Machine Learning arbetsyta.](how-to-manage-workspace.md)

## <a name="create-a-data-labeling-project"></a>Skapa ett projekt för etikettering av data

Etiketteringsprojekt administreras från Azure Machine Learning. Du kan använda **sidan Etiketteringsprojekt** för att hantera dina projekt.

Om dina data redan finns i Azure Blob Storage bör du göra dem tillgängliga som ett datalager innan du skapar etiketteringsprojektet. Ett exempel på hur du använder ett datalager finns i [Självstudie: Skapa ditt första projekt för bildklassificeringsetikettering.](tutorial-labeling.md)

Om du vill skapa ett projekt väljer du **Lägg till projekt**. Ge projektet ett lämpligt namn och välj **Etikettuppgiftstyp**. Projektnamn kan inte återanvändas, även om projektet tas bort i framtiden.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Guide för att skapa etiketter för projekt":::

* Välj **Bildklassificering med flera** klasser för projekt när du bara vill använda en enskild etikett *från* en uppsättning etiketter till en bild.
* Välj **Bildklassificering med flera** etiketter för projekt när du vill använda en eller *flera* etiketter från en uppsättning etiketter till en bild. Ett foto av en hund kan till exempel märkas med både *hund och* *hund.*
* Välj **Objektidentifiering (Bounding Box)** för projekt när du vill tilldela en etikett och en avgränsare för varje objekt i en bild.
* Välj **Instanssegmentering (Polygon)** för projekt när du vill tilldela en etikett och rita en polygon runt varje objekt i en bild.

Välj **Nästa** när du är redo att fortsätta.

## <a name="specify-the-data-to-label"></a>Ange de data som ska märkas

Om du redan har skapat en datauppsättning som innehåller dina data väljer du den i listrutan Välj **en** befintlig datauppsättning. Du kan också välja **Skapa en datauppsättning** om du vill använda ett befintligt Azure-datalager eller ladda upp lokala filer.

> [!NOTE]
> Ett projekt får inte innehålla fler än 500 000 bilder.  Om din datauppsättning har fler kommer endast de första 500 000 bilderna att läsas in.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Skapa en datauppsättning från ett Azure-datalager

I många fall går det bra att bara ladda upp lokala filer. Men [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) ett snabbare och mer robust sätt att överföra en stor mängd data. Vi rekommenderar Storage Explorer som standard för att flytta filer.

Så här skapar du en datauppsättning från data som du redan har lagrat i Azure Blob Storage:

1. Välj **Skapa en datauppsättning**  >  **från datalager**.
1. Tilldela **datauppsättningen** ett namn.
1. Välj **Fil** som **Datauppsättningstyp.**  Endast fildatauppsättningstyper stöds.
1. Välj datalager.
1. Om dina data finns i en undermapp i bloblagringen väljer du **Bläddra för** att välja sökvägen.
    * Lägg till "/**" i sökvägen för att inkludera alla filer i undermappar för den valda sökvägen.
    * Lägg till "* */* .*" för att inkludera alla data i den aktuella containern och dess undermappar.
1. Ange en beskrivning för din datauppsättning.
1. Välj **Nästa**.
1. Bekräfta informationen. Välj **Tillbaka** för att ändra inställningarna eller Skapa **för** att skapa datauppsättningen.


### <a name="create-a-dataset-from-uploaded-data"></a>Skapa en datauppsättning från uppladdade data

Så här laddar du upp dina data direkt:

1. Välj **Skapa en datauppsättning**  >  **från lokala filer**.
1. Tilldela **datauppsättningen** ett namn.
1. Välj "File" (Fil) som **Datamängdstyp**.
1. *Valfritt:* Välj **Avancerade inställningar** för att anpassa datalager, container och sökväg till dina data.
1. Välj **Bläddra för** att välja de lokala filer som ska laddas upp.
1. Ange en beskrivning av din datauppsättning.
1. Välj **Nästa**.
1. Bekräfta informationen. Välj **Tillbaka** för att ändra inställningarna eller **Skapa för** att skapa datauppsättningen.

Data laddas upp till standardbloblagret ("workspaceblobstore") på din Machine Learning arbetsyta.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a> Konfigurera inkrementell uppdatering

Om du planerar att lägga till nya bilder i datauppsättningen använder du inkrementell uppdatering för att lägga till de nya bilderna i projektet.   När **inkrementell** uppdatering är aktiverat kontrolleras datauppsättningen regelbundet för att nya avbildningar ska läggas till i ett projekt, baserat på frekvensen för slutförande av etiketter.   Kontrollen av nya data stoppas när projektet innehåller högst 500 000 bilder.

Om du vill lägga till fler bilder i projektet [använder Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ladda upp till lämplig mapp i bloblagringen. 

Markera kryssrutan Aktivera **inkrementell** uppdatering när du vill att projektet kontinuerligt ska övervaka nya data i datalagringen. Dessa data hämtas till projektet en gång om dagen när det är aktiverat, så du måste vänta efter att du har lagt till nya data i datalagringen innan de visas i projektet.  Du kan se en tidsstämpel för när data senast uppdaterades i avsnittet **Inkrementell** uppdatering **på fliken** Information för projektet.

Avmarkera den här rutan om du inte vill att nya avbildningar som visas i datalagringen ska läggas till i projektet.

## <a name="specify-label-classes"></a>Ange etikettklasser

På sidan **Etikettklasser** anger du den uppsättning klasser som ska kategorisera dina data. Etiketternas noggrannhet och hastighet påverkas av deras möjlighet att välja bland klasserna. I stället för att till exempel stava ut hela familjen och arterna för plantor eller djur, använder du en fältkod eller förkortar kattödla.

Ange en etikett per rad. Använd knappen **+** för att lägga till en ny rad. Om du har fler än 3 eller 4 etiketter men färre än 10 kanske du vill lägga till siffror ("1: ", "2: ") så att etiketterna kan använda nummernycklarna för att påskynda arbetet.

## <a name="describe-the-data-labeling-task"></a>Beskriv uppgiften för dataetikettering

Det är viktigt att tydligt förklara märkningsuppgiften. På sidan **Etikettinstruktioner** kan du lägga till en länk till en extern webbplats för etiketteringsanvisningar eller ange instruktioner i redigeringsrutan på sidan. Håll instruktionerna uppgiftsorienterade och lämpliga för åhörarna. Tänk på följande frågor:

* Vilka etiketter kommer de att se och hur kommer de att välja bland dem? Finns det en referenstext att referera till?
* Vad ska de göra om ingen etikett verkar lämplig?
* Vad ska de göra om flera etiketter verkar lämpliga?
* Vilket tröskelvärde för konfidens ska de gälla för en etikett? Vill du ha deras "bästa gissning" om de inte är säkra?
* Vad ska de göra med delvis ockluderade eller överlappande objekt av intresse?
* Vad ska de göra om ett objekt av intresse klipps av bildens kant?
* Vad ska de göra när de har skickat en etikett om de tror att de har gjort ett misstag?

Viktiga frågor för avgränsade rutor är:

* Hur definieras begränsningsrutan för den här uppgiften? Bör det vara helt och hållet inuti objektet, eller bör det finnas på ytan? Bör den beskäras så nära som möjligt eller är viss behörighet acceptabel?
* Vilken nivå av vård och konsekvens förväntar du dig att etiketterna ska tillämpas på när du definierar avgränsningsrutor?
* Hur märks objektet som delvis visas i bilden? 
* Hur märks objektet som delvis omfattas av ett annat objekt?

>[!NOTE]
> Observera att etiketterna kommer att kunna välja de första 9 etiketterna med hjälp av nummernycklarna 1–9.

## <a name="use-ml-assisted-data-labeling"></a>Använda ML-assisterad dataetikettering

På **sidan ML-assisterad etikettering** kan du utlösa automatiska maskininlärningsmodeller för att påskynda etiketteringsuppgiften. I början av ditt etiketteringsprojekt blandas bilderna i en slumpmässig ordning för att minska potentiell bias. Eventuella bias som finns i datauppsättningen återspeglas dock i den tränade modellen. Om 80 % av dina bilder till exempel finns i en enda klass kommer cirka 80 % av de data som används för att träna modellen att vara av den klassen. Den här utbildningen omfattar inte aktiv inlärning.


Välj *Aktivera ML-assisterad etikettering* och ange en GPU för att aktivera assisterad etikettering, som består av två faser:
* Klustring
* Prelabeling

Det exakta antalet märkta bilder som krävs för att starta assisterad märkning är inte ett fast tal.  Detta kan variera avsevärt från ett etiketteringsprojekt till ett annat. För vissa projekt går det ibland att se prelabel- eller klusteruppgifter när 300 avbildningar har märkts manuellt. ML-assisterad etikettering använder en teknik *som kallas Transfer Learning*, som använder en förtränad modell för att komma igång med träningsprocessen. Om datamängdens klasser liknar dem i den förtränade modellen kan företiketter vara tillgängliga efter bara några hundra manuellt märkta bilder. Om datamängden skiljer sig avsevärt från de data som används för att träna modellen kan det ta mycket längre tid.

Eftersom de slutliga etiketterna fortfarande förlitar sig på indata från etiketteraren kallas den här tekniken *ibland för människa i loopetiketter.*

> [!NOTE]
> ML-assisterad datamärkning stöder inte standardlagringskonton som skyddas bakom ett [virtuellt nätverk](how-to-network-security-overview.md). Du måste använda ett lagringskonto som inte är standard för ml-assisterad dataförseelse. Lagringskontot som inte är standard kan skyddas bakom det virtuella nätverket.

### <a name="clustering"></a>Klustring

När ett visst antal etiketter har skickats börjar maskininlärningsmodellen för bildklassificering gruppera liknande bilder.  Dessa liknande bilder visas för etiketterna på samma skärm för att påskynda manuell taggning. Klustring är särskilt användbart när etiketteraren visar ett rutnät med 4, 6 eller 9 bilder. 

När en maskininlärningsmodell har tränats på manuellt märkta data trunkeras modellen till det senaste helt anslutna lagret. Omärkta bilder skickas sedan genom den trunkerade modellen i en process som ofta kallas "inbäddning" eller "featurization". Detta bäddar in varje bild i ett högdimensionellt utrymme som definieras av det här modellskiktet. Avbildningar som är närmaste grannar i utrymmet används för klustringsuppgifter. 

Klustringsfasen visas inte för objektidentifieringsmodeller.

### <a name="prelabeling"></a>Förmärkning

När tillräckligt många bildetiketter har skickats används en klassificeringsmodell för att förutsäga bildtaggar. Eller så används en objektidentifieringsmodell för att förutsäga begränsningsrutor. Etiketten ser nu sidor som innehåller förutsagda etiketter som redan finns på varje bild. För objektidentifiering visas också förutsagda rutor. Uppgiften är sedan att granska dessa förutsägelser och korrigera felmärkta bilder innan sidan skickas.  

När en maskininlärningsmodell har tränats på manuellt märkta data utvärderas modellen på en testuppsättning med manuellt märkta bilder för att fastställa dess noggrannhet vid olika konfidenströsklar. Den här utvärderingsprocessen används för att fastställa ett konfidenströskelvärde över vilket modellen är tillräckligt noggrann för att visa företiketter. Modellen utvärderas sedan mot omärkta data. Bilder med förutsägelser som är säkrare än det här tröskelvärdet används för företikettering.

## <a name="initialize-the-data-labeling-project"></a>Initiera dataetiketteringsprojektet

När etiketteringsprojektet har initierats är vissa aspekter av projektet oföränderliga. Du kan inte ändra uppgiftstyp eller datauppsättning. Du *kan* ändra etiketter och URL:en för uppgiftsbeskrivningen. Granska inställningarna noggrant innan du skapar projektet. När du har skickat projektet kommer du tillbaka till startsidan **för dataetiketter,** som visar projektet som **Initierar**.

> [!NOTE]
> Den här sidan kanske inte uppdateras automatiskt. Efter en paus uppdaterar du därför sidan manuellt för att se projektets status som **Skapad.**

## <a name="run-and-monitor-the-project"></a>Köra och övervaka projektet
När du har initierat projektet börjar Azure köra det. Välj projektet på huvudsidan **för dataetikettering** för att se information om projektet

Om du vill pausa eller starta om projektet ändrar du **statusen Körs** längst upp till höger. Du kan bara märka data när projektet körs.

### <a name="dashboard"></a>Instrumentpanel

Fliken **Instrumentpanel** visar etikettuppgiftens förlopp.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Instrumentpanel för dataetiketter":::

Förloppsdiagrammet visar hur många objekt som har märkts och hur många som ännu inte har gjorts.  Väntande objekt kan vara:

* Har ännu inte lagts till i en uppgift
* Ingår i en uppgift som har tilldelats en etiketterare men som ännu inte har slutförts 
* I kön med uppgifter som ännu inte har tilldelats

I det mellersta avsnittet visas kön med aktiviteter som ännu inte har tilldelats. När ML-assisterad etikettering är inaktiverat visar det här avsnittet antalet manuella uppgifter som ska tilldelas. När ML-assisterad etikettering är på visas även:

* Uppgifter som innehåller klustrade objekt i kön
* Uppgifter som innehåller förmärkta objekt i kön

När ML-assisterad etikettering är aktiverat visas dessutom en liten förloppsstapel när nästa träningskörning ska ske.  Avsnitten Experiment innehåller länkar för var och en av maskininlärningskörningarna.

* Träning – tränar en modell för att förutsäga etiketterna
* Validering – avgör om den här modellens förutsägelse ska användas för att företikettera objekten 
* Slutsatsledning – förutsägelsekörning för nya objekt
* Featurisering – klusterobjekt (endast för bildklassificeringsprojekt)

På höger sida finns en distribution av etiketterna för de uppgifter som har slutförts.  Kom ihåg att i vissa projekttyper kan ett objekt ha flera etiketter, vilket innebär att det totala antalet etiketter kan vara större än det totala antalet objekt.

### <a name="data-tab"></a>Fliken Data

På fliken **Data** kan du se din datauppsättning och granska märkta data. Om du ser felaktigt märkta data markerar du dem och väljer **Avvisa,** vilket tar bort etiketterna och lägger tillbaka data i den omärkta kön.

### <a name="details-tab"></a>Fliken Information

Visa information om projektet.  På den här fliken kan du:

* Visa projektinformation och indatauppsättningar
* Aktivera inkrementell uppdatering
* Visa information om lagringscontainern som används för att lagra märkta utdata i projektet
* Lägga till etiketter i projektet
* Redigera de instruktioner du ger etiketterna
* Redigera information om ML-assisterad etikettering, inklusive aktivera/inaktivera

### <a name="access-for-labelers"></a>Åtkomst för etiketter

Alla som har åtkomst till din arbetsyta kan märka data i projektet.  Du kan också anpassa behörigheterna för etiketter så att de kan komma åt etiketter men inte andra delar av arbetsytan eller ditt etiketteringsprojekt.  Mer information finns i Hantera [åtkomst till en Azure Machine Learning arbetsyta](how-to-assign-roles.md)och lär dig hur du skapar den anpassade rollen för [etiketterare.](how-to-assign-roles.md#labeler)

## <a name="add-new-label-class-to-a-project"></a>Lägga till en ny etikettklass i ett projekt

Under processen för dataetikettering kanske du upptäcker att ytterligare etiketter behövs för att klassificera dina bilder.  Du kanske till exempel vill lägga till etiketten "Okänt" eller "Annat" för att visa förvirrande bilder.

Använd de här stegen för att lägga till en eller flera etiketter i ett projekt:

1. Välj projektet på huvudsidan **För dataetiketter.**
1. Längst upp till höger på sidan växlar du Körs **till** **Pausad för** att stoppa etiketterna från aktiviteten.
1. Välj fliken **Information**.
1. I listan till vänster väljer du **Etikettklasser**.
1. Längst upp i listan väljer du + Lägg **till etiketter Lägg** till en ![ etikett](media/how-to-create-labeling-projects/add-label.png)
1. Lägg till den nya etiketten i formuläret och välj hur du vill fortsätta.  Eftersom du har ändrat de tillgängliga etiketterna för en bild väljer du hur data som redan är märkta ska behandlas:
    * Börja om och ta bort alla befintliga etiketter.  Välj det här alternativet om du vill börja etikettera från början med den nya fullständiga uppsättningen etiketter. 
    * Börja om och behålla alla befintliga etiketter.  Välj det här alternativet om du vill markera alla data som omärkta, men behåll de befintliga etiketterna som en standardtagg för bilder som tidigare har märkts.
    * Fortsätt och behålla alla befintliga etiketter. Välj det här alternativet om du vill att alla data ska vara märkta som de är och börja använda den nya etiketten för data som ännu inte är märkta.
1. Ändra din instruktionssida efter behov för de nya etiketterna.
1. När du har lagt till alla nya etiketter startar du om projektet genom att längst **upp** till höger på sidan växla **Pausat** till Körs.  

## <a name="export-the-labels"></a>Exportera etiketterna

Du kan exportera etikettdata för Machine Learning när som helst. Bildetiketter kan exporteras i [FORMAT ELLER](http://cocodataset.org/#format-data) SOM en Azure Machine Learning [datauppsättning med etiketter](how-to-use-labeled-dataset.md). Använd knappen **Exportera** på sidan **Projektinformation** i ditt etiketteringsprojekt.

THEAL-filen skapas i standardbloblagret på Azure Machine Learning i en mapp inom *export/blob*. Du kan komma åt den Azure Machine Learning datauppsättningen i **avsnittet Datauppsättningar** i Machine Learning. Datauppsättningens informationssida innehåller också exempelkod för att komma åt dina etiketter från Python.

![Exporterad datauppsättning](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Felsökning

Använd de här tipsen om du ser något av dessa problem.

|Problem  |Lösning  |
|---------|---------|
|Endast datauppsättningar som skapats i blobdatalager kan användas.     |  Detta är en känd begränsning i den aktuella versionen.       |
|När projektet har skapats visas "Initierar" under en längre tid.     | Uppdatera sidan manuellt. Initieringen bör fortsätta med ungefär 20 datapunkter per sekund. Bristen på autorefresh är ett känt problem.         |
|När du granskar bilder visas inte nyligen märkta bilder.     |   Om du vill läsa in alla märkta bilder väljer du **knappen** Första. Knappen **Första** tar dig tillbaka till början av listan, men läser in alla märkta data.      |
|Om du trycker på Esc när du etiketterar för objektidentifiering skapas en etikett med noll storlek i det övre vänstra hörnet. Det går inte att skicka etiketter i det här tillståndet.     |   Ta bort etiketten genom att klicka på kryssmarkeringen bredvid den.  |
|Det går inte att tilldela en viss uppgiftsuppsättning till en viss etiketterare.     |   Det här är en känd begränsning i den aktuella versionen.  |

## <a name="next-steps"></a>Nästa steg

* [Självstudie: Skapa ditt första projekt för bildklassificeringsetikettering.](tutorial-labeling.md)
* Etikettbilder för [bildklassificering eller objektidentifiering](how-to-label-images.md)
* Läs mer [om Azure Machine Learning och Machine Learning Studio (klassisk)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
