---
title: Självstudie – återge en blandnings-scen med Azure Batch och Batch Explorer
description: Självstudie – Så här renderar du flera bildrutor från en Blender-scen med Azure Batch och Batch Explorer-klientprogrammet
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ROBOTS: NOINDEX
ms.openlocfilehash: 11805d2a15e7f369ad38ab7e92409c1b357d2d8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103490848"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>Självstudie: Rendera en Blender-scen med Batch Explorer

I den här självstudiekursen visas hur du renderar flera bildrutor från en Blender-demoscen. Blender används för den här självstudien eftersom det är kostnadsfritt för både klienten och virtuella renderingsdatorer, men processen är mycket lik om andra program, till exempel Maya eller 3ds Max, skulle användas.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Ladda upp en Blender-scen till Azure Storage
> * Skapa en Batch-pool med flera noder för att utföra renderingen
> * Rendera flera bildrutor
> * Visa och ladda ned de renderade bildrutefilerna

## <a name="prerequisites"></a>Förutsättningar

Du behöver en användningsbaserad prenumeration eller annat Azure-köpalternativ för att använda renderingsprogram i Batch för betalning per användningstillfälle. Användningsbaserad licensiering stöds inte om du använder ett kostnadsfritt Azure-erbjudande som ger penningkredit.

Du behöver ett Azure Batch-konto med ett tillhörande lagringskonto.  Se någon av Snabbstart-artiklarna om Batch, till exempel [CLI-artikeln](./quick-create-cli.md) för at skapa ett Batch-konto.

En kärnkvot med låg prioritet på minst 50 kärnor krävs för storleken på virtuell dator och antal virtuella datorer som anges i den här självstudien. Standardkvoten kan användas men mindre storlek på virtuell dator måste användas, vilket betyder att bilderna kan ta längre tid att rendera. Processen för att begära en ökad kärnkvot beskrivs i [den här artikeln](./batch-quota-limit.md).

Till sist: [Batch Explorer](https://azure.github.io/BatchExplorer/) måste installeras, och finns för Windows, OSX och Linux. Om du installerar [Blender](https://www.blender.org/download/) kan du visa exemplet på modellfil (valfritt).

## <a name="download-the-demo-scene"></a>Ladda ned demoscenen

Ladda ned ZIP-filen med demo, ”Class room”, för Blender från [webbsidan med Blender-demofiler](https://www.blender.org/download/demo-files/) och packa upp den på en lokal enhet.

## <a name="upload-a-scene-to-azure-storage"></a>ladda upp en scen till Azure-lagringen

Skapa en lagringskontocontainer för demoscenfilerna:

* Starta Batch Explorer
* Välj menyalternativet ”Data” i huvudmenyn på vänster sida.
* Välj ”File groups” (Filgrupper) i den nedrullningsbara menyn.
* Välj ”+”-knappen och skapa en ny ”Empty file group” (Tom filgrupp) med namnet ”blender-classroom”
  * En filgrupp är helt enkelt en Azure Storage-blobcontainer med prefixet ”fgrp-”. Det är en konvention som används till att filtrera bort andra containrar i lagringskontot

![Filgrupp för scenfiler](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

Ladda upp scenfilerna:

* Välj den nya containern och dra och släpp innehållet i mappen ”classroom” till containern i Batch Explorer.

![Uppladdade scenfiler](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>Skapa Azure Storage-container för utdatabilder

Skapa en lagringskontocontainer för utdatafiler för demoscener:

* Välj menyalternativet ”Data” i huvudmenyn på vänster sida.
* Välj ”+”-knappen och skapa en ny ”Empty file group” (Tom filgrupp) med namnet ”render-output”

![Filgrupp för utdatafiler](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>Skapa en pool med virtuella datorer för rendering

Skapa en Batch-pool med rendering av Azure Marketplace VM-avbildning som innehåller Blender-programmet:

* Välj menyalternativet ”Gallery” (Galleri) i huvudmenyn på vänster sida.
* Välj objektet ”Blender” för listan med programobjekt.
* Välj objekt för rendering av bildrutor på Windows Server
* Du kan välja länkikonen till höger om objektet om du vill visa de mallfiler som kommer att användas för att skapa en pool och ett jobb.

![Blender-galleriobjekt](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* Välj knappen ”Create pool for later use” (Skapa pool för senaste användning)
  * Lämna ”blender-windows” som namn
  * Ange ”0” för ”Dedicated vm count” (Dedikerat antal virtuella datorer)
  * Ange ”3” för ”Low priority vm count” (Antal virtuella datorer med låg prioritet)
  * Ange ”Standard_F16” för ”Node size” (Nodstorlek) – en annan storlek för virtuell dator kan väljas men tiden det tar att rendera en bildruta beror huvudsakligen på antalet kärnor.
* Välj den gröna knappen för att skapa poolen
  * Poolen skapas nästan direkt men det tar några minuter för de virtuella datorer som ska tilldelas och startas.

![Poolmall för Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> Obs! När det finns virtuella datorer i en pool debiteras dessa virtuella datorer i din Azure-prenumeration. Poolen eller de virtuella datorerna måste tas bort för att stoppa debiteringarna. Ta bort poolen i slutet av den här självstudiekursen för att undvika löpande avgifter.

Statusen för poolen och de virtuella datorerna kan övervakas i vyn ”Pools” (Pooler). I följande exempel visas att alla tre virtuella datorer har tilldelats, två har startats och är inaktiva och en fortfarande startas: ![termisk poolkarta](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>Skapa ett renderingsjobb

Skapa ett renderingsjobb för att rendera några bildrutor med poolen som har skapats:

* Välj menyalternativet ”Gallery” (Galleri) i huvudmenyn på vänster sida.
* Välj objektet ”Blender” för listan med programobjekt.
* Välj objekt för rendering av bildrutor på Windows Server.
* Välj knappen ”Run job with existing pool” (Kör jobb med befintlig pool)
* Välj poolen ”blender-windows”
* Ange ”blender-render-tutorial1” som ”Job name” (Jobbnamn)
* Välj ”fgrp-blender-classroom” som ”Input data” (Indata)
* Välj filikonen för ”Blend file” (Blanda fil) och välj ”classroom.blend”
* Lämna värdet ”1” för ”Frame start” (Start för bildruta) och ange ”5” för ”Frame end” (Slut för bildruta)
* Ange ”fgrp-render-output” för ”Outputs” (Utdata)
* Välj den gröna knappen för att skapa jobbet. Ett jobb skapas med fem uppgifter, en för varje bildruta.

![Jobbmall för Blender](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

När jobbet och alla uppgifter har skapats visas jobbet tillsammans med jobbuppgifterna: ![Lista över jobbuppgifter](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

När en uppgift börjar köras för första gången på en virtuell dator i poolen körs en förberedelseuppgift för Batch-jobb, som kopierar scenfilerna från lagringsfilgruppen till den virtuella datorn så att Blender kan komma åt dem.
Statusen för renderingen kan fastställas genom att visa loggfilen stdout.txt som skapas av Blender.  Välj en uppgift, ”Task Outputs” (Uppgiftsutdata), visas som standard, och filen ”stdout.txt” kan väljas och visas.
![stdout-filen](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

Om du väljer poolen ”blender-windows” visas de virtuella datorerna i poolen i körningstillstånd: ![Termisk poolkarta med noder som körs](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

De renderade bilderna tar flera minuter att skapa, beroende på vilken virtuell dator-storlek som har valts.  Med virtuell dator F16 som angavs tidigare tog bildrutorna 16 minuter att rendera.

## <a name="view-the-rendering-output"></a>Visa renderingens utdata

När renderingen av bildrutorna är klar visas de uppgifterna som slutförda: ![Uppgifter slutförs](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

Den renderade bilden skrivs först till den virtuella datorn och kan visas genom att välja mappen ”wd”: ![Renderad bild på poolnod](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

Jobbmallen anger också att utdatabildrutan och loggfilerna skrivs tillbaka till Azure Storage-kontofilgruppen som angavs när jobbet skapades.  Användargränssnittet ”Data” kan användas för att visa utdatafilerna och loggarna. Det kan också användas till att ladda ned filerna: ![Renderad bild i lagringsfilgruppen](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

När alla uppgifter har slutförts markeras jobbet som klart: ![Jobbet och alla uppgifter slutförda](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>Rensa resurser

> [!WARNING]
> Poolen måste tas bort (den kan också minskas till noll noder) för att förhindra att avgifter tas ut på Azure-prenumerationen.

* Välj ”Pools” (Pooler)
* Välj poolen ”blender-windows”
* Högerklicka och välj ”Delete” (Ta bort) eller välj papperskorgsikonen ovanför poolen

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ladda upp en Blender-scen till Azure Storage
> * Skapa en Batch-pool med flera noder för att utföra renderingen
> * Rendera flera bildrutor
> * Visa och ladda ned de renderade bildrutefilerna

Fortsätt genom att utforska åter givnings programmen som är tillgängliga via Batch Explorer i avsnittet **Galleri** . För varje program finns det flera tillgängliga mallar, som utökas med tiden. Till exempel kan du för Blender-mallar dela upp en enda bild i olika rutor, så delar av en bild kan renderas parallellt.

Mer information om rendering i molnskala finns bland alternativ för Batch Rendering-tjänsten.

> [!div class="nextstepaction"]
> [Batch-tjänsten för återgivning](batch-rendering-service.md)
