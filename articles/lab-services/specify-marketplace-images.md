---
title: Ange Marketplace-avbildningar för ett labb i Azure Lab Services
description: Den här artikeln visar hur du anger de Marketplace-avbildningar som labb skaparen kan använda för att skapa labb i ett labb konto i Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5c81c8d7a15d67055729a29e98f5b7e30f3d0764
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434744"
---
# <a name="specify-marketplace-images-available-to-lab-creators"></a>Ange Marketplace-avbildningar som är tillgängliga för labbuppgiftsförfattare
Som labbkontoägare kan du ange Marketplace-avbildningar som labbskapare kan använda för att skapa labb i labbkontot. 

## <a name="select-images-available-for-labs"></a>Välj bilder som är tillgängliga för labb
Välj **Marketplace-avbildningar** på menyn till vänster. Som standard kan se du en fullständig lista över avbildningar (både aktiverade och inaktiverade). Du kan filtrera listan om du bara vill se aktiverade/inaktiverade bilder genom att välja alternativet **aktive rad endast** / **inaktive** rad i list rutan längst upp. 
    
![Sida för Marketplace-avbildningar](./media/tutorial-setup-lab-account/marketplace-images-page.png)

De Marketplace-avbildningar som visas i listan är bara de filer som uppfyller följande villkor:
    
- Skapar en enskild virtuell dator.
- Använder Azure Resource Manager för att etablera virtuella datorer
- Kräver inte att du köper en extra licensieringsplan

## <a name="disable-images-for-a-lab"></a>Inaktivera bilder för ett labb 
Om du vill inaktivera en enskild bild för ett labb väljer du **... (tre punkter)** i den sista kolumnen och välj **inaktivera avbildning**. 

![Inaktivera en avbildning](./media/tutorial-setup-lab-account/disable-one-image.png) 

Du kan också markera kryss rutan före avbildningens namn och välja **inaktivera markerade bilder** i verktygsfältet. 

Om du vill inaktivera flera avbildningar samtidigt väljer du kryss rutorna före bild namnen och väljer **inaktivera markerade bilder** i verktygsfältet. 

![Inaktivera flera avbildningar](./media/tutorial-setup-lab-account/disable-multiple-images.png) 


## <a name="enable-images-for-a-lab"></a>Aktivera bilder för ett labb
Om du vill aktivera en inaktive rad avbildning väljer du **... (tre punkter)** i den sista kolumnen och välj **Aktivera avbildning**. Du kan också markera kryss rutan före bild namnet och välja **aktivera markerade bilder** i verktygsfältet. 

Om du vill inaktivera flera avbildningar samtidigt väljer du kryss rutorna före bild namnen och väljer **aktivera markerade bilder** i verktygsfältet. 

## <a name="enable-images-at-the-time-of-lab-creation"></a>Aktivera bilder vid tidpunkten för att skapa labbet
Du kan aktivera fler avbildningar när du skapar tids labbet: 

1. Logga in på [Azure Lab Services webbplats](https://labs.azure.com) med hjälp av autentiseringsuppgifter för **labb konto ägare**
2. Välj Standard avbildning för virtuell dator eller nedåtpilen. 
3. Välj **aktivera fler bild alternativ**. 

    ![Aktivera fler bild alternativ](./media/specify-marketplace-images/enable-more-images-menu.png)
4. Följ anvisningarna från föregående avsnitt för att aktivera de avbildningar du väljer. 
5. Du kan behöva stänga det **nya labb** fönstret och öppna det igen för att se de bilder som du valde i föregående steg. 



## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera labb som labbägare](how-to-manage-classroom-labs.md)
- [Konfigurera och publicera mallar som labbägare](how-to-create-manage-template.md)
- [Konfigurera och kontroller användning av ett labb som labbägare](how-to-configure-student-usage.md)
- [Som labb användare, åtkomst labb](how-to-use-classroom-lab.md)
