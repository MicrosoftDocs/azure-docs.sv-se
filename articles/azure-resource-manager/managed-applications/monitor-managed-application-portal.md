---
title: Använda Azure Portal för att övervaka en hanterad app
description: Visar hur du använder Azure Portal för att övervaka tillgänglighet och aviseringar för ett hanterat program.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: a02062edd1a940bcc6588ab53457e0af91fedd9a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578265"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Övervaka en distribuerad instans av ett hanterat program

När du har distribuerat ett hanterat program till din Azure-prenumeration kanske du vill kontrol lera status för programmet. Den här artikeln innehåller alternativ i Azure Portal för att kontrol lera statusen. Du kan övervaka tillgängligheten för resurserna i ditt hanterade program. Du kan också konfigurera och visa aviseringar.

## <a name="view-resource-health"></a>Visa information om resurshälsa

1. Välj din instansen av hanterade program.

   ![Välj hanterat program](./media/monitor-managed-application-portal/select-managed-application.png)

1. Välj **Resource Health**.

   ![Välj resurs hälsa](./media/monitor-managed-application-portal/select-resource-health.png)

1. Visa tillgängligheten för resurserna i ditt hanterade program.

   ![Visa information om resurshälsa](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Visa aviseringar

1. Välj **aviseringar**.

   ![Välj aviseringar](./media/monitor-managed-application-portal/select-alerts.png)

1. Om du har konfigurerat varnings regler visas information om aviseringar som har Aktiver ATS.

   ![Visa aviseringar](./media/monitor-managed-application-portal/view-alerts.png)

1. Om du vill lägga till aviserings regler väljer du **+ ny varnings regel**.

   ![Skapa avisering](./media/monitor-managed-application-portal/create-new-alert.png)

Du kan skapa aviseringar för din hanterade program instans eller resurserna i det hanterade programmet. Information om hur du skapar aviseringar finns i [Översikt över aviseringar i Microsoft Azure](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Nästa steg

* Exempel på hanterade program finns i [exempel projekt för Azure-hanterade program](sample-projects.md).
* Information om hur du distribuerar ett hanterat program finns i [distribuera tjänst katalog program via Azure Portal](deploy-service-catalog-quickstart.md).