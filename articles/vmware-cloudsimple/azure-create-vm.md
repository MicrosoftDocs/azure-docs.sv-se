---
title: Azure VMware-lösning av CloudSimple – skapa en virtuell dator i Azure med mallar för virtuella datorer
description: Beskriver hur du skapar virtuella datorer i Azure med hjälp av VM-mallar i VMware-infrastrukturen för ditt CloudSimple privata moln
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b01afe60a78a746eb0dc5f03cc7b45989f8cf81e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898767"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Skapa en virtuell dator i Azure med hjälp av mallar för virtuella datorer i VMware-infrastrukturen

Du kan skapa en virtuell dator i Azure Portal med hjälp av mallar för virtuella datorer i VMware-infrastrukturen som din CloudSimple-administratör har aktiverat för din prenumeration.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Skapa CloudSimple virtuell dator

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple Virtual Machines**.

3. Klicka på **Lägg till**.

    ![Skapa CloudSimple virtuell dator](media/create-cloudsimple-virtual-machine.png)

4. Ange grundläggande information klicka på **Nästa: storlek**.

    > [!NOTE]
    > CloudSimple för att skapa virtuella datorer på Azure kräver en mall för virtuella datorer.  Den här mallen för virtuell dator bör finnas i ditt privata moln vCenter.  Skapa en virtuell dator i ditt privata moln från vCenter-ANVÄNDARGRÄNSSNITTET med önskat operativ system och konfigurationer.  Skapa en mall med hjälp av instruktionerna i [klona en virtuell dator till en mall i vSphere-webbklienten](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html).

    ![Skapa CloudSimple virtuella datorer – grunder](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Fält | Beskrivning |
    | ------------ | ------------- |
    | Prenumeration | Azure-prenumeration som är associerad med ditt privata moln.  |
    | Resursgrupp | Resurs grupp som den virtuella datorn ska tilldelas till. Du kan välja en befintlig grupp eller skapa en ny. |
    | Name | Namn för att identifiera den virtuella datorn.  |
    | Location | Azure-region där den här virtuella datorn finns.  |
    | Privata moln | CloudSimple privata moln där du vill skapa den virtuella datorn. |
    | Resurspool | Mappad resurspool för den virtuella datorn. Välj bland tillgängliga resurspooler. |
    | vSphere-mall | vSphere-mall för den virtuella datorn.  |
    | Användarnamn | Användar namn för VM-administratören (för Windows-mallar)|
    | Lösenord <br>Bekräfta lösenordet | Lösen ord för VM-administratören (för Windows-mallar).  |

5. Välj antalet kärnor och minnes kapacitet för den virtuella datorn och klicka på **Nästa: konfigurationer**. Markera kryss rutan om du vill exponera full CPU-virtualisering till gäst operativ systemet så att program som kräver virtualisering av maskin vara kan köras på virtuella datorer utan binär översättning eller paravirtualiseringsegenskaperna. Mer information finns i VMware-artikeln [exponerar VMware-maskinvarustödd virtualisering](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html).

    ![Skapa CloudSimple virtuell dator-storlek](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurera nätverks gränssnitt och diskar enligt beskrivningen i följande tabeller och klicka på **Granska + skapa**.

    ![Skapa CloudSimple-konfigurationer för virtuella datorer](media/create-cloudsimple-virtual-machine-configurations.png)

    För nätverks gränssnitt klickar du på **Lägg till nätverks gränssnitt** och konfigurerar följande inställningar.

    | Kontroll | Beskrivning |
    | ------------ | ------------- |
    | Name | Ange ett namn för att identifiera gränssnittet.  |
    | Nätverk | Välj i listan över konfigurerade distribuerade Port grupper i ditt privata moln vSphere.  |
    | Adapter | Välj en vSphere-adapter i listan över tillgängliga typer som har kon figurer ATS för den virtuella datorn. Mer information finns i VMware Knowledge Base-artikeln [välja ett nätverkskort för den virtuella datorn](https://kb.vmware.com/s/article/1001805). |
    | Starta vid start | Välj om du vill aktivera NÄTVERKSKORTets maskin vara när den virtuella datorn startas. Standardvärdet är **Enable**. |

    För diskar klickar du på **Lägg till disk** och konfigurerar följande inställningar.

    | Objekt | Beskrivning |
    | ------------ | ------------- |
    | Name | Ange ett namn för att identifiera disken.  |
    | Storlek | Välj en av de tillgängliga storlekarna.  |
    | SCSI-styrenhet | Välj en SCSI-styrenhet för disken.  |
    | Läge | Anger hur disken deltar i ögonblicks bilder. Välj något av följande alternativ: <br> -Oberoende beständig: all data som skrivs till disken skrivs permanent.<br> -Oberoende icke-permanent: ändringar som skrivs till disken ignoreras när du stänger av eller återställer den virtuella datorn.  Oberoende icke-beständigt läge gör att du alltid kan starta om den virtuella datorn i samma tillstånd. Mer information finns i VMware- [dokumentationen](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html).

7. När verifieringen är klar granskar du inställningarna och klickar på **skapa**. Klicka på flikarna längst upp eller klicka om du vill göra några ändringar.

    ![Skapa CloudSimple virtuell dator – granska](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Visa lista med virtuella CloudSimple-datorer

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple Virtual Machines**.

3. Välj den som ditt privata moln skapades på.

    ![Lista över CloudSimple-Virtual Machines](media/list-cloudsimple-virtual-machines.png)

Lista över virtuella CloudSimple-datorer innehåller virtuella datorer som skapats från Azure Portal.  Virtuella datorer som har skapats i privata moln vCenter i den mappade vCenter-resurspoolen visas i listan.  
