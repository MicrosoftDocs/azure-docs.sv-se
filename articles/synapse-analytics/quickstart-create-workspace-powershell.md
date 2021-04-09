---
title: 'Snabb start: skapa en Synapse-arbetsyta med hjälp av Azure PowerShell'
description: Skapa en Azure dataSynapses-arbetsyta med hjälp av Azure PowerShell genom att följa stegen i den här hand boken.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a19d1f26616697e15ae4c39a63c44bdaf83f78f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101675767"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Snabb start: skapa en Azure datasynapses-arbetsyta med Azure PowerShell

Azure PowerShell är en uppsättning cmdletar för att hantera Azure-resurser direkt från PowerShell. Du kan använda det i din webbläsare med Azure Cloud Shell. Du kan även installera det på macOS, Linux eller Windows.

I den här snabb starten lär du dig att skapa en Synapse-arbetsyta med hjälp av Azure PowerShell.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- [Azure Data Lake Storage Gen2 lagrings konto](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Azure Synapse-arbetsytan måste kunna läsa och skriva till det valda ADLS Gen2 kontot. För alla lagrings konton som du länkar som primärt lagrings konto måste du aktivera **hierarkiskt namn område** när lagrings kontot skapas enligt beskrivningen i [skapa ett lagrings konto](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account).

Om du väljer att använda Cloud Shell, se [Översikt över Azure Cloud Shell](../cloud-shell/overview.md) för mer information.

### <a name="install-the-azure-powershell-module-locally"></a>Installera Azure PowerShell-modulen lokalt

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

Mer information om autentisering med Azure PowerShell finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

### <a name="install-the-azure-synapse-powershell-module"></a>Installera Azure Synapse PowerShell-modulen

> [!IMPORTANT]
> Även om **AZ. Synapse** PowerShell-modulen är i för hands version måste du installera den separat med hjälp av `Install-Module` cmdleten. När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Skapa en Azure dataSynapses-arbetsyta med hjälp av Azure PowerShell

1. Definiera nödvändiga miljövariabler för att skapa resurser för Azure dataSynapses-arbetsytan.

   |        Variabelnamn        |                                                 Beskrivning                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | Namn på ditt befintliga ADLS Gen2 lagrings konto.                                                           |
   | StorageAccountResourceGroup | Namnet på din befintliga ADLS Gen2 lagrings konto resurs grupp.                                             |
   | FileShareName               | Namnet på ditt befintliga lagrings fil system.                                                                  |
   | SynapseResourceGroup        | Välj ett nytt namn för din resurs grupp för Azure-Synapse.                                                    |
   | Region                      | Välj en av [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
   | SynapseWorkspaceName        | Välj ett unikt namn för din nya Azure Synapse-arbetsyta.                                                  |
   | SqlUser                     | Välj ett värde för ett nytt användar namn.                                                                          |
   | SqlPassword                 | Välj ett säkert lösen ord.                                                                                   |
   | ClientIP                    | Den offentliga IP-adressen för det system som du kör PowerShell från.                                             |
   |                             |                                                                                                             |

1. Skapa en resurs grupp som en behållare för din Azure Synapse-arbetsyta:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Skapa en Azure dataSynapses-arbetsyta:

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Hämta webb-och dev-URL för Azure dataSynapses-arbetsyta:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Skapa en brand Väggs regel som ger åtkomst till Azure Synapse-arbetsytan från din dator:

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. Öppna webb adressen för webb adressen för Azure Synapse-arbetsytan som lagras i miljövariabeln `WorkspaceWeb` för att få åtkomst till arbets ytan

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse-arbetsyta](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort Azure dataSynapses-arbetsytan.

> [!WARNING]
> Om du tar bort en Azure Synapse-arbetsyta tas analys motorerna och de data som lagras i databasen med SQL-pooler och metadata för arbets ytan bort. Det går inte längre att ansluta till SQL-eller Apache Spark-slutpunkter. Alla kod artefakter tas bort (frågor, antecknings böcker, jobb definitioner och pipeliner). Att ta bort arbets ytan påverkar **inte** data i den data Lake Store Gen2 som är länkad till arbets ytan.

Om Azure Synapse-arbetsytan som skapats i den här artikeln inte behövs kan du ta bort den genom att köra följande exempel.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Sedan kan du [skapa SQL-pooler](quickstart-create-sql-pool-studio.md) eller [skapa Apache Spark pooler](quickstart-create-apache-spark-pool-studio.md) för att börja analysera och utforska dina data.