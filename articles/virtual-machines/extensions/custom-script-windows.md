---
title: Anpassat skripttillägg för Azure för Windows
description: Automatisera konfigurationsuppgifter för virtuella Windows-datorer med hjälp av tillägget för anpassat skript
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
ms.author: amjads
author: amjads1
ms.collection: windows
ms.date: 08/31/2020
ms.openlocfilehash: 6341e3abbf591d0e6e0395e17ccf15ec73a3ac43
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835460"
---
# <a name="custom-script-extension-for-windows"></a>Anpassat skripttillägg för Windows

Tillägget för anpassat skript laddar ned och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurations- eller hanteringsuppgifter. Skripten kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Tillägget för anpassat skript integreras med Azure Resource Manager-mallar och kan köras med hjälp av Azure CLI, PowerShell, Azure Portal eller Azure Virtual Machine REST API.

Det här dokumentet beskriver hur du använder tillägget för anpassade skript med hjälp Azure PowerShell-modulen, Azure Resource Manager-mallar och information om felsökningssteg i Windows-system.

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]  
> Använd inte tillägget för anpassat skript för att Update-AzVM med samma virtuella dator som parametern, eftersom det väntar på sig självt.  

### <a name="operating-system"></a>Operativsystem

Det anpassade skripttillägget för Windows körs på tilläggsoperativsystemet som stöds.

### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>Skriptplats

Du kan konfigurera tillägget så att det använder dina autentiseringsuppgifter för Azure Blob Storage för att få åtkomst till Azure Blob Storage. Skriptplatsen kan vara var som helst, så länge den virtuella datorn kan dirigeras till den här startpunkten, till exempel GitHub eller en intern filserver.

### <a name="internet-connectivity"></a>Internetanslutning

Om du behöver ladda ned ett skript externt, till exempel från GitHub eller Azure Storage, måste ytterligare portar för brandvägg och nätverkssäkerhetsgrupp öppnas. Om skriptet till exempel finns i en Azure Storage kan du tillåta åtkomst med hjälp av Azure NSG-tjänsttaggar för [Storage](../../virtual-network/network-security-groups-overview.md#service-tags).

Observera att CustomScript-tillägget inte kan kringgå certifikatverifieringen. Så om du laddar ned från en skyddad plats med t.ex. ett självloggat certifikat kan du få fel som *"Fjärrcertifikatet är ogiltigt enligt valideringsproceduren".* Kontrollera att certifikatet är korrekt installerat i arkivet *"betrodda rotcertifikatutfärdare"* på den virtuella datorn.

Om skriptet finns på en lokal server kan du fortfarande behöva ytterligare portar för brandväggen och nätverkssäkerhetsgruppen måste öppnas.

### <a name="tips-and-tricks"></a>Tips

* Den högsta felfrekvensen för det här tillägget beror på syntaxfel i skriptet, test av skriptet körs utan fel och ytterligare loggning i skriptet för att göra det enklare att hitta var det misslyckades.
* Skriv skript som är idempotenta. Detta säkerställer att systemändringar inte orsakar om de körs igen av misstag.
* Se till att skripten inte kräver indata från användaren när de körs.
* Skripten kan köra i 90 minuter. Längre körningar gör att etableringen av tillägget misslyckas.
* Lägg inte in omstarter i skriptet eftersom det leder till problem med andra tillägg som installeras. Tillägget fortsätter inte efter omstarten.
* Om du har ett skript som orsakar en omstart, installerar program och kör skript kan du schemalägga omstarten med hjälp av en schemalagd uppgift i Windows eller använda verktyg som DSC-, Chef- eller Puppet-tillägg.
* Vi rekommenderar inte att du kör ett skript som orsakar ett stopp eller en uppdatering av VM-agenten. Detta kan lämna tillägget i övergångstillstånd, vilket leder till en tidsgräns.
* Tillägget kör bara ett skript en gång. Om du vill köra ett skript vid varje start måste du använda tillägget för att skapa en schemalagd uppgift i Windows.
* Om du vill schemalägga när ett skript ska köras använder du tillägget för att skapa en schemalagd uppgift i Windows.
* När skriptet körs visas tillägget med övergångsstatus på Azure-portalen eller i CLI. Om du behöver mer frekventa statusuppdateringar för ett skript som körs måste du skapa en egen lösning.
* Tillägget anpassat skript har inte inbyggt stöd för proxyservrar, men du kan använda ett filöverföringsverktyg som stöder proxyservrar i skriptet, till exempel *Invoke-WebRequest*
* Om dina skript eller kommandon använder andra katalogplatser än standardplatserna krävs logik som kan hantera den situationen.
* Det anpassade skripttillägget körs under LocalSystem-kontot
* Om du planerar att använda *egenskaperna storageAccountName* och *storageAccountKey* måste dessa egenskaper vara samplacerade i *protectedSettings*.

## <a name="extension-schema"></a>Tilläggsschema

Konfigurationen för det anpassade skripttillägget anger saker som skriptplats och kommandot som ska köras. Du kan lagra den här konfigurationen i konfigurationsfiler, ange den på kommandoraden eller ange den i en Azure Resource Manager mall.

Du kan lagra känsliga data i en skyddad konfiguration som är krypterad och endast dekrypterad på den virtuella datorn. Den skyddade konfigurationen är användbar när körningskommandot innehåller hemligheter, till exempel ett lösenord eller en filreferens för signatur för delad åtkomst (SAS), som ska skyddas.

Dessa objekt ska behandlas som känsliga data och anges i konfigurationen för inställningen Tilläggsskyddad. Skyddade inställningsdata för Azure VM-tillägg krypteras och dekrypteras endast på den virtuella måldatorn.

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "virtualMachineName/config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
        "[variables('musicstoresqlName')]"
    ],
    "tags": {
        "displayName": "config-app"
    },
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.10",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {},
            "fileUris": [
                "script location"
            ]
        }
    }
}
```

> [!NOTE]
> managedIdentity-egenskapen **får** inte användas tillsammans med storageAccountName- eller storageAccountKey-egenskaper

> [!NOTE]
> Endast en version av ett tillägg kan installeras på en virtuell dator vid en tidpunkt. Det går inte att ange anpassade skript två gånger i samma Resource Manager-mall för samma virtuella dator.

> [!NOTE]
> Vi kan använda det här schemat i VirtualMachine-resursen eller som en fristående resurs. Namnet på resursen måste ha formatet "virtualMachineName/extensionName", om det här tillägget används som en fristående resurs i ARM-mallen.

### <a name="property-values"></a>Egenskapsvärden

| Name | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| utgivare | Microsoft.Compute | sträng |
| typ | CustomScriptExtension | sträng |
| typeHandlerVersion | 1,10 | int |
| fileUris (t.ex. ) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matris |
| tidsstämpel (t.ex.) | 123456789 | 32-bitars heltal |
| commandToExecute (t.ex.) | powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 | sträng |
| storageAccountName (t.ex.) | examplestorageacct | sträng |
| storageAccountKey (t.ex.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQftrappQj8hg== | sträng |
| managedIdentity (t.ex.) | { } eller { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" } eller { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" } | json-objekt |

>[!NOTE]
>Dessa egenskapsnamn är fallkänsliga. Undvik distributionsproblem genom att använda namnen som visas här.

#### <a name="property-value-details"></a>Information om egenskapsvärde

* `commandToExecute`: (**obligatorisk**, sträng) startpunktsskriptet som ska köras. Använd det här fältet i stället om kommandot innehåller hemligheter, till exempel lösenord, eller om dina fileUris är känsliga.
* `fileUris`: (valfritt, strängmatris) URL:erna för de filer som ska laddas ned. Om URL:er är känsliga (till exempel URL:er som innehåller nycklar) ska det här fältet anges i protectedSettings
* `timestamp` (valfritt, 32-bitars heltal) använd endast det här fältet för att utlösa en omkörning av skriptet genom att ändra värdet för det här fältet.  Alla heltalsvärden är godtagbara. Det får bara vara ett annat värde än det tidigare värdet.
* `storageAccountName`: (valfritt, sträng) namnet på lagringskontot. Om du anger autentiseringsuppgifter för lagring `fileUris` måste alla vara URL:er för Azure Blobs.
* `storageAccountKey`: (valfritt, sträng) åtkomstnyckeln för lagringskontot
* `managedIdentity`: (valfritt, json-objekt) den [hanterade](../../active-directory/managed-identities-azure-resources/overview.md) identiteten för nedladdning av filer
  * `clientId`: (valfritt, sträng) klient-ID för den hanterade identiteten
  * `objectId`: (valfritt, sträng) objekt-ID för den hanterade identiteten

Följande värden kan anges i antingen offentliga eller skyddade inställningar. Tillägget avvisar alla konfigurationer där värdena nedan anges i både offentliga och skyddade inställningar.

* `commandToExecute`
* `fileUris`

Offentliga inställningar kan vara användbara för felsökning, men vi rekommenderar att du använder skyddade inställningar.

Offentliga inställningar skickas i klartext till den virtuella dator där skriptet ska köras.  Skyddade inställningar krypteras med en nyckel som endast är känd för Azure och den virtuella datorn. Inställningarna sparas på den virtuella datorn när de skickades, det vill säga om inställningarna har krypterats så sparas de krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn och används för att dekryptera inställningar (om det behövs) vid körning.

####  <a name="property-managedidentity"></a>Egenskap: managedIdentity
> [!NOTE]
> Den här **egenskapen** får endast anges i skyddade inställningar.

CustomScript (version 1.10 och [](../../active-directory/managed-identities-azure-resources/overview.md) senare) stöder hanterad identitet för nedladdning av filer från URL:er som anges i inställningen "fileUris". Det gör att CustomScript kan komma Azure Storage privata blobar eller containrar utan att användaren behöver skicka hemligheter som SAS-token eller lagringskontonycklar.

Om du vill använda den här funktionen [](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) måste användaren lägga till en [systemtilldelade](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) eller användartilldelade identitet till den virtuella datorn eller VMSS där CustomScript förväntas köras och ge den hanterade identiteten åtkomst till [Azure Storage-containern eller bloben](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Om du vill använda den system tilldelade identiteten på den virtuella måldatorn/VMSS anger du fältet "managedidentity" till ett tomt json-objekt. 

> Exempel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Om du vill använda den användar tilldelade identiteten på den virtuella måldatorn/VMSS konfigurerar du fältet "managedidentity" med klient-ID:t eller objekt-ID:t för den hanterade identiteten.

> Exempel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> managedIdentity-egenskapen **får** inte användas tillsammans med storageAccountName- eller storageAccountKey-egenskaper

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. JSON-schemat, som beskrivs i föregående avsnitt, kan användas i en Azure Resource Manager för att köra tillägget för anpassat skript under distributionen. Följande exempel visar hur du använder tillägget för anpassat skript:

* [Självstudie: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Distribuera tvånivåprogram i Windows och Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell-distribution

Kommandot `Set-AzVMCustomScriptExtension` kan användas för att lägga till tillägget för anpassat skript till en befintlig virtuell dator. Mer information finns i [Set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName <resourceGroupName> `
    -VMName <vmName> `
    -Location myLocation `
    -FileUri <fileUrl> `
    -Run 'myScript.ps1' `
    -Name DemoScriptExtension
```

## <a name="additional-examples"></a>Ytterligare exempel

### <a name="using-multiple-scripts"></a>Använda flera skript

I det här exemplet har du tre skript som används för att skapa servern. **CommandToExecute anropar** det första skriptet och sedan har du alternativ för hur de andra anropas. Du kan till exempel ha ett huvudskript som styr körningen, med rätt felhantering, loggning och tillståndshantering. Skripten laddas ned till den lokala datorn för körning. I anropar du till exempel genom att lägga till i skriptet och upprepar `1_Add_Tools.ps1` den här processen för de andra `2_Add_Features.ps1`  `.\2_Add_Features.ps1` skripten som du definierar i `$settings` .

```powershell
$fileUri = @("https://xxxxxxx.blob.core.windows.net/buildServer1/1_Add_Tools.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/2_Add_Features.ps1",
"https://xxxxxxx.blob.core.windows.net/buildServer1/3_CompleteInstall.ps1")

$settings = @{"fileUris" = $fileUri};

$storageAcctName = "xxxxxxx"
$storageKey = "1234ABCD"
$protectedSettings = @{"storageAccountName" = $storageAcctName; "storageAccountKey" = $storageKey; "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File 1_Add_Tools.ps1"};

#run command
Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "buildserver1" `
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -Settings $settings `
    -ProtectedSettings $protectedSettings;
```

### <a name="running-scripts-from-a-local-share"></a>Köra skript från en lokal resurs

I det här exemplet kanske du vill använda en lokal SMB-server för din skriptplats. På så sätt behöver du inte ange några andra inställningar, förutom **commandToExecute**.

```powershell
$protectedSettings = @{"commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File \\filesvr\build\serverUpdate1.ps1"};

Set-AzVMExtension -ResourceGroupName <resourceGroupName> `
    -Location <locationName> `
    -VMName <vmName> `
    -Name "serverUpdate"
    -Publisher "Microsoft.Compute" `
    -ExtensionType "CustomScriptExtension" `
    -TypeHandlerVersion "1.10" `
    -ProtectedSettings $protectedSettings

```

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Köra anpassade skript mer än en gång med CLI

Om du vill köra det anpassade skripttillägget mer än en gång kan du bara göra den här åtgärden under följande villkor:

* Parametern **extension Name** är samma som den tidigare distributionen av tillägget.
* Uppdatera konfigurationen, annars körs inte kommandot igen. Du kan lägga till en dynamisk egenskap i kommandot, till exempel en tidsstämpel.

Du kan också ange [forceUpdateTag-egenskapen](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) till **true**.

### <a name="using-invoke-webrequest"></a>Använda Invoke-WebRequest

Om du använder [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) i skriptet måste du ange parametern . Annars får du följande felmeddelande `-UseBasicParsing` när du kontrollerar den detaljerade statusen:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Information om hur du distribuerar det anpassade skripttillägget på en [skalningsuppsättning finns i Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)

## <a name="classic-vms"></a>Klassiska virtuella datorer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Om du vill distribuera tillägget för anpassat skript på klassiska virtuella datorer kan du använda Azure Portal eller klassiska Azure PowerShell cmdlets.

### <a name="azure-portal"></a>Azure Portal

Gå till den klassiska VM-resursen. Välj **Tillägg** under **Inställningar.**

Klicka **på + Lägg** till och välj Anpassat skripttillägg i listan över **resurser.**

På sidan **Installera tillägg** väljer du den lokala PowerShell-filen och fyller i eventuella argument och klickar på **OK.**

### <a name="powershell"></a>PowerShell

Använd [cmdleten Set-AzureVMCustomScriptExtension](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) för att lägga till tillägget för anpassat skript till en befintlig virtuell dator.

```powershell
# define your file URI
$fileUri = 'https://xxxxxxx.blob.core.windows.net/scripts/Create-File.ps1'

# create vm object
$vm = Get-AzureVM -Name <vmName> -ServiceName <cloudServiceName>

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri $fileUri -Run 'Create-File.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure Portal och med hjälp av Azure PowerShell modulen. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Utdata från tillägget loggas till filer som finns under följande mapp på den virtuella måldatorn.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De angivna filerna laddas ned till följande mapp på den virtuella måldatorn.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

där `<n>` är ett decimal heltal, som kan ändras mellan körningar av tillägget.  Värdet `1.*` matchar det faktiska, aktuella värdet för `typeHandlerVersion` tillägget.  Den faktiska katalogen kan till exempel vara `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` .  

När du kör kommandot anger tillägget den här katalogen (till exempel `commandToExecute` ) som den aktuella `...\Downloads\2` arbetskatalogen. Den här processen gör det möjligt att använda relativa sökvägar för att hitta de filer som laddats ned via `fileURIs` egenskapen . Exempel finns i tabellen nedan.

Eftersom den absoluta nedladdningssökvägen kan variera över tid är det bättre att välja relativa skript-/filsökvägar i `commandToExecute` strängen när det är möjligt. Exempel:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Sökvägsinformation efter det första URI-segmentet behålls för filer som laddas ned via `fileUris` egenskapslistan.  Som du ser i tabellen nedan mappas nedladdade filer till underkataloger för att återspegla värdenas `fileUris` struktur.  

#### <a name="examples-of-downloaded-files"></a>Exempel på nedladdade filer

| URI i fileUris | Relativ nedladdad plats | Absolut nedladdad plats <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> De absoluta katalogsökvägarna ändras under den virtuella datorns livslängd, men inte inom en enda körning av CustomScript-tillägget.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på [MSDN Azure](https://azure.microsoft.com/support/forums/)och Stack Overflow forumen . Du kan också skapa en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Få support. Information om hur du använder Azure Support finns i vanliga Microsoft Azure [vanliga frågor och svar om support.](https://azure.microsoft.com/support/faq/)
