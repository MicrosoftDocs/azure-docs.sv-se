---
title: Visa logg historik för webbjobb
description: Visa logg historik för misslyckade och slutförda jobb.
author: ggailey777
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b4e421458438aecc7c08e397f1fc919a8cc2225
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746692"
---
# <a name="view-webjob-history-in-the-azure-portal"></a>Visa webb jobbs historik i Azure Portal

Visa logg historik för misslyckade och slutförda jobb.

1. Välj det webbjobb som du vill visa historik för och välj sedan knappen **loggar** .

    ![Knappen loggar](./media/web-sites-create-web-jobs/wjbladelogslink.png)

1. På sidan **information om webb jobb** väljer du en tid för att visa information om en körning.

    ![Information om webb jobb](./media/web-sites-create-web-jobs/webjobdetails.png)

1. På sidan **jobb körnings information** väljer du **Växla utdata** för att se texten i logg innehållet.

    ![Körnings information för webb jobb](./media/web-sites-create-web-jobs/webjobrundetails.png)

    Om du vill visa utmatnings texten i ett separat webbläsarfönster väljer du **Hämta**. Om du vill ladda ned själva texten högerklickar du på **Ladda ned** och använder webbläsarens alternativ för att spara filens innehåll.

1. Välj länken **WebJobs** -länk överst på sidan för att gå till en lista med WebJobs.

    ![Webb jobbets dynamiska adress](./media/web-sites-create-web-jobs/breadcrumb.png)

    ![Lista med WebJobs på Historik panelen](./media/web-sites-create-web-jobs/webjobslist.png)

## <a name="next-steps"></a>Nästa steg

* Använd [WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) för att förenkla många programmerings aktiviteter

* Lär dig att [utveckla och distribuera WebJobs med Visual Studio](webjobs-dotnet-deploy-vs.md)