---
title: Säkra Azure Data Lake Analytics för flera användare
description: Lär dig hur du konfigurerar flera användare att köra jobb i Azure Data Lake Analytics.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 05/30/2018
ms.openlocfilehash: 52bdd80dba1d5de150f403199fbd5c14d188a410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92220150"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Konfigurera användar åtkomst till jobb information till jobb information i Azure Data Lake Analytics 

I Azure Data Lake Analytics kan du använda flera användar konton eller tjänstens huvud namn för att köra jobb. 

För att samma användare ska kunna se den detaljerade jobb informationen måste användarna kunna läsa innehållet i arbetsmapparna. Jobbets mappar finns i `/system/` katalogen. 

Om de behörigheter som krävs inte har kon figurer ATS kan användaren se ett fel: `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>Konfigurera användar åtkomst till jobb information

Du kan använda **guiden Lägg till användare** för att konfigurera ACL: er på mapparna. Mer information finns i [lägga till en ny användare](data-lake-analytics-manage-use-portal.md#add-a-new-user).

Om du behöver mer detaljerad kontroll eller om du behöver skripta behörigheterna skyddar du mapparna på följande sätt:

1. Bevilja **Kör** -behörigheter (via en åtkomst-ACL) i rotmappen:
   - /
   
2. Bevilja **Kör** -och **Läs** behörigheter (via en åtkomst-ACL och en standard-ACL) på de mappar som innehåller arbetsmapparna. Till exempel, för ett jobb som kördes den 25 maj 2018, behöver du åtkomst till dessa mappar:
   - /system
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>Nästa steg
[Lägg till en ny användare](data-lake-analytics-manage-use-portal.md#add-a-new-user)
