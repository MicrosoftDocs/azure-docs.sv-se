---
title: ta med fil
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 03/25/2021
ms.openlocfilehash: 8898a762e8a1e7a2d5c104f99d12032c676a5ca4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630252"
---
## <a name="generalize-the-image"></a>Generalisera avbildningen

Alla avbildningar på Azure Marketplace måste kunna återanvändas på ett generiskt sätt. För att uppnå detta måste den virtuella hård disken generaliseras, en åtgärd som tar bort alla instans-/regionsspecifika identifierare och program driv rutiner från en virtuell dator.

### <a name="for-windows"></a>För Windows

Windows OS-diskar generaliseras med [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) -verktyget. Om du senare uppdaterar eller omkonfigurerar operativ systemet måste du köra Sysprep igen.

> [!WARNING]
> När du har kört Sysprep stänger du av den virtuella datorn tills den har distribuerats eftersom uppdateringar kan köras automatiskt. Den här avstängningen kommer att undvika efterföljande uppdateringar från att göra instansbaserade ändringar i operativ systemet eller de installerade tjänsterna. Mer information om hur du kör Sysprep finns i [steg för att generalisera en virtuell hård disk](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>För Linux

Följande process generaliserar en virtuell Linux-dator och distribuerar den igen som en separat virtuell dator. Mer information finns i [så här skapar du en avbildning av en virtuell dator eller virtuell hård disk](../../virtual-machines/linux/capture-image.md). Du kan stoppa när du når avsnittet "skapa en virtuell dator från avbildningen".

1. Ta bort Azure Linux-agenten.
    1. Anslut till din virtuella Linux-dator med hjälp av en SSH-klient.
    2. I SSH-fönstret anger du följande kommando: `sudo waagent –deprovision+user` .
    3. Skriv in Y om du vill fortsätta (du kan lägga till parametern-Force i föregående kommando för att undvika bekräftelse steget).
    4. När kommandot har slutförts anger du **Avsluta** för att stänga SSH-klienten.
2. Stoppa virtuell dator.
    1. I Azure Portal väljer du resurs gruppen (RG) och avallokerar den virtuella datorn.
    2. Den virtuella datorn är nu generaliserad och du kan skapa en ny virtuell dator med den här virtuella dator disken.

### <a name="capture-image"></a>Avbilda avbildning

När den virtuella datorn är klar kan du avbilda den i ett Azure-Galleri för delad avbildning. Följ stegen nedan för att avbilda:

1. På [Azure Portal](https://ms.portal.azure.com/)går du till den virtuella datorns sida.
2. Välj **avbildning**.
3. Under **dela avbildningen till den delade avbildnings galleriet** väljer du **Ja, dela den i ett galleri som en avbildnings version**.
4. Välj generaliserad under **operativ system tillstånd** .
5. Välj ett mål avbildnings Galleri eller **Skapa nytt**.
6. Välj en mål avbildnings definition eller **Skapa ny**.
7. Ange ett **versions nummer** för avbildningen.
8. Välj **Granska + skapa** för att granska dina val.
9. När verifieringen har slutförts väljer du **skapa**.

Azure-prenumerationen som innehåller SIG måste ligga under samma klient organisation som utgivar kontot för att kunna publicera. Utgivar kontot måste också ha en ägar åtkomst till SIG själv. 

För att bevilja åtkomst:

1. Gå till galleriet för delad avbildning.
2. Välj **åtkomst kontroll** (IAM) på den vänstra panelen.
3. Välj **Lägg till** och **Lägg sedan till roll tilldelning**.
4. Välj en **roll** eller **ägare**.
5. Under **tilldela åtkomst till väljer du** **användare, grupp eller tjänstens huvud namn**.
6. Välj Azure-e-post för den person som ska publicera avbildningen.
7. Välj **Spara**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Visar fönstret Lägg till roll tilldelning.":::

> [!NOTE]
> Du behöver inte skapa SAS-URI: er eftersom du nu kan publicera en SIG-avbildning på Partner Center. Men om du fortfarande behöver referera till stegen för att skapa SAS-URI: n, se [så här skapar du en SAS-URI för en VM-avbildning](../azure-vm-get-sas-uri.md).
