---
title: Skapa ett innehålls bibliotek för att distribuera virtuella datorer i Azure VMware-lösningen
description: Skapa ett innehålls bibliotek för att distribuera en virtuell dator i ett privat moln i Azure VMware-lösningen.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: a50b12ef8e139bf7de171398fd28f74fc3f310c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382012"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Skapa ett innehålls bibliotek för att distribuera virtuella datorer i Azure VMware-lösningen

Ett innehålls bibliotek lagrar och hanterar innehåll i form av biblioteks objekt. Ett enda biblioteks objekt består av en eller flera filer som du använder för att distribuera virtuella datorer (VM). 

I den här artikeln går vi igenom proceduren för att skapa ett innehålls bibliotek.  Sedan kommer vi att gå igenom distributionen av en virtuell dator med en ISO-avbildning från innehålls biblioteket.

## <a name="prerequisites"></a>Förutsättningar

Ett NSX-T-segment (logisk växel) och en hanterad DHCP-tjänst krävs för att slutföra den här självstudien.  Mer information finns i artikeln [Hantera DHCP i Azure VMware Solution](manage-dhcp.md) .

## <a name="create-a-content-library"></a>Skapa ett innehålls bibliotek

1. Från den lokala vSphere-klienten väljer du **meny > innehålls bibliotek**.

   ![Välj Meny-> innehålls bibliotek](./media/content-library/vsphere-menu-content-libraries.png)

1. Välj knappen **Lägg till** för att skapa ett nytt innehålls bibliotek.

   ![Välj knappen Lägg till för att skapa ett nytt innehålls bibliotek.](./media/content-library/create-new-content-library.png)

1. Ange ett namn och bekräfta IP-adressen för vCenter-servern och välj **Nästa**.

   ![Ange ett namn och en anteckning om ditt val och välj sedan Nästa.](./media/content-library/new-content-library-step1.png)

1. Välj det **lokala innehålls biblioteket** och välj **Nästa**.

   ![I det här exemplet ska vi skapa ett lokalt innehålls bibliotek och välja nästa.](./media/content-library/new-content-library-step2.png)

1. Välj det data lager som ska lagra innehålls biblioteket och välj sedan **Nästa**.

   ![Välj det data lager som du vill använda som värd för innehålls biblioteket, välj Nästa.](./media/content-library/new-content-library-step3.png)

1. Granska och verifiera inställningarna för innehålls bibliotek och välj sedan **Slutför**.

   ![Verifiera inställningarna och välj Slutför.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Ladda upp en ISO-avbildning till innehålls biblioteket

Nu när innehålls biblioteket har skapats kan du lägga till en ISO-avbildning för att distribuera en virtuell dator till ett privat moln kluster. 

1. Välj **meny > innehålls bibliotek** i vSphere-klienten.

1. Högerklicka på det innehålls bibliotek som du vill använda för den nya ISO-filen och välj **Importera objekt**.

1. Importera ett biblioteks objekt för källan genom att göra något av följande och välj sedan **Importera**:
   1. Välj URL och ange en URL för att ladda ned en ISO-fil.

   1. Välj **lokal fil** att ladda upp från det lokala systemet.

   > [!TIP]
   > Valfritt kan du definiera ett anpassat objekt namn och noteringar för målet.

1. Öppna biblioteket och välj fliken **andra typer** för att kontrol lera att din ISO-överföring har laddats upp.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Distribuera en virtuell dator till ett privat moln kluster

1. Från vSphere-klienten väljer du **meny > värdar och kluster**.

1. I den vänstra panelen expanderar du trädet och väljer ett kluster.

1. Välj **åtgärder > ny virtuell dator**.

1. Gå igenom guiden och ändra de inställningar som du vill använda.

1. Välj **ny CD/DVD-enhet > klienten het > innehålls bibliotekets ISO-fil**.

1. Välj den ISO som överfördes i föregående avsnitt och välj sedan **OK**.

1. Markera kryss rutan **Anslut** så att ISO-filen monteras vid ström tillfället.

1. Välj **nytt nätverk > Välj List rutan > bläddra**.

1. Välj den **logiska växeln (segment)** och välj **OK**.

1. Ändra andra maskin varu inställningar och välj **Nästa**.

1. Kontrol lera inställningarna och välj **Slutför**.


## <a name="next-steps"></a>Nästa steg

Nu när du har täckt ett innehålls bibliotek för att distribuera virtuella datorer i Azure VMware-lösningen kanske du vill lära dig mer om:

- [Distribuera och konfigurera VMware-HCX](tutorial-deploy-vmware-hcx.md) för att migrera VM-arbetsbelastningar till ditt privata moln.
- [Livs cykel hantering av virtuella datorer i Azure VMware-lösningen](lifecycle-management-of-azure-vmware-solution-vms.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
