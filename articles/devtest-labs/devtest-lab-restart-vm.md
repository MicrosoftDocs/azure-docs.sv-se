---
title: Starta om en virtuell dator i ett labb i Azure DevTest Labs | Microsoft Docs
description: Den här artikeln innehåller steg för att snabbt och enkelt starta om virtuella datorer (VM) i Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d2bcbcbea613aa84da91789fb9f118afd7023fab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481999"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Starta om en virtuell dator i ett labb i Azure DevTest Labs
Du kan snabbt och enkelt starta om en virtuell dator i DevTest Labs genom att följa anvisningarna i den här artikeln. Tänk på följande innan du startar om en virtuell dator:

- Den virtuella datorn måste vara igång för att funktionen restart ska vara aktive rad.
- Om en användare är ansluten till en virtuell dator som körs när de utför en omstart måste de återansluta till den virtuella datorn när den har börjat säkerhetskopiera.
- Om en artefakt används när du startar om den virtuella datorn visas en varning om att artefakten kanske inte tillämpas.

    ![Varning vid omstart när artefakter tillämpas](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Om den virtuella datorn har stoppats när du använder en artefakt kan du använda funktionen starta om virtuell dator som ett möjligt sätt att lösa problemet.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Steg för att starta om en virtuell dator i ett labb i Azure DevTest Labs
1. Logga in på [Azure-portalen](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Välj **alla tjänster** och välj sedan **DevTest Labs** i listan.
1. I listan med labb väljer du labbet som innehåller den virtuella dator som du vill starta om.
1. I den vänstra panelen väljer du **mina Virtual Machines**.
1. I listan över virtuella datorer väljer du en virtuell dator som körs.
1. Överst i fönstret hantering av virtuell dator väljer du **starta om**.

    ![Knappen starta om virtuell dator](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Övervaka statusen för omstarten genom att välja **aviserings** ikonen längst upp till höger i fönstret.

    ![Visa status för VM-omstart](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Du kan också starta om en virtuell dator som körs genom att välja dess ellips (...) i listan med **mina Virtual Machines**.

![Starta om den virtuella datorn via ellipser](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Nästa steg
* När den har startats om kan du återansluta till den virtuella datorn genom att välja **Anslut** i fönstret hantering.
* Utforska [DevTest Labs Azure Resource Manager snabb start Template Gallery](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
