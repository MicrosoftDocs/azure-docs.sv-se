---
title: Skapa och hantera projekt
description: Hitta, skapa, hantera och ta bort projekt i Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: cb0ac41d469ad9a7670ce4b1bae23b315a17dc38
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104871103"
---
# <a name="create-and-manage-projects"></a>Skapa och hantera projekt

Den här artikeln beskriver hur du skapar, hanterar och tar bort [projekt](migrate-services-overview.md). 

Den klassiska Azure Migrate tas ur bruk i feb 2024. Efter feb 2024 kommer den klassiska versionen av Azure Migrate inte längre att stödjas och lager-metadata i det klassiska projektet tas bort. Om du använder klassiska projekt tar du bort projekten och följer stegen för att skapa ett nytt projekt. Du kan inte uppgradera klassiska projekt eller komponenter till Azure Migrate. Se [vanliga frågor och svar](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) innan du börjar skapa processen.

Project används för att lagra metadata för identifiering, utvärdering och migrering som samlas in från den miljö som du bedömer eller migrerar. I ett projekt kan du spåra identifierade till gångar, skapa utvärderingar och dirigera migreringar till Azure.  

## <a name="verify-permissions"></a>Kontrollera behörigheter

Kontrol lera att du har rätt behörighet för att skapa ett projekt:

1. Öppna den relevanta prenumerationen i Azure Portal och välj **åtkomst kontroll (IAM)**.
2. Leta upp det relevanta kontot i **kontrol lera åtkomst** och välj Visa behörigheter. Du bör ha behörighet som *deltagare* eller *ägare* . 


## <a name="create-a-project-for-the-first-time"></a>Skapa ett projekt för första gången

Skapa ett nytt projekt i en Azure-prenumeration.

1. Sök efter *Azure Migrate* i Azure Portal.
2. I **tjänster** väljer du **Azure Migrate**.
3. I **översikten** väljer du **Utvärdera och migrera servrar**.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Alternativ i Översikt för att utvärdera och migrera servrar":::

4. I **servrar** väljer du **skapa projekt**.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="För att börja skapa projekt":::

5. I **skapa projekt** väljer du Azure-prenumeration och resurs grupp. Skapa en resurs grupp om du inte har någon.
6. I **projekt information** anger du projekt namnet och geografin som du vill skapa projektet i.
    - Geografin används bara för att lagra metadata som samlats in från lokala servrar. Du kan välja valfri mål region för migrering. 
    - Granska stödda geografiska områden för [offentliga](migrate-support-matrix.md#supported-geographies-public-cloud) och [offentliga moln](migrate-support-matrix.md#supported-geographies-azure-government).

8. Välj **Skapa**.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Sida för att mata in projekt inställningar":::


Vänta några minuter tills projektet har distribuerats.

## <a name="create-a-project-in-a-specific-region"></a>Skapa ett projekt i en angiven region

I portalen kan du välja det geografiskt område där du vill skapa projektet. Om du vill skapa projektet i en angiven Azure-region, använder du följande API-kommando för att skapa projektet.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Skapa ytterligare projekt

Om du redan har ett projekt och vill skapa ett nytt projekt gör du följande:  

1. Sök efter **Azure Migrate** i [Azures offentliga Portal](https://portal.azure.com) eller [Azure Government](https://portal.azure.us).
2. På Azure Migrate instrument panelens >- **servrar** väljer du **ändra** i det övre högra hörnet.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Ändra projekt":::

3. Om du vill skapa ett nytt projekt väljer du **Klicka här**.


## <a name="find-a-project"></a>Hitta ett projekt

Sök efter ett projekt enligt följande:

1. Sök efter *Azure Migrate* i [Azure Portal](https://portal.azure.com).
2. I Azure Migrate instrument panelens >- **servrar** väljer du **ändra** i det övre högra hörnet.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Växla till ett befintligt projekt":::

3. Välj lämplig prenumeration och projekt.


### <a name="find-a-classic-project"></a>Hitta ett klassiskt projekt

Om du har skapat projektet i den [tidigare versionen](migrate-services-overview.md#azure-migrate-versions) av Azure Migrate kan du söka efter det på följande sätt:

1. Sök efter *Azure Migrate* i [Azure Portal](https://portal.azure.com).
2. Om du har skapat ett projekt i den tidigare versionen av Azure Migrate instrument panelen visas en banderoll som refererar till äldre projekt. Välj banderollen.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Komma åt befintliga projekt":::

3. Granska listan över gamla projekt.


## <a name="delete-a-project"></a>Ta bort ett projekt

Ta bort enligt följande:

1. Öppna Azure-resurs gruppen där projektet skapades.
2. På sidan resurs grupp väljer du **Visa dolda typer**.
3. Välj det projekt som du vill ta bort och dess associerade resurser.
    - Resurs typen är **Microsoft. Migrate/migrateprojects**.
    - Om resurs gruppen uteslutande används av projektet kan du ta bort hela resurs gruppen.

Tänk på följande:

- När du tar bort raderas både projektet och metadata om identifierade servrar.
- Om du använder den äldre versionen av Azure Migrate öppnar du Azure-resurs gruppen där projektet skapades. Välj det projekt som du vill ta bort (resurs typen är **migreringsjobb**).
- Om du använder beroende analys med en Azure Log Analytics-arbetsyta:
    - Om du har kopplat en Log Analytics arbets yta till Server Assessment-verktyget tas arbets ytan bort automatiskt. Samma Log Analytics arbets yta kan användas för flera scenarier.
    - Om du vill ta bort arbets ytan Log Analytics gör du det manuellt.
- Borttagningen av projektet går inte att ångra. Det går inte att återställa borttagna objekt.

### <a name="delete-a-workspace-manually"></a>Ta bort en arbets yta manuellt

1. Bläddra till Log Analytics arbets ytan som är kopplad till projektet.

    - Om du inte har tagit bort projektet kan du hitta länken till arbets ytan i utvärderingen av **Essentials**-  >  **servern**.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="LA-arbetsyta":::
       
    - Om du redan har tagit bort projektet väljer du **resurs grupper** i den vänstra rutan i Azure Portal och letar upp arbets ytan.
       
2. [Följ anvisningarna](../azure-monitor/logs/delete-workspace.md) för att ta bort arbets ytan.

## <a name="next-steps"></a>Nästa steg

Lägg till [utvärderings](how-to-assess.md) -eller [Migreringsverktyg](how-to-migrate.md) i projekt.