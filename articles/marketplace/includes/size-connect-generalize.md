---
title: ta med fil
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: e119d40cd0b8f482d33c3c86c644cf6a0846390a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727136"
---
## <a name="generalize-the-image"></a>Generalisera avbildningen

Alla bilder i Azure Marketplace måste kunna återanvändas på ett allmänt sätt. För att uppnå detta måste den virtuella hårddisken för operativsystemet generaliseras, en åtgärd som tar bort alla instansspecifika identifierare och programvarudrivrutiner från en virtuell dator.

### <a name="for-windows"></a>För Windows

Windows OS-diskar generaliseras med [sysprep-verktyget.](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) Om du senare uppdaterar eller konfigurerar om operativsystemet måste du köra sysprep igen.

> [!WARNING]
> När du har kört sysprep stänger du av den virtuella datorn tills den har distribuerats eftersom uppdateringar kan köras automatiskt. Den här avstängningen förhindrar att efterföljande uppdateringar gör instansspecifika ändringar i operativsystemet eller installerade tjänster. Mer information om hur du kör sysprep finns i [Steg för att generalisera en virtuell hårddisk.](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

### <a name="for-linux"></a>För Linux

Följande process generaliserar en virtuell Linux-dator och omdistribuerar den som en separat virtuell dator. Mer information finns i [Så här skapar du en avbildning av en virtuell dator eller VHD.](../../virtual-machines/linux/capture-image.md) Du kan stoppa när du når avsnittet "Skapa en virtuell dator från den avbildade avbildningen".

1. Ta bort Azure Linux-agenten.
    1. Anslut till din virtuella Linux-dator med hjälp av en SSH-klient.
    2. I SSH-fönstret anger du följande kommando: `sudo waagent –deprovision+user` .
    3. Skriv Y för att fortsätta (du kan lägga till parametern -force i föregående kommando för att undvika bekräftelsesteget).
    4. När kommandot har slutförts anger du **Avsluta för** att stänga SSH-klienten.
2. Stoppa den virtuella datorn.
    1. I Azure Portal väljer du din resursgrupp (RG) och avallokering av den virtuella datorn.
    2. Den virtuella datorn är nu generaliserad och du kan skapa en ny virtuell dator med hjälp av den här virtuella datordisken.

### <a name="capture-image"></a>Avbildning

> [!NOTE]
> Azure-prenumerationen som innehåller SIG måste finnas under samma klientorganisation som utgivarkontot för att kunna publicera. Dessutom måste utgivarkontot minst ha deltagaråtkomst till prenumerationen som innehåller SIG.

När den virtuella datorn är klar kan du skapa den i ett delat avbildningsgalleri i Azure. Följ stegen nedan för att samla in:

1. På [Azure Portal](https://ms.portal.azure.com/)går du till sidan för den virtuella datorn.
2. Välj **Capture (Avbilda).**
3. Under **Dela bild till delat bildgalleri** väljer du **Ja, dela den till ett galleri som en avbildningsversion.**
4. Under **Operativsystemstillstånd** väljer du Generaliserad.
5. Välj ett målavbildningsgalleri **eller Skapa ny.**
6. Välj en målavbildningsdefinition **eller Skapa ny.**
7. Ange ett **versionsnummer** för avbildningen.
8. Välj **Granska + skapa för** att granska dina val.
9. När verifieringen har godkänts väljer du **Skapa**.

Så här beviljar du åtkomst:

1. Gå till Shared Image Gallery.
2. Välj **Åtkomstkontroll** (IAM) på den vänstra panelen.
3. Välj **Lägg till** och sedan Lägg till **rolltilldelning.**
4. Välj en **roll** eller **ägare.**
5. Under **Tilldela åtkomst väljer** du **Användare, grupp eller tjänstens huvudnamn.**
6. Välj Azure-e-postadressen för den person som ska publicera avbildningen.
7. Välj **Spara**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Visar fönstret Lägg till rolltilldelning.":::

> [!NOTE]
> Du behöver inte generera SAS-URI:er eftersom du nu kan publicera en SIG-avbildning på Partnercenter. Men om du fortfarande behöver referera till genereringsstegen för SAS-URI kan du se Så här genererar du en [SAS-URI för en VM-avbildning.](../azure-vm-get-sas-uri.md)
