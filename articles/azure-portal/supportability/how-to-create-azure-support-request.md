---
title: Skapa en supportbegäran för Azure
description: Kunder som behöver hjälp kan använda Azure Portal för att hitta självbetjänings lösningar och för att skapa och hantera support förfrågningar.
ms.assetid: fd6841ea-c1d5-4bb7-86bd-0c708d193b89
ms.topic: how-to
ms.date: 06/25/2020
ms.openlocfilehash: 71aba8f2769a75805f47d90b71a8db4eeb26a97a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652402"
---
# <a name="create-an-azure-support-request"></a>Skapa en Azure-supportbegäran

Med Azure kan du skapa och hantera support förfrågningar, även kallade support biljetter. Du kan skapa och hantera begär anden i [Azure Portal](https://portal.azure.com), som beskrivs i den här artikeln. Du kan också skapa och hantera begär Anden via programmering, med hjälp av [Azures support biljett REST API](/rest/api/support)eller med hjälp av [Azure CLI](/cli/azure/azure-cli-support-request).

> [!NOTE]
> Azure Portal-URL: en är unik för det Azure-moln där din organisation har distribuerats.
>
>* Azure Portal för kommersiellt bruk är: [https://portal.azure.com](https://portal.azure.com)
>* Azure Portal för Tyskland: [https://portal.microsoftazure.de](https://portal.microsoftazure.de)
>* Azure Portal för USA myndigheter är: [https://portal.azure.us](https://portal.azure.us)

Support ärendets upplevelse fokuserar på tre huvudsakliga mål:

* **Effektiviserat**: gör support och fel sökning lätt att hitta och förenkla hur du skickar en supportbegäran.
* **Integrerad**: du kan enkelt öppna en support förfrågan när du felsöker ett problem med en Azure-resurs, utan att växla sammanhang.
* **Effektiv**: samla in viktig information som support teknikern behöver för att effektivt lösa problemet.

Azure ger obegränsad support för prenumerations hantering, bland annat fakturering, kvot justeringar och konto överföringar. För teknisk support behöver du en Support plan. Mer information finns i [Jämföra supportplaner](https://azure.microsoft.com/support/plans).

## <a name="getting-started"></a>Komma igång

Du kan få **Hjälp + Support** i Azure Portal. Den finns på Azure Portal-menyn, i den globala rubriken eller på resurs-menyn för en tjänst. Innan du kan skicka en förfrågan till en supportbegäran måste du ha rätt behörighet.

### <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll

Om du vill skapa en supportbegäran måste du vara [ägare](../../role-based-access-control/built-in-roles.md#owner), [deltagare](../../role-based-access-control/built-in-roles.md#contributor) eller tilldelad rollen [Support Request Contributor](../../role-based-access-control/built-in-roles.md#support-request-contributor) på prenumerations nivå. Om du vill skapa en supportbegäran utan en prenumeration, till exempel ett Azure Active Directory scenario, måste du vara [administratör](../../active-directory/roles/permissions-reference.md).

### <a name="go-to-help--support-from-the-global-header"></a>Gå till hjälp + support från den globala rubriken

Så här startar du en supportbegäran var som helst i Azure Portal:

1. Välj **?** i den globala rubriken. Välj sedan **Hjälp + Support**.

   ![Hjälp och support](./media/how-to-create-azure-support-request/helpandsupportnewlower.png)

1. Välj **Ny supportbegäran**. Följ anvisningarna för att ange information om problemet. Vi föreslår några möjliga lösningar, samlar in information om problemet och hjälper dig att skicka och spåra support förfrågan.

   ![Ny supportbegäran](./media/how-to-create-azure-support-request/newsupportrequest2lower.png)

### <a name="go-to-help--support-from-a-resource-menu"></a>Gå till hjälp + support från en resurs meny

För att starta en support förfrågan i kontexten för resursen arbetar du för närvarande med:

1. På resurs-menyn i avsnittet **support och fel sökning** väljer du **ny supportbegäran**.

   ![I sammanhang](./media/how-to-create-azure-support-request/incontext2lower.png)

1. Följ anvisningarna för att ge oss information om problemet. När du startar support förfrågnings processen från resursen väljs vissa alternativ i förväg.

## <a name="create-a-support-request"></a>Skapa en supportbegäran

Vi vägleder dig genom några steg för att samla in information om ditt problem och hjälpa dig att lösa det. Varje steg beskrivs i följande avsnitt.

### <a name="basics"></a>Grundläggande inställningar

Det första steget i support förfrågnings processen samlar in grundläggande information om ditt problem och ditt support avtal.

På fliken **grundläggande** i den **nya support förfrågan** använder du selectrs för att tala om för oss om problemet. Först ska du identifiera några allmänna kategorier för ärende typen och välja den relaterade prenumerationen. Välj tjänsten, till exempel **virtuell dator som kör Windows**. Välj resursen, till exempel namnet på den virtuella datorn. Beskriv problemet med dina egna ord och välj sedan **problem typ** och **problem under typ** för att få mer information.

![Bladet Grundläggande inställningar](./media/how-to-create-azure-support-request/basics2lower.png)

### <a name="solutions"></a>Lösningar

När du har samlat in grundläggande information visar vi lösningar som du kan prova på egen hand. I vissa fall kan vi till och med köra en snabb diagnostik. Lösningar skrivs av Azure-tekniker och kommer att lösa de vanligaste problemen.

### <a name="details"></a>Information

Sedan samlar vi in ytterligare information om problemet. Att tillhandahålla grundlig och detaljerad information i det här steget hjälper oss att skicka din support förfrågan till rätt tekniker.

1. Om möjligt ska du meddela oss när problemet har inletts och hur du kan återskapa det. Du kan ladda upp en fil, till exempel en loggfil eller utdata från diagnostiken. Mer information om fil uppladdningar finns i [rikt linjer för fil uppladdning](how-to-manage-azure-support-request.md#file-upload-guidelines).

1. När vi har all information om problemet väljer du hur du får support. I avsnittet **support metod** i **detalj** väljer du allvarlighets graden för påverkan. Den högsta allvarlighets graden beror på [support avtalet](https://azure.microsoft.com/support/plans).

    Som standard är alternativet **dela diagnostisk information** valt. På så sätt kan Azure-supporten samla in [diagnostikinformation](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) från dina Azure-resurser. I vissa fall finns det en andra fråga som inte är markerad som standard, till exempel att begära åtkomst till en virtuell dators minne.

1. Ange önskad kontakt metod, en bra tid att kontakta dig och ditt support språk.

1. Fyll sedan i avsnittet **kontakt information** så att vi kan kontakta dig.

### <a name="review--create"></a>Granska + skapa

Slutför all nödvändig information på varje flik och välj sedan **Granska + skapa**. Kontrol lera den information som du skickar till supporten. Gå tillbaka till en flik och gör en ändring om det behövs. När du är nöjd med att support förfrågan är klar väljer du **skapa**.

En support tekniker kontaktar dig genom att använda den metod du angav. Information om första svars tider finns i [support omfattning och svars](https://azure.microsoft.com/support/plans/response/)tider.


## <a name="next-steps"></a>Nästa steg

Titta på den här videon om du vill veta mer om support alternativ för självhjälp i Azure:

> [!VIDEO https://www.youtube.com/embed/gNhzR5FE9DY]

Följ dessa länkar om du vill veta mer:

* [Så här hanterar du en support förfrågan för Azure](how-to-manage-azure-support-request.md)
* [REST-API för Azure-supportbegäranden](/rest/api/support)
* [Skicka feedback och förslag till oss](https://feedback.azure.com/forums/266794-support-feedback)
* Engagera oss på [Twitter](https://twitter.com/azuresupport)
* Få hjälp från dina kollegor på [sidan Microsoft Q&en fråga](/answers/products/azure)
* Läs mer i [vanliga frågor och svar om support för Azure](https://azure.microsoft.com/support/faq)