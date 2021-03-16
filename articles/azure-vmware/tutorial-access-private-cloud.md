---
title: Självstudie – åtkomst till ditt privata moln
description: Lär dig hur du kommer åt ett privat moln i Azure VMware-lösningen
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f2af1cffda08bf4b9c62e63f32d36cc9bbd7024a
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494401"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Självstudie: få åtkomst till ett privat moln i Azure VMware-lösningen

Med Azure VMware-lösningen kan du inte hantera ditt privata moln med din lokala vCenter. Du måste ansluta till vCenter-instansen för Azure VMware-lösningen via en hopp ruta. 

I den här självstudien skapar du en hopp ruta i resurs gruppen som du skapade i [föregående självstudie](tutorial-configure-networking.md) och loggar in på Azure VMware-lösningen vCenter. Den här hopp rutan är en virtuell Windows-dator (VM) i samma virtuella nätverk som du skapade.  Den ger åtkomst till både vCenter och NSX Manager. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows-dator för åtkomst till Azure VMware-lösningen vCenter
> * Logga in på vCenter från den här virtuella datorn

## <a name="create-a-new-windows-virtual-machine"></a>Skapa en ny virtuell Windows-dator

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Ansluta till det privata molnets lokala vCenter

1. I rutan hopp loggar du in på vSphere-klienten med VMware vCenter SSO med hjälp av ett moln administratörs användar namn och kontrollerar att användar gränssnittet visas korrekt.

1. I Azure Portal väljer du ditt privata moln och **hanterar** sedan  >  **identiteten**. 

   URL: er och användarautentiseringsuppgifter för privat moln vCenter och NSX-T Manager-visning.

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Visa URL: er och autentiseringsuppgifter för privata moln-vCenter och NSX Manager." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. Navigera till den virtuella dator som du skapade i föregående steg och Anslut till den virtuella datorn. 

   Om du behöver hjälp med att ansluta till den virtuella datorn kan du läsa mer i [ansluta till en virtuell dator](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) .

1. I den virtuella Windows-datorn öppnar du en webbläsare och navigerar till URL: erna vCenter och NSX-T Manager på två flikar. 

1. På fliken vCenter anger du autentiseringsuppgifterna för `cloudadmin@vmcp.local` användaren från föregående steg.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Logga in i privat moln vCenter." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="vCenter-portalen." border="true":::

1. Logga in på NSX-T-hanteraren på den andra fliken i webbläsaren.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Logga in på NSX-T-hanteraren på den andra fliken i webbläsaren." border="true":::



## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en virtuell Windows-dator som ska användas för att ansluta till vCenter
> * Logga in på vCenter från den virtuella datorn

Fortsätt till nästa självstudie och lär dig hur du skapar ett virtuellt nätverk för att konfigurera lokal hantering för dina privata moln kluster.

> [!div class="nextstepaction"]
> [Skapa en Virtual Network](tutorial-configure-networking.md)


