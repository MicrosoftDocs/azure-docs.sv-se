---
title: 'Snabb start: använda virtuella VMware-datorer i Azure'
titleSuffix: Azure VMware Solution by CloudSimple
description: Lär dig hur du konfigurerar och använder virtuella VMware-datorer från Azure Portal med Azure VMware-lösningen från CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4ab613c251bc43a025e0381046805ec998a04227
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77019561"
---
# <a name="quickstart---consume-vmware-vms-on-azure"></a>Snabb start – förbruka virtuella VMware-datorer i Azure

Om du vill skapa en virtuell dator i Azure Portal använder du mallar för virtuella datorer som din CloudSimple-administratör har aktiverat för din prenumeration. VM-mallarna finns i VMware-infrastrukturen.

## <a name="cloudsimple-vm-creation-on-azure-requires-a-vm-template"></a>CloudSimple VM-skapande på Azure kräver en mall för virtuell dator

Skapa en virtuell dator i ditt privata moln från vCenter-ANVÄNDARGRÄNSSNITTET. Om du vill skapa en mall följer du anvisningarna i [klona en virtuell dator till en mall i vSphere-webbklienten](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE.html). Lagra VM-mallen i ditt privata moln vCenter.

## <a name="create-a-virtual-machine-in-the-azure-portal"></a>Skapa en virtuell dator på Azure Portal

1. Välj **Alla tjänster**.

2. Sök efter **CloudSimple Virtual Machines**.

3. Klicka på **Lägg till**.

    ![Skapa CloudSimple virtuell dator](media/create-cloudsimple-virtual-machine.png)

4. Ange grundläggande information och klicka på **Nästa: storlek**.

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
    | Användarnamn | Användar namnet för den virtuella dator administratören (för Windows-mallar).|
    | Lösenord |  Lösen ord för VM-administratören (för Windows-mallar). |
    | Bekräfta lösenordet | Bekräfta lösenordet. |

5. Välj antalet kärnor och minnes kapacitet för den virtuella datorn och klicka på **Nästa: konfigurationer**. Markera kryss rutan om du vill exponera fullständig CPU-virtualisering till gäst operativ systemet. Program som kräver virtualisering av maskin vara kan köras på virtuella datorer utan binär översättning eller paravirtualiseringsegenskaperna. Mer information finns i VMware-artikeln <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html" target="_blank">exponerar VMware-maskinvarustödd virtualisering</a>.

    ![Skapa CloudSimple virtuell dator-storlek](media/create-cloudsimple-virtual-machine-size.png)

6. Konfigurera nätverks gränssnitt och diskar enligt beskrivningen i följande tabeller och klicka på **Granska + skapa**.

    ![Skapa CloudSimple-konfigurationer för virtuella datorer](media/create-cloudsimple-virtual-machine-configurations.png)

    För nätverks gränssnitt klickar du på **Lägg till nätverks gränssnitt** och konfigurerar följande inställningar.

    | Kontroll | Beskrivning |
    | ------------ | ------------- |
    | Name | Ange ett namn för att identifiera gränssnittet.  |
    | Nätverk | Välj i listan över konfigurerade distribuerade Port grupper i ditt privata moln vSphere.  |
    | Adapter | Välj en vSphere-adapter i listan över tillgängliga typer som har kon figurer ATS för den virtuella datorn. Mer information finns i VMware Knowledge Base-artikeln <a href="https://kb.vmware.com/s/article/1001805" target="_blank">välja ett nätverkskort för den virtuella datorn</a>. |
    | Starta vid start | Välj om du vill aktivera NÄTVERKSKORTets maskin vara när den virtuella datorn startas. Standardvärdet är **Enable**. |

    För diskar klickar du på **Lägg till disk** och konfigurerar följande inställningar.

    | Objekt | Beskrivning |
    | ------------ | ------------- |
    | Name | Ange ett namn för att identifiera disken.  |
    | Storlek | Välj en av de tillgängliga storlekarna.  |
    | SCSI-styrenhet | Välj en SCSI-styrenhet för disken.  |
    | Läge | Anger hur disken deltar i ögonblicks bilder. Välj något av följande alternativ: <br> -Oberoende beständig: all data som skrivs till disken skrivs permanent.<br> -Oberoende icke-permanent: ändringar som skrivs till disken ignoreras när du stänger av eller återställer den virtuella datorn.  Oberoende icke-beständigt läge gör att du alltid kan starta om den virtuella datorn i samma tillstånd. Mer information finns i VMware- <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html" target="_blank">dokumentationen</a>.

7. När verifieringen är klar granskar du inställningarna och klickar på **skapa**. Klicka på flikarna längst upp eller klicka om du vill göra några ändringar.

    ![Skapa CloudSimple virtuell dator – granska](media/create-cloudsimple-virtual-machine-review.png)

## <a name="next-steps"></a>Nästa steg

* [Visa lista med virtuella CloudSimple-datorer](azure-create-vm.md#view-list-of-cloudsimple-virtual-machines)
* [Hantera virtuella CloudSimple-datorer från Azure](azure-manage-vm.md)
