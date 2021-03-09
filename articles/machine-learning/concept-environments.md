---
title: Om Azure Machine Learning miljöer
titleSuffix: Azure Machine Learning
description: Lär dig mer om Machine Learning-miljöer, som möjliggör återare, gransknings bara, & bärbara Machine Learning-beroende definitioner för olika beräknings mål.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: BlackMist
ms.date: 11/16/2020
ms.openlocfilehash: 648dbe6b8d275c832f219cb6f3119ac0bc518a54
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102508477"
---
# <a name="what-are-azure-machine-learning-environments"></a>Vad är Azure Machine Learning miljöer?

Azure Machine Learning miljöer är en inkapsling av miljön där din Machine Learning-utbildning sker. De anger python-paket, miljövariabler och program varu inställningar kring dina utbildnings-och bedömnings skript. De anger också kör tider (python, Spark eller Docker). Miljöerna är hanterade och versioner av entiteter inom din Machine Learning arbets yta som möjliggör återskapande, gransknings bara och bärbara Machine Learning-arbetsflöden över flera olika beräknings mål.

Du kan använda ett `Environment` objekt i din lokala beräkning för att:
* Utveckla ditt utbildnings skript.
* Återanvänd samma miljö på Azure Machine Learning beräkning för modell träning i stor skala.
* Distribuera din modell med samma miljö.
* Gå tillbaka i miljön där en befintlig modell tränades.

Följande diagram illustrerar hur du kan använda ett enda `Environment` objekt i både din körnings konfiguration (för träning) och din konfiguration för konfigurations härledning och distribution (för webb tjänst distributioner).

![Diagram över en miljö i Machine Learning-arbetsflöde](./media/concept-environments/ml-environment.png)

Skripten miljö, Compute Target och Training bildar körnings konfigurationen: den fullständiga specifikationen av en utbildnings körning.

## <a name="types-of-environments"></a>Typer av miljöer

Miljöer kan i stort sett delas in i tre kategorier: *granskade*, *hanterade* och *systemhanterade*.

Granskade miljöer tillhandahålls av Azure Machine Learning och är tillgängliga i arbets ytan som standard. De innehåller samlingar med python-paket och inställningar som är avsedda att användas i befintligt skick för att komma igång med olika ramverk för maskin inlärning. De här i förväg skapade miljöerna tillåter också snabbare distributions tid. En fullständig lista finns i [artikeln granskade miljöer](resource-curated-environments.md).

I användar hanterade miljöer ansvarar du för att konfigurera din miljö och installera varje paket som utbildnings skriptet behöver på beräknings målet. Conda kontrollerar inte din miljö eller installerar något åt dig. Om du definierar en egen miljö måste du lista `azureml-defaults` med version `>= 1.0.45` som ett pip-beroende. Det här paketet innehåller de funktioner som krävs för att vara värd för modellen som en webb tjänst.

Du använder systemhanterade miljöer när du vill att [Conda](https://conda.io/docs/) ska hantera python-miljön och skript beroenden åt dig. En ny Conda-miljö bygger på Conda-beroende objekt. Den Azure Machine Learning tjänsten antar den här typen av miljö som standard, på grund av dess användbarhet på fjärranslutna beräknings mål som inte kan konfigureras manuellt.

## <a name="create-and-manage-environments"></a>Skapa och hantera miljöer

Du kan skapa miljöer genom att:

* Definiera nya `Environment` objekt, antingen genom att använda en granskad miljö eller genom att definiera egna beroenden.
* Använda befintliga `Environment` objekt från din arbets yta. Den här metoden möjliggör konsekvens och reproducerbarhet med dina beroenden.
* Importera från en befintlig Anaconda-miljö definition.
* Använda Azure Machine Learning CLI
* [Använda VS Code-tillägget](how-to-manage-resources-vscode.md#create-environment)

Vissa kod exempel finns i avsnittet "skapa en miljö" i [hur du använder miljöer](how-to-use-environments.md#create-an-environment). Miljöer hanteras också enkelt genom din arbets yta. De innehåller följande funktioner:

* Miljöer registreras automatiskt på arbets ytan när du skickar ett experiment. De kan också registreras manuellt.
* Du kan hämta miljöer från din arbets yta för att använda utbildning eller distribution, eller för att göra ändringar i miljö definitionen.
* Med versions hantering kan du se ändringar i dina miljöer över tid, vilket säkerställer reproducerbarhet.
* Du kan bygga Docker-avbildningar automatiskt från dina miljöer.

Kod exempel finns i avsnittet "hantera miljöer" i [använda miljöer](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Miljö uppbyggnad, cachelagring och åter användning

Azure Machine Learnings tjänsten bygger miljö definitioner i Docker-avbildningar och Conda-miljöer. Den cachelagrar också miljöerna så att de kan återanvändas i efterföljande utbildningar och distributioner av tjänst slut punkter. Att köra ett övnings skript kräver fjärr anslutning av en Docker-avbildning medan en lokal körning kan använda en Conda-miljö direkt. 

### <a name="submitting-a-run-using-an-environment"></a>Skicka in en körning med en miljö

Första gången du skickar en fjärrkörning med hjälp av en miljö anropar tjänsten Azure Machine Learning en [ACR build-aktivitet](../container-registry/container-registry-tasks-overview.md) på den Azure Container Registry (ACR) som är kopplad till arbets ytan. Den inbyggda Docker-avbildningen cachelagras sedan på arbets ytans ACR. Granskade miljöer backas upp av Docker-avbildningar som cachelagras i globala ACR. I början av körnings körningen hämtas avbildningen av beräknings målet från relevanta ACR.

För lokala körningar skapas en Docker-eller Conda-miljö baserat på miljö definitionen. Skripten körs sedan på mål beräkningen – en lokal körnings miljö eller en lokal Docker-motor.

### <a name="building-environments-as-docker-images"></a>Skapa miljöer som Docker-avbildningar

Om miljö definitionen inte redan finns i arbets ytan ACR skapas en ny avbildning. Avbildnings versionen består av två steg:

 1. Hämta en bas avbildning och köra eventuella Docker-steg
 2. Skapa en Conda-miljö enligt Conda-beroenden som anges i miljö definitionen.

Det andra steget utelämnas om du anger [användar hanterade beroenden](/python/api/azureml-core/azureml.core.environment.pythonsection). I det här fallet är du ansvarig för att installera python-paket, genom att inkludera dem i bas avbildningen eller ange anpassade Docker-steg i det första steget. Du är också ansvarig för att ange rätt plats för den körbara python-filen. Det är också möjligt att använda en [anpassad Docker-bas avbildning](how-to-deploy-custom-docker-image.md).

### <a name="image-caching-and-reuse"></a>Cachelagring av bilder och åter användning

Om du använder samma miljö definition för en annan körning återanvänder Azure Machine Learnings tjänsten den cachelagrade avbildningen från arbets ytans ACR. 

Om du vill visa information om en cachelagrad avbildning använder [Environment.get_image_details](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-) metoden.

För att avgöra om du ska återanvända en cachelagrad avbildning eller skapa en ny, beräknar tjänsten [ett hash-värde](https://en.wikipedia.org/wiki/Hash_table) från miljö definitionen och jämför den med hasharna i befintliga miljöer. Hashen baseras på:
 
 * Egenskaps värde för bas avbildning
 * Egenskaps värde för anpassade Docker-steg
 * Lista över python-paket i Conda-definitionen
 * Lista över paket i Spark-definition 

Hash-värdet är inte beroende av miljö namn eller version – om du byter namn på din miljö eller skapar en ny miljö med de exakta egenskaperna och paketen för en befintlig, ändras inte hash-värdet. Miljö definitions ändringar, till exempel att lägga till eller ta bort ett python-paket eller ändra paket versionen, gör att hash-värdet ändras. Att ändra ordningen på beroenden eller kanaler i en miljö leder till en ny miljö och kräver därför att en ny avbildning skapas. Det är viktigt att Observera att alla ändringar i en granskad miljö gör att hashen ogiltig förklaras och resulterar i en ny "icke-granskad" miljö.

Det beräknade hash-värdet jämförs med dem i arbets ytan och globala ACR (eller på beräknings målet för lokala körningar). Om det finns en matchning hämtas den cachelagrade avbildningen, annars utlöses en avbildnings version. Den tid det tar att hämta en cachelagrad avbildning inkluderar hämtnings tiden medan varaktigheten för att hämta en nyligen skapad avbildning inkluderar både bygg tiden och nedladdnings tiden. 

Följande diagram visar tre miljö definitioner. Två av dem har olika namn och versioner, men samma bas avbildning och python-paket. Men de har samma hash-värde och motsvarar därmed samma cachelagrade avbildning. Den tredje miljön har olika python-paket och-versioner och motsvarar därför en annan cachelagrad avbildning.

![Diagram över cachelagring av miljöer som Docker-avbildningar](./media/concept-environments/environment-caching.png)

>[!IMPORTANT]
> Om du skapar en miljö med ett ej fixerat paket beroende kan du till exempel ```numpy``` använda paket versionen som är installerad _när miljön skapades_. Dessutom fortsätter all framtida miljö med matchnings definition att använda den gamla versionen. 

Om du vill uppdatera paketet anger du ett versions nummer som tvingar avbildnings återskapning, till exempel ```numpy==1.18.1``` . Nya beroenden, inklusive kapslade, kommer att installeras som kan bryta ett tidigare arbets scenario. 

> [!WARNING]
>  [Miljön. Build](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) -metoden kommer att återskapa den cachelagrade avbildningen med möjlig sido effekt på att uppdatera icke-fästa paket och bryta reproducerbarhet för alla miljö definitioner som motsvarar den cachelagrade avbildningen.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar och använder miljöer](how-to-use-environments.md) i Azure Machine Learning.
* Se referens dokumentation för python SDK för [miljö klassen](/python/api/azureml-core/azureml.core.environment%28class%29).
* Se referens dokumentationen för R SDK för [miljöer](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-environments).