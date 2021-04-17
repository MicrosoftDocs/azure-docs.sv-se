---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: lajanuar
ms.openlocfilehash: dfd2b32094aae06675ea8ee9157370f9d2833e91
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518236"
---
Formigenkänning på indatadokument som uppfyller dessa krav:

* Formatet måste vara JPG, PNG, PDF (text eller skannat) eller TIFF. Text-inbäddade PDF-filer är bäst eftersom det inte finns någon risk för fel vid extrahering av tecken och plats.
* Filstorleken måste vara mindre än 50 MB.
* Bilddimensionerna måste vara mellan 50 x 50 bildpunkter och 1 0000 x 1 0000 bildpunkter.
* PDF-måtten måste vara högst 17 x 17 tum, vilket motsvarar pappersstorlekarna Juridiskt eller A3 och mindre.
* För PDF och TIFF bearbetas endast de första 200 sidorna (med en kostnadsfri nivåprenumeration bearbetas bara de första två sidorna).
* Den totala storleken på träningsdatauppsättningen måste vara 500 sidor eller mindre.
* Om dina PDF-filer är lösenordslåsta måste du ta bort låset innan du skickar dem.
* Om de skannas från pappersdokument ska formulären vara genomsökningar av hög kvalitet.
* För oövervakad inlärning (utan märkta data) måste data innehålla nycklar och värden.
* För oövervakad inlärning (utan märkta data) måste nycklarna visas ovanför eller till vänster om värdena. de kan inte visas nedan eller till höger.
