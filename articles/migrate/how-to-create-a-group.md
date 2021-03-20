---
title: Gruppera datorer för utvärdering med Azure Migrate | Microsoft Docs
description: Beskriver hur du grupperar datorer innan du kör en utvärdering med tjänsten Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 364b5949e944a4317aa25f1f1b12545122881cec
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752115"
---
# <a name="create-a-group-for-assessment"></a>Skapa en grupp för utvärdering

Den här artikeln beskriver hur du skapar grupper av datorer för utvärdering med Azure Migrate: Server utvärdering.

[Azure Migrate](migrate-services-overview.md) hjälper dig att migrera till Azure. Azure Migrate tillhandahåller en central hubb för att spåra identifiering, utvärdering och migrering av lokal infrastruktur, program och data till Azure. Hubben ger Azure-verktyg för utvärdering och migrering, samt oberoende program varu leverantörer från tredje part (ISV). 

## <a name="grouping-machines"></a>Gruppera datorer

Du samlar in datorer i grupper för att bedöma om de är lämpliga för migrering till Azure och för att få Azure storleks-och kostnads uppskattningar för dem. Det finns ett par olika sätt att skapa grupper:

- Om du vet vilka datorer som behöver migreras tillsammans kan du skapa gruppen manuellt i Azure Migrate.
- Om du inte är säker på vilka datorer som måste grupperas tillsammans kan du använda funktionen för beroende visualisering i Azure Migrate för att skapa grupper. 

> [!NOTE]
> Funktionen för beroendevisualisering är inte tillgänglig i Azure Government.

## <a name="create-a-group-manually"></a>Skapa en grupp manuellt

Du kan skapa en grupp på samma tidpunkt som du [skapar en utvärdering](how-to-create-assessment.md).

Om du vill skapa en grupp manuellt utanför skapandet av en utvärdering gör du följande:

1. Klicka på **utvärdera och migrera servrar** i > **Översikt** för Azure Migrate projekt. I **Azure Migrate: Server utvärdering** klickar du på **grupper**
    - Om du ännu inte har lagt till Azure Migrate: Server utvärderings verktyget, klickar du på för att lägga till det. [Läs mer](how-to-assess.md).
    - Om du ännu inte har skapat ett Azure Migrate-projekt kan du [läsa mer](./create-manage-projects.md).

    ![Välj grupper](./media/how-to-create-a-group/select-groups.png)

2. Klicka på **grupp** ikonen.
3. I **Skapa grupp** anger du ett grupp namn och i **namn** väljer du den Azure Migrate-enhet som du använder för dator identifiering.
4. I listan dator väljer du de datorer som du vill lägga till i gruppen > **skapa**.

    ![Skapa grupp](./media/how-to-create-a-group/create-group.png)

Du kan nu använda den här gruppen när du [skapar en utvärdering av Azure VM](how-to-create-assessment.md) eller [en Azure VMware-lösning (AVS)](how-to-create-azure-vmware-solution-assessment.md). Observera att du kan skapa en AVS-utvärdering på grupper med endast virtuella VMware-datorer. 

## <a name="refine-a-group-with-dependency-mapping"></a>Förfina en grupp med beroende mappning

Beroende mappning hjälper dig att visualisera beroenden mellan datorer. Du använder vanligt vis beroende mappning när du vill utvärdera dator grupper med högre Tillförlitlighets nivåer.
- Det hjälper dig att utföra en kontroll av dator beroenden innan du kör en utvärdering. 
- Den hjälper också till att effektivt planera din migrering till Azure, genom att se till att inget är kvar bakom och därmed undvika oväntade avbrott under migreringen.
- Du kan identifiera beroende system som behöver migreras tillsammans och identifiera om ett system som körs fortfarande betjänar användare eller är en kandidat för inaktive ring i stället för migrering.

Om du redan har [konfigurerat beroende mappning](how-to-create-group-machine-dependencies.md)och vill förfina en befintlig grupp gör du följande:

1. På fliken **servrar** , i panelen **Azure Migrate: Server bedömning** , klickar du på **grupper**.
2. Klicka på den grupp som du vill förfina.
    - Om du ännu inte har konfigurerat beroende mappning, visar kolumnen **beroenden** att installations status **krävs** . Klicka på **kräver installation** för varje virtuell dator som du vill visualisera beroenden för. Installera några agenter på varje virtuell dator innan du kan mappa dator beroenden. [Läs mer](how-to-create-group-machine-dependencies.md).

        ![Lägg till beroende mappning](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Om du redan har konfigurerat beroende mappning klickar du på **Visa beroenden** på sidan grupp för att öppna grupp beroende kartan.

3. När du klickar på **Visa beroenden** visar grupp beroende kartan följande:

    - Inkommande (klienter) och utgående (servrar) TCP-anslutningar till och från alla datorer i gruppen som har beroende agenter installerade.
    - Beroende datorer som inte har beroende agenter installerade grupperas efter port nummer.
    - Beroende datorer med installerade beroende agenter visas som separata rutor.
    - Processer som körs inuti datorn. Expandera alla dator rutor om du vill visa processerna.
    - Dator egenskaper (inklusive fullständigt domän namn, operativ system, MAC-adress). Klicka på varje dator box för att visa information.

4. Om du vill visa beroenden i ett tidsintervall som du väljer ändrar du tidsintervallet (en timme som standard) genom att ange start-och slutdatum, eller varaktighet.

    > [!NOTE]
    > Tidsintervallet kan vara upp till en timme. Om du behöver ett längre intervall använder [du Azure Monitor för att köra frågor mot beroende data](how-to-create-group-machine-dependencies.md) under en längre period.

5. När du har identifierat de beroenden som du vill lägga till i eller ta bort från gruppen kan du ändra gruppen. Använd Ctrl och klicka för att lägga till eller ta bort datorer från gruppen.

    - Du kan bara lägga till datorer som har identifierats.
    - Om du lägger till och tar bort datorer inutvärderas tidigare utvärderingar för en grupp.
    - Du kan också skapa en ny utvärdering när du ändrar gruppen.


## <a name="next-steps"></a>Nästa steg

Lär dig hur du konfigurerar och använder [beroende mappning](how-to-create-group-machine-dependencies.md) för att skapa grupper med hög exakthet.