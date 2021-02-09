---
title: Skapa och hantera Azure Migrate-projekt
description: Hitta, skapa, hantera och ta bort projekt i Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0d4ccc1a60f9ac3c2183023d3d3772da6a1e307b
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99830761"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Skapa och hantera Azure Migrate-projekt

Den här artikeln beskriver hur du skapar, hanterar och tar bort [Azure Migrate](migrate-services-overview.md) projekt. 

Den klassiska Azure Migrate tas ur bruk i feb 2024. Efter feb 2024 kommer den klassiska versionen av Azure Migrate inte längre att stödjas och lager-metadata i det klassiska projektet tas bort. Om du använder klassiska Azure Migrate projekt tar du bort projekten och följer stegen för att skapa ett nytt Azure Migrate-projekt. Du kan inte uppgradera klassiska Azure Migrate projekt eller komponenter till Azure Migrate. Se [vanliga frågor och svar](https://docs.microsoft.com/azure/migrate/resources-faq#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) innan du börjar skapa processen.

Ett Azure Migrate-projekt används för att lagra identifierings-, utvärderings-och migrerings-metadata som samlas in från den miljö som du bedömer eller migrerar. I ett projekt kan du spåra identifierade till gångar, skapa utvärderingar och dirigera migreringar till Azure.  

## <a name="verify-permissions"></a>Kontrollera behörigheter

Kontrol lera att du har rätt behörighet för att skapa ett Azure Migrate-projekt:

1. Öppna den relevanta prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst** och välj Visa behörigheter. Du bör ha behörighet som *deltagare* eller *ägare* . 


## <a name="create-a-project-for-the-first-time"></a>Skapa ett projekt för första gången

Skapa ett nytt Azure Migrate-projekt i en Azure-prenumeration.

1. Sök efter *Azure Migrate* i Azure Portal.
2. I **tjänster** väljer du **Azure Migrate**.
3. I **översikten** väljer du **Utvärdera och migrera servrar**.

    ![Alternativ i Översikt för att utvärdera och migrera servrar](./media/create-manage-projects/assess-migrate-servers.png)

4. I **servrar** väljer du **skapa projekt**.

    ![För att börja skapa projekt](./media/create-manage-projects/create-project.png)

5. I **skapa projekt** väljer du Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information** anger du projekt namnet och geografin som du vill skapa projektet i.
    - Geografin används bara för att lagra metadata som samlats in från lokala datorer. Du kan välja valfri mål region för migrering. 
    - Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

8. Välj **Skapa**.

   ![Sida för att mata in projekt inställningar](./media/create-manage-projects/project-details.png)


Vänta några minuter tills Azure Migrate-projektet har distribuerats.

## <a name="create-a-project-in-a-specific-region"></a>Skapa ett projekt i en angiven region

I portalen kan du välja det geografiskt område där du vill skapa projektet. Om du vill skapa projektet i en angiven Azure-region, använder du följande API-kommando för att skapa projektet.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Skapa ytterligare projekt

Om du redan har ett Azure Migrate projekt och vill skapa ett nytt projekt gör du följande:  

1. Sök efter **Azure Migrate** i [Azures offentliga Portal](https://portal.azure.com) eller [Azure Government](https://portal.azure.us).
2. På Azure Migrate instrument panelens >- **servrar** väljer du **ändra** i det övre högra hörnet.

   ![Ändra Azure Migrate projekt](./media/create-manage-projects/switch-project.png)

3. Om du vill skapa ett nytt projekt väljer du **Klicka här**.


## <a name="find-a-project"></a>Hitta ett projekt

Sök efter ett projekt enligt följande:

1. Sök efter *Azure Migrate* i [Azure Portal](https://portal.azure.com).
2. I Azure Migrate instrument panelens >- **servrar** väljer du **ändra** i det övre högra hörnet.

    ![Växla till ett befintligt Azure Migrate-projekt](./media/create-manage-projects/switch-project.png)

3. Välj lämplig prenumeration och Azure Migrate projekt.


### <a name="find-a-legacy-project"></a>Hitta ett äldre projekt

Om du har skapat projektet i den [tidigare versionen](migrate-services-overview.md#azure-migrate-versions) av Azure Migrate kan du söka efter det på följande sätt:

1. Sök efter *Azure Migrate* i [Azure Portal](https://portal.azure.com).
2. Om du har skapat ett projekt i den tidigare versionen av Azure Migrate instrument panelen visas en banderoll som refererar till äldre projekt. Välj banderollen.

    ![Komma åt befintliga projekt](./media/create-manage-projects/access-existing-projects.png)

3. Granska listan över gamla projekt.


## <a name="delete-a-project"></a>Ta bort ett projekt

Ta bort enligt följande:

1. Öppna Azure-resurs gruppen där projektet skapades.
2. På sidan resurs grupp väljer du **Visa dolda typer**.
3. Välj det migrerade projektet som du vill ta bort och dess associerade resurser.
    - Resurs typen är **Microsoft. Migrate/migrateprojects**.
    - Om resurs gruppen uteslutande används av Azure Migrate-projektet kan du ta bort hela resurs gruppen.

Tänk på följande:

- När du tar bort raderas både projektet och metadata om identifierade datorer.
- Om du använder den äldre versionen av Azure Migrate öppnar du Azure-resurs gruppen där projektet skapades. Välj det migrerings projekt som du vill ta bort (resurs typen är **migreringsjobb**).
- Om du använder beroende analys med en Azure Log Analytics-arbetsyta:
    - Om du har kopplat en Log Analytics arbets yta till Server Assessment-verktyget tas arbets ytan bort automatiskt. Samma Log Analytics arbets yta kan användas för flera scenarier.
    - Om du vill ta bort arbets ytan Log Analytics gör du det manuellt.
- Borttagningen av projektet går inte att ångra. Det går inte att återställa borttagna objekt.

### <a name="delete-a-workspace-manually"></a>Ta bort en arbets yta manuellt

1. Bläddra till Log Analytics arbets ytan som är kopplad till projektet.

    - Om du inte har tagit bort det Azure Migrate projektet kan du hitta länken till arbets ytan i **Essentials**  >  **Server Assessment**.
       ![LA-arbetsytan ](./media/create-manage-projects/loganalytics-workspace.png) .
       
    - Om du redan har tagit bort Azure Migrate-projektet väljer du **resurs grupper** i den vänstra rutan i Azure Portal och letar upp arbets ytan.
       
2. [Följ anvisningarna](../azure-monitor/platform/delete-workspace.md) för att ta bort arbets ytan.

## <a name="next-steps"></a>Nästa steg

Lägg till [utvärderings](how-to-assess.md) -eller [migreringsverktyg](how-to-migrate.md) för att Azure Migrate projekt.
