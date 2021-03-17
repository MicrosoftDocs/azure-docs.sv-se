---
author: baanders
description: inkludera fil för Azure Digitals-självstudier – krav för exempelprojektet
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622058"
---
## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här självstudien måste du först uppfylla följande krav. 

Om du inte har en Azure-prenumeration kan du **skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** innan du börjar.

### <a name="get-required-resources"></a>Hämta nödvändiga resurser

För att slutföra den här självstudien **installerar du [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), version 16,5 eller senare** på din utvecklings dator. Om du redan har installerat en äldre version kan du öppna *Visual Studio Installer* -appen på datorn och följa anvisningarna för att uppdatera installationen.

>[!NOTE]
> Se till att installationen av Visual Studio 2019 omfattar **[arbets belastningen Azure Development](/dotnet/azure/configure-visual-studio)**. Den här arbets belastningen gör det möjligt för ett program att publicera Azure Functions och utföra andra Azure-utvecklings uppgifter.

Självstudien drivs av ett exempel projekt som skrivits i C#. Exemplet finns här: [Azure Digitals dubblare-exempel från slut punkt till slut punkt](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Hämta exempelprojektet** på datorn genom att gå till exempel länken och välja knappen *Bläddra kod* under rubriken. Detta tar dig till GitHub-lagrings platsen för exemplen, som du kan hämta som en *. ZIP* genom att välja knappen *kod* och *Hämta zip*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Vy över de digitala-och lagrings platsen-exemplen på GitHub. Knappen kod är markerad och skapar en liten dialog ruta där hämtnings ZIP-knappen är markerad." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Då hämtas en *. ZIP* -mapp på din dator som **digital-twins-samples-master.zip**. Zippa upp mappen och extrahera filerna.

### <a name="prepare-an-azure-digital-twins-instance"></a>Förbered en digital Azure-instans

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
