---
title: Skapa en anpassad Conda-kanal för pakethantering
description: Lär dig hur du skapar en anpassad Conda-kanal för pakethantering
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 26b6adefd2d334c9fe570bfa7e63bb06b55b9d20
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588776"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Skapa en anpassad Conda-kanal för pakethantering 
När du installerar Python-paket använder Conda-pakethanteraren kanaler för att söka efter paket. Du kan behöva skapa en anpassad Conda-kanal av olika skäl. Du kan till exempel se att:

- din arbetsyta är data exfiltreringsskyddad och utgående anslutningar blockeras.  
- du har paket som du inte vill ladda upp till offentliga lagringsplatsen.
- du vill konfigurera en alternativ lagringsplats för användarna på din arbetsyta.

I den här artikeln tillhandahåller vi en stegvis guide som hjälper dig att skapa din anpassade Conda-kanal i ditt Azure Data Lake Storage konto.

## <a name="set-up-your-local-machine"></a>Konfigurera din lokala dator

1. Installera Conda på den lokala datorn. Du kan referera till [Azure Synapse Spark-körningen](./apache-spark-version-support.md) för att identifiera vilken Conda-version som används i samma körning.
   
2. Om du vill skapa en anpassad kanal installerar du conda-build.
```
conda install conda-build
```
3. Organisera alla paket i för den plattform som du vill använda. I det här exemplet installerar vi Anaconda-arkivet på den lokala datorn.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Montera lagringskontot på datorn
Därefter monterar vi Azure Data Lake Storage Gen2 på den lokala datorn. Den här processen kan också göras med ett WASB-konto. Vi går dock igenom ett exempel för ADLSg2-kontot 
 
Mer information om hur du monterar lagringskontot på den lokala datorn finns på den [här sidan.](https://github.com/Azure/azure-storage-fuse#blobfuse ) 

1. Du kan installera blobfuse från Linux-programvarulagringsplatsen för Microsoft-produkter.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Skapa monteringspunkten ( ```mkdir /path/to/mount``` ) och montera en blobcontainer med blobfuse. I det här exemplet använder vi värdet **privatechannel för** **variabeln mycontainer.**
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Skapa kanalen
I nästa uppsättning steg skapar vi en anpassad Conda-kanal. 

1. På den lokala datorn skapar du en katalog för att organisera alla paket för din anpassade kanal.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Ordna alla ```tar.bz2``` paket från i https://repo.anaconda.com/pkgs/main/linux-64/ -underkatalogen. Se även till att inkludera alla beroende tar.bz2-paket också. 

```
cd channel1 
mkdir noarch 
echo '{}' > noarch/repodata.json 
bzip2 -k noarch/repodata.json 

// Create channel 

conda index channel1/noarch 
conda index channel1/linux-64 
conda index channel1 
```

Mer information finns också i [Conda-användarhandboken för att](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/create-custom-channels.html) skapa anpassade kanaler. 

## <a name="storage-account-permissions"></a>Behörigheter för lagringskonto
Nu måste vi verifiera behörigheterna för lagringskontot. Om du vill ange dessa behörigheter går du till sökvägen där den anpassade kanalen ska skapas. Skapa sedan en SAS-token för ```privatechannel``` som har läs-, list- och körningsbehörigheter. 

Kanalnamnet kommer nu att vara blob-SAS-URL:en som genereras från den här processen.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Skapa en exempelkonfigurationsfil för Conda-miljön
Kontrollera sist installationsprocessen genom att skapa en ```environment.yml``` Conda-exempelfil. Om du har i en DEP-aktiverad arbetsyta måste du ange ``nodefaults`` kanalen i din miljöfil.

Här är ett exempel på en Conda-konfigurationsfil:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
När du har skapat Conda-exempelfilen kan du skapa en virtuell Conda-miljö. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Nu när du har verifierat din anpassade kanal kan du använda [Python-poolhanteringsprocessen](./apache-spark-manage-python-packages.md) för att uppdatera biblioteken i Apache Spark poolen.

## <a name="next-steps"></a>Nästa steg
- Visa standardbiblioteken: [stöd Apache Spark version](apache-spark-version-support.md)
- Hantera Python-paket: [Python-pakethantering](./apache-spark-manage-python-packages.md)

