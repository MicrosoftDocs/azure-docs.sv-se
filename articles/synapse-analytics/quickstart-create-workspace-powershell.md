---
title: 'Snabbstart: Skapa en Synapse-arbetsyta med Azure PowerShell'
description: Skapa en Azure Synapse med hjälp Azure PowerShell genom att följa stegen i den här guiden.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1f02833ef7497c34b72db6b858a51c6046bbf3df
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567578"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Snabbstart: Skapa en Azure Synapse-arbetsyta med Azure PowerShell

Azure PowerShell är en uppsättning cmdlets för att hantera Azure-resurser direkt från PowerShell. Du kan använda det i din webbläsare med Azure Cloud Shell. Du kan också installera det på macOS, Linux eller Windows.

I den här snabbstarten lär du dig att skapa en Synapse-arbetsyta med Azure PowerShell.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- [Azure Data Lake Storage Gen2 lagringskonto](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Arbetsytan Azure Synapse måste kunna läsa och skriva till det valda ADLS Gen2 konto. För alla lagringskonto som du länkar som primärt lagringskonto måste du aktivera hierarkisk **namnrymd** när lagringskontot skapas enligt beskrivningen i [Skapa ett lagringskonto](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account).

Om du väljer att använda Cloud Shell finns [mer information i Översikt Azure Cloud Shell](../cloud-shell/overview.md) information.

### <a name="install-the-azure-powershell-module-locally"></a>Installera Azure PowerShell lokalt

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar Az PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Mer information om hur du installerar Az PowerShell-modulen finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps).

Mer information om autentisering med Azure PowerShell finns i [Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps).

### <a name="install-the-azure-synapse-powershell-module"></a>Installera Azure Synapse PowerShell-modulen

> [!IMPORTANT]
> När **Az.Synapse** PowerShell-modulen är i förhandsversion måste du installera den separat med hjälp av `Install-Module` cmdleten . När modulen blir allmänt tillgänglig kommer den att ingå i framtida versioner av Az PowerShell-modulen och vara tillgänglig som standard i Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Skapa en Azure Synapse med Azure PowerShell

1. Definiera nödvändiga miljövariabler för att skapa resurser för Azure Synapse arbetsyta.

   |        Variabelnamn        |                                                 Description                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | Namnet på ditt ADLS Gen2 lagringskonto.                                                           |
   | StorageAccountResourceGroup | Namnet på din ADLS Gen2 resursgrupp för lagringskonto.                                             |
   | FileShareName               | Namnet på ditt befintliga lagringsfilsystem.                                                                  |
   | SynapseResourceGroup        | Välj ett nytt namn för Azure Synapse resursgrupp.                                                    |
   | Region                      | Välj en av [Azure-regionerna](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
   | SynapseWorkspaceName        | Välj ett unikt namn för den nya Azure Synapse arbetsyta.                                                  |
   | SqlUser                     | Välj ett värde för ett nytt användarnamn.                                                                          |
   | SqlPassword                 | Välj ett säkert lösenord.                                                                                   |
   | ClientIP                    | Offentlig IP-adress för det system som du kör PowerShell från.                                             |
   |                             |                                                                                                             |

1. Skapa en resursgrupp som en container för Azure Synapse arbetsyta:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Skapa en Azure Synapse arbetsyta:

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

1. Hämta webb- och utvecklings-URL för Azure Synapse arbetsyta:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Skapa en brandväggsregel som ger åtkomst till Azure Synapse arbetsyta från datorn:

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

1. Öppna webbadressadressen Azure Synapse för arbetsyta som lagras i miljövariabeln `WorkspaceWeb` för att få åtkomst till din arbetsyta:

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse webbarbetsyta](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Rensa resurser

Följ stegen nedan för att ta bort Azure Synapse arbetsyta.

> [!WARNING]
> Om du tar Azure Synapse en arbetsyta tas analysmotorerna och data som lagras i databasen för sql-pooler och metadata för arbetsytan bort. Det går inte längre att ansluta till SQL- eller Apache Spark slutpunkter. Alla kodartefakter tas bort (frågor, notebook-filer, jobbdefinitioner och pipelines). Om du tar bort **arbetsytan påverkas** inte data i Data Lake Store Gen2 som är länkade till arbetsytan.

Om den Azure Synapse som skapades i den här artikeln inte behövs kan du ta bort den genom att köra följande exempel.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Sedan kan du skapa [SQL-pooler](quickstart-create-sql-pool-studio.md) [eller skapa Apache Spark för](quickstart-create-apache-spark-pool-studio.md) att börja analysera och utforska dina data.