---
title: Skapa ett innehållsbibliotek för att distribuera virtuella datorer i Azure VMware Solution
description: Skapa ett innehållsbibliotek för att distribuera en virtuell dator i ett Azure VMware Solution privat moln.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: b27d2682d8799bec6b09a08e5063359113b20a88
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873891"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Skapa ett innehållsbibliotek för att distribuera virtuella datorer i Azure VMware Solution

Ett innehållsbibliotek lagrar och hanterar innehåll i form av biblioteksobjekt. Ett enskilt biblioteksobjekt består av en eller flera filer som du använder för att distribuera virtuella datorer (VM). 

I den här artikeln går vi igenom proceduren för att skapa ett innehållsbibliotek.  Sedan går vi igenom hur du distribuerar en virtuell dator med hjälp av en ISO-avbildning från innehållsbiblioteket.

## <a name="prerequisites"></a>Förutsättningar

Ett NSX-T-segment (logisk växel) och en hanterad DHCP-tjänst krävs för att slutföra den här självstudien.  Mer information finns i artikeln [Så här hanterar du DHCP i Azure VMware Solution.](manage-dhcp.md)

## <a name="create-a-content-library"></a>Skapa ett innehållsbibliotek

1. Från den lokala vSphere-klienten väljer du **Meny för >-innehållsbibliotek.**

   ![Välj meny – > innehållsbibliotek](./media/content-library/vsphere-menu-content-libraries.png)

1. Välj knappen **Lägg till** för att skapa ett nytt innehållsbibliotek.

   ![Välj knappen Lägg till för att skapa ett nytt innehållsbibliotek.](./media/content-library/create-new-content-library.png)

1. Ange ett namn och bekräfta IP-adressen för vCenter-servern och välj **Nästa.**

   ![Ange ett val av namn och anteckningar och välj sedan Nästa.](./media/content-library/new-content-library-step1.png)

1. Välj det **lokala innehållsbiblioteket** och välj **Nästa.**

   ![I det här exemplet ska vi skapa ett lokalt innehållsbibliotek och välja Nästa.](./media/content-library/new-content-library-step2.png)

1. Välj det datalager som ska lagra innehållsbiblioteket och välj sedan **Nästa.**

   ![Välj det datalager som du vill använda som värd för innehållsbiblioteket och välj nästa.](./media/content-library/new-content-library-step3.png)

1. Granska och verifiera inställningarna för innehållsbiblioteket och välj sedan **Slutför**.

   ![Verifiera dina inställningar och välj Slutför.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Ladda upp en ISO-avbildning till innehållsbiblioteket

Nu när innehållsbiblioteket har skapats kan du lägga till en ISO-avbildning för att distribuera en virtuell dator till ett privat molnkluster. 

1. Från vSphere-klienten väljer du **Meny > Innehållsbibliotek**.

1. Högerklicka på det innehållsbibliotek som du vill använda för den nya ISO-filen och välj **Importera objekt.**

1. Importera ett biblioteksobjekt för källan genom att göra något av följande och välj sedan **Importera**:
   1. Välj URL och ange en URL för att ladda ned en ISO.

   1. Välj **Lokal fil att** ladda upp från ditt lokala system.

   > [!TIP]
   > Du kan också definiera ett anpassat objektnamn och anteckningar för målet.

1. Öppna biblioteket och välj fliken **Andra typer** för att kontrollera att DIN ISO har laddats upp.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Distribuera en virtuell dator till ett privat molnkluster

1. Från vSphere-klienten väljer du **Meny för > värdar och kluster.**

1. I den vänstra panelen expanderar du trädet och väljer ett kluster.

1. Välj **Åtgärder > Ny virtuell dator**.

1. Gå igenom guiden och ändra de inställningar du vill ha.

1. Välj **Ny CD/DVD-enhet > klientenhet > ISO-fil för innehållsbiblioteket.**

1. Välj ISO som laddades upp i föregående avsnitt och välj sedan **OK.**

1. Markera **kryssrutan Anslut** så att ISO monteras vid ström.

1. Välj **Nytt nätverk > Välj listrutan > Bläddra**.

1. Välj den **logiska växeln (segmentet)** och **välj OK.**

1. Ändra eventuella andra maskinvaruinställningar och välj **Nästa.**

1. Kontrollera inställningarna och välj **Slutför.**


## <a name="next-steps"></a>Nästa steg

Nu när du har gått in på hur du skapar ett innehållsbibliotek för att distribuera virtuella datorer Azure VMware Solution kanske du vill veta mer om:

- [Så här migrerar du VM-arbetsbelastningar till ditt privata moln](tutorial-deploy-vmware-hcx.md)
- [Livscykelhantering för virtuella Azure VMware Solution datorer](lifecycle-management-of-azure-vmware-solution-vms.md)

<!-- LINKS - external-->

<!-- LINKS - internal -->
