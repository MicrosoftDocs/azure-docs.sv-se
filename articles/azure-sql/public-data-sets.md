---
title: Offentliga data uppsättningar för Azure Analytics
description: Lär dig mer om offentliga data uppsättningar som du kan använda för att prototyp och testa Azure Analytics-tjänster och-lösningar.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/01/2018
ms.openlocfilehash: 2e836a175b45c6fca46f9c76760e1cbaef305a47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792590"
---
# <a name="public-data-sets-for-testing-and-prototyping"></a>Offentliga data uppsättningar för testning och prototypering
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Bläddra i listan över offentliga data uppsättningar för data som du kan använda för att prototyp och testa lagrings-och analys tjänster och-lösningar.

## <a name="us-government-and-agency-data"></a>Amerikanska myndigheter och byrå uppgifter

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [AMERIKANSKA myndighets data](https://catalog.data.gov/dataset) | Över 250 000 data uppsättningar som omfattar jordbruk, klimat, konsument, eko system, utbildning, energi, ekonomi, hälsa, lokal myndighet, tillverkning, sjö, hav, offentlig säkerhet, vetenskap och forskning i USA | Filer av olika storlekar i olika format, inklusive HTML, XML, CSV, JSON, Excel och många andra. Du kan filtrera tillgängliga data uppsättningar efter fil format. |
| [AMERIKANSKA inventerings data](https://www.census.gov/data.html) | Statistisk information om befolkningen i USA | Data uppsättningar är i olika format. |
| [Earth Science-data från NASA](https://earthdata.nasa.gov/) | Över 32 000 data samlingar som omfattar jord-, atmosfär-, biosfär-, klimat-, cryosphere-, mänsklig-, hydrosphere-, yt-, havs-, havs-och jord-och. | Data uppsättningar är i olika format. |
| [Flyg fördröjningar och andra transport data](https://www.transtats.bts.gov/OT_Delay/OT_DelayCause1.asp) | "Det amerikanska departementet transport (punkt) Bureau of transport Statistics (BTS) spårar den dagliga prestandan hos inrikes flygningar som drivs av stora lufttrafik företag. Sammanfattande information om antalet försenade, annullerade och avvisade flygningar visas... i sammanfattnings tabeller som publicerats på den här webbplatsen. " | Filerna är i CSV-format. |
| [Oåterkalleliga trafik – US oåterkalleligt analys rapporterings system (FARS)](https://www.nhtsa.gov/FARS) | "FARS är en Nationwide-räkning som tillhandahåller NHTSA, Congress och amerikanska offentliga årliga data om allvarliga skador som uppstått i motor fordons trafik krascher." | "Skapa dina egna data för allvarliga data kör online med hjälp av FARS Query system. Du kan också hämta alla FARS-data från 1975 för att presentera dem från FTP-platsen. " |
| [Giftiga kemiska data – EPA-ToxCast (giftig information &trade; )](https://www.epa.gov/chemical-research/toxicity-forecaster-toxcasttm-data) | "EPA: s mest uppdaterade, allmänt tillgängliga data flödes toxiska data på tusentals kemikalier. Dessa data genereras via EPA: s ToxCast Research ansträngning. " | Data uppsättningar är tillgängliga i olika format, inklusive kalkyl blad, R-paket och MySQL-databasfiler. |
| [Giftiga kemiska data – NIH Tox21 data Challenge 2014](https://tripod.nih.gov/tox21) | "2014 Tox21-datautmaningen är utformad för att hjälpa forskare att förstå potentialen för de kemikalier och föreningar som testas genom toxikologi i ett på 2000-talet för att störa biologiska vägar på ett sätt som kan resultera i toxiska effekter." | Data uppsättningar är tillgängliga i leende-och SDF-format. Datan innehåller "analys av aktivitets data och kemiska strukturer i Tox21-samlingen på ~ 10 000-föreningar (Tox21 10K)." |
| [Bio teknik och-genomta data från NCBI](https://www.ncbi.nlm.nih.gov/guide/data-software/) | Flera data uppsättningar som omfattar gener, uppgifter och proteiner. | Data uppsättningar är i text-, XML-, kampanj-och andra format. En Mas-app är tillgänglig. |

## <a name="other-statistical-and-scientific-data"></a>Andra statistiska och vetenskapliga data

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [New York, stad, taxi data](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml) | "Taxi resor innehåller fält som fångar in hämtnings-och DropOff-datum/-tider, hämtnings-och DropOff-platser, rese sträckor, avgiftsbelagda priser, pris typer, betalnings typer och driv rutins rapporterad person antal." | Data uppsättningar är i CSV-filer per månad. |
| [Microsoft Research data uppsättningar – "data vetenskap för forskning"](https://www.microsoft.com/research/academic-program/data-science-microsoft-research/) | Flera data uppsättningar som täcker interaktion med mänsklig dator, ljud/video, Data utvinning/informations hämtning, geospatialt/plats, naturlig språk bearbetning och Robotics/dator vision. | Data uppsättningar är i olika format, zippade för hämtning. |
| [Öppna data moln data i vetenskap](https://www.opensciencedatacloud.org/projects/) | "Det öppna vetenskaps informations molnet ger den vetenskapliga gruppen resurser för att lagra, dela och analysera terabyte och petabytea vetenskapliga data uppsättningar."| Data uppsättningar är i olika format. |
| [Global klimat information – WorldClim](https://worldclim.org/) | "WorldClim är en uppsättning globala klimat lager (Gridded klimat data) med en avstånds upplösning på ca 1 km2. Dessa data kan användas för mappning och spatial modellering. " | De här filerna innehåller geospatiala data. Mer information finns i [data format](https://worldclim.org/formats1). |
| [Data om Human samhälle – GDELT-projektet](https://www.gdeltproject.org/data.html) | "GDELT-projektet är den största, mest omfattande och mest upplösningen öppen databas för mänskligt samhälle som någonsin skapats." | RAW-datafilerna är i CSV-format. |
| [Annonsering Klicka på förutsägelse data för Machine Learning från Criteo](https://labs.criteo.com/2013/12/download-terabyte-click-logs/) | "Den största allt offentligt utgivna ML-datauppsättningen." Mer information finns i [Criteo 1 TB Klicka på förutsägelse data uppsättning](/archive/blogs/machinelearning/now-available-on-azure-ml-criteos-1tb-click-prediction-dataset). | |
| [ClueWeb09 text utvinnings data uppsättning från Lemur-projektet](https://www.lemurproject.org/clueweb09.php/) | "ClueWeb09-datauppsättningen skapades för att ge stöd för forskning om informations hämtning och relaterad mänsklig språk teknik. Det består av cirka 1 000 000 000 webb sidor på 10 språk som samlades in i januari och februari 2009. " | Se [data uppsättnings information](https://www.lemurproject.org/clueweb09/datasetInformation.php).|

## <a name="online-service-data"></a>Online tjänst data

| Datakälla | Om de data som används | Om filerna |
|---|---|---|
| [GitHub-Arkiv](https://www.githubarchive.org/) | "GitHub Archive är ett projekt för att registrera den offentliga GitHub-tidslinjen [av händelser], arkivera den och göra det enkelt att komma åt ytterligare analyser." | Ladda ned JSON-kodade händelse Arkiv i formatet. gz (gzip) från en webb klient. |
| [GitHub-aktivitets data från GHTorrent-projektet](http://ghtorrent.org/) | "GHTorrent-projektet [är] ett arbete för att skapa en skalbar, fråga offline-spegling av data som erbjuds via GitHub-REST API. GHTorrent övervakar den GitHub offentliga händelsens tids linje. För varje händelse hämtas dess innehåll och deras beroenden, så omfattande. " | MySQL-databasens dumpar är i CSV-format. |
| [Stack Overflow data dump](https://archive.org/details/stackexchange) | "Det här är en anonymiserats-dump av allt innehåll som läggs till av användaren i Stack Exchange-nätverket [inklusive Stack Overflow]." | "Varje plats [till exempel Stack Overflow] formateras som ett separat arkiv som består av XML-filer som är zippade via 7-zip med bzip2-komprimering. Varje plats arkiv innehåller inlägg, användare, röster, kommentarer, PostHistory och PostLinks. " |