---
title: Konfigurera bicep utvecklings-och distributions miljöer
description: Så här konfigurerar du bicep utvecklings-och distributions miljöer
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0434c08a4427d2549a9adf4e4ab1e7e73a465ad8
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620137"
---
# <a name="setup-bicep-development-and-deployment-environment"></a>Konfigurera bicep utvecklings-och distributions miljö

Lär dig hur du ställer in bicep utvecklings-och distributions miljöer.

## <a name="development-environment"></a>Utvecklingsmiljö

Du behöver två komponenter för att få bästa redigerings upplevelse för bicep:

- **Bicep-tillägg för Visual Studio Code**. Om du vill skapa bicep-filer behöver du en lämplig bicep-redigerare. Vi rekommenderar [Visual Studio Code](https://code.visualstudio.com/) med [bicep-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Dessa verktyg ger språk stöd och slut för ande av resurser. De hjälper dig att skapa och validera bicep-filer. Mer information finns i [snabb start: skapa bicep-filer med Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- **BICEP CLI**. Använd bicep CLI för att kompilera bicep-filer till ARM JSON-mallar och dekompilera ARM JSON-mallar till bicep-filer. Mer information finns i [Installera BICEP CLI](#install-bicep-cli).

## <a name="deployment-environment"></a>Distributionsmiljö

Du kan distribuera bicep-filer med hjälp av Azure CLI eller Azure PowerShell. För Azure CLI behöver du version 2.20.0 eller senare. för Azure PowerShell behöver du version 5.6.0 eller senare. Installations anvisningar finns i:

- [Installera Azure PowerShell](/powershell/azure/install-az-ps)
- [Installera Azure CLI på Windows](/cli/azure/install-azure-cli-windows)
- [Installera Azure CLI på Linux](/cli/azure/install-azure-cli-linux)
- [Installera Azure CLI på macOS](/cli/azure/install-azure-cli-macos)

> [!NOTE]
> För närvarande kan både Azure CLI och Azure PowerShell bara distribuera lokala bicep-filer. Mer information om hur du distribuerar bicep-filer med hjälp av Azure CLI finns i [Deploy-CLI](/deploy-cli.md#deploy-remote-template). Mer information om hur du distribuerar bicep-filer med hjälp av Azure PowerShell finns i [Deploy-PowerShell](/deploy-powershell.md#deploy-remote-template).

När den version av Azure PowerShell eller Azure CLI som stöds har installerats kan du distribuera en bicep-fil med:

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

Du kan installera bicep CLI med hjälp av Azure CLI genom att använda Azure PowerShell eller manuellt.

### <a name="use-azure-cli"></a>Använda Azure CLI

När AZ CLI version 2.20.0 eller senare installeras installeras bicep CLI automatiskt när ett kommando som är beroende av det körs. Exempel: `az deployment ... -f *.bicep` eller `az bicep ...`.

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
az bicep install --version v0.2.212
```

> [!NOTE]
> AZ CLI installerar en separat version av bicep CLI som inte är i konflikt med andra bicep-installationer som du kan ha, och AZ CLI lägger inte till bicep till din sökväg.

Så här visar du de installerade versionerna:

```bash
az bicep version
```

Visa en lista över alla tillgängliga versioner av bicep CLI:

```bash
az bicep list-versions
```

### <a name="use-azure-powershell"></a>Använda Azure PowerShell

Azure PowerShell har inte möjlighet att installera bicep CLI ännu. Azure PowerShell (v 5.6.0 eller senare) förväntar sig att bicep CLI redan är installerat och tillgängligt i sökvägen. Följ någon av de [manuella installations metoderna](#install-manually). När bicep CLI har installerats anropas bicep CLI när det krävs för en distributions-cmdlet. Till exempel `New-AzResourceGroupDeployment ... -TemplateFile main.bicep`.

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
