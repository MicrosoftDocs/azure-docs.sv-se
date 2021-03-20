---
title: Spara och distribuera avbildningar i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln innehåller anvisningar för hur du sparar anpassade avbildningar från de redan skapade virtuella datorerna (VM) i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5278626f8cdd4299912f3c952786422436fe916
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85476248"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>Spara anpassade avbildningar och distribuera till flera labb
Den här artikeln innehåller anvisningar för hur du sparar anpassade avbildningar från redan skapade virtuella datorer (VM). Det beskriver också hur du distribuerar dessa anpassade avbildningar till andra DevTest Labs i organisationen.

## <a name="prerequisites"></a>Förutsättningar
Följande objekt bör redan vara på plats:

- Ett labb för avbildnings fabriken i Azure DevTest Labs.
- Ett Azure DevOps-projekt som används för att automatisera avbildnings fabriken.
- Käll kods plats som innehåller skripten och konfigurationen (i vårt exempel i samma DevOps-projekt som nämns i föregående steg).
- Bygg definition för att dirigera Azure PowerShell-aktiviteter.

Om det behövs följer du stegen i [kör en avbildnings fabrik från Azure DevOps](image-factory-set-up-devops-lab.md) för att skapa eller konfigurera dessa objekt. 

## <a name="save-vms-as-generalized-vhds"></a>Spara virtuella datorer som generaliserade virtuella hård diskar
Spara de befintliga virtuella datorerna som generaliserade virtuella hård diskar.  Det finns ett exempel på PowerShell-skript för att spara de befintliga virtuella datorerna som generaliserade virtuella hård diskar. Om du vill använda den lägger du först till en annan **Azure PowerShell** -uppgift i build-definitionen som visas i följande bild:

![Lägg till Azure PowerShell steg](./media/save-distribute-custom-images/powershell-step.png)

När du har den nya uppgiften i listan väljer du objektet så att vi kan fylla i all information som visas i följande bild: 

![PowerShell-inställningar](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>Generaliserade kontra specialiserade anpassade avbildningar
När du skapar en anpassad avbildning från en virtuell dator i [Azure Portal](https://portal.azure.com)kan du välja att ha en generaliserad eller specialiserad anpassad avbildning.

- **Specialiserad anpassad avbildning:** Sysprep/avetableringen kördes inte på datorn. Det innebär att avbildningen är en exakt kopia av OS-disken på den befintliga virtuella datorn (en ögonblicks bild).  Samma filer, program, användar konton, dator namn och så vidare, är alla närvarande när vi skapar en ny dator från den här anpassade avbildningen.
- **Generaliserad anpassad avbildning:** Sysprep/avetablering har körts på datorn.  När den här processen körs tar den bort användar konton, tar bort dator namnet, tar bort registreringsdatafiler för användar registret, osv., med målet att generalisera avbildningen så att den kan anpassas när du skapar en annan virtuell dator.  När du generaliserar en virtuell dator (genom att köra Sysprep), förstör processen den aktuella virtuella datorn – den kommer inte längre att fungera.

Skriptet för att fästa anpassade avbildningar i avbildnings fabriken sparar virtuella hård diskar för virtuella datorer som skapats i föregående steg (identifieras baserat på en tagg på resursen i Azure).

## <a name="update-configuration-for-distributing-images"></a>Uppdatera konfigurationen för att distribuera avbildningar
Nästa steg i processen är att skicka de anpassade avbildningarna från image Factory-labbet till andra labb som behöver dem. Kärn delen av den här processen är **labs.jsi** konfigurations filen. Du hittar den här filen i mappen **konfiguration** som ingår i avbildnings fabriken.

Det finns två viktiga saker som anges i labs.jsi konfigurations filen:

- Unikt identifiera ett särskilt mål labb med prenumerations-ID och labb namn.
- Den angivna uppsättningen avbildningar som ska flyttas till labbet som relativa sökvägar till konfigurations roten. Du kan också ange hela mappen (för att hämta alla avbildningar i mappen).

Här är ett exempel labs.jspå en fil med två labb i listan. I det här fallet distribuerar du bilder till två olika labb.

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>Skapa en versionsuppgift
Med samma steg som du såg tidigare i den här artikeln lägger du till ytterligare en **Azure PowerShell** -build-uppgift i bygg definitionen. Fyll i informationen på det sätt som visas i följande bild: 

![Bygg uppgift för att distribuera avbildningar](./media/save-distribute-custom-images/second-build-task-powershell.png)

Parametrarna är: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

Den här uppgiften tar alla anpassade avbildningar som finns i avbildnings fabriken och skickar dem till eventuella labb som definierats i Labs.jsi filen.

## <a name="queue-the-build"></a>Köa versionen
När distributionen har slutförts skapar du en ny version i kö för att se till att allt fungerar. När skapandet har slutförts visas de nya anpassade bilderna i mål labbet som angavs i Labs.jsi konfigurations filen.

## <a name="next-steps"></a>Nästa steg
I nästa artikel i serien uppdaterar du avbildnings fabriken med en bevarande princip och rensnings steg: [Ange bevarande princip och kör rensnings skript](image-factory-set-retention-policy-cleanup.md).
