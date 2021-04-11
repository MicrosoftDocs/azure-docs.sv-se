---
title: Ansluta en datafabrik till Azure Purview
description: Lär dig hur du ansluter en Data Factory till Azure-avdelningens kontroll
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.topic: conceptual
ms.custom:
- seo-lt-2019
- references_regions
ms.date: 12/3/2020
ms.openlocfilehash: 44f093f96d0f4653a6fcca94aaa97264c93e3c7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727948"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Ansluta Data Factory till Azure-avdelningens kontroll (för hands version)
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du ansluter Data Factory till Azure-avdelningens kontroll och hur du rapporterar data härkomst för Azure Data Factory aktiviteter kopiera data, data flöde och kör SSIS-paket.


## <a name="connect-data-factory-to-azure-purview"></a>Ansluta Data Factory till Azure-avdelningens kontroll
Azure dataavdelningens kontrolls är en ny moln tjänst som används av data användare centralt för att hantera data styrning över sin datafastighet, som sträcker sig över moln-och lokal miljöer. Du kan ansluta dina Data Factory till Azure-avdelningens kontroll och anslutningen gör att du kan använda Azure avdelningens kontroll för att samla in härkomst data från kopierings-, data flödes-och EXECUTE SSIS-paket. Det finns två sätt att ansluta Data Factory till Azure avdelningens kontroll:
### <a name="register-azure-purview-account-to-data-factory"></a>Registrera Azure avdelningens kontroll-konto till Data Factory
1. I ADF-portalen går du till **Hantera**  ->  **Azure-avdelningens kontroll**. Välj **Anslut till ett avdelningens kontroll-konto**. 

:::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Skärm bild för att registrera ett avdelningens kontroll-konto.":::
2. Du kan välja **från Azure-prenumerationen** eller **ange manuellt**. **Från Azure-prenumeration** kan du välja det konto som du har åtkomst till. 
3. När du är ansluten bör du kunna se namnet på avdelningens kontroll-kontot på fliken **avdelningens kontroll-konto**. 
4. Du kan använda Sök fältet längst upp i mitten av Azure Data Factory Portal för att söka efter data. 

Om du ser varning i Azure Data Factory Portal när du har registrerat Azure avdelningens kontroll-kontot för Data Factory följer du stegen nedan för att åtgärda problemet:

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Skärm bild för varning om att registrera ett avdelningens kontroll-konto.":::

1. Gå till Azure Portal och hitta din data fabrik. Välj avsnittet Taggar och se om det finns en tagg med namnet **catalogUri**. Om inte, kopplar du från och återansluter Azure avdelningens kontroll-kontot på ADF-portalen.

:::image type="content" source="./media/data-factory-purview/register-purview-account-tag.png" alt-text="Skärm bild för taggar för att registrera ett avdelningens kontroll-konto.":::

2. Kontrol lera om behörigheten har beviljats för att registrera ett Azure avdelningens kontroll-konto till Data Factory. Se [hur du ansluter Azure Data Factory och Azure avdelningens kontroll](../purview/how-to-link-azure-data-factory.md#create-new-data-factory-connection)

### <a name="register-data-factory-in-azure-purview"></a>Registrera Data Factory i Azure avdelningens kontroll
Information om hur du registrerar Data Factory i Azure-avdelningens kontroll finns i [så här ansluter du Azure Data Factory och Azure avdelningens kontroll](../purview/how-to-link-azure-data-factory.md). 

## <a name="report-lineage-data-to-azure-purview"></a>Rapportera härkomst-data till Azure avdelningens kontroll
När kunder kör SSIS för att kopiera, data flöde eller köra-paket i Azure Data Factory kan kunder få beroende relationen och få en översikt över hela arbets flödes processen mellan data källor och mål.
Information om hur du samlar in härkomst från Azure Data Factory finns i [Data Factory härkomst](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities).

## <a name="next-steps"></a>Nästa steg
[Användar guide för katalog härkomst](../purview/catalog-lineage-user-guide.md)

[Självstudie: push Data Factory härkomst data till Azure avdelningens kontroll](turorial-push-lineage-to-purview.md)