---
title: Skapa eller ändra labb med Azure Resource Manager mallar
description: Lär dig hur du använder Azure Resource Manager mallar med PowerShell för att skapa eller ändra labb automatiskt i ett DevTest-labb
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 645f1d0717514d2c7e7b16844513327127e4e1a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87272648"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Skapa eller ändra labb automatiskt med hjälp av Azure Resource Manager mallar och PowerShell

DevTest Labs innehåller många Azure Resource Manager mallar och PowerShell-skript som hjälper dig att snabbt och automatiskt skapa nya labb eller ändra befintliga labb och sedan distribuera resurserna.

Den här artikeln hjälper dig genom processen att använda dessa mallar och skript för att automatisera skapande, ändring och distribution av dina labb. Den här artikeln visar också var du hittar mer information om hur du använder PowerShell för att utföra vanliga uppgifter i DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Steg 1: samla dina mallar och skript
Du kan hitta färdiga [Azure Resource Manager mallar](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) och PowerShell- [skript](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) på vår offentliga [GitHub-lagringsplats](https://github.com/Azure/azure-devtestlab). Använd dem som de är, eller anpassa dem efter dina behov och lagra dem i din egen [privata git-lagrings platsen](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Steg 2: ändra Azure Resource Manager-mallen
Du kan följa stegen i [skapa din första Azure Resource Manager-mall](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) om du aldrig har skapat en mall tidigare.

Dessutom erbjuder [metod tips för att skapa Azure Resource Manager mallar](../azure-resource-manager/templates/template-best-practices.md) många rikt linjer och förslag som hjälper dig att skapa Azure Resource Manager mallar som är pålitliga och enkla att använda. Normalt använder du en variant av en av de metoder eller exempel som tillhandahålls och ändrar din mall efter dina behov.

## <a name="step-3-deploy-resources-with-powershell"></a>Steg 3: distribuera resurser med PowerShell
När du har anpassat dina mallar och skript följer du de steg som krävs för att [distribuera resurser med Resource Manager-mallar och Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). Artikeln innehåller allmän information om hur du använder Azure PowerShell med Azure Resource Manager mallar för att distribuera dina resurser till Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Vanliga uppgifter som du kan utföra i DevTest Labs med PowerShell
Det finns många andra vanliga uppgifter som du kan automatisera med hjälp av PowerShell. I följande avsnitt i dokumentationen beskrivs de steg som krävs för att utföra dessa uppgifter.

* [Skapa en anpassad avbildning från en VHD-fil med hjälp av PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [Ladda upp VHD-filen till Labbets lagrings konto med hjälp av PowerShell](devtest-lab-upload-vhd-using-powershell.md)
* [Lägga till en extern användare i ett labb med hjälp av PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Skapa en anpassad labb roll med PowerShell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Nästa steg
* Lär dig hur du skapar en [privat git-lagringsplats](devtest-lab-add-artifact-repo.md) där du kommer att lagra dina anpassade mallar eller skript.
* Utforska [mallarna för Azure Resource Manager mallar från Azure snabb starts galleriet](https://github.com/Azure/azure-quickstart-templates).
