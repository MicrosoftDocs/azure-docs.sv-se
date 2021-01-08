---
title: Anpassat skript tillägg för Azure för Windows
description: Automatisera konfigurations uppgifter för virtuella Windows-datorer med hjälp av tillägget för anpassat skript
services: virtual-machines-windows
manager: carmonm
author: bobbytreed
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/31/2020
ms.author: robreed
ms.openlocfilehash: b0502fb05043a54d81d768a7809d19b108cc6248
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976851"
---
# <a name="custom-script-extension-for-windows"></a>Anpassat skripttillägg för Windows

Det anpassade skript tillägget laddar ned och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration av distribution, program varu installation eller andra konfigurations-eller hanterings uppgifter. Skripten kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning. Det anpassade skript tillägget integreras med Azure Resource Manager mallar och kan köras med hjälp av Azure CLI, PowerShell, Azure Portal eller den virtuella Azure-datorn REST API.

Det här dokumentet beskriver hur du använder tillägget för anpassat skript med hjälp av Azure PowerShell-modulen, Azure Resource Manager mallar och information om fel söknings steg i Windows-system.

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]  
> Använd inte anpassat skript tillägg för att köra Update-AzVM med samma virtuella dator som parameter, eftersom det väntar på sig själv.  

### <a name="operating-system"></a>Operativsystem

Tillägget för anpassat skript för Windows kommer att köras i tillägget tillägg som stöds OSs.
### <a name="windows"></a>Windows

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows 10
* Windows Server 2016
* Windows Server 2016 Core
* Windows Server 2019
* Windows Server 2019 Core

### <a name="script-location"></a>Skript plats

Du kan konfigurera tillägget så att det använder Azure Blob Storage-autentiseringsuppgifterna för att få åtkomst till Azure Blob Storage. Skript platsen kan vara valfri, så länge den virtuella datorn kan dirigeras till den slut punkten, till exempel GitHub eller en intern fil server.

### <a name="internet-connectivity"></a>Internet anslutning

Om du behöver hämta ett skript externt, till exempel från GitHub eller Azure Storage, måste du öppna ytterligare brand Väggs-och nätverks säkerhets grupps portar. Om ditt skript till exempel finns i Azure Storage kan du tillåta åtkomst med hjälp av Azure NSG service-taggar för [lagring](../../virtual-network/network-security-groups-overview.md#service-tags).

Observera att CustomScript-tillägget inte har något sätt att kringgå certifikat verifieringen. Så om du laddar ned från en säker plats med t. ex. ett självsignerat certifikat kan du sluta med fel som *"Fjärrcertifikatet är ogiltigt enligt validerings proceduren"*. Kontrol lera att certifikatet är korrekt installerat i arkivet *"betrodda rot certifikat utfärdare"* på den virtuella datorn.

Om ditt skript finns på en lokal server kanske du fortfarande behöver fler brand väggar och nätverks säkerhets grupps portar måste öppnas.

### <a name="tips-and-tricks"></a>Tips

* Den högsta fel frekvensen för det här tillägget beror på syntaxfel i skriptet, testa skriptet körs utan fel och Lägg även till ytterligare loggning i skriptet så att det blir lättare att hitta det som misslyckades.
* Skriv skript som är idempotenta. Detta säkerställer att om de körs igen av misstag, kommer de inte att orsaka system ändringar.
* Se till att skripten inte kräver indata från användaren när de körs.
* Skripten kan köra i 90 minuter. Längre körningar gör att etableringen av tillägget misslyckas.
* Lägg inte in omstarter i skriptet eftersom det leder till problem med andra tillägg som installeras. Tillägget fortsätter inte efter omstarten.
* Om du har ett skript som gör en omstart, sedan installerar program och kör skript, kan du schemalägga omstarten med en schemalagd aktivitet i Windows eller använda verktyg som DSC, chef eller Puppet-tillägg.
* Vi rekommenderar inte att du kör ett skript som gör att VM-agenten stoppas eller uppdateras. Detta kan lämna tillägget i ett över gångs tillstånd, vilket leder till en tids gräns.
* Tillägget kör bara ett skript en gång. Om du vill köra ett skript vid varje start måste du använda tillägget för att skapa en schemalagd uppgift i Windows.
* Om du vill schemalägga när ett skript ska köras använder du tillägget för att skapa en schemalagd uppgift i Windows.
* När skriptet körs visas tillägget med övergångsstatus på Azure-portalen eller i CLI. Om du behöver mer frekventa statusuppdateringar för ett skript som körs måste du skapa en egen lösning.
* Anpassat skript tillägg stöder inte proxyservrar, men du kan använda ett fil överförings verktyg som stöder proxyservrar i skriptet, till exempel *vändning*
* Om dina skript eller kommandon använder andra katalogplatser än standardplatserna krävs logik som kan hantera den situationen.
* Anpassat skript tillägg körs under kontot LocalSystem
* Om du planerar att använda egenskaperna *storageAccountName* och *storageAccountKey* måste dessa egenskaper vara samordnad i *protectedSettings*.

## <a name="extension-schema"></a>Tilläggsschema

Konfigurationen för det anpassade skript tillägget anger saker som skript plats och kommandot som ska köras. Du kan lagra konfigurationen i konfigurationsfiler, ange den på kommando raden eller ange den i en Azure Resource Manager mall.

Du kan lagra känsliga data i en skyddad konfiguration, som krypteras och bara dekrypteras i den virtuella datorn. Den skyddade konfigurationen är användbar när körnings kommandot innehåller hemligheter som lösen ord.

De här objekten ska behandlas som känsliga data och anges i konfigurationerna för tilläggen för skyddad inställning. Skyddade inställnings data för Azure VM-tillägg krypteras och endast dekrypteras på den virtuella mål datorn.

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
            "fileUris": [
                "script location"
            ],
            "timestamp":123456789
        },
        "protectedSettings": {
            "commandToExecute": "myExecutionCommand",
            "storageAccountName": "myStorageAccountName",
            "storageAccountKey": "myStorageAccountKey",
            "managedIdentity" : {}
        }
    }
}
```

> [!NOTE]
> Egenskapen managedIdentity **får inte** användas tillsammans med storageAccountName-eller storageAccountKey-egenskaper

> [!NOTE]
> Endast en version av ett tillägg kan installeras på en virtuell dator vid en viss tidpunkt, vilket innebär att det inte går att ange ett anpassat skript två gånger i samma Resource Manager-mall för samma virtuella dator.

> [!NOTE]
> Vi kan använda det här schemat i VirtualMachine-resursen eller som en fristående resurs. Namnet på resursen måste ha formatet "virtualMachineName/tillägg", om tillägget används som en fristående resurs i ARM-mallen.

### <a name="property-values"></a>Egenskaps värden

| Namn | Värde/exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| utgivare | Microsoft.Compute | sträng |
| typ | CustomScriptExtension | sträng |
| typeHandlerVersion | 1,10 | int |
| fileUris (t. ex.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 | matris |
| tidsstämpel (t. ex.) | 123456789 | 32-bitars heltal |
| commandToExecute (t. ex.) | PowerShell – ExecutionPolicy obegränsade-File configure-music-app.ps1 | sträng |
| storageAccountName (t. ex.) | examplestorageacct | sträng |
| storageAccountKey (t. ex.) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | sträng |
| managedIdentity (t. ex.) | {} eller {"clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232"} eller {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | JSON-objekt |

>[!NOTE]
>Dessa egenskaps namn är Skift läges känsliga. Använd de namn som visas här för att undvika distributions problem.

#### <a name="property-value-details"></a>Information om egenskaps värde

* `commandToExecute`: (**krävs**, sträng) Start punkt skriptet som ska köras. Använd det här fältet i stället om kommandot innehåller hemligheter som lösen ord, eller om dina fileUris är känsliga.
* `fileUris`: (valfritt, sträng mat ris) URL: er för fil (er) som ska hämtas.
* `timestamp` (valfritt, 32-bitars heltal) Använd endast det här fältet för att utlösa en körning av skriptet genom att ändra värdet för det här fältet.  Alla heltals värden är acceptabla. Det får bara vara ett annat än det tidigare värdet.
* `storageAccountName`: (valfritt, sträng) namnet på lagrings kontot. Om du anger autentiseringsuppgifter `fileUris` för lagring måste alla vara URL: er för Azure-blobar.
* `storageAccountKey`: (valfritt, sträng) åtkomst nyckeln för lagrings kontot
* `managedIdentity`: (valfritt, JSON-objekt) den [hanterade identiteten](../../active-directory/managed-identities-azure-resources/overview.md) för nedladdning av fil (er)
  * `clientId`: (valfritt, sträng) klient-ID: t för den hanterade identiteten
  * `objectId`: (valfritt, sträng) objekt-ID för den hanterade identiteten

Följande värden kan anges i offentliga eller skyddade inställningar. tillägget kommer att neka alla konfigurationer där värdena nedan anges i både offentliga och skyddade inställningar.

* `commandToExecute`

Användning av offentliga inställningar kan vara användbart för fel sökning, men vi rekommenderar att du använder skyddade inställningar.

Offentliga inställningar skickas i klartext till den virtuella dator där skriptet ska köras.  Skyddade inställningar krypteras med en nyckel som endast är känd för Azure och den virtuella datorn. Inställningarna sparas på den virtuella datorn när de skickades, det vill säga om inställningarna har krypterats som de har sparats krypterade på den virtuella datorn. Certifikatet som används för att dekryptera de krypterade värdena lagras på den virtuella datorn och används för att dekryptera inställningar (vid behov) vid körning.

####  <a name="property-managedidentity"></a>Egenskap: managedIdentity
> [!NOTE]
> Den här egenskapen **måste** endast anges i skyddade inställningar.

CustomScript (version 1,10 och senare) stöder [hanterad identitet](../../active-directory/managed-identities-azure-resources/overview.md) för hämtning av filer från URL: er som finns i inställningen "fileUris". Det ger CustomScript åtkomst till Azure Storage privata blobbar eller behållare utan att användaren måste skicka hemligheter som SAS-token eller lagrings konto nycklar.

Om du vill använda den här funktionen måste användaren lägga till en [tilldelad](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) eller [användardefinierad](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) identitet till den virtuella datorn eller VMSS där CustomScript förväntas köras, och [ge hanterad identitets åtkomst till Azure Storage containern eller blobben](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Om du vill använda den systemtilldelade identiteten på den virtuella mål datorn/VMSS anger du fältet managedidentity till ett tomt JSON-objekt. 

> Exempel:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.ps1"],
>   "commandToExecute": "powershell.exe script1.ps1",
>   "managedIdentity" : {}
> }
> ```

Om du vill använda den användardefinierade identiteten på den virtuella mål datorn/VMSS konfigurerar du fältet managedidentity med klient-ID: t eller objekt-ID: t för den hanterade identiteten.

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
> Egenskapen managedIdentity **får inte** användas tillsammans med storageAccountName-eller storageAccountKey-egenskaper

## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager mallar. JSON-schemat, som beskrivs i föregående avsnitt, kan användas i en Azure Resource Manager mall för att köra det anpassade skript tillägget under distributionen. Följande exempel visar hur du använder tillägget för anpassat skript:

* [Självstudie: Distribuera tillägg för virtuell dator med Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-tutorial-deploy-vm-extensions.md)
* [Distribuera program på två nivåer i Windows och Azure SQL DB](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows)

## <a name="powershell-deployment"></a>PowerShell-distribution

`Set-AzVMCustomScriptExtension`Kommandot kan användas för att lägga till det anpassade skript tillägget till en befintlig virtuell dator. Mer information finns i [set-AzVMCustomScriptExtension](/powershell/module/az.compute/set-azvmcustomscriptextension).

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

I det här exemplet har du tre skript som används för att bygga servern. **CommandToExecute** anropar det första skriptet och du har också alternativ för hur andra anropas. Du kan till exempel ha ett huvud skript som styr körningen, med rätt fel hantering, loggning och tillstånds hantering. Skripten laddas ned till den lokala datorn för att köra. `1_Add_Tools.ps1`Du skulle till exempel kunna anropa `2_Add_Features.ps1` genom att lägga till `.\2_Add_Features.ps1` i skriptet och upprepa processen för de andra skript som du definierar i `$settings` .

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
    -Settings $settings    `
    -ProtectedSettings $protectedSettings `
```

### <a name="running-scripts-from-a-local-share"></a>Köra skript från en lokal resurs

I det här exemplet kanske du vill använda en lokal SMB-server för din skript plats. Genom att göra detta behöver du inte ange några andra inställningar, förutom **commandToExecute**.

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

### <a name="how-to-run-custom-script-more-than-once-with-cli"></a>Så här kör du ett anpassat skript mer än en gång med CLI

Om du vill köra det anpassade skript tillägget mer än en gång kan du bara utföra den här åtgärden under följande omständigheter:

* Tilläggs parameterns **namn** är samma som den tidigare distributionen av tillägget.
* Uppdatera konfigurationen annars körs inte kommandot igen. Du kan lägga till i en dynamisk egenskap i kommandot, till exempel en tidsstämpel.

Du kan också ange egenskapen [ForceUpdateTag](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension.forceupdatetag) till **True**.

### <a name="using-invoke-webrequest"></a>Använda Invoke-WebRequest

Om du använder [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) i skriptet måste du ange parametern, `-UseBasicParsing` annars visas följande fel meddelande när du kontrollerar den detaljerade statusen:

```error
The response content cannot be parsed because the Internet Explorer engine is not available, or Internet Explorer's first-launch configuration is not complete. Specify the UseBasicParsing parameter and try again.
```
## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Om du vill distribuera tillägget för anpassat skript i en skalnings uppsättning, se [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension?view=azps-3.3.0)

## <a name="classic-vms"></a>Klassiska virtuella datorer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Om du vill distribuera tillägget för anpassat skript på klassiska virtuella datorer kan du använda Azure Portal eller de klassiska Azure PowerShell-cmdletarna.

### <a name="azure-portal"></a>Azure Portal

Navigera till den klassiska VM-resursen. Välj **tillägg** under **Inställningar**.

Klicka på **+ Lägg till** och i listan över resurser väljer du **anpassat skript tillägg**.

På sidan **installations tillägg** väljer du den lokala PowerShell-filen och fyller i eventuella argument och klickar på **OK**.

### <a name="powershell"></a>PowerShell

Använd [set-AzureVMCustomScriptExtension-](/powershell/module/servicemanagement/azure.service/set-azurevmcustomscriptextension) cmdleten kan användas för att lägga till det anpassade skript tillägget till en befintlig virtuell dator.

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

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av modulen Azure PowerShell. Kör följande kommando för att se distributions status för tillägg för en virtuell dator:

```powershell
Get-AzVMExtension -ResourceGroupName <resourceGroupName> -VMName <vmName> -Name myExtensionName
```

Utökning av utdata loggas till filer som finns i följande mapp på den virtuella mål datorn.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

De angivna filerna laddas ned till följande mapp på den virtuella mål datorn.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads\<n>
```

där `<n>` är ett decimal tal som kan ändras mellan körningar av tillägget.  `1.*`Värdet matchar det faktiska, aktuella `typeHandlerVersion` värdet för tillägget.  Den faktiska katalogen kan till exempel vara `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2` .  

När `commandToExecute` kommandot körs anger tillägget den här katalogen (till exempel `...\Downloads\2` ) som den aktuella arbets katalogen. Den här processen gör det möjligt att använda relativa sökvägar för att hitta filerna som hämtats via `fileURIs` egenskapen. Se tabellen nedan för exempel.

Eftersom den absoluta nedladdnings Sök vägen kan variera med tiden är det bättre att välja relativa skript-och fil Sök vägar i `commandToExecute` strängen, närhelst det är möjligt. Ett exempel:

```json
"commandToExecute": "powershell.exe . . . -File \"./scripts/myscript.ps1\""
```

Sök vägs information efter det första URI-segmentet behålls för filer som hämtats via `fileUris` egenskaps listan.  Som visas i tabellen nedan mappas hämtade filer till under kataloger med hämtning för att avspegla `fileUris` värdenanas struktur.  

#### <a name="examples-of-downloaded-files"></a>Exempel på hämtade filer

| URI i fileUris | Relativ nedladdnings plats | Absolut Hämtad plats <sup>1</sup> |
| ---- | ------- |:--- |
| `https://someAcct.blob.core.windows.net/aContainer/scripts/myscript.ps1` | `./scripts/myscript.ps1` |`C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\scripts\myscript.ps1`  |
| `https://someAcct.blob.core.windows.net/aContainer/topLevel.ps1` | `./topLevel.ps1` | `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.8\Downloads\2\topLevel.ps1` |

<sup>1</sup> de absoluta katalog Sök vägarna ändras under den virtuella datorns livstid, men inte inom en enskild körning av CustomScript-tillägget.

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
