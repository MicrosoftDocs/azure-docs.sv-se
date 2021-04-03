---
title: Anslut till dina virtuella Linux-datorer i Azure DevTest Labs
description: Lär dig hur du ansluter till din virtuella Linux-dator i ett labb (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86532187"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Ansluta till en virtuell Linux-dator i labbet (Azure DevTest Labs)
Den här artikeln visar hur du ansluter till en virtuell Linux-dator i labbet. 

## <a name="connect-to-a-linux-vm"></a>Ansluta till en virtuell Linux-dator
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I Sök fältet söker du efter och väljer **DevTest Labs**. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="Sök efter och välj DevTest Labs":::    
1. I listan med labb väljer du ditt **labb**.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Välj ditt labb":::            
1. På Start sidan för ditt labb väljer du din virtuella Linux-dator i listan **mina virtuella datorer** . 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Välj din virtuella Linux-dator":::        
5. På sidan **Översikt** kan du se det fullständigt kvalificerade domän namnet (FQDN) eller IP-adressen för den virtuella datorn. Du kan också se porten som visas i följande bild.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="Fullständigt kvalificerat domän namn för den virtuella datorn":::    

    Observera att knappen **Anslut** är nedtonad även om den virtuella datorn har startats. Detta är avsiktligt.
6.  Använd SSH för att ansluta till din virtuella Linux-dator. Följande exempel ansluter till den virtuella datorn med FQDN `mydtl07172452621450000.eastus.cloudapp.azure.com` med användar namnet `vmuser` och porten `51637` . Ange lösen ordet för användaren för att ansluta till den virtuella datorn. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    Du kan använda verktyg som till exempel [SparaTillFil](https://www.putty.org/) eller andra SSH-klienter för att ansluta till den virtuella datorn. 

    När du har anslutit med SSH kan du installera och konfigurera en Skriv bords miljö ([xfce](https://www.xfce.org)) och fjärr skrivbord ([xrdp](http://xrdp.org)).  Detaljerad information finns i [Installera och konfigurera fjärr skrivbord för att ansluta till en virtuell Linux-dator i Azure](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Nästa steg
[Gör så här: Anslut till en virtuell Windows-dator](connect-windows-virtual-machine.md)
