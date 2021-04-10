---
author: baanders
description: inkludera fil för Azure Digitals dubblare-självstudier – konfigurera ett exempel projekt
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 67a2799a93141ad84f458642d8499a58784cc19c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463906"
---
## <a name="configure-the-sample-project"></a>Konfigurera exempelprojektet

Konfigurera sedan ett exempel klient program som ska interagera med din Azure Digital-instansen.

Navigera på datorn till filen som du laddade ned tidigare från [*Azure Digitals dubbla delar från slut punkt till slut punkt*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) (och zippa upp den om du inte redan har gjort det).

När du är i mappen navigerar du till _AdtSampleApp_. Öppna _**AdtE2ESample. SLN**_ i Visual Studio 2019. 

I Visual Studio väljer du den _SampleClientApp-> **appsettings.jspå**_ filen för att öppna den i redigerings fönstret. Detta fungerar som en för hands uppsättnings-JSON-fil med nödvändiga konfigurationsfiler för att köra projektet.

I fil texten ändrar du `instanceUrl` till *URL: en* för Azure Digital-instansen med värdnamn (genom att lägga till **_https://_** framför *värd namnet*, som visas nedan).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Spara och stäng filen. 

Konfigurera sedan *appsettings.jspå* filen som ska kopieras till utdatakatalogen när du skapar *SampleClientApp*. Det gör du genom att högerklicka på *appsettings.jspå* filen och välja **Egenskaper**. I panelen **Egenskaper** letar du reda på egenskapen *Kopiera till utdata-katalog* . Ändra värdet till **Kopiera om** det är senare om det inte redan är inställt på det.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Utdrag från Visual Studio-fönstret som visar Solution Explorer fönstret med appsettings.jsmarkerat och egenskaps fönstret med egenskapen kopiera till utgående katalog inställt på Kopiera om nyare" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Se till att _**AdtE2ESample**_ -projektet är öppet i Visual Studio och fortsätt att använda det i självstudien.

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
