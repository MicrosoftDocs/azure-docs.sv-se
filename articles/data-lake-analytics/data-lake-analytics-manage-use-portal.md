---
title: Hantera Azure Data Lake Analytics med hjälp av Azure Portal
description: Den här artikeln beskriver hur du använder Azure Portal för att hantera Data Lake Analytics-konton, data källor, användare & jobb.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: c1057478d9ae7d63bef9477a0b1297fa2c507ffa
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218433"
---
# <a name="manage-azure-data-lake-analytics-using-the-azure-portal"></a>Hantera Azure Data Lake Analytics med hjälp av Azure-portalen
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Den här artikeln beskriver hur du hanterar Azure Data Lake Analytics-konton, data källor, användare och jobb med hjälp av Azure Portal.


<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-lake-analytics-accounts"></a>Hantera Data Lake Analytics-konton

### <a name="create-an-account"></a>Skapa ett konto

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Klicka på **skapa en resurs** informations-och  >  **analys**  >  **data Lake Analytics**.
3. Välj värden för följande objekt: 
   1. **Namn**: namnet på data Lake Analytics kontot.
   2. **Prenumeration**: den Azure-prenumeration som används för kontot.
   3. **Resurs grupp**: Azure-resurs gruppen där kontot ska skapas. 
   4. **Plats**: Azure-datacenter för det data Lake Analytics kontot. 
   5. **Data Lake Store**: standard arkivet som ska användas för data Lake Analytics-kontot. Azure Data Lake Store konto och Data Lake Analytics kontot måste finnas på samma plats.
4. Klicka på **Skapa**. 

### <a name="delete-a-data-lake-analytics-account"></a>Ta bort ett Data Lake Analytics konto

Ta bort standard Data Lake Stores kontot innan du tar bort ett Data Lake Analytics konto.

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Klicka på **Ta bort**.
3. Ange konto namnet.
4. Klicka på **Ta bort**.

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-data-sources"></a>Hantera datakällor

Data Lake Analytics stöder följande data Källor:

* Data Lake Store
* Azure Storage

Du kan använda Datautforskaren för att bläddra bland data källor och utföra grundläggande fil hanterings åtgärder. 

### <a name="add-a-data-source"></a>Lägga till en datakälla

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Klicka på **data källor**.
3. Klicka på **Lägg till data källa**.
    
   * Om du vill lägga till ett Data Lake Store-konto behöver du konto namnet och åtkomst till kontot för att kunna fråga det.
   * Du behöver lagrings kontot och konto nyckeln för att lägga till Azure Blob Storage. Du hittar dem genom att gå till lagrings kontot i portalen.

## <a name="set-up-firewall-rules"></a>Konfigurera brand Väggs regler

Du kan använda Data Lake Analytics för att ytterligare låsa åtkomsten till ditt Data Lake Analytics-konto på nätverks nivå. Du kan aktivera en brand vägg, ange en IP-adress eller definiera ett IP-adressintervall för dina betrodda klienter. När du har aktiverat dessa åtgärder kan endast klienter som har IP-adresserna inom det definierade intervallet ansluta till butiken.

Om andra Azure-tjänster, t. ex. Azure Data Factory eller virtuella datorer, ansluter till Data Lake Analytics konto kontrollerar du att **Tillåt Azure-tjänster** **är aktiverat.** 

### <a name="set-up-a-firewall-rule"></a>Konfigurera en brandväggsregel

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Klicka på **brand vägg** på menyn till vänster.

## <a name="add-a-new-user"></a>Lägga till en ny användare

Du kan använda **guiden Lägg till användare** för att enkelt etablera nya data Lake användare.

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Till vänster, under **komma igång** klickar du på **guiden Lägg till användare**.
3. Välj en användare och klicka sedan på **Välj**.
4. Välj en roll och klicka sedan på **Välj**. Om du vill konfigurera en ny utvecklare att använda Azure Data Lake väljer du rollen **data Lake Analytics utvecklare** .
5. Välj åtkomst kontrol listor (ACL: er) för U-SQL-databaserna. När du är nöjd med dina val klickar du på **Välj**.
6. Välj ACL: er för filer. Ändra inte ACL: er för rotmappen "/" och för mappen/system för standard arkivet. Klicka på **Välj**.
7. Granska alla valda ändringar och klicka sedan på **Kör**.
8. När guiden har slutförts klickar du på **klart**.

## <a name="manage-azure-role-based-access-control"></a>Hantera rollbaserad åtkomst kontroll i Azure

Precis som andra Azure-tjänster kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att styra hur användare interagerar med tjänsten.

De vanliga Azure-rollerna har följande funktioner:
* **Ägare**: kan skicka jobb, övervaka jobb, avbryta jobb från valfri användare och konfigurera kontot.
* **Deltagare**: kan skicka jobb, övervaka jobb, avbryta jobb från valfri användare och konfigurera kontot.
* **Läsare**: kan övervaka jobb.

Använd rollen som Data Lake Analytics utvecklare för att göra det möjligt för U-SQL-utvecklare att använda tjänsten Data Lake Analytics. Du kan använda rollen Data Lake Analytics utvecklare för att:
* Skicka jobb.
* Övervaka jobb status och förlopp för jobb som skickats av en användare.
* Se U-SQL-skripten från jobb som skickats av någon användare.
* Avbryt endast dina egna jobb.

### <a name="add-users-or-security-groups-to-a-data-lake-analytics-account"></a>Lägga till användare eller säkerhets grupper i ett Data Lake Analytics konto

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Klicka på **åtkomst kontroll (IAM)**  >  **Lägg till roll tilldelning**.
3. Välj en roll.
4. Lägg till en användare.
5. Klicka på **OK**.

>[!NOTE]
>Om en användare eller en säkerhets grupp behöver skicka jobb måste de också ha behörighet för Store-kontot. Mer information finns i [skydda data som lagrats i data Lake Store](../data-lake-store/data-lake-store-secure-data.md).
>

<!-- ################################ -->
<!-- ################################ -->

## <a name="manage-jobs"></a>Hantera jobb

### <a name="submit-a-job"></a>Skicka ett jobb

1. I Azure Portal går du till ditt Data Lake Analytics-konto.

2. Klicka på **nytt jobb**. För varje jobb konfigurerar du:

    1. **Jobb namn**: namnet på jobbet.
    2. **Prioritet**: lägre tal har högre prioritet. Om två jobb placeras i kö körs värdet med lägre prioritet först.
    3. **Parallelitet**: det maximala antalet beräknings processer som ska reserveras för det här jobbet.

3. Klicka på **Skicka jobb**.

### <a name="monitor-jobs"></a>Övervaka jobb

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Klicka på **Visa alla jobb**. En lista över alla aktiva och senast avslutade jobb i kontot visas.
3. Du kan också klicka på **filter** för att hitta jobben efter **tidsintervall**, **jobb namn** och **författar** värden. 

### <a name="monitoring-pipeline-jobs"></a>Övervaka pipeline-jobb
Jobb som ingår i en pipeline fungerar tillsammans, vanligt vis i turordning, för att uppnå ett speciellt scenario. Du kan till exempel ha en pipeline som rensar, extraherar, transformerar och sammanställer användning för kund insikter. Pipeline-jobb identifieras med hjälp av egenskapen "Pipeline" när jobbet skickades. Jobb som schemalagts med ADF v2 har automatiskt den här egenskapen ifylld. 

Så här visar du en lista över U-SQL-jobb som ingår i pipelines: 

1. I Azure Portal går du till dina Data Lake Analytics-konton.
2. Klicka på **Job Insights (jobb insikter**). Fliken "alla jobb" är standard som visar en lista över aktiva, köade och avslutade jobb.
3. Klicka på fliken **pipeline-jobb** . En lista med pipeline-jobb visas tillsammans med sammanställd statistik för varje pipeline.

### <a name="monitoring-recurring-jobs"></a>Övervaka återkommande jobb
Ett återkommande jobb är ett som har samma affärs logik men använder olika indata varje gång den körs. Vi rekommenderar att återkommande jobb alltid lyckas och har relativt stabil körnings tid. genom att övervaka dessa beteenden kan du se till att jobbet är felfritt. Återkommande jobb identifieras med hjälp av egenskapen "upprepning". Jobb som schemalagts med ADF v2 har automatiskt den här egenskapen ifylld.

Så här visar du en lista över U-SQL-jobb som är återkommande: 

1. I Azure Portal går du till dina Data Lake Analytics-konton.
2. Klicka på **Job Insights (jobb insikter**). Fliken "alla jobb" är standard som visar en lista över aktiva, köade och avslutade jobb.
3. Klicka på fliken **återkommande jobb** . En lista med återkommande jobb visas tillsammans med sammanställd statistik för varje återkommande jobb.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Hantera Azure Data Lake Analytics med Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Hantera Azure Data Lake Analytics med hjälp av principer](data-lake-analytics-account-policies.md)
