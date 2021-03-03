---
title: Justera kvoter och gränser i Azure Data Lake Analytics
description: Lär dig hur du justerar och ökar kvoter och gränser i Azure Data Lake Analytics-konton (ADLA).
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 03/15/2018
ms.openlocfilehash: 416b77fd86316acb8098531257e12b591a0ff052
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710183"
---
# <a name="adjust-quotas-and-limits-in-azure-data-lake-analytics"></a>Justera kvoter och gränser i Azure Data Lake Analytics

Lär dig hur du justerar och ökar kvoten och gränserna i Azure Data Lake Analytics-konton (ADLA). Genom att känna till dessa begränsningar kan du förstå ditt U-SQL-jobb. Alla kvot gränser är mjuka, så du kan öka gränsen genom att kontakta Azure-supporten.

## <a name="azure-subscriptions-limits"></a>Begränsningar för Azure-prenumeration

**Maximalt antal ADLA-konton per prenumeration per region:**  5

Om du försöker skapa ett sjätte ADLA-konto får du ett fel meddelande om att du har nått det maximala antalet tillåtna Data Lake Analytics-konton (5) i region under prenumerations namn.

Om du vill gå utöver den här gränsen kan du prova följande alternativ:

- Välj en annan region om det är lämpligt
- kontakta Azure-supporten genom att [öppna ett support ärende](#increase-maximum-quota-limits) för att begära en kvot ökning.

## <a name="default-adla-account-limits"></a>Standard gränser för ADLA-konton

**Maximalt antal analys enheter (Australien) per konto:** 250, standard 32

Detta är det maximala antalet Australien som kan köras samtidigt i ditt konto. Om det totala antalet aktiva Australien över alla jobb överskrider den här gränsen placeras nyare jobb automatiskt i kö. Exempel:

- Om du bara har ett jobb som körs med 32, kommer det att vänta i jobbkön tills det första jobbet har slutförts när du skickar ett andra jobb.
- Om du redan har fyra jobb som kör och var och en använder 8-Australien, och du skickar ett femte jobb som kräver 8 Australien väntar det i jobbkön tills det finns 8 tillgängliga.

    ![Sidan Azure Data Lake Analytics gränser och kvot](./media/data-lake-analytics-quota-limits/adjust-quota-limits.png)

**Maximalt antal analys enheter per jobb:** 250, standard 32

Detta är det maximala antalet Australien som varje enskilt jobb kan tilldelas i ditt konto. Jobb som har tilldelats mer än den här gränsen avvisas, såvida inte inskickaren påverkas av en beräknings princip (gräns för jobb överföring) som ger dem fler Australien per jobb. Den övre gränsen för det här värdet är den automatiska storleken för kontot.

**Maximalt antal samtidiga U-SQL-jobb per konto:** 20

Detta är det maximala antalet jobb som kan köras samtidigt i ditt konto. Utöver det här värdet köas de nyare jobben automatiskt.

## <a name="adjust-adla-account-limits"></a>Justera ADLA konto gränser

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Välj ett befintligt ADLA-konto.
3. Klicka på **Egenskaper**.
4. Justera värdena för **maximal Australien**, **maximalt antal jobb som körs** och begränsningar för **jobb överföring** för att passa dina behov.

## <a name="increase-maximum-quota-limits"></a>Öka gränserna för högsta kvot

Du hittar mer information om Azure-begränsningar i [dokumentationen för Azures begränsade gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-analytics-limits).

1. Öppna en supportbegäran i Azure Portal.

   ![Azure Data Lake Analytics Portal – hjälp och support](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

   ![Azure Data Lake Analytics Portal nytt support ärende](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)

2. Välj **kvoten** för ärende typen.

3. Välj din **prenumeration** (se till att det inte är en "utvärderings prenumeration").

4. Välj typ av kvot **data Lake Analytics**.

   ![Kvot typ för Azure Data Lake Analytics-supportbegäran](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. På sidan problem förklarar du din begärda öknings gräns med **information** om varför du behöver den här extra kapaciteten.

   ![Azure Data Lake Analytics information om support förfrågan](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Verifiera din kontakt information och skapa support förfrågan.

Microsoft granskar din begäran och försöker hantera dina affärs behov så snart som möjligt.

## <a name="next-steps"></a>Nästa steg

- [Översikt över Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Hantera Azure Data Lake Analytics med hjälp av Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
- [Övervaka och felsök Azure Data Lake Analytics-jobb med hjälp av Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
