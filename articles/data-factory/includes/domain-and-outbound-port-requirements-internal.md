---
title: ta med fil
description: ta med fil
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 24a541080b580eab967987fa7c92cea64b99d65c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389523"
---
| Domännamn                                          | Utgående portar | Beskrivning                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Offentligt moln: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> Porslin `*.servicebus.chinacloudapi.cn`   | 443            | Krävs av integration runtime med egen värd för interaktiv redigering. |
| Offentligt moln: `{datafactory}.{region}.datafactory.azure.net`<br> eller `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> Porslin `{datafactory}.{region}.datafactory.azure.cn` | 443            | Krävs av integration runtime med egen värd för att ansluta till tjänsten Data Factory. <br>För nya skapade Data Factory i det offentliga molnet kan du hitta det fullständiga domän namnet från din egen värd Integration Runtime nyckel som är i formatet {DataFactory}. {region}. DataFactory. Azure. net. Om du inte ser det fullständiga domän namnet i den egna värdbaserade integrerings nyckeln använder du *. frontend.clouddatahub.net i stället för gammal data fabrik. |
| `download.microsoft.com`    | 443            | Krävs av den egna värdbaserade integrerings körningen för att ladda ned uppdateringarna. Om du har inaktiverat automatisk uppdatering kan du hoppa över konfigurationen av den här domänen. |
| Key Vault-URL | 443           | Krävs av Azure Key Vault om du lagrar autentiseringsuppgifterna i Key Vault. |
