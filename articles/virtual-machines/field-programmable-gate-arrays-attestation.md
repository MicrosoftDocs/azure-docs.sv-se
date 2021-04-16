---
title: Azure FPGFA Attestation Service
description: Attestationstjänst för virtuella datorer i NP-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: ab9c9c6b9d908e86912565ba43cec665432aeda5
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389629"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>FPGA-attestation för virtuella Azure NP-Series-datorer (förhandsversion)

FPGA-attestationstjänsten utför en serie verifieringar på en designkontrollpunktsfil (kallas "netlist") som genereras av Xilinx-verktygsuppsättningen och skapar en fil som innehåller den verifierade avbildningen (kallas "bitström") som kan läsas in på Xilinx U250 FPGA-kortet i en virtuell NP-serie.  

## <a name="prerequisites"></a>Förutsättningar  

Du behöver en Azure-prenumeration och ett Azure Storage konto. Prenumerationen ger dig åtkomst till Azure och lagringskontot används för att lagra dina nätliste- och utdatafiler för attestationstjänsten.  

Vi tillhandahåller PowerShell- och Bash-skript för att skicka attestationsbegäranden.   Skripten använder Azure CLI, som kan köras i Windows och Linux. PowerShell kan köras i Windows, Linux och macOS.  

Azure CLI-nedladdning (krävs):  

https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest  

Nedladdning av PowerShell för Windows, Linux och macOS (endast för PowerShell-skript):  

https://docs.microsoft.com/powershell/scripting/install/installing-powershell?view=powershell-7  

Du måste ha din klientorganisation och ditt prenumerations-ID auktoriserade att skicka till attestationstjänsten. Besök https://aka.ms/AzureFPGAAttestationPreview för att begära åtkomst. 

## <a name="building-your-design-for-attestation"></a>Skapa din design för atterering  

Den föredragna Xilinx-verktygsuppsättningen för att skapa design är Vitis 2020.2. Netlist-filer som har skapats med en tidigare version av verktygsuppsättningen och som fortfarande är kompatibla med 2020.2 kan användas. Kontrollera att du har läst in rätt gränssnitt att bygga mot. Den version som stöds för närvarande xilinx_u250_gen3x16_xdma_2_1_202010_1. Du kan ladda ned supportfilerna från Xilinx Alveo- eller Alveo-filen. 

Du måste inkludera följande argument till Vitis (v++-cmd-raden) för att skapa en xclbin-fil som innehåller en netlist i stället för en bitstream.   

```--advanced.param compiler.acceleratorBinaryContent=dcp  ```

## <a name="logging-into-azure"></a>Logga in på Azure  

Innan du utför några åtgärder med Azure måste du logga in på Azure och ange den prenumeration som har behörighet att anropa tjänsten. Använd ```az login``` kommandona ```az account set –s <Sub ID or Name>``` och för detta ändamål. Mer information om den här processen finns dokumenterat här:  

https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest. Använd antingen alternativet "logga in interaktivt" eller "logga in med autentiseringsuppgifter" på kommandoraden.  

## <a name="creating-a-storage-account-and-blob-container"></a>Skapa ett lagringskonto och en blobcontainer  

Din nätlistefil måste laddas upp till en Azure Storage Blob-container för åtkomst av attestationstjänsten.  

På den här sidan finns mer information om hur du skapar kontot, en container och laddar upp din nätlista som en blob till den containern: [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](/azure/storage/blobs/storage-quickstart-blobs-cli) .  

Du kan också använda Azure Portal för detta.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Ladda upp din netlist-fil till Azure Blob Storage  

Det finns flera sätt att kopiera filen: ett exempel som använder cmdleten az storage upload visas nedan. Az-kommandona körs på både Linux och Windows. Du kan välja ett namn för "blob"-namnet, men se till att behålla xclbin-tillägget. 

```az storage blob upload --account-name <storage account to receive netlist> container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>Ladda ned attestationsskripten  

Valideringsskripten kan laddas ned från följande Azure Storage Blob-container:  

https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip  

ZIP-filen har två PowerShell-skript, ett att skicka och det andra som ska övervakas, medan den tredje filen är ett bash-skript som utför båda funktionerna.  

## <a name="running-the-attestation-scripts"></a>Köra attestationsskripten  

Om du vill köra skripten måste du ange namnet på ditt lagringskonto, namnet på den blobcontainer där filen netlist lagras och namnet på netlist-filen. Du måste också skapa en signatur för delad åtkomst (SAS) för tjänsten som ger läs-/skrivåtkomst till din container (inte netlist). Denna SAS används av attestationstjänsten för att göra en lokal kopia av din netlist-fil och för att skriva tillbaka de resulterande utdatafilerna för verifieringsprocessen till containern.  

En översikt över signaturer för delad åtkomst finns här med specifik information om tjänst-SAS som finns här. Sidan Tjänst-SAS innehåller en viktig varning om att skydda den genererade SAS:en.  Läs varningen för att förstå behovet av att skydda SAS från skadlig eller oavsiktlig användning.  

Du kan generera en SAS för din container med hjälp av cmdleten az storage container generate-sas. Ange en förfallotid i UTC-format som är minst ett par timmar efter sändningstiden. cirka 6 timmar bör vara mer än tillräckligt.  

Om du vill använda virtuella kataloger måste du inkludera kataloghierarkin som en del av containerargumentet. Om du till exempel har en container med namnet "netlists" och har en virtuell katalog med namnet "image1" som innehåller netlist-bloben anger du "netlists/image1" som containernamn. Lägg till ytterligare katalognamn för att ange en djupare hierarki. 

### <a name="powershell"></a>PowerShell   

```$sas=$(az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)  ```

```.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>  ```

### <a name="bash"></a>Bash  

``` sas=az storage container generate-sas --account-name <storage acct name> -name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  ```

```validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas ``` 

## <a name="checking-on-the-status-of-your-submission"></a>Kontrollera statusen för ditt bidrag  

Attestationstjänsten returnerar orkestrerings-ID:t för din överföring. Inskickningsskripten börjar automatiskt övervaka överföringen genom att avse slutförandet. Orkestrerings-ID:t är det huvudsakliga sättet för oss att granska vad som hände med din överföring, så behåll det om du har problem. Som referenspunkter tar det cirka 30 minuter att slutföra attestation för en liten netlist-fil (300 MB). en fil på 1,6 GB tog en timme. 

Du kan anropa Monitor-Validation.ps1 när som helst för att hämta status och resultat för attestation, och ange orkestrerings-ID:t som ett argument:  

```.\Monitor-Validation.ps1 -OrchestrationId < Orchestration ID>  ```

Du kan också skicka HTTP-postbegäran till attestationstjänstens slutpunkt:  

https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus  

Begärandetexten ska innehålla ditt prenumerations-ID, klientorganisations-ID och orkestrerings-ID för din atterstationsbegäran:  

```
{  

  "OrchestrationId": ”< orchestration ID>”,  

  "ClientSubscriptionId": “<your subscription ID>”,  

  "ClientTenantId": “<your tenant ID>”  

}
```

## <a name="post-validation-steps"></a>Steg efter validering

Tjänsten skriver sina utdata tillbaka till containern. Om verifieringen lyckas har containern den ursprungliga netlist-filen (abc.xclbin), en fil med bitstreamen (abc.bit.xclbin), en fil som identifierar den privata platsen för din lagrade bitström (abc.azure.xclbin) och fyra loggfiler: en för startprocessen (abc-log.txt) och en för de tre parallella faserna som utför verifieringen. Dessa heter *logPhaseX.txt där X är ett tal för fasen. Azure.xclbin används på den virtuella datorn för att signalera uppladdningen av din verifierade avbildning till U250. 

Om verifieringen misslyckades skrivs en error-*.txt-fil som anger vilket steg som misslyckades. Kontrollera också loggfilerna om felloggen indikerar att att attatetering misslyckades. När du kontaktar oss för support bör du inkludera alla dessa filer som en del av supportbegäran tillsammans med orkestrerings-ID:t.  

Du kan använda Azure Portal för att skapa din container samt ladda upp din netlist och ladda ned bitstream- och loggfilerna. Att skicka en attestationsbegäran och övervaka förloppet via portalen stöds inte för närvarande och måste göras via skript enligt beskrivningen ovan. 

