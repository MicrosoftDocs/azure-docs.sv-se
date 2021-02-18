---
title: 'Snabb start: etikett formulär, träna en modell och analysera formulär med hjälp av verktyget exempel etikettering-formulär tolken'
titleSuffix: Azure Cognitive Services
description: I den här snabb starten använder du verktyget formulär igenkännings exempel etikettering för att manuellt etikettera formulär dokument. Sedan tränar du en anpassad dokument bearbetnings modell med de märkta dokumenten och använder modellen för att extrahera nyckel/värde-par.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: dokument bearbetning
ms.openlocfilehash: 3814eb9d71f38406533d0bcecf594bbdcd42d5b7
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095798"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
# <a name="train-a-form-recognizer-model-with-labels-using-the-sample-labeling-tool"></a>Träna en formulär igenkännings modell med etiketter med hjälp av verktyget för att använda exempel etiketter

I den här snabb starten använder du formulär tolken REST API med verktyget för att träna en anpassad dokument bearbetnings modell med manuellt märkta data. Se avsnittet [träna med etiketter](../overview.md#train-with-labels) i översikten om du vill veta mer om övervakad inlärning med formulär igenkänning.

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabb starten måste du ha:

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" skapa en formulär igenkännings resurs "  target="_blank"> skapa en formulär igenkännings resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
  * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Forms igenkännings-API: et. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
  * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* En uppsättning av minst sex formulär av samma typ. Du använder dessa data för att träna modellen och testa ett formulär. Du kan använda en [exempel data uppsättning](https://go.microsoft.com/fwlink/?linkid=2090451) (Hämta och extrahera *sample_data.zip*) för den här snabb starten. Ladda upp utbildnings-filerna till roten för en Blob Storage-behållare i ett Azure Storage konto med standard prestanda nivå.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="try-it-out"></a>Prova

Om du vill testa formulär tolkens exempel etikett verktyg online går du till FOTT- [webbplatsen](https://fott-preview.azurewebsites.net/).

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

> [!div class="nextstepaction"]
> [Prova färdiga modeller](https://fott-preview.azurewebsites.net/)

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!div class="nextstepaction"]
> [Prova färdiga modeller](https://fott.azurewebsites.net/)

---

Du behöver en Azure-prenumeration ([skapa en kostnads fri](https://azure.microsoft.com/free/cognitive-services)) och en resurs slut punkt för [formulär igenkänning](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) och en nyckel för att testa formulär igenkännings tjänsten.

## <a name="set-up-the-sample-labeling-tool"></a>Konfigurera verktyget för samplings etiketter

Du använder Docker-motorn för att köra verktyget för etikettering. Följ de här stegen för att konfigurera Docker-behållaren. En introduktion till grunderna för Docker och containrar finns i [Docker-översikt](https://docs.docker.com/engine/docker-overview/).

> [!TIP]
> Etikett verktyget för OCR-form är också tillgängligt som ett projekt med öppen källkod på GitHub. Verktyget är ett TypeScript-webbprogram som skapats med hjälp av reagerar + Redux. Om du vill veta mer eller Contribute kan du läsa [OCR-verktyget etiketting Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md#run-as-web-application) lagrings platsen. Om du vill prova verktyget online går du till [FOTT-webbplatsen](https://fott.azurewebsites.net/).

1. Börja med att installera Docker på en värddator. I den här guiden visas hur du använder en lokal dator som värd. Om du vill använda en Docker-värd tjänst i Azure kan du läsa mer i guiden [distribuera exempel etikett verktyg](../deploy-label-tool.md) .

   Värddatorn måste uppfylla följande maskin varu krav:

    | Container | Minimum | Rekommenderas|
    |:--|:--|:--|
    |Exempel på etikett verktyg|2 kärnor, 4 GB minne|4 kärnor, 8 GB minne|

   Installera Docker på datorn genom att följa lämpliga instruktioner för ditt operativ system:

   * [Windows](https://docs.docker.com/docker-for-windows/)
   * [macOS](https://docs.docker.com/docker-for-mac/)
   * [Linux](https://docs.docker.com/install/)

1. Hämta exempel på etiketting Tool-behållaren med `docker pull` kommandot.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```console
 docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```console
docker pull mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool
```

---
</br>
  3. Nu är du redo att köra behållaren med `docker run` .

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

```console
 docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview eula=accept
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```console
docker run -it -p 3000:80 mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool eula=accept
```

---

   Det här kommandot gör verktyget för exempel etiketter tillgängligt via en webbläsare. Gå till `http://localhost:3000`.

> [!NOTE]
> Du kan också märka dokument och träna modeller med hjälp av formulär tolken REST API. Om du vill träna och analysera med REST API, se [träna med etiketter med hjälp av REST API och python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="set-up-input-data"></a>Konfigurera indata

Se först till att alla utbildnings dokument har samma format. Om du har formulär i flera format ordnar du dem i undermappar efter format. När du tränar måste du dirigera API:et till en undermapp.

### <a name="configure-cross-domain-resource-sharing-cors"></a>Konfigurera en resurs delning mellan domäner (CORS)

Aktivera CORS på ditt lagrings konto. Välj ditt lagrings konto i Azure Portal och klicka på fliken **CORS** i det vänstra fönstret. Fyll i följande värden på den nedersta raden. Klicka sedan på **Spara** högst upp.

* Tillåtna ursprung = *
* Tillåtna metoder = \[ Markera alla\]
* Tillåtna huvuden = *
* Exponerade rubriker = *
* Max ålder = 200

> [!div class="mx-imgBorder"]
> ![CORS-konfiguration i Azure Portal](../media/label-tool/cors-setup.png)

## <a name="connect-to-the-sample-labeling-tool"></a>Ansluta till verktyget för att märka exempel

Verktyget för att skapa etiketter ansluter till en källa (där de ursprungliga formulären finns) och ett mål (där det exporterar de skapade etiketterna och utdata).

Anslutningar kan konfigureras och delas mellan projekt. De använder en utöknings bar leverantörs modell, så att du enkelt kan lägga till nya käll-/mål leverantörer.

Om du vill skapa en ny anslutning klickar du på ikonen **nya anslutningar** (plugg) i det vänstra navigerings fältet.

Fyll i fälten med följande värden:

* **Visnings namn** – anslutningens visnings namn.
* **Beskrivning** – din projekt beskrivning.
* **SAS-URL** – URL: en för signaturen för delad åtkomst (SAS) för din Azure Blob Storage-behållare. [!INCLUDE [get SAS URL](../includes/sas-instructions.md)]

   :::image type="content" source="../media/quickstarts/get-sas-url.png" alt-text="Hämtning av SAS-URL":::

:::image type="content" source="../media/label-tool/connections.png" alt-text="Anslutnings inställningar för verktyget för exempel etiketter.":::


## <a name="create-a-new-project"></a>Skapa ett nytt projekt

I exempel verktyget för etikettering kan du lagra dina konfigurationer och inställningar i projekt. Skapa ett nytt projekt och fyll i fälten med följande värden:

* **Visnings namn** – projektets visnings namn
* **Säkerhetstoken – vissa** projekt inställningar kan innehålla känsliga värden, t. ex. API-nycklar eller andra delade hemligheter. Varje projekt kommer att generera en säkerhetstoken som kan användas för att kryptera/dekryptera känsliga projekt inställningar. Du kan hitta säkerhetstoken i program inställningarna genom att klicka på kugg hjuls ikonen längst ned i det vänstra navigerings fältet.
* **Käll anslutning** – Azure Blob Storage-anslutningen som du skapade i det föregående steget som du vill använda för det här projektet.
* **Mappsökväg** – valfritt – om dina käll formulär finns i en mapp på BLOB-behållaren anger du mappnamnet här
* **Formulär tolkens tjänst-URI** – URL för formulär tolkens slut punkt.
* **API-nyckel** – ditt formulärs igenkännings prenumerations nyckel.
* **Beskrivning** – valfri projekt Beskrivning

:::image type="content" source="../media/label-tool/new-project.png" alt-text="Sidan ny projekt på exempel etikett verktyg.":::

## <a name="label-your-forms"></a>Etikettera formulär

När du skapar eller öppnar ett projekt öppnas huvud tag gen redigerarens fönster. Kod redigeraren består av tre delar:

* Ett förhands gransknings fönster som innehåller en rullnings bar lista med formulär från käll anslutningen.
* Huvud redigerings fönstret där du kan använda taggar.
* Rutan kod redigerings fönster där användare kan ändra, låsa, ordna om och ta bort taggar.

### <a name="identify-text-elements"></a>Identifiera text element

Klicka på **Kör OCR på alla filer** i den vänstra rutan för att få information om layoutinformation för varje dokument. Etikett verktyget ritar avgränsnings rutor runt varje text element.

Då visas även vilka tabeller som har extraherats automatiskt. Klicka på ikonen för tabell/rutnät till vänster i dokumentet för att se den extraherade tabellen. I den här snabb starten, eftersom tabell innehållet hämtas automatiskt, kommer vi inte att märka tabell innehållet, utan förlitar sig på den automatiserade extraheringen.

:::image type="content" source="../media/label-tool/table-extraction.png" alt-text="Tabell visualisering i exempel etikett verktyg.":::

### <a name="apply-labels-to-text"></a>Använd etiketter på text

Därefter skapar du taggar (etiketter) och tillämpar dem på de text element som du vill att modellen ska analysera.

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

1. Använd först rutan Tags Editor för att skapa de taggar som du vill identifiera:
   * Klicka **+** om du vill skapa en ny tagg.
   * Ange namnet på taggen.
   * Spara taggen genom att trycka på RETUR.
1. Klicka i huvud redigeraren för att välja ord från de markerade text elementen. I för _hands versionen av v 2.1_ , kan du också klicka för att välja _markerings märken_ som alternativ knappar och kryss rutor som nyckel värdes par. Formulär tolken identifierar om markerings markeringen är "markerad" eller "omarkerad" som värde.
1. Klicka på den tagg som du vill använda, eller tryck på motsvarande tangent bords nyckel. Siffer nycklarna tilldelas som kortkommandon för de första 10 taggarna. Du kan ändra ordning på taggarna med hjälp av upp-och nedpilens ikoner i rutan kod redigeraren.
    > [!Tip]
    > Tänk på följande när du har etiketter på formulären:
    >
    > * Du kan bara använda en tagg för varje markerat text element.
    > * Varje tagg kan bara tillämpas en gång per sida. Om ett värde visas flera gånger i samma formulär skapar du olika taggar för varje instans. Till exempel: "faktura nummer 1", "faktura nummer 2" och så vidare.
    > * Taggar kan inte sträcka sig över sidor.
    > * Etikettera värden som de visas i formuläret. Försök inte att dela upp ett värde i två delar med två olika taggar. Ett adress fält ska till exempel märkas med en enda tagg även om det sträcker sig över flera rader.
    > * Ta inte med nycklar i dina märkta fält &mdash; enbart värdena.
    > * Tabell data ska identifieras automatiskt och är tillgängliga i den slutgiltiga JSON-filen för utdata. Men om modellen inte kan identifiera alla tabell data kan du tagga dessa fält manuellt. Tagga varje cell i tabellen med en annan etikett. Om dina formulär har tabeller med varierande antal rader, se till att du tagga minst ett formulär med största möjliga tabell.
    > * Använd knapparna till höger om **+** för att söka, byta namn på, ändra ordningen och ta bort taggarna.
    > * Om du vill ta bort en tillämpad tagg utan att ta bort själva taggen väljer du den taggade rektangeln i dokument visningen och trycker på Delete-tangenten.
    >

### <a name="v20"></a>[v2.0](#tab/v2-0)

1. Använd först rutan Tags Editor för att skapa de taggar som du vill identifiera.
   1. Klicka **+** om du vill skapa en ny tagg.
   1. Ange namnet på taggen.
   1. Spara taggen genom att trycka på RETUR.
1. Klicka i huvud redigeraren för att välja ord från de markerade text elementen.
1. Klicka på den tagg som du vill använda, eller tryck på motsvarande tangent bords nyckel. Siffer nycklarna tilldelas som kortkommandon för de första 10 taggarna. Du kan ändra ordning på taggarna med hjälp av upp-och nedpilens ikoner i rutan kod redigeraren.
    > [!Tip]
    > Tänk på följande när du har etiketter på formulären:
    >
    > * Du kan bara använda en tagg för varje markerat text element.
    > * Varje tagg kan bara tillämpas en gång per sida. Om ett värde visas flera gånger i samma formulär skapar du olika taggar för varje instans. Till exempel: "faktura nummer 1", "faktura nummer 2" och så vidare.
    > * Taggar kan inte sträcka sig över sidor.
    > * Etikettera värden som de visas i formuläret. Försök inte att dela upp ett värde i två delar med två olika taggar. Ett adress fält ska till exempel märkas med en enda tagg även om det sträcker sig över flera rader.
    > * Ta inte med nycklar i dina märkta fält &mdash; enbart värdena.
    > * Tabell data ska identifieras automatiskt och är tillgängliga i den slutgiltiga JSON-filen för utdata. Men om modellen inte kan identifiera alla tabell data kan du tagga dessa fält manuellt. Tagga varje cell i tabellen med en annan etikett. Om dina formulär har tabeller med varierande antal rader, se till att du tagga minst ett formulär med största möjliga tabell.
    > * Använd knapparna till höger om **+** för att söka, byta namn på, ändra ordningen och ta bort taggarna.
    > * Om du vill ta bort en tillämpad tagg utan att ta bort själva taggen väljer du den taggade rektangeln i dokument visningen och trycker på Delete-tangenten.
>

---

:::image type="content" source="../media/label-tool/main-editor-2-1.png" alt-text="Huvud redigerings fönstret för exempel etikett verktyg.":::

Följ stegen ovan för att etikettera minst fem av formulären.

### <a name="specify-tag-value-types"></a>Ange tagg värde typer

Alternativt kan du ange den förväntade data typen för varje tagg. Öppna snabb menyn till höger om en tagg och välj en typ på menyn. Med den här funktionen kan detektions algoritmen göra vissa antaganden som förbättrar precisionen för text identifiering. Det säkerställer också att de identifierade värdena returneras i standardiserat format i de slutliga JSON-utdata. Värde typs information sparas i *fields.jspå* filen i samma sökväg som dina etikettfiler.

> [!div class="mx-imgBorder"]
> ![Val av värde typ med exempel etikett verktyg](../media/whats-new/value-type.png)

Följande värde typer och varianter stöds för närvarande:

* `string`
  * standard, `no-whitespaces``alphanumeric`

* `number`
  * objekt `currency`

* `date`
  * standard, `dmy` , `mdy` , `ymd`

* `time`
* `integer`
* `selectionMark` – _Nytt i v 2.1 – för hands version. 1!_

> [!NOTE]
> Se följande regler för datum format:
>
> Du måste ange ett format ( `dmy` , `mdy` , `ymd` ) för att datum formatet ska fungera.
>
> Följande tecken kan användas som datum avgränsare: `, - / . \` . Det går inte att använda blank steg som avgränsare. Exempel:
>
> * 01, 01, 2020
> * 01-01-2020
> * 01/01/2020
>
> Dagen och månaden kan skrivas med en eller två siffror och året kan vara två eller fyra siffror:
>
> * 1-1-2020
> * 1-01-20
>
> Om en datum sträng har åtta siffror är avgränsaren valfri:
>
> * 01012020
> * 01 01 2020
>
> Månaden kan också skrivas som fullständigt eller kort namn. Om namnet används är avgränsnings tecken valfria. Det här formatet kan dock tolkas mindre precis som andra.
>
> * 01/jan/2020
> * 01Jan2020
> * 01 jan 2020

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Klicka på ikonen träna i det vänstra fönstret för att öppna sidan utbildning. Klicka sedan på knappen **träna** för att börja träna modellen. När inlärnings processen har slutförts visas följande information:

* **Modell-ID** – ID: t för den modell som skapades och tränades. Varje utbildnings anrop skapar en ny modell med sitt eget ID. Kopiera den här strängen till en säker plats. du behöver det om du vill utföra förutsägelse samtal via [REST API](./client-library.md?pivots=programming-language-rest-api) eller [klient biblioteket](./client-library.md).
* **Genomsnittlig noggrannhet** – modellens genomsnittliga noggrannhet. Du kan förbättra modell precisionen genom att märka ytterligare formulär och utbildning igen för att skapa en ny modell. Vi rekommenderar att du börjar med att märka fem formulär och lägga till fler formulär efter behov.
* Listan med taggar och den uppskattade noggrannheten per tagg.


:::image type="content" source="../media/label-tool/train-screen.png" alt-text="Tränings visning.":::

Efter att utbildningen har slutförts undersöker du det **genomsnittliga noggrannhet** svärdet. Om den är låg bör du lägga till fler indatamängder och upprepa stegen ovan. Dokumenten som du redan har märkt kommer att finnas kvar i projekt indexet.

> [!TIP]
> Du kan också köra övnings processen med ett REST API-anrop. Information om hur du gör detta finns i [träna med etiketter med hjälp av python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="compose-trained-models"></a>Skapa utbildade modeller

### <a name="v21-preview"></a>[v 2.1 Preview](#tab/v2-1)

Med Model Compose kan du ha upp till 100 modeller med ett enda modell-ID. När du anropar Analysera med detta modell-ID klassificerar Formigenkänning först formen du har skickat, matchar den mot den modell som stämmer in bäst och returnerar sedan resultat för den modellen. Detta är användbart när inkommande former kan tillhöra en av flera mallar.

Om du vill skapa modeller i exempel etikett verktyget klickar du på ikonen skapa (sammanfoga pilen) till vänster. Till vänster väljer du de modeller som du vill skapa tillsammans. Modeller med ikonen pilar består redan av modeller.
Klicka på knappen "skapa". I popup-fönstret namnger du din nya sammansatta modell och klickar på "skapa". När åtgärden har slutförts ska den nya, färdiga modellen visas i listan.

:::image type="content" source="../media/label-tool/model-compose.png" alt-text="Skapa UX-vy för modell.":::

### <a name="v20"></a>[v2.0](#tab/v2-0)

Den här funktionen är för närvarande tillgänglig i v 2.1. förhandsgranskningsvyn.

---

## <a name="analyze-a-form"></a>Analysera ett formulär

Klicka på ikonen förutsägelse (ljus lampa) till vänster för att testa din modell. Ladda upp ett formulär dokument som du inte har använt i övnings processen. Klicka sedan på knappen **predict** till höger för att hämta nyckel/värde-förutsägelser för formuläret. Verktyget använder taggar i markerings rutor och rapporterar förtroendet för varje tagg.

> [!TIP]
> Du kan också köra analys-API: et med ett REST-anrop. Information om hur du gör detta finns i [träna med etiketter med hjälp av python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md).

## <a name="improve-results"></a>Förbättra resultaten

Beroende på den rapporterade noggrannheten kanske du vill göra ytterligare utbildningar för att förbättra modellen. När du har gjort en förutsägelse undersöker du konfidens intervallet för varje tillämpad tagg. Om inlärning svärdet för genomsnittlig precision var högt, men förtroende poängen är lågt (eller om resultatet är felaktigt) bör du lägga till filen som används för förutsägelse i tränings uppsättningen, ge den etiketten och träna igen.

Den rapporterade genomsnittliga noggrannheten, konfidens resultatet och faktisk noggrannhet kan vara inkonsekvent när de analyserade dokumenten skiljer sig från de som används i utbildningen. Tänk på att vissa dokument ser likadana ut när de visas av personer, men kan se distinkt ut för AI-modellen. Du kan till exempel träna med en formulär typ som har två varianter, där inlärnings uppsättningen består av 20% variation A och 80% variation B. Vid förutsägelsen kommer förtroende poängen för dokument variation A sannolikt att vara lägre.

## <a name="save-a-project-and-resume-later"></a>Spara ett projekt och återuppta senare

Om du vill återuppta projektet vid en annan tidpunkt eller i en annan webbläsare måste du spara ditt projekts säkerhetstoken och ange det igen senare.

### <a name="get-project-credentials"></a>Hämta autentiseringsuppgifter för projekt

Gå till sidan projekt inställningar (skjutreglage) och Anteckna namnet på säkerhetstoken. Gå sedan till dina program inställningar (kugg hjuls ikon) som visar alla säkerhetstoken i den aktuella webb läsar instansen. Sök efter ditt projekts säkerhetstoken och kopiera dess namn och nyckel värde till en säker plats.

### <a name="restore-project-credentials"></a>Återställ autentiseringsuppgifter för projekt

När du vill återuppta ditt projekt måste du först skapa en anslutning till samma Blob Storage-behållare. Upprepa stegen ovan för att göra detta. Gå sedan till sidan program inställningar (kugg hjuls ikon) och se om ditt projekts säkerhetstoken finns där. Om den inte är det lägger du till en ny säkerhetstoken och kopierar över ditt token-namn och nyckel från föregående steg. Klicka sedan på Spara inställningar.

### <a name="resume-a-project"></a>Återuppta ett projekt

Till sist går du till huvud sidan (House-ikonen) och klickar på öppna moln projekt. Välj sedan Blob Storage-anslutningen och välj din projekts *. fott* -fil. Programmet kommer att läsa in alla projekt inställningar eftersom det har säkerhetstoken.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder formulär tolkens exempel etikett verktyg för att träna en modell med manuellt märkta data. Om du vill bygga ditt eget verktyg för att märka tränings data använder du de REST-API: er som hanterar etiketterad data träning.

> [!div class="nextstepaction"]
> [Träna med etiketter med python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-labeled-data.md)

* [Vad är formigenkänning?](../overview.md)
* [Snabb start för formulär tolken](client-library.md)
