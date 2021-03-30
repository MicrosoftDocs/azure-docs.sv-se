---
title: Anslut till dina virtuella Windows-datorer i Azure DevTest Labs
description: Lär dig hur du ansluter till en virtuell Windows-dator i ett labb (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86540718"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>Ansluta till en virtuell Windows-dator i labbet (Azure DevTest Labs)
Den här artikeln visar hur du ansluter till en virtuell Windows-dator i labbet. 

## <a name="connect-to-a-windows-vm"></a>Ansluta till en virtuell Windows-dator
1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I Sök fältet söker du efter och väljer **DevTest Labs**. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="Sök efter och välj DevTest Labs":::    
1. I listan med labb väljer du ditt **labb**.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="Välj ditt labb":::            
1. På Start sidan för ditt labb väljer du din virtuella Windows-dator i listan **mina virtuella datorer** . 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Välj din virtuella Windows-dator":::                
1. På sidan **virtuell dator** för den virtuella datorn väljer du **Anslut** i verktygsfältet. Om den virtuella datorn är stoppad väljer du **Starta** först för att starta den virtuella datorn.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="Välj Anslut i verktygsfältet":::                    
1. Om du använder Edge-webbläsaren visas länken till den **nedladdade RDP-filen** längst ned i webbläsaren. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="RDP-nedladdning":::                        
1. Öppna RDP-filen och ange dina autentiseringsuppgifter för virtuella datorer som du angav när du skapade den virtuella datorn. Du bör vara ansluten till den virtuella Windows-datorn nu. 

## <a name="next-steps"></a>Nästa steg
[Gör så här: ansluta till en virtuell Linux-dator](connect-linux-virtual-machine.md)
