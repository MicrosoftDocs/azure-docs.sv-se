---
title: Skicka aviseringar från Azure Application Insights | Microsoft Docs
description: Självstudie om hur du skickar aviseringar som svar på fel i ditt program med hjälp av Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 6b8cc951301ec315f758478eda1c0736882e66f2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100628192"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Övervaka och skicka aviseringar om programmets hälsotillstånd med Azure Application Insights

Med Azure Application Insights kan du övervaka programmet och skicka aviseringar när det inte är tillgängligt, har drabbats av fel eller har prestandaproblem.  Den här självstudien vägleder dig genom processen att skapa tester för att kontinuerligt kontrol lera tillgängligheten för ditt program.

Lär dig att:

> [!div class="checklist"]
> * Skapa tillgänglighetstest för att kontinuerligt kontrollera programmets respons
> * Skicka e-post till administratörer när ett problem uppstår

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

Skapa en [Application Insights-resurs](../app/create-new-resource.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="create-availability-test"></a>Skapa tillgänglighetstest

Med tillgänglighetstester i Application Insights kan du automatiskt testa ditt program från olika platser runt om i världen.   I den här självstudien utför du ett URL-test för att säkerställa att webb programmet är tillgängligt.  Du kan även skapa en fullständig genomgång för att testa driften detaljerat. 

1. Välj **Application Insights** och sedan din prenumeration.  

2. Välj **tillgänglighet** under menyn **Undersök** och klicka sedan på **skapa test**.

    ![Lägga till tillgänglighetstest](media/tutorial-alert/add-test-001.png)

3. Skriv ett namn på testet och lämna de andra standardvärdena.  Det här valet utlöser begär Anden för programmets URL var 5: e minut från fem olika geografiska platser.

4. Välj **aviseringar** för att öppna List rutan **varningar** där du kan definiera information för hur du ska svara om testet Miss lyckas. Välj **nära Real** tid och Ställ in statusen på **aktive rad.**

    Skriv en e-postadress att skicka när aviseringsvillkoren uppfylls.  Om du vill kan du ange adressen till en webhook att anropa när aviseringsvillkoren uppfylls.

    ![Skapa test](media/tutorial-alert/create-test-001.png)

5. Gå tillbaka till test panelen, Välj ellipserna och redigera aviseringen för att ange konfigurationen för din nära real tids avisering.

    ![Redigera avisering](media/tutorial-alert/edit-alert-001.png)

6. Ange att platser som inte är större än eller lika med 3. Skapa en [Åtgärds grupp](../alerts/action-groups.md) för att konfigurera vem som får ett meddelande när tröskelvärdet för aviseringar överskrids.

    ![Spara aviserings gränssnitt](media/tutorial-alert/save-alert-001.png)

7. När du har konfigurerat aviseringen klickar du på test namnet för att visa information från varje plats. Tester kan visas i både linje diagram-och punkt diagram format för att visualisera lyckade/misslyckade under ett angivet tidsintervall.

    ![Testinformation](media/tutorial-alert/test-details-001.png)

8. Du kan öka detalj nivån för alla tester genom att klicka på dess punkt i punkt diagrammet. Då startas vyn transaktions detaljer från slut punkt till slut punkt. Exemplet nedan visar informationen för en misslyckad begäran.

    ![Testresultat](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du aviserar om problem går du vidare till nästa självstudie, där du lär dig att analyserar hur användare interagerar med ditt program.

> [!div class="nextstepaction"]
> [Förstå användare](./tutorial-users.md)

