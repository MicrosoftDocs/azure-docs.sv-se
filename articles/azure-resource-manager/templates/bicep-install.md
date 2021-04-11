---
title: Konfigurera bicep utvecklings-och distributions miljöer
description: Så här konfigurerar du bicep utvecklings-och distributions miljöer
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0e62e6a4633bee09fcbe8b783118cc95ccd5702e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626109"
---
# <a name="install-bicep-preview"></a>Installera bicep (för hands version)

Lär dig hur du konfigurerar bicep utvecklings-och distributions miljöer.

## <a name="development-environment"></a>Utvecklingsmiljö

Du behöver två komponenter för att få bästa redigerings upplevelse för bicep:

- **Bicep-tillägg för Visual Studio Code**. Om du vill skapa bicep-filer behöver du en lämplig bicep-redigerare. Vi rekommenderar [Visual Studio Code](https://code.visualstudio.com/) med [bicep-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Dessa verktyg ger språk stöd och slut för ande av resurser. De hjälper dig att skapa och validera bicep-filer. Mer information om hur du använder Visual Studio Code och bicep-tillägget finns i [snabb start: skapa bicep-filer med Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- **BICEP CLI**. Använd bicep CLI för att kompilera bicep-filer till ARM JSON-mallar och dekompilera ARM JSON-mallar till bicep-filer. Installations anvisningar finns i [Installera BICEP CLI](#install-manually).

## <a name="deployment-environment"></a>Distributionsmiljö

Om du vill distribuera lokala bicep-filer behöver du två komponenter:

- **Azure CLI version 2.20.0 eller senare, eller Azure PowerShell version 5.6.0 eller senare**. Installations anvisningar finns i:

  - [Installera Azure PowerShell](/powershell/azure/install-az-ps)
  - [Installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows)
  - [Installera Azure CLI på Linux](/cli/azure/install-azure-cli-linux)
  - [Installera Azure CLI på macOS](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > För närvarande kan både Azure CLI och Azure PowerShell bara distribuera lokala bicep-filer. Mer information om hur du distribuerar bicep-filer med hjälp av Azure CLI finns i [Deploy-CLI](./deploy-cli.md#deploy-remote-template). Mer information om hur du distribuerar bicep-filer med hjälp av Azure PowerShell finns i [Deploy-PowerShell]( ./deploy-powershell.md#deploy-remote-template).

- **BICEP CLI**. Bicep CLI krävs för att kompilera bicep-filer till JSON-mallar före distributionen. Installations anvisningar finns i [Installera BICEP CLI](#install-bicep-cli).

När komponenterna har installerats kan du distribuera en bicep-fil med:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Installera bicep CLI

- Om du vill använda bicep CLI för att kompilera och dekompilera bicep-filer, se [installera manuellt](#install-manually).
- Information om hur du använder Azure CLI för att distribuera bicep-filer finns i [använda med Azure CLI](#use-with-azure-cli).
- Om du vill använda Azure PowerShell för att distribuera bicep-filer, se [använda med Azure PowerShell](#use-with-azure-powershell).

### <a name="use-with-azure-cli"></a>Använd med Azure CLI

Med Azure CLI version 2.20.0 eller senare installerat installeras bicep CLI automatiskt när ett kommando som är beroende av det körs. Exempel:

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

eller

```azurecli
az bicep ...
```

Du kan också installera CLI manuellt med hjälp av de inbyggda kommandona:

```bash
az bicep install
```

Så här uppgraderar du till den senaste versionen:

```bash
az bicep upgrade
```

Så här installerar du en angiven version:

```bash
az bicep install --version v0.3.126
```

> [!IMPORTANT]
> Azure CLI installerar en separat version av bicep CLI som inte är i konflikt med andra bicep-installationer som du kan ha, och Azure CLI lägger inte till bicep CLI i sökvägen. Om du vill använda bicep CLI för att kompilera/dekompilera bicep-filer, eller om du vill använda Azure PowerShell för att distribuera bicep-filer, se [installera manuellt](#install-manually) eller [Använd med Azure PowerShell](#use-with-azure-powershell).

Visa en lista över alla tillgängliga versioner av bicep CLI:

```bash
az bicep list-versions
```

Så här visar du de installerade versionerna:

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>Använd med Azure PowerShell

Azure PowerShell har inte möjlighet att installera bicep CLI ännu. Azure PowerShell (v 5.6.0 eller senare) förväntar sig att bicep CLI redan är installerat och tillgängligt i sökvägen. Följ någon av de [manuella installations metoderna](#install-manually).

För att distribuera bicep-filer krävs bicep CLI version 0.3.1 eller senare. Så här kontrollerar du bicep CLI-versionen:

```cmd
bicep --version
```

> [!IMPORTANT]
> Azure CLI installerar sin egen självständiga version av bicep CLI. Azure PowerShell distributionen Miss lyckas även om du har de versioner som krävs installerade för Azure CLI.

När bicep CLI har installerats anropas bicep CLI när det krävs för en distributions-cmdlet. Exempel:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>Installera manuellt

Följande metoder installerar bicep CLI och lägger till det i din sökväg.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>via homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
```

##### <a name="macos-manual-install"></a>Installera macOS manuellt

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows installationsverktyg

Hämta och kör den [senaste versionen av Windows Installer](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe). Installations programmet kräver inte administratörs behörighet. Efter installationen läggs bicep CLI till i din användar Sök väg. Stäng och öppna alla öppna kommando tolks fönster så att Sök VÄGS ändringen börjar gälla.

##### <a name="chocolatey"></a>Chocolatey

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>Manuell med PowerShell

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>Installera de natt byggen

Om du vill testa de senaste för hands versions bitarna av bicep innan de släpps, kan du läsa mer i [Installera natt byggen](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md).

> [!WARNING]
> Dessa för hands versioner är mycket mer sannolika kända eller okända buggar.

## <a name="next-steps"></a>Nästa steg

Kom igång med snabb starten för [bicep](./quickstart-create-bicep-use-visual-studio-code.md).
