---
author: baanders
description: inkludera fil Azure Digital Twins självstudier – förutsättningar för exempelprojektet
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 00d584690d37f1dcc47b785ef533abe888befec3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107510737"
---
## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här självstudien måste du först slutföra följande krav. 

Om du inte har en Azure-prenumeration kan du **skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** innan du börjar.

### <a name="get-required-resources"></a>Hämta nödvändiga resurser

För att slutföra den här **[självstudien installerar Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), version 16.5** eller senare på utvecklingsdatorn. Om du redan har en äldre version installerad kan du *öppna Visual Studio Installer-appen* på datorn och följa anvisningarna för att uppdatera installationen.

>[!NOTE]
> Se till att installationen Visual Studio 2019 innehåller **[arbetsbelastningen Azure Development](/dotnet/azure/configure-visual-studio)**. Med den här arbetsbelastningen kan ett program publicera Azure-funktioner och utföra andra Azure-utvecklingsuppgifter.

Självstudien drivs av ett exempelprojekt som skrivits i C#. Exemplet finns här: [Azure Digital Twins exempel från slutet till slut.](/samples/azure-samples/digital-twins-samples/digital-twins-samples) **Hämta exempelprojektet på** datorn genom att gå till exempellänken och välja *knappen Bläddra* efter kod under rubriken. Då kommer du till GitHub-lagringsplatsen för exemplen, som du kan ladda ned som *en . ZIP* genom att välja *knappen Kod* och Ladda *ned ZIP.*

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Skärmbild av lagringsplatsen digital-twins-samples på GitHub. Knappen Kod är markerad och skapar en liten dialogruta där knappen Ladda ned ZIP är markerad." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Då laddas en *. ZIP-mapp* till datorn som **digital-twins-samples-master.zip**. Packa upp mappen och extrahera filerna.

### <a name="prepare-an-azure-digital-twins-instance"></a>Förbereda en Azure Digital Twins instans

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
