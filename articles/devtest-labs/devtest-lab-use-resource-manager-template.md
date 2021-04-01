---
title: Visa och använda en virtuell dators Azure Resource Manager mall
description: Lär dig hur du använder Azure Resource Manager-mallen från en virtuell dator för att skapa andra virtuella datorer
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fb7cb38dfb2c1664017f0e342fefecdd464df45f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696004"
---
# <a name="create-virtual-machines-using-an-azure-resource-manager-template"></a>Skapa virtuella datorer med hjälp av en Azure Resource Manager mall 

När du skapar en virtuell dator (VM) i DevTest Labs via [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)kan du Visa Azure Resource Manager mal len innan du sparar den virtuella datorn. Mallen kan sedan användas som grund för att skapa fler labb virtuella datorer med samma inställningar.

I den här artikeln beskrivs flera virtuella datorer jämfört med en Resource Manager-mall för virtuella datorer och hur du visar och sparar en mall när du skapar en virtuell dator.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="multi-vm-vs-single-vm-resource-manager-templates"></a>Resource Manager-mallar för flera virtuella datorer kontra Single-VM
Det finns två sätt att skapa virtuella datorer i DevTest Labs med en Resource Manager-mall: etablera Microsoft. DevTestLab/Labs/virtualmachines-resursen eller etablera resursen Microsoft. Compute/virtualmachines. Varje används i olika scenarier och kräver olika behörigheter.

- Resource Manager-mallar som använder en Microsoft. DevTestLab/Labs/virtualmachines-resurs typ (enligt vad som anges i egenskapen "resurs" i mallen) kan etablera enskilda virtuella labb datorer. Varje virtuell dator visas sedan som ett enskilt objekt i DevTest Labs Virtual Machines List:

   ![Skärm bild som visar en lista över virtuella datorer som enskilda objekt i DevTest Labs Virtual Machines-listan.](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-item.png)

   Den här typen av Resource Manager-mall kan tillhandahållas via Azure PowerShell kommandot **New-AzResourceGroupDeployment** eller via Azure CLI-kommandot **AZ distribution Group Create**. Det krävs administratörs behörighet, så användare som har tilldelats en användar roll för DevTest Labs kan inte utföra distributionen. 

- Resource Manager-mallar som använder en resurs typ av typen Microsoft. Compute/virtualmachines kan etablera flera virtuella datorer som en enda miljö i DevTest Labs Virtual Machines List:

   ![Lista över virtuella datorer som enskilda objekt i DevTest Labs Virtual Machines List](./media/devtest-lab-use-arm-template/devtestlab-lab-vm-single-environment.png)

   Virtuella datorer i samma miljö kan hanteras tillsammans och delas med samma livs cykel. Användare som har tilldelats en användar roll för DevTest Labs kan skapa miljöer med dessa mallar så länge som administratören har konfigurerat labbet på det sättet.

I resten av den här artikeln beskrivs Resource Manager-mallar som använder Microsoft. DevTestLab/Labs/virtualmachines. Dessa används av labb administratörer för att automatisera skapandet av virtuella labb datorer (till exempel virtuella datorer som kan krävas) eller en gyllene avbildnings generation (till exempel bild fabrik).

[Metod tips för att skapa Azure Resource Manager mallar](../azure-resource-manager/templates/template-best-practices.md) ger många rikt linjer och förslag som hjälper dig att skapa Azure Resource Manager mallar som är pålitliga och enkla att använda.

## <a name="view-and-save-a-virtual-machines-resource-manager-template"></a>Visa och spara en virtuell dators Resource Manager-mall
1. Följ stegen i [skapa din första virtuella dator i ett labb](tutorial-create-custom-lab.md#add-a-vm-to-the-lab) för att börja skapa en virtuell dator.
1. Ange den information som krävs för den virtuella datorn och Lägg till eventuella artefakter som du vill använda för den här virtuella datorn.
1. Växla till fliken **Avancerade inställningar** . 
1. Längst ned i fönstret Konfigurera inställningar väljer du **Visa arm-mall**.
1. Kopiera och spara Resource Manager-mallen och Använd den senare för att skapa en annan virtuell dator.

   ![Resource Manager-mall som ska sparas för senare användning](./media/devtest-lab-use-arm-template/devtestlab-lab-copy-rm-template.png)

När du har sparat Resource Manager-mallen måste du uppdatera avsnittet parametrar i mallen innan du kan använda det. Du kan skapa en parameter.jspå som bara anpassar parametrarna, utanför den aktuella Resource Manager-mallen. 

![Anpassa parametrar med en JSON-fil](./media/devtest-lab-use-arm-template/devtestlab-lab-custom-params.png)

Nu kan du använda Resource Manager-mallen för att [skapa en virtuell dator](devtest-lab-create-environment-from-arm.md).

## <a name="set-expiration-date"></a>Ange förfallo datum
I scenarier som utbildning, demonstrationer och test versioner kanske du vill skapa virtuella datorer och ta bort dem automatiskt efter en fast varaktighet så att du inte debiteras onödiga kostnader. Du kan skapa en virtuell labb dator med ett förfallo datum genom att ange egenskapen **expirationDate** för den virtuella datorn. Kolla in samma Resource Manager-mall i [vår GitHub-lagringsplats](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates/101-dtl-create-vm-username-pwd-customimage-with-expiration).



### <a name="next-steps"></a>Nästa steg
* Lär dig hur du [skapar miljöer för flera virtuella datorer med Resource Manager-mallar](devtest-lab-create-environment-from-arm.md).
* [Distribuera en Resource Manager-mall för att skapa en virtuell dator](devtest-lab-create-environment-from-arm.md#automate-deployment-of-environments)
* Utforska fler starter Resource Manager-mallar för DevTest Labs automation från den [offentliga DevTest Labs GitHub lagrings platsen](https://github.com/Azure/azure-quickstart-templates).
