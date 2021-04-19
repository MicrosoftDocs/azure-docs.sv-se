---
title: Skapa och hantera projekt
description: Hitta, skapa, hantera och ta bort projekt i Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/23/2020
ms.openlocfilehash: 0c9102f8ca724e431bb478945c5f4ba0369643d6
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714854"
---
# <a name="create-and-manage-projects"></a>Skapa och hantera projekt

I den här artikeln beskrivs hur du skapar, hanterar och tar bort [projekt.](migrate-services-overview.md) 

Klassisk Azure Migrate går i pension i februari 2024. Efter feb 2024 kommer den klassiska versionen av Azure Migrate inte längre att stödjas och inventeringsmetadata i det klassiska projektet tas bort. Om du använder klassiska projekt tar du bort dessa projekt och följer stegen för att skapa ett nytt projekt. Du kan inte uppgradera klassiska projekt eller komponenter till Azure Migrate. Visa [vanliga frågor](./resources-faq.md#i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version) och svar innan du börjar skapa processen.

Projektet används för att lagra identifierings-, utvärderings- och migreringsmetadata som samlas in från miljön som du utvärderar eller migrerar. I ett projekt kan du spåra identifierade tillgångar, skapa utvärderingar och dirigera migreringar till Azure.  

## <a name="verify-permissions"></a>Kontrollera behörigheter

Kontrollera att du har rätt behörigheter för att skapa ett projekt:

1. I Azure Portal du relevant prenumeration och väljer **Åtkomstkontroll (IAM).**
2. I **Kontrollera åtkomst** hittar du relevant konto och väljer det visningsbehörigheter. Du bör ha *behörighet som* deltagare *eller* ägare. 


## <a name="create-a-project-for-the-first-time"></a>Skapa ett projekt för första gången

Konfigurera ett nytt projekt i en Azure-prenumeration.

1. I Azure Portal du efter *Azure Migrate*.
2. I **Tjänster** väljer du **Azure Migrate**.
3. I **översikten** väljer du **Utvärdera och migrera servrar**.

    :::image type="content" source="./media/create-manage-projects/assess-migrate-servers.png" alt-text="Alternativ i Översikt för att utvärdera och migrera servrar":::

4. I **Servrar** väljer du **Skapa projekt**.

    :::image type="content" source="./media/create-manage-projects/create-project.png" alt-text="Knapp för att börja skapa projekt":::

5. I **Skapa projekt** väljer du Azure-prenumerationen och resursgruppen. Skapa en resursgrupp om du inte har någon.
6. I **Projektinformation** anger du projektnamnet och det geografiska område där du vill skapa projektet.
    - Geografin används endast för att lagra metadata som samlas in från lokala servrar. Du kan välja valfri målregion för migrering. 
    - Granska geografiska områden som stöds för [offentliga moln](migrate-support-matrix.md#supported-geographies-public-cloud) [och myndighetsmoln.](migrate-support-matrix.md#supported-geographies-azure-government) 


    > [!Note]
    > Använd avsnittet **Avancerad** konfiguration för att skapa ett Azure Migrate projekt med privat slutpunktsanslutning. [Läs mer](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity) 

7. Välj **Skapa**.

     :::image type="content" source="./media/create-manage-projects/project-details.png" alt-text="Sida för att ange projektinställningar":::


Vänta några minuter tills projektet har distribuerats.

## <a name="create-a-project-in-a-specific-region"></a>Skapa ett projekt i en specifik region

I portalen kan du välja det geografiska område där du vill skapa projektet. Om du vill skapa projektet i en specifik Azure-region använder du följande API-kommando för att skapa projektet.

```rest
PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"
```

## <a name="create-additional-projects"></a>Skapa ytterligare projekt

Om du redan har ett projekt och vill skapa ytterligare ett projekt gör du följande:  

1. I den [offentliga Azure-portalen](https://portal.azure.com) [Azure Government](https://portal.azure.us)du efter **Azure Migrate**.
2. På Azure Migrate instrumentpanelen > **Servrar** väljer **du Ändra** i det övre högra hörnet.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Ändra projekt":::

3. Om du vill skapa ett nytt projekt väljer du **klicka här.**


## <a name="find-a-project"></a>Hitta ett projekt

Hitta ett projekt på följande sätt:

1. I [Azure Portal](https://portal.azure.com)du efter *Azure Migrate*.
2. I Azure Migrate instrumentpanelen > **Servrar** väljer **du Ändra** i det övre högra hörnet.

    :::image type="content" source="./media/create-manage-projects/switch-project.png" alt-text="Växla till ett befintligt projekt":::

3. Välj lämplig prenumeration och projekt.


### <a name="find-a-classic-project"></a>Hitta ett klassiskt projekt

Om du skapade projektet i den [tidigare versionen av](migrate-services-overview.md#azure-migrate-versions) Azure Migrate hittar du det på följande sätt:

1. I [Azure Portal](https://portal.azure.com)du efter *Azure Migrate*.
2. Om du Azure Migrate ett projekt i den tidigare versionen på instrumentpanelen visas en banderoll som refererar till äldre projekt. Välj banderollen.

    :::image type="content" source="./media/create-manage-projects/access-existing-projects.png" alt-text="Få åtkomst till befintliga projekt":::

3. Granska listan över gamla projekt.


## <a name="delete-a-project"></a>Ta bort ett projekt

Ta bort på följande sätt:

1. Öppna den Azure-resursgrupp där projektet skapades.
2. På resursgruppssidan väljer du **Visa dolda typer.**
3. Välj det projekt som du vill ta bort och dess associerade resurser.
    - Resurstypen är **Microsoft.Migrate/migrateprojects**.
    - Om resursgruppen endast används av projektet kan du ta bort hela resursgruppen.

Tänk på följande:

- När du tar bort tas både projektet och metadata om identifierade servrar bort.
- Om du använder den äldre versionen av Azure Migrate öppnar du den Azure-resursgrupp där projektet skapades. Välj det projekt som du vill ta bort (resurstypen är **Migreringsprojekt**).
- Om du använder beroendeanalys med en Azure Log Analytics-arbetsyta:
    - Om du har kopplat en Log Analytics-arbetsyta till verktyget Serverutvärdering tas arbetsytan inte bort automatiskt. Samma Log Analytics-arbetsyta kan användas för flera scenarier.
    - Om du vill ta bort Log Analytics-arbetsytan gör du det manuellt.
- Projektborttagningen går inte att ångra. Det går inte att återställa borttagna objekt.

### <a name="delete-a-workspace-manually"></a>Ta bort en arbetsyta manuellt

1. Bläddra till Log Analytics-arbetsytan som är kopplad till projektet.

    - Om du inte har tagit bort projektet finns länken till arbetsytan i **Essentials**  >  **Server Assessment**.
    :::image type="content" source="./media/create-manage-projects/loganalytics-workspace.png" alt-text="LA-arbetsyta":::
       
    - Om du redan har tagit bort projektet väljer du **Resursgrupper** i den vänstra rutan i Azure Portal och hittar arbetsytan.
       
2. [Följ instruktionerna för](../azure-monitor/logs/delete-workspace.md) att ta bort arbetsytan.

## <a name="next-steps"></a>Nästa steg

Lägga [till utvärderings-](how-to-assess.md) [eller migreringsverktyg](how-to-migrate.md) i projekt.