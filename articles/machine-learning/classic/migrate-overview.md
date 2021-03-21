---
title: 'ML Studio (klassisk): migrera till Azure Machine Learning'
description: Migrera från Studio (klassisk) till Azure Machine Learning för en modern data vetenskaps plattform.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: fda34a7ee06d35846bcec571e904297d0421c38f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565368"
---
# <a name="migrate-to-azure-machine-learning"></a>Migrera till Azure Machine Learning

I den här artikeln får du lära dig hur du migrerar Studio (klassiska) till gångar till Azure Machine Learning. För att migrera resurser måste du för närvarande återskapa experimenten manuellt.

Azure Machine Learning tillhandahåller en modern data vetenskaps plattform som kombinerar inga koder och kod-första metoder. Mer information om skillnaderna mellan Studio (klassisk) och Azure Machine Learning finns i avsnittet [utvärdera Azure Machine Learning](#step-1-assess-azure-machine-learning) .


## <a name="recommended-approach"></a>Rekommenderad metod

För att migrera till Azure Machine Learning rekommenderar vi följande:

> [!div class="checklist"]
> * Steg 1: utvärdera Azure Machine Learning
> * Steg 2: skapa en migrations plan
> * Steg 3: återskapa experiment och webb tjänster
> * Steg 4: integrera klient program
> * Steg 5: Rensa Studio (klassiska) till gångar


## <a name="step-1-assess-azure-machine-learning"></a>Steg 1: utvärdera Azure Machine Learning
1. Läs mer om [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/); Det är fördelar, kostnader och arkitektur.

1. Jämför funktionerna i Azure Machine Learning och Studio (klassisk).

    >[!NOTE]
    > Funktionen **Designer** i Azure Machine Learning ger en liknande dra-och-släpp-upplevelse till Studio (klassisk). Azure Machine Learning ger dock även robusta [kod-första arbets flöden](../concept-model-management-and-deployment.md) som ett alternativ. Den här migrations serien fokuserar på designern, eftersom den liknar den klassiska Studio-upplevelsen.

    [!INCLUDE [aml-compare-classic](../../../includes/machine-learning-compare-classic-aml.md)]

3. Kontrol lera att dina kritiska Studio (klassiska) moduler stöds i Azure Machine Learning designer. Mer information finns i [modulen Studio (klassisk) och designern-mappning](#studio-classic-and-designer-module-mapping) nedan.

4. [Skapa en Azure Machine Learning-arbetsyta](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace?tabs=azure-portal).

## <a name="step-2-create-a-migration-plan"></a>Steg 2: skapa en migrations plan

1. Identifiera de Studio (klassiska) **data uppsättningar**, **modeller** och **webb tjänster** som du vill migrera.

1. Avgör vilken effekt en migrering kommer att ha på din verksamhet.

1. Skapa en migrerings plan.

## <a name="step-3-rebuild-experiments-and-web-services"></a>Steg 3: återskapa experiment och webb tjänster

1. [Migrera data uppsättningar till Azure Machine Learning](migrate-register-dataset.md).
1. Använd designern för att [Återskapa experiment](migrate-rebuild-experiment.md).
1. Använd designern för att [distribuera om webb tjänster](migrate-rebuild-web-service.md).

    >[!NOTE]
    > Azure Machine Learning stöder även kod-första arbets flöden för [data uppsättningar](../how-to-create-register-datasets.md), [utbildning](../how-to-set-up-training-targets.md)och [distribution](../how-to-deploy-and-where.md).

## <a name="step-4-integrate-client-apps"></a>Steg 4: integrera klient program

1. Ändra klient program som anropar Studio (klassiska) webb tjänster för att använda dina nya [Azure Machine Learning-slutpunkter](migrate-rebuild-integrate-with-client-app.md).

## <a name="step-5-cleanup-studio-classic-assets"></a>Steg 5: Rensa Studio (klassiska) till gångar

1. [Rensa Studio (klassiska) till gångar](export-delete-personal-data-dsr.md) för att undvika extra kostnader. Du kanske vill behålla till gångar för återställning tills du har verifierat Azure Machine Learning arbets belastningar.

## <a name="studio-classic-and-designer-module-mapping"></a>Studio (klassisk) och designer-modul-mappning

I följande tabell ser du vilka moduler som du kan använda när du återskapar Studio-experiment (klassiska) i designern.


> [!IMPORTANT]
> Designern implementerar moduler via python-paket med öppen källkod i stället för C#-paket som Studio (klassisk). På grund av den här skillnaden kan utdata från designer-moduler variera något från deras Studio (klassiska) motsvarigheter.


|Kategori|Studio-modul (klassisk)|Modulen för ersättnings utvecklare|
|--------------|----------------|--------------------------------------|
|Indata och utdata|-Ange data manuellt </br> – Exportera data </br> – Importera data </br> – Läs in tränad modell </br> -Packa upp zippade data uppsättningar|-Ange data manuellt </br> – Exportera data </br> – Importera data|
|Konverteringar av data format|-Konvertera till CSV </br> -Konvertera till data uppsättning </br> -Konvertera till ARFF </br> -Konvertera till SVMLight </br> -Konvertera till TSV|-Konvertera till CSV </br> -Konvertera till data uppsättning|
|Data omvandling-manipulering|– Lägg till kolumner</br> -Lägg till rader </br> -Använd SQL-transformering </br> -Rensa saknade data </br> -Konvertera till indikator värden </br> – Redigera metadata </br> – Koppla data </br> -Ta bort dubblettrader </br> -Välj kolumner i data uppsättning </br> -Välj kolumner transformera </br> – SMOTE </br> -Gruppera kategoriska-värden|– Lägg till kolumner</br> -Lägg till rader </br> -Använd SQL-transformering </br> -Rensa saknade data </br> -Konvertera till indikator värden </br> – Redigera metadata </br> – Koppla data </br> -Ta bort dubblettrader </br> -Välj kolumner i data uppsättning </br> -Välj kolumner transformera </br> – SMOTE|
|Data omvandling – skala och minska |– Klipp värden </br> – Gruppera data till lager platser </br> -Normalisera data </br>-Huvud komponent analys |– Klipp värden </br> – Gruppera data till lager platser </br> -Normalisera data|
|Data omvandling – exempel och delning|– Partition och exempel </br> -Dela data|– Partition och exempel </br> -Dela data|
|Data omvandling – filter |-Använd filter </br> -FIR-filter </br> – IIR-filter </br> – Median filter </br> – Glidande medelvärde filter </br> – Tröskelvärdes filter </br> – Användardefinierat filter||
|Data omvandling – inlärning med antal |– Transformering av generering av räkning </br> -Export räknings tabell </br> -Import tabell räkning </br> – Transformering av sammanfognings antal</br>  – Ändra räkna tabell parametrar||
|Val av funktion |– Filtrera baserat funktions val </br> – Discriminant analys av Fisher Lineing  </br> – Permutation, funktions prioritet |– Filtrera baserat funktions val </br>  – Permutation, funktions prioritet|
| Modell klassificering| – Besluts skog med flera klasser </br> – Jungle för besluts fattande av klass  </br> – Logistik regression med multiklass  </br>– Neurala nätverk för multiklass  </br>-En-mot-alla – multiklass </br>-Two-Class genomsnittlig Perceptron </br>– Two-Class Bayes Point Machine </br>-Two-Class utökat besluts träd  </br> -Two-Class besluts skog  </br> – Two-Class beslut Jungle  </br> -Two-Class Locally-Deep SVM </br> – Two-Class logistik regression  </br> – Two-Class neurala nätverk </br> – Two-Class support Vector Machine  | – Besluts skog med flera klasser </br>  – Besluts träd för ökning av multiklass  </br> – Logistik regression med multiklass </br> – Neurala nätverk för multiklass </br> -En-mot-alla – multiklass  </br> -Two-Class genomsnittlig Perceptron  </br> -Two-Class utökat besluts träd  </br> -Two-Class besluts skog </br>– Two-Class logistik regression </br> – Two-Class neurala nätverk </br>– Two-Class support Vector Machine  |
| Modell-klustring| -K-innebär klustring| -K-innebär klustring|
| Modell-regression| – Bayesian linjär regression  </br> -Förstärkt besluts träds regression  </br>-Besluts skogs regression  </br> – Fast skog Quantile regression  </br> – Linjär regression </br> – Neurala nätverks regression </br> – Ordinal regression Poisson regression| -Förstärkt besluts träds regression  </br>-Besluts skogs regression  </br> – Fast skog Quantile regression </br> – Linjär regression  </br> – Neurala nätverks regression </br> – Poisson-regression|
| Modell – avvikelse identifiering| -One-Class SVM  </br> -PCA-Based avvikelse identifiering | -PCA-Based avvikelse identifiering|
| Machine Learning – utvärdera  | – Kors validerings modell  </br>– Utvärdera modell  </br>– Utvärdera rekommendation | – Kors validerings modell  </br>– Utvärdera modell </br> – Utvärdera rekommendation|
| Machine Learning – träna| – Rensnings kluster  </br> -Träna avvikelse identifierings modell </br>– Träna kluster modell  </br> -Träna matchbox-rekommendation-</br> Träningsmodell  </br>-Finjustera modellens egenskaper| -Träna avvikelse identifierings modell  </br> – Träna kluster modell </br> -Träna modell –</br> – Träna PyTorch-modell  </br>– Träna SVD-rekommendation  </br>– Träna bred och djup rekommendation </br>-Finjustera modellens egenskaper|
| Machine Learning – Poäng| -Tillämpa omvandling  </br>– Tilldela data till kluster  </br>– Score matchbox-rekommendation </br> – Poäng modell|-Tillämpa omvandling  </br> – Tilldela data till kluster </br> – Poäng avbildnings modell  </br> – Poäng modell </br>– Resultat av SVD-rekommendation </br> – Resultat bred och djup rekommendation|
| OpenCV-bibliotek| – Importera bilder </br>-Förtränad bild klassificering för kaskad | |
| Python-språkmoduler| -Kör Python-skript| -Kör Python-skript  </br> – Skapa python-modell |
| R-språkmoduler  | -Kör R-skript  </br> -Skapa R-modell| -Kör R-skript|
| Statistiska funktioner | -Tillämpa matematik åtgärd </br>– Beräkna elementär statistik  </br>– Compute linjär korrelation  </br>– Utvärdera sannolikhets funktionen  </br>-Ersätt diskreta värden  </br>– Sammanfatta data  </br>– Testa hypotes med t-test| -Tillämpa matematik åtgärd  </br>– Sammanfatta data|
| Textanalys| -Identifiera språk  </br>– Extrahera nyckel fraser från text  </br>– Extrahera N-gram-funktioner från text  </br>– Funktion-hashing </br>-Latend Dirichlet-allokering  </br>-Namngiven enhets igenkänning </br>– Förbearbeta text  </br>-Score Vowpal Wabbit version 7-10-modellen  </br>-Score Vowpal Wabbit version 8-modell </br>-Träna Vowpal Wabbit version 7-10-modellen  </br>-Träna Vowpal Wabbit version 8-modellen |– Konvertera ord till vektor </br> – Extrahera N-gram-funktioner från text </br>– Funktion-hashing  </br>-Latend Dirichlet-allokering </br>– Förbearbeta text  </br>– Score Vowpal Wabbit-modell </br> – Träna Vowpal Wabbit-modellen|
| Tids serier| – Avvikelse identifiering för tids serier | |
| Webbtjänst | -Indatamängd </br> – Utdata | -Indatamängd </br>  - Utdata|
| Visuellt innehåll| | -Använd avbildnings omvandling </br> -Konvertera till avbildnings katalog </br> -Init-avbildnings omvandling </br> – Dela avbildnings katalog  </br> – DenseNet bild klassificering   </br>– ResNet bild klassificering |

Mer information om hur du använder enskilda designer-moduler finns i referens för [Designer-modulen](../algorithm-module-reference/module-reference.md).

### <a name="what-if-a-designer-module-is-missing"></a>Vad händer om en designer-modul saknas?

Azure Machine Learning designer innehåller de mest populära modulerna från Studio (klassisk). Den innehåller också nya moduler som utnyttjar de senaste teknikerna för maskin inlärning. 

Om migreringen blockeras på grund av saknade moduler i designern, kontakta oss genom att [skapa ett support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="example-migration"></a>Exempel på migrering

I följande experiment-migrering beskrivs några skillnader mellan Studio (klassisk) och Azure Machine Learning.

### <a name="datasets"></a>Datauppsättningar

I Studio (klassisk) sparades **data uppsättningar** i din arbets yta och kan bara användas av Studio (klassisk).

![Automobile-Price-klassisk-dataset](./media/migrate-overview/studio-classic-dataset.png)

I Azure Machine Learning registreras **data uppsättningar** på arbets ytan och kan användas i alla Azure Machine Learning. Mer information om fördelarna med Azure Machine Learning data uppsättningar finns i [skydda data åtkomst](../concept-data.md#reference-data-in-storage-with-datasets).

![Automobile-pris-AML – data uppsättning](./media/migrate-overview/aml-dataset.png)

### <a name="pipeline"></a>Pipeline

I Studio (klassisk) innehöll **experiment** bearbetnings logiken för ditt arbete. Du har skapat experiment med dra-och-släpp-moduler.


![bil-pris – klassisk-experiment](./media/migrate-overview/studio-classic-experiment.png)

I Azure Machine Learning innehåller **pipeliner** bearbetnings logiken för ditt arbete. Du kan skapa pipelines med antingen dra-och-släpp-moduler eller genom att skriva kod.

![Automobile-Price-AML-pipeline](./media/migrate-overview/aml-pipeline.png)

### <a name="web-service-endpoint"></a>Webb tjänst slut punkt

I Studio (klassisk) användes **API för begäran/svar** för real tids förutsägelse. **API för batch-körning** användes för batch-förutsägelse eller omträning.

![Automobile-Price-klassisk-WebService](./media/migrate-overview/studio-classic-web-service.png)

I Azure Machine Learning används **real tids slut punkter** för förutsägelse i real tid. **Pipelines-slutpunkter** används för batch-förutsägelse eller omträning.

![Automobile-pris-AML-Endpoint](./media/migrate-overview/aml-endpoint.png)


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig de övergripande kraven för att migrera till Azure Machine Learning. Detaljerade anvisningar finns i de andra artiklarna i serien Studio (klassisk) migrering:

1. **Översikt över migrering**.
1. [Migrera data uppsättning](migrate-register-dataset.md).
1. [Återskapa en Studio (klassisk) utbildnings pipeline](migrate-rebuild-experiment.md).
1. [Återskapa en Studio-webbtjänst (klassisk)](migrate-rebuild-web-service.md).
1. [Integrera en Azure Machine Learning-webbtjänst med klient program](migrate-rebuild-integrate-with-client-app.md).
1. [Migrera kör R-skript](migrate-execute-r-script.md).






