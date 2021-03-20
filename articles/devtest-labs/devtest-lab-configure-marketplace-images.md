---
title: Konfigurera inställningarna för Azure Marketplace-avbildningar i Azure DevTest Labs
description: Konfigurera vilka Azure Marketplace-avbildningar som kan användas när du skapar en virtuell dator i Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a5158ed33bf253db1dbe0eb3232bc43d27ce15e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86512443"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurera inställningarna för Azure Marketplace-avbildningar i Azure DevTest Labs
DevTest Labs har stöd för att skapa virtuella datorer baserat på Azure Marketplace-avbildningar beroende på hur du har konfigurerat Azure Marketplace-avbildningar som ska användas i labbet. Den här artikeln visar hur du anger vilka Azure Marketplace-avbildningar som kan användas när du skapar virtuella datorer i ett labb. Det garanterar att ditt team bara har åtkomst till de Marketplace-avbildningar som de behöver. 

## <a name="specify-allowed-images-for-creating-vms"></a>Ange tillåtna avbildningar för att skapa virtuella datorer
Följ dessa steg för att ange vilka Azure Marketplace-avbildningar som tillåts när du skapar en virtuell dator. 

1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
3. I listan med labb väljer du ditt labb. 
4. På Start sidan för labb sidan väljer du **konfiguration och principer**.
5. På sidan **konfiguration och principer** för labb i de **virtuella dator baserna** väljer du **Marketplace-avbildningar**.
6. Ange om du vill att alla kvalificerade Azure Marketplace-avbildningar ska vara tillgängliga för användning som bas för en ny virtuell dator. Om du väljer **Ja** tillåts alla Azure Marketplace-avbildningar som uppfyller alla följande kriterier i labbet:
   
   * Avbildningen skapar en enskild virtuell dator **och**
   * Avbildningen använder Azure Resource Manager för att etablera virtuella datorer **och**
   * Avbildningen kräver inte att du köper en extra licens plan
     
     Om du inte vill att några bilder ska tillåtas eller om du vill ange vilka bilder som kan användas väljer du **Nej**.
     
     ![Alternativ för att tillåta att alla Marketplace-avbildningar används som bas avbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Om du väljer **Nej** till föregående steg aktive ras kryss rutan **tillåtna bilder/Markera alla** . 
   Du kan använda det här alternativet tillsammans med sökrutan för att snabbt välja eller avmarkera alla objekt som visas i listan.
   * Välj de Azure Marketplace-avbildningar som du vill tillåta för att skapa virtuella datorer individuellt genom att kontrol lera varje avbildnings motsvarande kryss ruta.
   * Välj ingenting från listan om du inte vill att några Azure Marketplace-avbildningar ska användas i labbet.
   
     ![Du kan ange vilka Azure Marketplace-avbildningar som kan användas som bas avbildningar för virtuella datorer](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>Felsöka
Om du inte kan hitta en avbildning som ska aktive ras för labbet följer du dessa steg: 

- Kontrol lera om du kan se avbildningen när du skapar en virtuell dator för beräkning.
- Avbildningen kanske inte är tillgänglig i den typ av prenumeration du använder. Kontakta prenumerations administratören för typen av prenumeration (till exempel MSDN, kostnads fri, betala per användning osv.). 
- Stöd för gen 2-bilder i DevTest Labs är begränsat. Om både gen 1-och generation 2-versioner är tillgängliga för en avbildning, visar DevTest Labs bara generation 1-versionen av avbildningen när du skapar en virtuell dator. Lösning är att skapa en anpassad gen 2-avbildning utanför labbet och använda den för att skapa en virtuell dator. Om det bara finns en version av avbildningen som är tillgänglig, stöds den och visas i listan med DevTest Labs. 
      


## <a name="next-steps"></a>Nästa steg
När du har konfigurerat hur Azure Marketplace-avbildningar tillåts när du skapar en virtuell dator är nästa steg att [lägga till en virtuell dator i labbet](devtest-lab-add-vm.md).

