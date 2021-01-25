---
title: Använda Cloud Services (utökad support) (förhands granskning)
description: Lär dig nu att skapa och distribuera en moln tjänst i Azure med hjälp av Azure Resource Manager med Visual Studio
author: ghogen
ms.service: cloud-services-extended-support
manager: jillfra
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: tutorial
ms.date: 10/5/2020
ms.author: ghogen
ms.openlocfilehash: b12f8beeee054e547a6b58750d8482d32ee14453
ms.sourcegitcommit: 3c8964a946e3b2343eaf8aba54dee41b89acc123
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/25/2021
ms.locfileid: "98747264"
---
# <a name="create-and-deploy-a-azure-cloud-service-extended-support-using-visual-studio"></a>Skapa och distribuera en Azure Cloud Service (utökad support) med Visual Studio

Från och med [Visual Studio 2019 version 16,9](https://visualstudio.microsoft.com/vs/preview/) (för närvarande i för hands version) kan du arbeta med moln tjänster med Azure Resource Manager (arm), vilket avsevärt fören klar och moderniserar underhåll och hantering av Azure-resurser. Detta aktive ras av en ny Azure-tjänst som kallas Cloud Services (utökad support). Du kan publicera en befintlig moln tjänst till Cloud Services (utökad support). Information om den här Azure-tjänsten finns i [dokumentationen för Cloud Services (utökad support)](overview.md).

> [!IMPORTANT]
> Cloud Services (utökad support) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-feature-for-your-subscription"></a>Registrera funktionen för din prenumeration
Cloud Services (utökad support) är för närvarande en för hands version. Registrera funktionen för din prenumeration på följande sätt:

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```
Mer information finns i [krav för distribution av Cloud Services (utökad support)](deploy-prerequisite.md)

## <a name="create-a-project"></a>Skapa ett projekt

Visual Studio innehåller en projektmall som gör att du kan skapa en moln tjänst i Azure med utökad support, med namnet **Azure Cloud Service (utökad support)**. En moln tjänst är en enkel allmän Azure-tjänst. När projektet har skapats kan du konfigurera, felsöka och distribuera moln tjänsten till Azure i Visual Studio.

### <a name="to-create-an-azure-cloud-service-extended-support-project-in-visual-studio"></a>Skapa ett Azure Cloud Service-projekt (utökad support) i Visual Studio

Det här avsnittet vägleder dig genom att skapa ett Azure Cloud Service-projekt i Visual Studio med en eller flera webb roller.

1. I fönstret Starta väljer du **skapa ett nytt projekt**.

1. I rutan Sök skriver du i *Cloud* och väljer sedan **Azure Cloud Service (utökad support)**.

   ![Ny Azure Cloud service med utökad support](./media/choose-project-template.png)

1. Ge projektet ett namn och välj **skapa**.

   ![Ge projektet ett namn](./media/configure-new-project.png)

1. I dialog rutan **ny Microsoft Azure moln tjänst** väljer du de roller som du vill lägga till och klickar sedan på högerpilen för att lägga till dem i din lösning.

    ![Välj nya Azure Cloud Service-roller](./media/choose-roles.png)

1. Om du vill byta namn på en roll som du har lagt till, hovrar du över rollen i dialog rutan **ny Microsoft Azure moln tjänst** och väljer sedan **Byt namn** på snabb menyn. Du kan också byta namn på en roll i din lösning (i **Solution Explorer**) när den har lagts till.

    ![Byt namn på Azure Cloud Service-rollen](./media/new-cloud-service-rename.png)

Visual Studio Azure-projektet innehåller associationer till roll projekten i lösningen. Projektet innehåller också *tjänst definitions filen* och *tjänst konfigurations filen*:

- **Tjänst definitions fil** – definierar körnings inställningarna för programmet, inklusive vilka roller som krävs, slut punkter och storlek på virtuella datorer.
- **Tjänst konfigurations fil** – konfigurerar hur många instanser av en roll som ska köras och värdena för inställningarna som definierats för en roll.

Mer information om dessa filer finns i [Konfigurera roller för en Azure-moln tjänst med Visual Studio](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service).

## <a name="publish-a-cloud-service"></a>Publicera en moln tjänst

1. Skapa eller öppna ett Azure Cloud Service-projekt i Visual Studio.

1. I **Solution Explorer** högerklickar du på projektet och väljer **publicera** på snabb menyn.

   ![Inloggnings sida](./media/publish-step-1.png)

1. **Konto** – Välj ett konto eller Välj **Lägg till ett konto** i list rutan konto.

1. **Välj din prenumeration** – Välj den prenumeration som ska användas för din distribution. Den prenumeration som du använder för att distribuera Cloud Services (utökad support) måste ha ägar-eller deltagar roller tilldelade via rollbaserad åtkomst kontroll (RBAC). Om din prenumeration inte har någon av dessa roller, se [steg för att lägga till en roll tilldelning](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-steps) för att lägga till den innan du fortsätter.

1. Välj **Nästa** för att gå till sidan **Inställningar** .

   ![Vanliga inställningar](./media/publish-settings.png)

1. **Moln tjänst** – Använd List rutan, Välj en befintlig moln tjänst eller Välj **Skapa ny** och skapa en moln tjänst. Data centret visas inom parentes för varje moln tjänst. Vi rekommenderar att data Center platsen för moln tjänsten är densamma som data Center platsen för lagrings kontot.

   Om du väljer att skapa en ny moln tjänst visas dialog rutan **skapa moln tjänst (utökad support)** . Ange den plats och resurs grupp som du vill använda för moln tjänsten.

   ![Skapa en moln tjänst med utökad support](./media/extended-support-dialog.png)

1. **Build-konfiguration** – Välj antingen **Debug** eller **release**.

1. **Tjänst konfiguration** – Välj antingen **moln** eller **lokalt**.

1. **Lagrings konto** – Välj det lagrings konto som ska användas för distributionen, eller **skapa ett nytt** för att skapa ett lagrings konto. Regionen visas inom parentes för varje lagrings konto. Vi rekommenderar att data Center platsen för lagrings kontot är samma som data Center platsen för moln tjänsten (gemensamma inställningar).

   Azure Storage-kontot lagrar paketet för program distributionen. När programmet har distribuerats tas paketet bort från lagrings kontot.

1. **Key Vault** – ange Key Vault som innehåller hemligheterna för den här moln tjänsten. Detta är aktiverat om fjärr skrivbord är aktiverat eller om certifikat läggs till i konfigurationen.

1. **Aktivera fjärr skrivbord för alla roller** – Välj det här alternativet om du vill kunna fjärrans luta till tjänsten. Du uppmanas att ange autentiseringsuppgifter.

   ![Inställningar för fjärr skrivbord](./media/remote-desktop-configuration.png)

1. Välj **Nästa** för att gå till sidan **diagnostikinställningar** .

   ![Diagnostikinställningar](./media/diagnostics-settings.png)

   Med diagnostik kan du Felsöka en Azure-moln tjänst (eller virtuell Azure-dator). Information om diagnostik finns i [Konfigurera diagnostik för Azure Cloud Services och Virtual Machines](https://docs.microsoft.com/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines). Information om Application Insights finns i [Application Insights?](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview).

1. Välj **Nästa** för att gå till sidan **Sammanfattning** .

   ![Sammanfattning](./media/publish-summary.png)

1. **Mål profil** – du kan välja att skapa en publicerings profil från de inställningar som du har valt. Du kan till exempel skapa en profil för en test miljö och en annan för produktion. Spara profilen genom att välja ikonen **Spara** . Guiden skapar profilen och sparar den i Visual Studio-projektet. Om du vill ändra profil namnet öppnar du listan **mål profil** och väljer sedan **hantera...**.

   > [!Note]
   > Publicerings profilen visas i Solution Explorer i Visual Studio och profil inställningarna skrivs till en fil med fil namns tillägget. azurePubxml. Inställningarna sparas som attribut för XML-taggar.

1. När du har konfigurerat alla inställningar för projektets distribution väljer du **publicera** längst ned i dialog rutan. Du kan övervaka process statusen i fönstret för **Azure aktivitets loggens** utdata i Visual Studio.

Grattis! Du har publicerat ditt utökade support moln tjänst projekt till Azure. Om du vill publicera igen med samma inställningar kan du återanvända publicerings profilen eller upprepa stegen för att skapa en ny.

## <a name="clean-up-azure-resources"></a>Rensa Azure-resurser

Om du vill rensa Azure-resurserna som du skapade genom att följa den här självstudien går du till [Azure Portal](https://portal.azure.com), väljer **resurs grupper**, söker efter och öppnar den resurs grupp som du använde för att skapa tjänsten och väljer **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

Konfigurera kontinuerlig integrering (CI) med knappen **Konfigurera** på **publicerings** skärmen. Mer information finns i [dokumentationen om Azure pipeline](https://docs.microsoft.com/azure/devops/pipelines).
