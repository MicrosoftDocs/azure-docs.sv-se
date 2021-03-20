---
title: Azure VMware-lösning av CloudSimple – hantera virtuella datorer med privat moln i Azure
description: Beskriver hur du hanterar virtuella CloudSimple privata moln i Azure Portal, inklusive hur du lägger till diskar, ändrar VM-kapacitet och lägger till nätverks gränssnitt
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 090b7711ab061b989eae13113fe7048e8dd875ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895197"
---
# <a name="manage-your-cloudsimple-private-cloud-virtual-machines-in-azure"></a>Hantera dina virtuella CloudSimple privata moln datorer i Azure

Logga in på [Azure Portal](https://portal.azure.com)för att hantera de virtuella datorer som du har [skapat för ditt privata moln i CloudSimple](azure-create-vm.md). Sök efter och välj den virtuella (Sök under **alla tjänster** eller **Virtual Machines** på sido menyn).

## <a name="control-virtual-machine-operation"></a>Kontrol lera virtuell dator åtgärd

Följande kontroller är tillgängliga på **översikts** sidan för den valda virtuella datorn.

| Kontroll | Beskrivning |
| ------------ | ------------- |
| Ansluta | Anslut till den angivna virtuella datorn.  |
| Start | Starta den angivna virtuella datorn.  |
| Starta om | Stäng av och starta sedan den angivna virtuella datorn.  |
| Stoppa | Stäng av den angivna virtuella datorn.  |
| Capture | Avbilda en avbildning av den angivna virtuella datorn så att den kan användas som en avbildning för att skapa andra virtuella datorer. Se [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](../virtual-machines/windows/capture-image-resource.md).   |
| Flytta | Flytta till den angivna virtuella datorn.  |
| Ta bort | Ta bort den angivna virtuella datorn.  |
| Uppdatera | Uppdatera data i visningen.  |

### <a name="view-performance-information"></a>Visa prestanda information

Diagrammen i den nedre delen av **översikts** sidan visar prestanda data för det valda intervallet (senaste timmen till de senaste 30 dagarna; standard är den senaste timmen). I varje diagram kan du Visa de numeriska värdena när som helst inom intervallet genom att flytta markören tillbaka och tillbaka i diagrammet.

Följande diagram visas.

| Objekt | Beskrivning |
| ------------ | ------------- |
| PROCESSOR (genomsnitt) | Genomsnittlig CPU-användning i procent över det valda intervallet.   |
| Nätverk | Trafik in i och ut ur nätverket (MB) över det valda intervallet.  |
| Disk-byte | Totalt antal data som lästs från disk och skrivits till disk (MB) över det valda intervallet.  |
| Disk åtgärder | Genomsnittligt antal disk åtgärder (åtgärder/sekund) över det valda intervallet. |

## <a name="manage-vm-disks"></a>Hantera VM-diskar

Om du vill lägga till en virtuell dator disk öppnar du sidan **diskar** för den valda virtuella datorn. Om du vill lägga till en disk klickar du på **Lägg till disk**. Konfigurera var och en av följande inställningar genom att ange eller välja ett infogat alternativ. Klicka på **Spara**.

   | Objekt | Beskrivning |
   | ------------ | ------------- |
   | Name | Ange ett namn för att identifiera disken.  |
   | Storlek | Välj en av de tillgängliga storlekarna.  |
   | SCSI-styrenhet | Välj en SCSI-styrenhet. Tillgängliga styrenheter varierar för de olika operativ system som stöds.  |
   | Läge | Anger hur disken deltar i ögonblicks bilder. Välj något av följande alternativ: <br> -Oberoende beständig: all data som skrivs till disken skrivs permanent.<br> – Oberoende, icke-permanent: ändringar som skrivs till disken ignoreras när du stänger av eller återställer den virtuella datorn.  Med det här läget kan du alltid starta om den virtuella datorn i samma tillstånd. Mer information finns i VMware- [dokumentationen](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html). |

Om du vill ta bort en disk markerar du den och klickar på **ta bort**.

## <a name="change-the-capacity-of-the-vm"></a>Ändra kapaciteten för den virtuella datorn

Om du vill ändra kapaciteten för den virtuella datorn öppnar du **storleks** sidan för den valda virtuella datorn. Ange något av följande och klicka på **Spara**.

| Objekt | Beskrivning |
| ------------ | ------------- |
| Antal kärnor | Antalet kärnor som har tilldelats den virtuella datorn.  |
| Virtualisering av maskin vara | Markera kryss rutan för att exponera virtualisering av maskin varan till gäst operativ systemet. Se VMware-artikeln [exponerar VMware](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)-maskinvarustödd virtualisering. |
| Minnes storlek | Välj den mängd minne som ska allokeras till den virtuella datorn.  

## <a name="manage-network-interfaces"></a>Hantera nätverks gränssnitt

Klicka på **Lägg till nätverks gränssnitt** för att lägga till ett gränssnitt. Konfigurera var och en av följande inställningar genom att ange eller välja ett infogat alternativ. Klicka på **Spara**.

   | Kontroll | Beskrivning |
   | ------------ | ------------- |
   | Name | Ange ett namn för att identifiera gränssnittet.  |
   | Nätverk | Välj i listan över konfigurerade nätverk i ditt privata moln vSphere.  |
   | Adapter | Välj en vSphere-adapter i listan över tillgängliga typer som har kon figurer ATS för den virtuella datorn. Mer information finns i VMware Knowledge Base-artikeln [välja ett nätverkskort för den virtuella datorn](https://kb.vmware.com/s/article/1001805). |
   | Starta vid start | Välj om du vill aktivera NÄTVERKSKORTets maskin vara när den virtuella datorn startas. Standardvärdet är **Enable**. |

Om du vill ta bort ett nätverks gränssnitt markerar du det och klickar på **ta bort**.
