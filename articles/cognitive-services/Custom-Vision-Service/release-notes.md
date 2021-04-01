---
title: Viktig information – Custom Vision Service
titleSuffix: Azure Cognitive Services
description: Få den senaste informationen om nya versioner från Custom Visions teamet.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: 3724a7d515197c1f969bb051fc201b82bee64c42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "97813504"
---
# <a name="custom-vision-service-release-notes"></a>Viktig information om Custom Vision Service

## <a name="may-2-2019-and-may-10-2019"></a>2 maj 2019 och 10 maj 2019

- Förbättringar av BUGFIXES och Server delen

## <a name="may-23-2019"></a>23 maj 2019

- Förbättrad Portal GRÄNSSNITTs upplevelse som är relaterad till Azure-prenumerationer, vilket gör det enklare att välja Azure-kataloger.

## <a name="april-18-2019"></a>18 april 2019 

- Objekt identifierings export för vision AI dev Kit har lagts till.
- GRÄNSSNITTs justeringar, inklusive projekts ökning.

## <a name="april-3-2019"></a>3 april 2019

- Ökad gräns för antalet begränsnings rutor per bild till 200. 
- BUGFIXES, inklusive avsevärd prestanda uppdatering för modeller som exporteras till TensorFlow. 

## <a name="march-26-2019"></a>26 mars 2019

- Custom Vision Service har angett allmän tillgänglighet för Azure!
- Avancerad utbildning har lagts till med en ny maskin inlärnings Server del för bättre prestanda, särskilt vid utmanande data uppsättningar och detaljerade klassificeringar. Med avancerad utbildning kan du ange en budget för beräknings tid för utbildning och Custom Vision kommer att experimentellt identifiera bästa utbildning och förstärknings inställningar. För snabba iterationer kan du fortsätta att använda den befintliga snabb träningen.
- Introducerade 3,0 API: er. Meddelad om inaktuellitet av pre-3,0-API: er den 1 oktober 2019. Se [snabb starter](./quickstarts/image-classification.md) för dokumentationen om du vill ha exempel på hur du kommer igång.
- Ersatt "standard iterationer" med publicera/ta bort publicering i 3,0-API: erna.
- Nya modell export mål har lagts till. Dockerfile-exporten har uppgraderats till stöd för ARM för Raspberry Pi 3. Export stöd har lagts till i det [vision AI dev kit.](https://visionaidevkit.com/)
- Ökad gräns för Taggar per projekt till 500 för S0-nivån. Ökad gräns för avbildningar per projekt till 100 000 för S0-nivå.
- Tog bort vuxen domän. Allmän domän rekommenderas istället.
- [Priser](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) som är presenterade för allmän tillgänglighet.  

## <a name="february-25-2019"></a>25 februari 2019

- Presenterade slutet av ett begränsat utvärderings projekt (projekt som inte är associerade med en Azure-resurs), som Custom Vision nära slut för ande av flytten till den offentliga Azure-förhands granskningen. Från den 25 mars 2019 stöder CustomVision.ai-webbplatsen endast visning av projekt som är associerade med en Azure-resurs, till exempel den kostnads fria Custom Visions resursen. Till och med den 1 oktober 2019 kommer du fortfarande att kunna komma åt dina befintliga begränsade utvärderings projekt via Custom Vision API: er. Då får du tid att uppdatera API-nycklar för appar som du har skrivit med Custom Vision. Efter den 1 oktober 2019 tas alla begränsade utvärderings projekt som du inte har flyttat till Azure bort.

## <a name="january-22-2019"></a>22 januari 2019

- Support har lagts till för nya Azure-regioner: västra USA 2, östra USA, östra USA 2, Västeuropa, norra Europa, Sydostasien, östra Australien, centrala Indien, Storbritannien, södra, Japan, östra och norra centrala USA. Support fortsätter för södra centrala USA.

## <a name="december-12-2018"></a>12 december 2018

- Stöd för export av objekt identifierings modeller (introducerad komprimerad objekt identifierings domän).
- Ett antal tillgänglighets problem har åtgärd ATS för förbättrad skärm läsare och stöd för tangent bords navigering.
- UX-uppdateringar för Image Viewer och förbättrad taggning av objekt identifierings funktioner för snabbare taggning.  
- Bas modell för objekt identifierings domän har uppdaterats för bättre kvalitets objekts identifiering.
- Fel korrigeringar.

## <a name="november-6-2018"></a>6 november 2018

- Stöd har lagts till för logo typ domän i objekt identifiering.

## <a name="october-9-2018"></a>9 oktober 2018

- Objekt identifieringen anger betald förhands granskning. Nu kan du skapa objekt identifierings projekt med en Azure-resurs.
- Lade till funktionen "flytta till Azure" till webbplatsen, så att det blir enklare att uppgradera ett begränsat utvärderings projekt för att länka till en Azure. resurs länkat projekt (F0 eller S0.) Du hittar detta på sidan Inställningar för din produkt.  
- Export till ONNX 1,2 har lagts till för att stödja Windows 2018 oktober uppdaterings version av Windows ML.
Fel korrigeringar, inklusive för ONNX-export med specialtecken.

## <a name="august-14-2018"></a>14 augusti 2018

- Lade till "kom igång"-widgeten på customvision.ai-webbplatsen för att vägleda användare genom projekt träning.
- Ytterligare förbättringar av Machine Learning-pipelinen för att dra nytta av multilabel-projekt (nytt förlust lager).

## <a name="june-28-2018"></a>28 juni 2018

- Fel korrigeringar & Server dels förbättringar.
- Aktive rad multiklass-klassificering för projekt där bilder har exakt en etikett. I förutsägelser för multiklass-läge kommer sannolikheten att summeras till ett (alla bilder klassificeras bland de angivna taggarna).

## <a name="june-13-2018"></a>13 juni 2018

- UX-uppdatering, fokuserar på enkel användning och hjälpmedel.
- Förbättringar av Machine Learning-pipeline för att dra nytta av projekt med ett stort antal taggar.
- Fast bugg i TensorFlow-export. Aktiverade exporterade modell versioner, så iterationer kan exporteras mer än en gång.

## <a name="may-7-2018"></a>7 maj, 2018

- Introducerade förhandsfunktion för objektidentifiering för begränsad utvärderingsversion-projekt.
- Uppgraderade till 2.0 API:er
- Utökade av S0-nivån till upp till 250 taggar och 50 000 bilder.
- Betydande serverförbättringar av maskininlärningspipeline för bildklassificeringsprojekt. Projekt som tränas efter den 27 april 2018 kan dra nytta av dessa uppdateringar.
- Lade till modellexport för ONNX, för användning med Windows ML.
- Lade till modellexport till DockerFile. På så sätt kan du ladda ned artefakter för att skapa dina egna Windows- och Linux-containrar, till exempel DockerFile, TensorFlow-modell och tjänstkod.
- För nytränade modeller som exporter ATS till TensorFlow i de allmänna (kompakta) och Landmärkes domänerna (Compact) [är medelvärden (0,0) nu (0](https://github.com/azure-samples/cognitive-services-android-customvision-sample), 0), för konsekvens i alla projekt.

## <a name="march-1-2018"></a>1 mars 2018

- Betalad förhands granskning och har registrerats på Azure Portal. Projekt kan nu bifogas till Azure-resurser med nivå F0 (kostnadsfritt) eller S0 (standard). Introducerade S0-nivån projekt, som tillåter upp till 100 taggar och 25 000 bilder.
- Serverändringar av maskininlärningspipeline/parametern normalization. Detta ger kunderna bättre kontroll över kompromisser mellan precision och återanrop vid justeringar av sannolikhetströskelvärdet. Som en del av dessa ändringar angavs 50 % för sannolikhetströskelvärdet i CustomVision.ai-portalen.

## <a name="december-19-2017"></a>19 december 2017

- Export till Android (TensorFlow) har lagts till, förutom tidigare utgivna export till iOS (CoreML.) På så sätt kan du exportera en utbildad kompakt modell så att den körs offline i ett program.
- Lade till kompakta Retail- och Landmark-domäner för att möjliggöra modellexport för dessa domäner.
- Släppte version [1.2 av Training-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) och [1.1 av Prediction-API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164). Uppdaterade API:ernas stöd för modellexport, ny förutsägelseåtgärd som inte sparar bilder i Förutsägelser och introducerade batchåtgärder för Training-API:et.
- Justeringar av användargränssnittet, bland annat möjligheten att se vilken domän som har använts för att träna en iteration.
- Uppdaterade [C# SDK och exempel](https://github.com/Microsoft/Cognitive-CustomVision-Windows).