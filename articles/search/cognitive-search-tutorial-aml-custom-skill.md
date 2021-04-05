---
title: 'Exempel: skapa och distribuera en anpassad kunskap med Azure Machine Learning'
titleSuffix: Azure Cognitive Search
description: Det här exemplet visar hur du använder Azure Machine Learning för att bygga och distribuera en anpassad färdighet för Azure Kognitiv söknings pipeline för AI-anrikning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 98d8395236bf955eed88f36c03c96981fa0e4b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745641"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>Exempel: bygga och distribuera en anpassad kunskap med Azure Machine Learning 

I det här exemplet ska du använda [data uppsättningen för hotell granskning](https://www.kaggle.com/datafiniti/hotel-reviews) (distribuerad under Creative Commons License [CC by-NC-sa 4,0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)) för att skapa en [anpassad färdighet](./cognitive-search-aml-skill.md) med hjälp av Azure Machine Learning för att extrahera aspektbaserade sentiment från granskningarna. Detta gör det möjligt för tilldelning av positiva och negativa sentiment i samma granskning att bli korrekt tilldelad till identifierade entiteter som personal, rum, lobbyn eller pooler.

För att träna den aspektbaserade sentiment-modellen i Azure Machine Learning kommer du att använda [lagrings platsen NLP recept](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa). Modellen kommer sedan att distribueras som en slut punkt i ett Azure Kubernetes-kluster. När den har distribuerats läggs slut punkten till i pipeline för anrikning som en AML-färdighet som används av den Kognitiv sökning tjänsten.

Det finns två angivna data uppsättningar. Om du vill träna modellen själv, krävs hotel_reviews_1000.csv-filen. Vill du hoppa över övnings steget? Ladda ned hotel_reviews_100.csv.

> [!div class="checklist"]
> * Skapa en Azure Kognitiv sökning-instans
> * Skapa en Azure Machine Learning arbets yta (Sök tjänsten och arbets ytan bör vara i samma prenumeration)
> * Träna och distribuera en modell till ett Azure Kubernetes-kluster
> * Länka en pipeline för AI-anrikning till den distribuerade modellen
> * Mata in utdata från distribuerad modell som en anpassad färdighet

> [!IMPORTANT] 
> Den här kunskapen är för närvarande en offentlig för hands version. För hands versions funktionerna tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Det finns för närvarande inget stöd för .NET SDK.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – hämta en [kostnads fri prenumeration](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Kognitiv sökning tjänst](./search-get-started-arm.md)
* [Cognitive Services resurs](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [Azure-lagringskonto](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure Machine Learning-arbetsyta](../machine-learning/how-to-manage-workspace.md)

## <a name="setup"></a>Konfiguration

* Klona eller hämta innehållet i [exempel lagrings platsen](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill).
* Extrahera innehåll om hämtningen är en zip-fil. Kontrol lera att filerna är Läs-och skrivbara.
* När du konfigurerar Azure-konton och-tjänster kopierar du namn och nycklar till en lätt åtkomlig textfil. Namn och nycklar läggs till i den första cellen i antecknings boken där variabler för åtkomst till Azure-tjänsterna definieras.
* Om du inte känner till Azure Machine Learning och dess krav, ska du granska dessa dokument innan du börjar:
 * [Konfigurera en utvecklings miljö för Azure Machine Learning](../machine-learning/how-to-configure-environment.md)
 * [Skapa och hantera Azure Machine Learning arbets ytor i Azure Portal](../machine-learning/how-to-manage-workspace.md)
 * När du konfigurerar utvecklings miljön för Azure Machine Learning bör du överväga att använda den [molnbaserade beräknings instansen](../machine-learning/how-to-configure-environment.md#compute-instance) för snabb och lätt att komma igång.
* Överför data uppsättnings filen till en behållare i lagrings kontot. Den större filen är nödvändig om du vill utföra inlärnings steget i antecknings boken. Om du föredrar att hoppa över övnings steget rekommenderas den mindre filen.

## <a name="open-notebook-and-connect-to-azure-services"></a>Öppna antecknings boken och Anslut till Azure-tjänster

1. Lägg till all nödvändig information för variablerna som ger åtkomst till Azure-tjänsterna i den första cellen och kör cellen.
1. Genom att köra den andra cellen bekräftar du att du har anslutit till Sök tjänsten för din prenumeration.
1. I avsnitten 1,1-1,5 skapas Sök tjänstens data lager, färdigheter, index och indexerare.

Nu kan du välja att hoppa över stegen för att skapa inlärnings data uppsättningen och experimentera i Azure Machine Learning och hoppa direkt till att registrera de två modeller som finns i mappen modeller i GitHub-lagrings platsen. Om du hoppar över de här stegen går du vidare till avsnitt 3,5, skriv bedömnings skript i antecknings boken. Detta sparar tid; Det kan ta upp till 30 minuter att slutföra hämtningen av data och uppladdning.

## <a name="creating-and-training-the-models"></a>Skapa och träna modeller

Avsnitt 2 innehåller sex celler som laddar ned assisterad inbäddnings filen från lagrings platsen för NLP-recept. Efter nedladdningen överförs filen till Azure Machine Learning data lagret. . Zip-filen är cirka 2G och det tar lite tid att utföra dessa uppgifter. När du har laddat upp övnings informationen extraheras du och nu är du redo att gå vidare till avsnitt 3.

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>Träna den aspektbaserade sentiment-modellen och distribuera din slut punkt

Avsnitt 3 i Notebook kommer att träna de modeller som skapades i avsnitt 2, registrera dessa modeller och distribuera dem som en slut punkt i ett Azure Kubernetes-kluster. Om du inte är bekant med Azure Kubernetes rekommenderar vi starkt att du läser följande artiklar innan du försöker skapa ett utgångs kluster:

* [Översikt över Azure Kubernetes service](../aks/intro-kubernetes.md)
* [Kubernetes Core-koncept för Azure Kubernetes service (AKS)](../aks/concepts-clusters-workloads.md)
* [Kvoter, storleks begränsningar för virtuella datorer och regions tillgänglighet i Azure Kubernetes service (AKS)](../aks/quotas-skus-regions.md)

Det kan ta upp till 30 minuter att skapa och distribuera ett härlednings kluster. Om du testar webb tjänsten innan du går vidare till de sista stegen, rekommenderar vi att du uppdaterar din färdigheter och kör indexeraren.

## <a name="update-the-skillset"></a>Uppdatera färdigheter

Avsnitt 4 i antecknings boken har fyra celler som uppdaterar färdigheter och indexeraren. Alternativt kan du använda portalen för att välja och tillämpa den nya kunskapen på färdigheter och sedan köra indexeraren för att uppdatera Sök tjänsten.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

I portalen går du till färdigheter och väljer färdigheter definition (JSON). Portalen visar JSON för din färdigheter som skapades i de första cellerna i antecknings boken. Till höger om skärmen finns det en listruta där du kan välja mallen för kompetens definition. Välj mallen Azure Machine Learning (AML). Ange namnet på Azure ML-arbetsytan och slut punkten för den modell som distribueras till ett härlednings kluster. Mallen kommer att uppdateras med slut punkts-URI och nyckel.

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="Mall för färdigheter-definition":::

Kopiera färdigheter-mallen från fönstret och klistra in den i färdigheter-definitionen till vänster. Redigera mallen för att ange de värden som saknas för:

* Name
* Beskrivning
* Kontext
* namn och källa för indata
* ' utdata '-namn och targetName

Spara färdigheter.

När du har sparat färdigheter går du till indexeraren och väljer indexs definitions-länken (JSON). Portalen visar JSON för den indexerare som skapades i de första cellerna i antecknings boken. Mappningar av utdatakolumner måste uppdateras med ytterligare fält mappningar för att säkerställa att indexeraren kan hantera och skicka dem korrekt. Spara ändringarna och välj sedan kör. 

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Granska webb-API](./cognitive-search-custom-skill-web-api.md) 
>  för anpassad kompetens [Lär dig mer om att lägga till anpassade kunskaper i anriknings pipelinen](./cognitive-search-custom-skill-interface.md)