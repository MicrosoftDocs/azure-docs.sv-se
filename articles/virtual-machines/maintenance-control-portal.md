---
title: Underhålls kontroll för virtuella Azure-datorer med hjälp av Azure Portal
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med underhålls kontroll och Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 0dea30723a3ebe1598d6304ac3c98bfe1b55b466
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563021"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Styra uppdateringar med underhålls kontroll och Azure Portal

Med underhålls kontrollen kan du bestämma när du ska tillämpa uppdateringar på dina isolerade virtuella datorer och Azure-dedikerade värdar. I det här avsnittet beskrivs Azure Portal alternativ för underhålls kontroll. Mer information om fördelarna med att använda underhålls kontroll, dess begränsningar och andra hanterings alternativ finns i [Hantera plattforms uppdateringar med underhålls kontroll](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Skapa en underhållskonfiguration

1. Logga in på Azure-portalen.

1. Sök efter **underhålls konfigurationer**.

   ![Skärm bild som visar hur du öppnar underhålls konfigurationer](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. Klicka på **Lägg till**.

   ![Skärm bild som visar hur du lägger till en underhålls konfiguration](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Välj en prenumeration och resurs grupp, ange ett namn för konfigurationen och välj en region. Klicka på **Nästa**.

   ![Skärm bild som visar grunderna för underhålls konfiguration](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Lägg till taggar och värden. Klicka på **Nästa**.

   ![Skärm bild som visar hur du lägger till taggar i en underhålls konfiguration](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Granska sammanfattningen. Klicka på **Skapa**.

   ![Skärm bild som visar hur du skapar en underhålls konfiguration](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. När distributionen är klar klickar du på **gå till resurs**.

   ![Skärm bild som visar distribution av underhålls konfiguration slutförd](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>Tilldela konfigurationen

Klicka på tilldelningar på sidan information i underhålls konfigurationen och klicka sedan på **tilldela resurs**. 

![Skärm bild som visar hur du tilldelar en resurs](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Välj de resurser som du vill att underhålls konfigurationen ska tilldelas och klicka på **OK**. Kolumnen **typ** visar om resursen är en isolerad virtuell dator eller en dedikerad Azure-värd. Den virtuella datorn måste vara igång för att konfigurationen ska kunna tilldelas. Ett fel inträffar om du försöker tilldela en konfiguration till en virtuell dator som har stoppats. 

<!---Shantanu to add details about the error case--->

![Skärm bild som visar hur du väljer en resurs](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Kontrol lera konfigurationen

Du kan kontrol lera att konfigurationen har tillämpats korrekt eller kontrol lera om det finns någon underhålls konfiguration som för närvarande har tilldelats med hjälp av **underhålls konfigurationer**. Kolumnen **typ** visar om konfigurationen är kopplad till en isolerad virtuell dator eller en dedikerad Azure-värd. 

![Skärm bild som visar hur du kontrollerar en underhålls konfiguration](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

Du kan också kontrol lera konfigurationen för en speciell virtuell dator på sidan Egenskaper. Klicka på **Underhåll** för att se konfigurationen som tilldelats den virtuella datorn.

![Skärm bild som visar hur du kontrollerar underhåll för en värd](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Sök efter väntande uppdateringar

Det finns också två sätt att kontrol lera om uppdateringar väntar på en underhålls konfiguration. I **underhålls** konfiguration klickar du på **tilldelningar** i information om konfigurationen och kontrollerar **underhålls status**.

![Skärm bild som visar hur du kontrollerar väntande uppdateringar](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

Du kan också kontrol lera en särskild värd med hjälp av **Virtual Machines** eller egenskaperna för den dedikerade värden. 

![Skärm bild som visar det markerade underhålls läget.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Tillämpa uppdateringar

Du kan tillämpa väntande uppdateringar på begäran med hjälp av **Virtual Machines**. Klicka på **Underhåll** på den virtuella dator informationen och klicka sedan på **tillämpa underhåll nu**.

![Skärm bild som visar hur du använder väntande uppdateringar](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Kontrol lera status för att tillämpa uppdateringar 

Du kan kontrol lera förloppet för uppdateringarna för en konfiguration i **underhålls konfiguration** eller med hjälp av **Virtual Machines**. Klicka på **Underhåll** i den virtuella dator informationen. I följande exempel visar **underhålls status** en uppdatering **väntar**.

![Skärm bild som visar hur du kontrollerar status för väntande uppdateringar](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Ta bort en underhålls konfiguration

Om du vill ta bort en konfiguration öppnar du konfigurations informationen och klickar på **ta bort**.

![Skärm bild som visar hur du tar bort en konfiguration.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Nästa steg

Mer information finns i [underhåll och uppdateringar](maintenance-and-updates.md).
