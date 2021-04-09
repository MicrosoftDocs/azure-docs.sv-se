---
title: ta med fil
description: ta med fil
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 626074940ced57bdb1ae93f494b7a3847ef83a81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96445009"
---
| Domännamn                  | Utgående portar | Beskrivning                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | Krävs av integration runtime med egen värd för interaktiv redigering. |
| `{datafactory}.{region}.datafactory.azure.net`<br> eller `*.frontend.clouddatahub.net` | 443            | Krävs av integration runtime med egen värd för att ansluta till tjänsten Data Factory. <br>För nya Data Factory som skapats ska du hitta det fullständiga domän namnet från din egen värd Integration Runtime nyckel som är i formatet {DataFactory}. {region}. DataFactory. Azure. net. Om du inte ser det fullständiga domän namnet i den egna värdbaserade integrerings nyckeln använder du *. frontend.clouddatahub.net i stället för gammal data fabrik. |
| `download.microsoft.com`    | 443            | Krävs av den egna värdbaserade integrerings körningen för att ladda ned uppdateringarna. Om du har inaktiverat automatisk uppdatering kan du hoppa över konfigurationen av den här domänen. |
| `*.core.windows.net`          | 443            | Används av integration runtime med egen värd för att ansluta till Azure Storage-kontot när du använder funktionen för [mellanlagrad kopiering](../articles/data-factory/copy-activity-performance.md#staged-copy) . |
| `*.database.windows.net`      | 1433           | Krävs endast när du kopierar från eller till Azure SQL Database eller Azure Synapse Analytics och valfritt annat. Använd funktionen för mellanlagrad kopiering för att kopiera data till SQL Database eller Azure Synapse Analytics utan att öppna port 1433. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Krävs endast när du kopierar från eller till Azure Data Lake Store och valfritt annat. |
