---
author: craigshoemaker
ms.service: static-web-apps
ms.topic: include
ms.date: 03/25/2021
ms.author: cshoe
ms.openlocfilehash: 806a30214e0307b8fa101c0de51ed2f16622d433
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2021
ms.locfileid: "105543573"
---
| Egenskap | Beskrivning | Exempel | Obligatorisk |
|---|---|---|---|
| `app_location` | Plats för program koden. | Ange `/` om din program käll kod är i roten av lagrings platsen eller `/app` om program koden finns i en katalog som kallas `app` . | Yes |
| `api_location` | Azure Functionss kodens plats. | Ange `/api` om din app-kod finns i en mapp med namnet `api` . Om det inte går att hitta någon Azure Functions app i mappen, kan inte versionen av det här arbets flödet antas att du inte vill ha något API. | No |
| `output_location` | Platsen för build-utdatakatalogen i förhållande till `app_location` . | Om programmets källkod finns i `/app` och build-skriptet matar ut filer till `/app/build` mappen, anger du `build` som `output_location` värde. | Inga |