---
title: Regler för namngivning av Azure Data Factory entiteter
description: Beskriver namngivnings regler för Data Factory entiteter.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: a1a0622c0736bad5f6c205fab01f4405577ed67a
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783361"
---
# <a name="azure-data-factory---naming-rules"></a>Namngivnings regler för Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Följande tabell innehåller namngivnings regler för Data Factory artefakter.

| Name | Namn unikhet | Verifierings kontroller |
|:--- |:--- |:--- |
| Data fabrik | Unikt mellan Microsoft Azure. Namn är inte Skift läges känsliga, det `MyDF` vill säga och `mydf` referera till samma data fabrik. |<ul><li>Varje data fabrik är knuten till exakt en Azure-prenumeration.</li><li>Objekt namn måste börja med en bokstav eller en siffra och får bara innehålla bokstäver, siffror och bindestreck (-).</li><li>Varje bindestreck (-) måste föregås omedelbart och följas av en bokstav eller en siffra. Efterföljande bindestreck tillåts inte i behållar namn.</li><li>Namnet kan vara 3-63 tecken långt.</li></ul> |
| Länkade tjänster/data uppsättningar/pipelines/data flöden | Unikt inom en data fabrik. Namn är inte Skift läges känsliga. |<ul><li>Objekt namn måste börja med en bokstav.</li><li>Följande tecken är inte tillåtna: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", " \\ "</li><li>Bindestreck ("-") tillåts inte i namnen på länkade tjänster, data flöden och data uppsättningar.</li></ul>  |
| Integration Runtime |Unikt inom en data fabrik. Namn är inte Skift läges känsliga. |<ul><li>Namnet på integration runtime får bara innehålla bokstäver, siffror och bindestreck (-).</li><li>Det första och sista tecknet måste vara en bokstav eller en siffra. Varje bindestreck (-) måste föregås omedelbart och följas av en bokstav eller en siffra.</li><li>Efterföljande bindestreck tillåts inte i integration runtime-namn. </li></ul> |
| Dataflödesomvandlingar | Unikt inom ett data flöde. Namn är inte Skift läges känsliga | <ul><li>Transformerings namn för data flöde får bara innehålla bokstäver och siffror</li><li>Det första tecknet måste vara en bokstav. </li></ul> |
| Resursgrupp |Unikt mellan Microsoft Azure. Namn är inte Skift läges känsliga. | Mer information finns i [namngivnings regler och begränsningar för Azure](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). |
| Pipeline-parametrar & variabel  |Unikt i pipelinen. Namn är inte Skift läges känsliga. | <ul><li>Validerings kontrollen av parameter namn och variabel namn är begränsad till unikhet på grund av bakåtkompatibilitet.</li><li>När du använder parametrar eller variabler för att referera till entitetsnamn, till exempel länkad tjänst, gäller reglerna för namngivning av entiteter.</li><li>En bra idé är att följa namngivnings regler för data flödes omvandling för att namnge dina pipelines parametrar och variabler.</li></ul> |

## <a name="next-steps"></a>Nästa steg

Lär dig hur du skapar data fabriker genom att följa steg-för-steg-instruktioner i [snabb start: skapa en data fabriks](quickstart-create-data-factory-powershell.md) artikel. 
