---
title: Azure Image Builder-DevOps uppgift
description: Azure DevOps-uppgift för att mata in Bygg artefakter i en avbildning för virtuella datorer så att du kan installera och konfigurera ditt program och ditt operativ system.
author: danielsollondon
ms.author: danis
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: imaging
ms.openlocfilehash: 634fc183cc27db1ae949959c3ae7fae8eda5b644
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684550"
---
# <a name="azure-image-builder-service-devops-task"></a>Azure Image Builder-DevOps uppgift

Den här artikeln visar hur du använder en Azure DevOps-uppgift för att mata in Bygg artefakter i en VM-avbildning så att du kan installera och konfigurera ditt program och ditt operativ system.

## <a name="devops-task-versions"></a>DevOps uppgifts versioner
Det finns två Azure VM Image Builder-DevOps (AIB):

* ["Stabil" AIB-uppgift](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)gör det möjligt för oss att införa de senaste uppdateringarna och funktionerna, så att kunderna kan testa dem innan vi befordrar den till uppgiften "stabil", ungefär en vecka senare. 


* ["Instabil" AIB-uppgift](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary)gör det möjligt för oss att införa de senaste uppdateringarna och funktionerna, tillåta kunder att testa dem innan vi befordrar den till "stabil"-uppgiften. Om det inte finns några rapporterade problem, och vår telemetri visar att det inte finns några problem, ungefär en vecka senare, kommer vi att lansera uppgifts koden till "stabil". 

## <a name="prerequisites"></a>Förutsättningar

* Installera den [stabila DevOps-uppgiften från Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder).
* Du måste ha ett VSTS DevOps-konto och en build-pipeline som skapats
* Registrera och aktivera funktions kraven i Image Builder i prenumerationen som används av pipelinen:
    * [AZ PowerShell](../windows/image-builder-powershell.md#register-features)
    * [AZ CLI](../windows/image-builder.md#register-the-features)
    
* Skapa ett standard Azure Storage-konto i resurs gruppen käll avbildning kan du använda andra resurs grupper/lagrings konton. Lagrings kontot används för att överföra Bygg artefakterna från DevOps-aktiviteten till avbildningen.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Lägg till uppgift i versions pipeline

Välj **Frisläpp pipeline**-  >  **redigering**

Välj Lägg till på användar agenten och *+* Sök sedan efter **Image Builder**. Välj **Lägg till**.

Ange följande aktivitets egenskaper:

### <a name="azure-subscription"></a>Azure Subscription (Azure-prenumeration)

Välj i den nedrullningsbara menyn vilken prenumeration du vill att bild verktyget ska köras. Använd samma prenumeration där dina käll avbildningar finns och var bilderna ska distribueras. Du måste auktorisera Image Builder Contributor åtkomst till prenumerationen eller resurs gruppen.

### <a name="resource-group"></a>Resursgrupp

Använd resurs gruppen där den tillfälliga avbildnings mal len artefakt ska lagras. När du skapar en mall för en mall skapas ytterligare en tillfällig Image Builder-resurs grupp `IT_<DestinationResourceGroup>_<TemplateName>_guid` . Den tillfälliga resurs gruppen lagrar metadata för avbildningen, till exempel skript. I slutet av uppgiften tas avbildnings mallens artefakt och resurs grupp för tillfällig avbildnings byggare bort.
 
### <a name="location"></a>Plats

Platsen är den region där Image Builder ska köras. Endast ett angivet antal [regioner](../image-builder-overview.md#regions) stöds. Käll avbildningarna måste finnas på den här platsen. Om du till exempel använder delade avbildnings Galleri måste det finnas en replik i den regionen.

### <a name="managed-identity-required"></a>Hanterad identitet (krävs)
Image Builder kräver en hanterad identitet som används för att läsa anpassade käll bilder, ansluta till Azure Storage och skapa anpassade avbildningar. Mer information finns [här](../image-builder-overview.md#permissions).

### <a name="vnet-support"></a>VNET-stöd

För närvarande går det inte att ange ett befintligt undernät i DevOps-aktiviteten, men om du vill använda ett befintligt virtuellt nätverk kan du använda en ARM-mall med en bilds Builder-mall som är kapslad i. se Windows Image Builder-mallens exempel på hur detta uppnås, eller Använd [AZ AIB PowerShell](../windows/image-builder-powershell.md).

### <a name="source"></a>Källa

Käll avbildningarna måste vara av en Image Builder OSs-support. Du kan välja befintliga anpassade avbildningar i samma region som Image Builder körs från:
* Hanterad avbildning – du måste skicka in resourceId, till exempel:
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azures delade bild galleri – du måste skicka in avbildnings versionens resourceId, till exempel:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    Om du behöver hämta den senaste versionen av det delade avbildnings galleriet kan du ha en AZ PowerShell-eller AZ CLI-uppgift innan du får den senaste versionen och ställer in en DevOps-variabel. Använd variabeln i DevOps-aktiviteten AZ VM Image Builder. Mer information finns i [exemplen](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery).

* Marknaden Bas avbildning det finns en nedrullningsbar lista över populära bilder. dessa kommer alltid att använda den senaste versionen av de operativ system som stöds. 

    Om bas avbildningen inte finns i listan kan du ange den exakta avbildningen med hjälp av `Publisher:Offer:Sku` .

    Bas avbildnings version (valfritt) – du kan ange vilken version av avbildningen som du vill använda. standard är `latest` .

### <a name="customize"></a>Anpassa

#### <a name="provisioner"></a>Etablering

Två anpassningar stöds ursprungligen – **Shell** och **PowerShell**. Endast infogat stöds. Om du vill hämta skript kan du skicka infogade kommandon för att göra det.

För ditt operativ system väljer du PowerShell eller Shell.

#### <a name="windows-update-task"></a>Windows Update aktivitet

För Windows körs aktiviteten Windows Update i slutet av anpassningarna. Den hanterar omstarter som krävs.

Följande Windows Update konfiguration utförs:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Den installerar viktiga och rekommenderade Windows-uppdateringar som inte är för hands versioner.

#### <a name="handling-reboots"></a>Hantering av omstarter
För närvarande har DevOps-aktiviteten inte stöd för att starta om Windows-versioner. om du försöker starta om med PowerShell-koden Miss söker den här versionen. Du kan dock använda kod för att starta om Linux-versioner.

#### <a name="build-path"></a>Bygg sökväg

Uppgiften är utformad för att kunna mata in DevOps versions artefakter i avbildningen. Du måste konfigurera en pipeline för att skapa en pipeline för att det ska fungera. I installationen av pipelinen för version måste du lägga till lagrings platsen för bygg artefakterna.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Välj Lägg till en artefakt i versions pipelinen.":::

Välj knappen för att **bygga sökväg** för att välja den build-mapp som du vill placera på avbildningen. Image Builder-uppgiften kopierar alla filer och kataloger i den. När avbildningen skapas distribuerar Image Builder filerna och katalogerna till olika sökvägar, beroende på operativ system.

> [!IMPORTANT]
> När du lägger till en lagrings platsen-artefakt kan du se att katalogen föregås av ett under streck *_*. Under streck kan orsaka problem med infogade kommandon. Använd lämpliga citat tecken i kommandona.
> 

I följande exempel förklaras hur det fungerar:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="En katalog struktur som visar hierarkin.":::


* Windows-filer finns i `C:\` . En katalog med namnet `buildArtifacts` skapas som innehåller `webapp` katalogen.

* Linux – filer finns i  `/tmp` . `webapp`Katalogen skapas som innehåller alla filer och kataloger. Du måste flytta filerna från den här katalogen. Annars kommer de att tas bort eftersom de finns i den tillfälliga katalogen.

#### <a name="inline-customization-script"></a>Infogat anpassnings skript

* Windows – du kan ange infogade PowerShell-kommandon avgränsade med kommatecken. Om du vill köra ett skript i din build-katalog kan du använda:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

* Linux-på Linux-system placeras Bygg artefakterna i `/tmp` katalogen. Men på många Linux-OSs i en omstart tas katalogen/tmp-katalogens innehåll bort. Om du vill att artefakterna ska finnas i avbildningen måste du skapa en annan katalog och kopiera dem.  Till exempel:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    Om du är OK med katalogen "katalogen/tmp" kan du använda koden nedan för att köra skriptet.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>Vad händer med Bygg artefakterna när avbildningen har byggts?

> [!NOTE]
> Avbildnings verktyget tar inte bort Bygg artefakterna automatiskt, men vi rekommenderar starkt att du alltid har kod för att ta bort Bygg artefakterna.
> 

* Windows-Image Builder distribuerar filer till `c:\buildArtifacts` katalogen. Katalogen är beständig. du måste ta bort katalogen. Du kan ta bort den i skriptet som du kör. Till exempel:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux – build-artefakterna placeras i `/tmp` katalogen. Men på många Linux-OSs på en omstart `/tmp` raderas katalog innehållet. Vi rekommenderar att du har kod för att ta bort innehållet och inte förlitar dig på operativ systemet för att ta bort innehållet. Till exempel:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>Total längd på avbildnings version

Den totala längden kan inte ändras i DevOps pipeline-aktiviteten ännu. Standardvärdet är 240 minuter. Om du vill öka [buildTimeoutInMinutes](./image-builder-json.md#properties-buildtimeoutinminutes)kan du använda en AZ CLI-uppgift i versions pipelinen. Konfigurera uppgiften för att kopiera en mall och skicka den. Ett exempel finns i den här [lösningen](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)eller använda AZ PowerShell.


#### <a name="storage-account"></a>Lagringskonto

Välj det lagrings konto som du skapade i kraven. Om du inte ser den i listan har Image Builder inte behörighet till den.

När bygget startar skapar Image Builder en behållare som heter `imagebuilder-vststask` . Behållaren är den plats där Bygg artefakterna från lagrings platsen lagras.

> [!NOTE]
> Du måste manuellt ta bort lagrings kontot eller containern efter varje version.
>

### <a name="distribute"></a>Distribuera

Det finns tre distributions typer som stöds.

#### <a name="managed-image"></a>Hanterad avbildning

* ResourceID
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Platser

#### <a name="azure-shared-image-gallery"></a>Galleri för delad Azure-avbildning

Galleriet för delad avbildning **måste** redan finnas.

* ResourceID 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Regioner: lista över regioner, kommaavgränsade. Till exempel, väst, östra, centrala

#### <a name="vhd"></a>VHD

Det går inte att skicka några värden till detta, avbildnings-verktyget genererar den virtuella hård disken till resurs gruppen för den tillfälliga avbildnings byggare, `IT_<DestinationResourceGroup>_<TemplateName>` i behållaren för *virtuella hård diskar* . När du startar versionen genererar Image Builder loggar. När det är klart kommer den att generera VHD-URL: en.

### <a name="optional-settings"></a>Valfria inställningar

* [VM-storlek](image-builder-json.md#vmprofile) -du kan åsidosätta storleken på den virtuella datorn från standardvärdet för *Standard_D1_v2*. Du kan åsidosätta för att minska den totala anpassnings tiden eller på grund av att du vill skapa avbildningarna som är beroende av vissa VM-storlekar, till exempel GPU/HPC osv.

## <a name="how-it-works"></a>Så här fungerar det

När du skapar versionen skapar aktiviteten en behållare i lagrings kontot, med namnet *imagebuilder-vststask*. IT-zips och laddar upp dina build-artefakter och skapar en SAS-token för zip-filen.

Aktiviteten använder de egenskaper som har skickats till aktiviteten för att skapa avbildnings-mallens artefakt. Uppgiften gör följande:
* Laddar ned ZIP-filen för build-artefakten och andra tillhör ande skript. Filerna sparas i ett lagrings konto i resurs gruppen tillfälligt Image Builder `IT_<DestinationResourceGroup>_<TemplateName>` .
* Skapar en mall för fasta *T_* och ett 10-siffrigt enkel färgs heltal. Mallen sparas i den resurs grupp som du har valt. Mallen finns för varaktigheten för bygget i resurs gruppen. 

Exempel på utdata:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

När avbildnings versionen startar rapporteras körnings statusen i versions loggarna:

```text
starting run template...
```

När avbildnings versionen har slutförts visas utdata som liknar följande text:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

Avbildnings mal len och `IT_<DestinationResourceGroup>_<TemplateName>` tas bort.

Du kan ta $ (imageUri) VSTS-variabeln och använda den i nästa uppgift eller bara använda värdet och skapa en virtuell dator.

## <a name="output-devops-variables"></a>Variabler för utgående DevOps

Pub/erbjudande/SKU/version av käll Marketplace-avbildningen:
* $ (pirPublisher)
* $ (pirOffer)
* $ (pirSku)
* $ (pirVersion)

Avbildnings-URI – ResourceID för den distribuerade avbildningen:
* $ (imageUri)

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>Kan jag använda en befintlig avbildnings mall som jag redan har skapat, utanför DevOps?

För närvarande, inte just nu.

### <a name="can-i-specify-the-image-template-name"></a>Kan jag ange namnet på bildmallen?

Nej. Ett unikt Mallnamn används och tas sedan bort.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>Avbildnings verktyget misslyckades. Hur kan jag felsöka?

Om ett build-problem uppstår tar DevOps-aktiviteten inte bort den mellanlagrings resurs gruppen. Du kan komma åt den mellanlagrings resurs grupp som innehåller anpassnings loggen för build.

Du kommer att se ett fel i DevOps-loggen för aktiviteten VM Image Builder och se platsen anpassning. log. Till exempel:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Exempel på DevOps-aktivitets fel som visar ett fel.":::

Mer information om fel sökning finns i [Felsöka Azure Image Builder-tjänsten](image-builder-troubleshoot.md). 

När du har undersökt felen kan du ta bort den mellanlagrings resurs gruppen. Ta först bort resurs artefakten för avbildnings mal len. Artefakten föregås av *T_* och går att hitta i DevOps-uppgiftens versions logg:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

Resurs artefakten för avbildnings mal len finns i den resurs grupp som ursprungligen angavs i uppgiften. När du är klar med fel sökningen tar du bort artefakten. Om du tar bort med Azure Portal, i resurs gruppen, väljer du **Visa dolda typer** för att Visa artefakten.


## <a name="next-steps"></a>Nästa steg

Mer information finns i [Översikt över Azure Image Builder](../image-builder-overview.md).