---
title: Azure FPGFA-attesterings tjänst
description: Attesterings tjänsten för de virtuella datorerna i NP-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 563155bb6559f8443f1453a65fa0b1574af106f7
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106556247"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>FPGA-attestering för Azure NP-Series VM (för hands version)

FPGA attesterings tjänst utför en serie valideringar på en design punkts fil (kallas "Netlist") som genereras av Xilinx-Programverktygen och skapar en fil som innehåller den verifierade avbildningen (kallas "Bitstream") som kan läsas in på Xilinx U250 FPGA-kortet i en virtuell dator med NP-serien.  

## <a name="prerequisites"></a>Förutsättningar  

Du behöver en Azure-prenumeration och ett Azure Storage-konto. Prenumerationen ger dig åtkomst till Azure och lagrings kontot används för att lagra din Netlist-och output-filer för attesteringsservern.  

Vi tillhandahåller PowerShell-och bash-skript för att skicka begäran om attestering.   Skripten använder Azure CLI, som kan köras på Windows och Linux. PowerShell kan köras på Windows, Linux och macOS.  

Hämtning av Azure CLI (krävs):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

PowerShell för Windows, Linux och macOS Download (endast för PowerShell-skript):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

Du måste ha din klient organisation och ditt prenumerations-ID som du har behörighet att skicka till attesterings tjänsten. Besök https://aka.ms/AzureFPGAAttestationPreview för att begära åtkomst. 

## <a name="building-your-design-for-attestation"></a>Skapa din design för attestering  

De önskade Xilinx-verktygen för att skapa ritningar är Vitis 2020,2. Netlist-filer som har skapats med en tidigare version av verktygs uppsättningen och är fortfarande kompatibla med 2020,2 kan användas. Kontrol lera att du har läst in rätt gränssnitt för att bygga mot. Den version som stöds för närvarande är xilinx_u250_gen3x16_xdma_2_1_202010_1. Stödfiler kan hämtas från Xilinx ALVEO-lounge. 

Du måste inkludera följande argument för Vitis (v + + cmd-raden) för att bygga en xclbin-fil som innehåller en Netlist i stället för en Bitstream.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Logga in på Azure  

Innan du utför några åtgärder med Azure måste du logga in på Azure och ange den prenumeration som har behörighet att anropa tjänsten. Använd- ```az login``` och- ```az account set –s <Sub ID or Name>``` kommandona för det här ändamålet. Mer information om den här processen beskrivs här:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Använd något av alternativen ' logga in interaktivt ' eller ' logga in med autentiseringsuppgifter ' på kommando raden.  

## <a name="creating-a-storage-account-and-blob-container"></a>Skapa ett lagrings konto och en BLOB-behållare  

Din Netlist-fil måste laddas upp till en Azure Storage BLOB-behållare för åtkomst av attesteringsservern.  

På den här sidan finns mer information om hur du skapar kontot, en behållare och laddar upp din Netlist som en blob till den behållaren: https://docs.microsoft.com/azure/storage/blobs/storage-quickstartblobs-cli .  

Du kan också använda Azure Portal för detta.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Ladda upp din Netlist-fil till Azure Blob Storage  

Det finns flera sätt att kopiera filen. ett exempel på hur du använder AZ Storage upload-cmdleten visas nedan. AZ-kommandona körs på både Linux och Windows. Du kan välja ett namn för "BLOB"-namnet men se till att behålla xclbin-tillägget. 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Hämta skript för attestering  

Verifierings skripten kan laddas ned från följande Azure Storage BLOB-behållare:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

Zip-filen har två PowerShell-skript, en för att skicka och den andra som ska övervakas medan den tredje filen är ett bash-skript som utför båda funktionerna.  

## <a name="running-the-attestation-scripts"></a>Köra skript för attestering  

Om du vill köra skripten måste du ange namnet på ditt lagrings konto, namnet på BLOB-behållaren där Netlist-filen lagras och namnet på Netlist-filen. Du måste också skapa en signatur för delad åtkomst för tjänsten (SAS) som ger Läs-/Skriv behörighet till din behållare (inte i Netlist). Denna SAS används av attesterings tjänsten för att göra en lokal kopia av din Netlist-fil och skriva tillbaka de resulterande utdatafilerna i validerings processen till din behållare.  

En översikt över signaturer för delad åtkomst finns här med detaljerad information om tjänste-SAS som finns här. Sidan för tjänstens SAS innehåller en viktig varning om hur du skyddar de genererade SAS: erna.  Läs varningen för att förstå behovet av att behålla SAS-skyddet från skadlig eller oavsiktlig användning.  

Du kan skapa en SAS för din behållare med hjälp av AZ Storage container generate-cmdlet. Ange en förfallo tid i UTC-format som är minst några timmar efter sändnings tillfället. omkring 6 timmar bör vara mer än lämpligt.  

Om du vill använda virtuella kataloger måste du inkludera katalogpartitionen som en del av container argumentet. Om du till exempel har en behållare med namnet "netlister" och har en virtuell katalog med namnet "image1" som innehåller Netlist-blobben anger du "netlists/image1" som behållar namn. Lägg till ytterligare katalog namn för att ange en djupare hierarki. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>Kontrollerar status för ditt bidrag  

Attesterings tjänsten returnerar Orchestration-ID: t för ditt bidrag. Sändnings skripten börjar automatiskt att övervaka överföringen genom att avsökningen har slutförts. Orchestration-ID: t är det främsta sättet för oss att se vad som hände med ditt bidrag, så se till att du har ett problem. Som referens punkter tar attesteringen cirka 30 minuter att slutföra för en liten Netlist-fil (300MB i storlek). en 1,6 GB-fil tog en timme. 

Du kan när som helst anropa Monitor-Validation.ps1-skriptet för att få status och resultat från attesteringen, vilket ger Orchestration-ID: t som ett argument:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

Alternativt kan du skicka HTTP POST-begäran till attesterings tjänstens slut punkt:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

Begär ande texten ska innehålla ditt prenumerations-ID, klient-ID och Orchestration-ID för din begäran om attestering:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Efter verifierings steg

Tjänsten kommer att skriva tillbaka utdata till din behållare. Om verifierings passet lyckas, din behållare kommer att ha den ursprungliga Netlist-filen (ABC. xclbin), en fil med Bitstream (ABC. bit. xclbin), en fil som identifierar den privata platsen för din lagrade Bitstream (ABC. Azure. xclbin) och fyra loggfiler: en för start processen (abc-log.txt) och en för de tre parallella faserna som utför verifieringen. De heter * logPhaseX.txt där X är ett tal för fasen. Azure. xclbin används på den virtuella datorn för att signalera överföringen av den validerade avbildningen till U250. 

Om valideringen Miss lyckas skrivs en error-*. txt-fil som anger vilket steg som misslyckades. Kontrol lera också loggfilerna om fel loggen indikerar att attesteringen misslyckades. När du kontaktar oss för support måste du se till att inkludera alla dessa filer som en del av support förfrågan tillsammans med Orchestration-ID: t.  

Du kan använda Azure Portal för att skapa din behållare och ladda upp din Netlist och ladda ned Bitstream-och loggfilerna. Att skicka en begäran om attestering och övervaka förloppet via portalen stöds inte för tillfället och måste göras genom skript enligt beskrivningen ovan. 

