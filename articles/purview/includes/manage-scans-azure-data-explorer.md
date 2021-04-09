---
author: amberz
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.author: amberz
ms.openlocfilehash: bf872feae9c3a7ca94e5252872adee2b653f5524
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896173"
---
## <a name="creating-and-running-a-scan"></a>Skapa och köra en sökning

> [!Note] 
> De steg och skärm dum par som visas nedan illustrerar den allmänna processen för att hantera genomsökningar över olika typer av data källor. Alternativen kan skilja sig något beroende på vilka typer av data källor som du arbetar med.

Om du vill skapa och köra en ny genomsökning gör du följande:

1. Navigera till **källorna**

1. Välj den data källa som du har registrerat.

1. Välj **+ Ny skanning**

1. Välj autentiseringsuppgifter för att ansluta till data källan. 

   :::image type="content" source="media/manage-scans/set-up-scan-data-explorer.png" alt-text="Konfigurera genomsökning":::

1. Du kan begränsa sökningen till vissa delar av data källan, t. ex. mappar, samlingar eller scheman genom att kontrol lera lämpliga objekt i listan.

   :::image type="content" source="media/manage-scans/scope-your-scan-data-explorer.png" alt-text="Sök omfång i sökningen":::

1. Välj en skannings regel uppsättning som du vill skanna. Du kan välja mellan system standard, befintliga anpassade eller skapa en ny rad.

   :::image type="content" source="media/manage-scans/scan-rule-set-data-explorer.png" alt-text="Sök regel uppsättning":::

1. Välj din genomsöknings utlösare. Du kan ställa in ett schema eller köra genomsökningen en gång.

   :::image type="content" source="media/manage-scans/trigger-scan-data-explorer.png" alt-text="utlösare":::

1. Granska genomsökningen och välj **Spara och kör**.

## <a name="viewing-your-scans-and-scan-runs"></a>Visa genomsökningar och skannings körningar

Gör så här om du vill visa befintliga genomsökningar:

1. Gå till hanterings centret. Välj **data källor** under avsnittet **källor och sökning** . 

2. Välj önskad data källa. En lista över befintliga genomsökningar visas på den data källan.

3. Välj den genomsökning vars resultat du är intresse rad av.

4. På den här sidan visas alla tidigare skannings körningar tillsammans med mått och status för varje genomsöknings körning. Den visar även om din genomsökning har schemalagts eller manuell, hur många till gångar som har klassificeringar tillämpade, hur många totala till gångar som har identifierats, start-och slut tid för genomsökningen och den totala genomsökningens varaktighet.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>Hantera dina genomsökningar – redigera, ta bort eller Avbryt

Gör följande för att hantera eller ta bort en genomsökning:

1. Gå till hanterings centret. Välj **data källor** under avsnittet **källor och sökning** och välj sedan på önskad data källa.

2. Välj den genomsökning som du vill hantera. Du kan redigera sökningen genom att välja **Redigera**.

3. Du kan ta bort din genomsökning genom att välja **ta bort**. 
