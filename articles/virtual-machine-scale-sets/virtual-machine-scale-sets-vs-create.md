---
title: Distribuera skalnings uppsättningen för virtuella datorer med Visual Studio
description: Distribuera Virtual Machine Scale Sets med Visual Studio och en Resource Manager-mall
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: visual-studio
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 2db281f1b3278e37deca8486971a7f7a83f85ccf
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86503089"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Så här skapar du en skalnings uppsättning för virtuella datorer med Visual Studio

Den här artikeln visar hur du distribuerar en skalnings uppsättning för en virtuell Azure-dator med hjälp av en resurs grupps distribution i Visual Studio.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) är en Azure Compute-resurs som distribuerar och hanterar en samling liknande virtuella datorer med autoskalning och belastnings utjämning. Du kan etablera och distribuera Virtual Machine Scale Sets med [Azure Resource Manager-mallar](https://github.com/Azure/azure-quickstart-templates). Azure Resource Manager mallar kan distribueras med hjälp av Azure CLI, PowerShell, REST och även direkt från Visual Studio. Visual Studio innehåller en uppsättning exempel på mallar, som du kan distribuera som en del av ett distributions projekt för Azure-resurs grupper.

Distributioner av Azure-resurs grupper är ett sätt att gruppera och publicera en uppsättning relaterade Azure-resurser i en enda distributions åtgärd. Mer information finns i [skapa och Distribuera Azure-resurs grupper via Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång med att distribuera Virtual Machine Scale Sets i Visual Studio behöver du följande förutsättningar:

* Visual Studio 2013 eller senare
* Azure SDK 2,7, 2,8 eller 2,9

>[!NOTE]
>I den här artikeln används Visual Studio 2019 med [Azure SDK 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Skapa ett projekt <a name="creating-a-project"></a> 

1. Öppna Visual Studio och välj **skapa ett nytt projekt**.

1. I **skapa ett nytt projekt** väljer du **Azure resurs grupp** för C# och väljer sedan **Nästa**.

1. I **Konfigurera ditt nya projekt anger du** ett namn och väljer **skapa**.

    ![Namnge och skapa ditt projekt](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. I listan med mallar väljer du mallen för **skalnings uppsättningen för virtuella Windows-datorer** eller virtuella Linux- **datorer** . Välj **OK**.

   ![Välj en mall för virtuell dator](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

När du har skapat projektet innehåller **Solution Explorer** ett skript för PowerShell-distribution, en Azure Resource Manager-mall och en parameter fil för skalnings uppsättningen för den virtuella datorn.

## <a name="customize-your-project"></a>Anpassa ditt projekt

Nu kan du redigera mallen för att anpassa den efter programmets behov. Du kan lägga till egenskaper för virtuell dator tillägg eller redigera regler för belastnings utjämning. Som standard konfigureras mallarna för skalnings uppsättningar för virtuella datorer för att distribuera **AzureDiagnostics** -tillägget, vilket gör det enkelt att lägga till regler för autoskalning. Mallarna distribuerar också en belastningsutjämnare med en offentlig IP-adress som kon figurer ATS med inkommande NAT-regler.

Med belastningsutjämnaren kan du ansluta till de virtuella dator instanserna med SSH (Linux) eller RDP (Windows). Klient dels port intervallet börjar på 50000. Om du använder SSH till port 50000 i Linux dirigeras belastnings utjämning till port 22 på den första virtuella datorn i skalnings uppsättningen. Anslutning till port 50001 dirigeras till port 22 för den andra virtuella datorn och så vidare.

 Ett bra sätt att redigera dina mallar med Visual Studio är att använda **JSON-disposition**. Du kan organisera parametrarna, variablerna och resurserna. Med en förståelse för schemat kan Visual Studio peka ut fel i mallen innan du distribuerar det.

![JSON Explorer](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Distribuera projektet

Distribuera Azure Resource Manager-mallen för att skapa den virtuella datorns skalnings uppsättnings resurs:

1. I **Solution Explorer** högerklickar du på projektet och väljer **distribuera**  >  **nytt**.

    ![Distribuera projektet](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. I **distribuera till resurs grupp** väljer du vilken prenumeration som ska användas och väljer en resurs grupp. Om det behövs kan du skapa en resurs grupp.

1. Välj sedan **Redigera parametrar** för att ange parametrar som skickas till din mall.

   ![Ange prenumeration och resurs grupp](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Ange användar namn och lösen ord för operativ systemet. De här värdena krävs för att skapa distributionen. Om du inte har PowerShell-verktyg för Visual Studio installerat väljer du **Spara lösen ord** för att undvika en dold PowerShell-kommandotolk eller använder [Key Vault support](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Fortsätt genom att välja **Spara** .

    ![Redigera distributions parametrar](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. I **distribuera till resurs grupp** väljer du **distribuera**. Åtgärden kör **Deploy-AzureResourceGroup.ps1** -skriptet. I fönstret **utdata** visas distributions förloppet.

   ![Utdata visar resultat](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Utforska din skalnings uppsättning för virtuella datorer <a name="exploring-your-virtual-machine-scale-set"></a>

Välj **Visa**  >  **Cloud Explorer** för att visa den nya skalnings uppsättningen för virtuella datorer. Använd **Uppdatera alla**, om det behövs.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

Med **Cloud Explorer** kan du hantera Azure-resurser i Visual Studio när du utvecklar program. Du kan också Visa skalnings uppsättningen för den virtuella datorn i [Azure Portal](https://portal.azure.com) och [Azure Resource Explorer](https://resources.azure.com/).

 Portalen är det bästa sättet att hantera din Azure-infrastruktur med en webbläsare. Azure Resource Explorer är ett enkelt sätt att utforska och felsöka Azure-resurser. Azure Resource Explorer erbjuder instans visningen och visar även PowerShell-kommandon för de resurser som du tittar på.

## <a name="next-steps"></a>Nästa steg

När du har distribuerat Virtual Machine Scale Sets via Visual Studio kan du ytterligare anpassa ditt projekt så att det passar dina program krav. Konfigurera till exempel autoskalning genom att lägga till en **Insights** -resurs. Du kan lägga till en infrastruktur till din mall, till exempel fristående virtuella datorer eller distribuera program med hjälp av tillägget för anpassat skript. Användbara exempel mallar finns i GitHub-lagringsplatsen för [Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates) . Sök efter `vmss`.
