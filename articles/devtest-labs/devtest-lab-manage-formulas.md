---
title: Hantera formler i Azure DevTest Labs för att skapa virtuella datorer | Microsoft Docs
description: Den här artikeln visar hur du skapar en formel från antingen en bas (anpassad avbildning, Marketplace-avbildning eller en annan formel) eller en befintlig virtuell dator.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f012a8c59a8e938334c3e1abc4c7b3ccd0e48d3e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91308902"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Hantera Azure DevTest Labs formler

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Den här artikeln visar hur du skapar en formel från antingen en bas (anpassad avbildning, Marketplace-avbildning eller en annan formel) eller en befintlig virtuell dator. Den här artikeln vägleder dig genom hantering av befintliga formler.

## <a name="create-a-formula"></a>Skapa en formel
Vem som helst med DevTest Labs- *användare* kan skapa virtuella datorer med en formel som bas. Det finns två sätt att skapa formler: 

* Från en grundläggande användning när du vill definiera alla egenskaper för formeln.
* Från en befintlig labb VM – Använd när du vill skapa en formel som baseras på inställningarna för en befintlig virtuell dator.

Mer information om hur du lägger till användare och behörigheter finns i [Lägg till ägare och användare i Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Skapa en formel från en bas
Följande steg vägleder dig genom processen att skapa en formel från en anpassad avbildning, Marketplace-avbildning eller en annan formel.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.

3. I listan med labb väljer du önskat labb.  

4. På labb sidan väljer du **formler (återanvändbara baser)** på den vänstra menyn.
5. På sidan **formler** väljer du **+ Lägg till**.
   
    ![Lägg till en formel](./media/devtest-lab-create-formulas/add-formula.png)
6. På sidan **Välj en bas** väljer du bas (anpassad avbildning, Marketplace-avbildning eller en avbildning av ett delat bild galleri) som du vill skapa formeln från.

    :::image type="content" source="./media/devtest-lab-create-formulas/select-base.png" alt-text="Välj bas avbildningen":::
1. På fliken **grundläggande inställningar** på sidan **skapa formel** anger du följande värden:
   
    * **Formel namn** – ange ett namn för din formel. Det här värdet visas i listan med bas avbildningar när du skapar en virtuell dator. Namnet verifieras när du skriver det och om det inte är giltigt anger ett meddelande kraven för ett giltigt namn.
    - Ange en valfri **Beskrivning** av formeln. 
    * **Användar namn** – ange ett användar namn som beviljas administratörs behörighet.
    * **Lösen ord** – ange eller Välj i list rutan – ett värde som är associerat med hemligheten (lösen ordet) som du vill använda för den angivna användaren. Information om hur du sparar hemligheter i ett nyckel valv och använder dem när du skapar labb resurser finns i [lagra hemligheter i Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).

        Välj **Använd en sparad hemlighet** om du vill använda en hemlighet från Azure Key Vault i stället för att använda ett lösen ord. 
    * **Storlek på virtuell dator** – Välj **ändra storlek** om du vill ändra storleken på den virtuella datorn. 
    - **Typ av operativ system disk** – Välj den typ av disk som du vill använda (Standard HDD, Standard SSD eller Premium SSD).
    * **Artefakter** – Välj sidan **Lägg till eller ta bort artefakter** där du väljer och konfigurerar de artefakter som du vill lägga till i bas avbildningen. Mer information om artefakter finns i [skapa anpassade artefakter för din Azure DevTest Labs virtuella dator](devtest-lab-artifact-author.md).

        ![Sidan grundläggande inställningar](./media/devtest-lab-create-formulas/basic-settings.png)
8. Växla till fliken **Avancerade inställningar** och ange följande värden:
    - **Virtuellt nätverk** – om du vill ändra det virtuella nätverket väljer du **ändra VNet**. 
    - **Undernät** – om du vill ändra under nätet väljer du **ändra undernät**. 
    - **IP-adresskonfiguration** – ange om du vill använda offentliga, privata eller delade IP-adresser. Mer information om delade IP-adresser finns [i Förstå delade IP-adresser i Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Förfallo datum och-tid** -du kan inte redigera det här fältet. 
    - **Gör så att den här datorn kan kräva** en dator "anspråkable" innebär att den inte tilldelas ägande vid tidpunkten för skapandet. I stället kommer labb användare att kunna ta ägarskap ("anspråk") till datorn på labb sidan.  

        ![Skärm bild som visar avancerade inställningar för sidan "skapa formel (återanvändbara Base)".](./media/devtest-lab-create-formulas/advanced-settings.png)
    - Om du har valt en avbildning av ett delat bild galleri som bas, visas även fältet **bild version** som gör att du kan välja den version av avbildningen från galleriet som du vill använda som bas. 

        ![Sida för avancerade inställningar](./media/devtest-lab-create-formulas/advanced-settings-shared-image-gallery.png)
8. Välj **Skicka** för att skapa formeln.

9. När formeln har skapats visas den i listan på sidan **formler** .

### <a name="create-a-formula-from-a-vm"></a>Skapa en formel från en virtuell dator
Följande steg vägleder dig genom processen att skapa en formel som baseras på en befintlig virtuell dator. 

> [!NOTE]
> Om du vill skapa en formel från en virtuell dator måste den virtuella datorn ha skapats efter den 30 mars 2016. 
> 
> 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du önskat labb.  
4. På labb **översikts** sidan väljer du den virtuella dator som du vill skapa formeln från.
   
    ![Virtuella labb datorer](./media/devtest-lab-create-formulas/my-vms.png)
5. På sidan för den virtuella datorn väljer du **skapa formel (återanvändbar bas)**.
   
    ![Skapa formel](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. På sidan **skapa formel** anger du ett **namn** och en **Beskrivning** för den nya formeln.
   
    ![Sidan skapa formel](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Skapa formeln genom att klicka på **OK** .

## <a name="modify-a-formula"></a>Ändra en formel
Följ dessa steg om du vill ändra en formel:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du önskat labb.  
4. På labb sidan väljer du **formler (återanvändbara baser)**.
   
    ![Skärm bild som visar labb sidan med "formler (återanvändbara baser)" markerade.](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. På sidan **labb formler** väljer du den formel som du vill ändra.
6. På sidan **Uppdatera formel** gör du önskade ändringar och väljer **Uppdatera**.

## <a name="delete-a-formula"></a>Ta bort en formel
Följ dessa steg om du vill ta bort en formel:

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du önskat labb.  
4. På sidan labb **Inställningar** väljer du **formler**.
   
    ![Formel meny](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. På sidan **labb formler** väljer du ellipsen till höger om formeln som du vill ta bort.
   
    ![Skärm bild som visar sidan "labb formler" med tre punkter för formel alternativen markerade.](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. På formelns snabb meny väljer du **ta bort**.
   
    ![Snabb meny för formel](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Välj **Ja** till bekräftelse dialog rutan för borttagning.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Relaterade blogg inlägg
* [Anpassade bilder eller formler?](devtest-lab-faq.md#what-is-the-difference-between-a-custom-image-and-a-formula)

## <a name="next-steps"></a>Nästa steg
När du har skapat en formel som ska användas när du skapar en virtuell dator är nästa steg att [lägga till en virtuell dator i labbet](devtest-lab-add-vm.md).

