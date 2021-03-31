---
title: Hantera labb principer i Azure DevTest Labs | Microsoft Docs
description: Lär dig hur du definierar labb principer som VM-storlekar, maximalt antal virtuella datorer per användare och avstängnings automatisering.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: a7ada980b2a251b8164f2e1a20f65da54c89ba72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530360"
---
# <a name="manage-all-policies-for-a-lab-in-azure-devtest-labs"></a>Hantera alla principer för ett labb i Azure DevTest Labs

Azure DevTest Labs gör det möjligt att kontrol lera kostnaden och minimera spill i labbet genom att hantera principer (inställningar) för varje labb. Den här artikeln beskriver steg för steg hur du anger varje princip.  

## <a name="set-allowed-virtual-machine-sizes"></a>Ange tillåtna storlekar för virtuella datorer
Principen för att ställa in tillåtna VM-storlekar bidrar till att minimera labb avfall genom att göra det möjligt att ange vilka VM-storlekar som tillåts i labbet. Om den här principen är aktive rad kan endast VM-storlekar från den här listan användas för att skapa virtuella datorer.

1. I [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)väljer du ett labb och väljer sedan **konfiguration och principer**.

    ![Få till gång till Labbets konfiguration och principer](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. I rutan **konfiguration och principer** för labb väljer du **tillåtna storlekar för virtuella datorer**.
   
    ![Tillåtna storlekar för virtuella datorer](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)

1. Välj **på** för att aktivera den här principen och **Inaktivera för att** inaktivera den.

1. Om du aktiverar den här principen väljer du en eller flera storlekar på virtuella datorer som kan skapas i labbet.

1. Välj **Spara**.

## <a name="set-virtual-machines-per-user"></a>Ange virtuella datorer per användare
Med principen för **virtuella datorer per användare** kan du ange antalet virtuella datorer som kan skapas av en enskild användare. Om en användare försöker skapa eller anlägga anspråk på en virtuell dator när gränsen för användare har uppfyllts, anger ett fel meddelande att den virtuella datorn inte kan skapas/begäras. 

1. I rutan **konfiguration och principer** för labb väljer du **virtuella datorer per användare**.
   
    ![Virtuella datorer per användare](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Välj **Ja** om du vill begränsa antalet virtuella datorer per användare. Om du inte vill begränsa antalet virtuella datorer per användare väljer du **Nej**. Om du väljer **Ja** anger du ett numeriskt värde som anger antalet virtuella datorer som kan skapas eller begäras av en användare. 

1. Välj **Ja** om du vill begränsa antalet virtuella datorer som kan använda SSD (solid-state disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD väljer du **Nej**. Om du väljer **Ja** anger du ett värde som anger hur många virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-virtual-machines-per-lab"></a>Ange virtuella datorer per labb
Med principen för **virtuella datorer per labb** kan du ange antalet virtuella datorer som kan skapas för det aktuella labbet. Om en användare försöker skapa en virtuell dator när labb gränsen har uppfyllts, anger ett fel meddelande att den virtuella datorn inte kan skapas. 

1. I rutan **konfiguration och principer** för labb väljer du **virtuella datorer per labb**.
   
    ![Virtuella datorer per labb](./media/devtest-lab-set-lab-policy/max-vms-per-lab.png)

1. Välj **Ja** om du vill begränsa antalet virtuella datorer per labb. Om du inte vill begränsa antalet virtuella datorer per labb väljer du **Nej**. Om du väljer **Ja** anger du ett numeriskt värde som anger antalet virtuella datorer som kan skapas eller begäras av en användare. 

1. Välj **Ja** om du vill begränsa antalet virtuella datorer som kan använda SSD (solid-state disk). Om du inte vill begränsa antalet virtuella datorer som kan använda SSD väljer du **Nej**. Om du väljer **Ja** anger du ett värde som anger hur många virtuella datorer som kan skapas med SSD. 

1. Välj **Spara**.

## <a name="set-auto-shutdown"></a>Ange automatisk avstängning
Principen för automatisk avstängning hjälper till att minimera labb avfall genom att låta dig ange hur lång tid de virtuella datorerna i labbet ska stängas av.

1. I rutan **konfiguration och principer** för labb väljer du **Automatisk avstängning**.
   
    ![Automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Välj **på** för att aktivera den här principen och **Inaktivera för att** inaktivera den.

1. Om du aktiverar den här principen anger du tid (och tidszon) för att stänga av alla virtuella datorer i det aktuella labbet.

1. Ange **Ja** eller **Nej** om du vill skicka ett meddelande 15 minuter före den angivna automatiska avstängnings tiden. Om du väljer **Ja** anger du en webhook-URL-slutpunkt eller en e-postadress som anger var du vill att meddelandet ska publiceras eller skickas. Användaren får ett meddelande och det ges möjlighet att skjuta upp avstängningen.

   Mer information om Webhooks finns i [skapa en webhook eller API Azure-funktion](../azure-functions/functions-bindings-http-webhook.md). 

1. Välj **Spara**.

Som standard gäller den här principen för alla virtuella datorer i det aktuella labbet. Om du vill ta bort den här inställningen från en speciell virtuell dator öppnar du hanterings fönstret för den virtuella datorn och ändrar inställningen för **Automatisk avstängning** .

## <a name="set-auto-shutdown-policy"></a>Ange princip för automatisk avstängning
Som labb ägare kan du konfigurera ett avslutnings schema för alla virtuella datorer i labbet. Genom att göra det kan du spara kostnader från att köra datorer som inte används (inaktiva). Du kan tillämpa en avslutnings princip på alla dina virtuella labb datorer centralt men även spara dina labb användare för att ställa in ett schema för sina enskilda datorer. Med den här funktionen kan du ange principen för ditt labb schema med början från att ge dig ingen kontroll över fullständig behörighet till dina labb användare. Som labb ägare kan du konfigurera den här principen genom att utföra följande steg:

1. På Start sidan för ditt labb väljer du **konfiguration och principer**.
2. Välj **princip för automatisk avstängning** i avsnittet **scheman** på den vänstra menyn.
3. Välj ett av alternativen. I följande avsnitt får du mer information om de här alternativen: set-principen gäller endast för nya virtuella datorer som skapats i labbet och inte till redan befintliga virtuella datorer. 

    ![Princip alternativ för automatisk avstängning](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-options.png)

### <a name="user-sets-a-schedule-and-can-opt-out"></a>Användare anger ett schema och kan välja
Om du ställer in labbet på den här principen kan labb användarna åsidosätta eller välja ur labb schema. Det här alternativet ger labb användare fullständig kontroll över schemat för automatisk avstängning av deras virtuella datorer. Labb användare ser ingen ändring på sidan schema för automatisk avstängning av virtuell dator.

![Princip alternativ för automatisk avstängning-1](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-1.png)

### <a name="user-sets-a-schedule-and-cannot-opt-out"></a>Användare anger ett schema och kan inte avanmäla
Om du ställer in labbet på den här principen kan labb användare åsidosätta labb schema. De kan dock inte välja bort principen för automatisk avstängning. Det här alternativet ser till att varje dator i labbet är under ett schema för automatisk avstängning. Labb användare kan uppdatera schemat för de virtuella datorerna automatiskt och konfigurera avslutnings meddelanden.

![Princip alternativ för automatisk avstängning – 2](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-2.png)

### <a name="user-has-no-control-over-the-schedule-set-by-lab-admin"></a>Användaren har ingen kontroll över det schema som angetts av labb administratören
Om du ställer in labbet på den här principen kan inte labb användare åsidosätta eller välja ut ur labb schemat. Det här alternativet ger labb administratören fullständig kontroll av schemat för varje dator i labbet. Labb användare kan bara konfigurera meddelanden för automatisk avstängning för sina virtuella datorer.

![Princip alternativ för automatisk avstängning – 3](./media/devtest-lab-set-lab-policy/auto-shutdown-policy-option-3.png)

## <a name="set-autostart"></a>Konfigurera Autostart
Med principen Autostart kan du ange när de virtuella datorerna i det aktuella labbet ska startas.  

1. I rutan **konfiguration och principer** för labb väljer du automatisk **Start**.
   
    ![Skärm dum par visar alternativ för automatisk start för en virtuell dator.](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Välj **på** för att aktivera den här principen och **Inaktivera för att** inaktivera den.

3. Om du aktiverar den här principen anger du den schemalagda start tiden, tids zonen och de vecko dagar som tiden gäller. 

4. Välj **Spara**.

När den här principen är aktive rad tillämpas den inte automatiskt på virtuella datorer i det aktuella labbet. Om du vill använda den här inställningen för en speciell virtuell dator öppnar du hanterings fönstret för den virtuella datorn och ändrar inställningen för **Autostart** .

## <a name="set-expiration-date"></a>Ange förfallo datum
Du kan ange ett förfallo datum när du [skapar den virtuella datorn](devtest-lab-add-vm.md). I **Avancerade inställningar** väljer du kalender ikonen för att ange ett datum då den virtuella datorn tas bort automatiskt. Den virtuella datorn upphör som standard aldrig att gälla.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
När du har definierat och tillämpat de olika princip inställningarna för virtuella datorer för ditt labb kan du prova följande:

* [Förstå delade IP-adresser](devtest-lab-shared-ip.md) – förklarar hur delade IP-adresser används i DevTest Labs för att minimera antalet offentliga IP-adresser som krävs för att ansluta till dina virtuella labb datorer.
* [Konfigurera kostnads hantering](devtest-lab-configure-cost-management.md) – visar hur du använder diagrammet **månatlig uppskattad kostnads trend**  
  Om du vill visa den aktuella månadens beräknade kostnad hittills och den beräknade månads kostnaden.
* [Skapa anpassad avbildning](devtest-lab-create-template.md) – när du skapar en virtuell dator anger du en bas, som kan vara antingen en anpassad avbildning eller en Marketplace-avbildning. Den här artikeln visar hur du skapar en anpassad avbildning från en VHD-fil.
* [Konfigurera Marketplace-avbildningar](devtest-lab-configure-marketplace-images.md) – Azure DevTest Labs har stöd för att skapa virtuella datorer baserat på Azure Marketplace-avbildningar. Den här artikeln beskriver hur du anger vilka Azure Marketplace-avbildningar som kan användas när du skapar virtuella datorer i ett labb.
* [Skapa en virtuell dator i ett labb](devtest-lab-add-vm.md) – visar hur du skapar en virtuell dator från en bas avbildning (antingen anpassad eller Marketplace) och hur du arbetar med artefakter på den virtuella datorn.
