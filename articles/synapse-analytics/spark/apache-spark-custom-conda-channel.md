---
title: Skapa en anpassad Conda-kanal för paket hantering
description: Lär dig hur du skapar en anpassad Conda-kanal för paket hantering
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 528ba4a1be3650a81772d78a438f03611b9bd761
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102107943"
---
# <a name="create-a-custom-conda-channel-for-package-management"></a>Skapa en anpassad Conda-kanal för paket hantering 
När du installerar python-paket använder Conda Package Manager kanaler för att söka efter paket. Du kan behöva skapa en anpassad Conda-kanal av olika orsaker. Du kan till exempel se att:

- din arbets yta är data exfiltrering-skyddade och utgående anslutningar blockeras.  
- du har paket som du inte vill överföra till offentliga databaser.
- du vill konfigurera den alternativa lagrings platsen för användarna i din arbets yta.

I den här artikeln innehåller vi en steg-för-steg-guide som hjälper dig att skapa en anpassad Conda-kanal i ditt Azure Data Lake Storage-konto.

## <a name="set-up-your-local-machine"></a>Konfigurera din lokala dator

1. Installera Conda på den lokala datorn. Du kan se [Azure Synapse Spark-körningsmiljön](./apache-spark-version-support.md) för att identifiera den Conda-version som används på samma körnings tid.
   
2. Om du vill skapa en anpassad kanal installerar du Conda-build.
```
conda install conda-build
```
3. Organisera alla paket i för den plattform som du vill betjäna. I det här exemplet kommer vi att installera Anaconda-Arkiv på den lokala datorn.

```
sudo wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh 
sudo chmod +x Anaconda3-4.4.0-Linux-x86_64.sh  
sudo bash Anaconda3-4.4.0-Linux-x86_64.sh -b -p /usr/lib/anaconda3 
export PATH="/usr/lib/anaconda3/bin:$PATH" 
sudo chmod 777 -R /usr/lib/anaconda3a.  
```
## <a name="mount-the-storage-account-onto-your-machine"></a>Montera lagrings kontot på din dator
Nu ska vi montera Azure Data Lake Storage Gen2-kontot på den lokala datorn. Den här processen kan också göras med ett WASB-konto. Vi kommer dock att gå igenom ett exempel för ADLSg2-kontot 
 
Om du vill ha mer information om hur du monterar lagrings kontot på den lokala datorn kan du gå till [den här sidan](https://github.com/Azure/azure-storage-fuse#blobfuse ). 

1. Du kan installera blobfuse från Linux Software-lagringsplatsen för Microsoft-produkter.

```
wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install blobfuse fuse 
export AZURE_STORAGE_ACCOUNT=<<myaccountname>
export AZURE_STORAGE_ACCESS_KEY=<<myaccountkey>>
export AZURE_STORAGE_BLOB_ENDPOINT=*.dfs.core.windows.net 
```

2. Skapa din monterings punkt ( ```mkdir /path/to/mount``` ) och montera en BLOB-behållare med blobfuse. I det här exemplet ska vi använda värdet **privatechannel** för den här **variabeln** .
   
```
blobfuse /path/to/mount --container-name=mycontainer --tmp-path=/mnt/blobfusetmp --use-adls=true --log-level=LOG_DEBUG 
sudo mkdir -p /mnt/blobfusetmp
sudo chown <myuser> /mnt/blobfusetmp
```
## <a name="create-the-channel"></a>Skapa kanalen
I nästa uppsättning steg kommer vi att skapa en anpassad Conda-kanal. 

1. Skapa en katalog på den lokala datorn för att organisera alla paket för din anpassade kanal.
   
```
mkdir /home/trusted-service-user/privatechannel 
cd ~/privatechannel/ 
mkdir channel1/linux64 
```

2. Organisera alla ```tar.bz2``` paket från https://repo.anaconda.com/pkgs/main/linux-64/ i under katalogen. Se även till att även alla beroende. bz2-paket också inkluderas. 

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

För mer information, kan du även [gå till användar handboken för Conda](https://docs.conda.io/projects/conda/latest/user-guide/tasks/create-custom-channels.html) för att skapa anpassade kanaler. 

## <a name="storage-account-permissions"></a>Lagrings konto behörigheter
Nu kommer vi att behöva verifiera behörigheterna för lagrings kontot. Om du vill ange dessa behörigheter navigerar du till sökvägen där en anpassad kanal ska skapas. Skapa sedan en SAS-token för ```privatechannel``` som har Läs-, list-och körnings behörighet. 

Kanal namnet är nu den BLOB SAS-URL som genereras av den här processen.  

## <a name="create-a-sample-conda-environment-configuration-file"></a>Skapa en exempel miljö konfigurations fil för Conda
Senast kontrollerar du installations processen genom att skapa en exempel Conda- ```environment.yml``` fil. Om du har i en DEP-aktiverad arbets yta måste du ange ``nodefaults`` kanalen i din miljö fil.

Här är ett exempel på en Conda konfigurations fil:
```
name: sample 
channels: 
  - https://<<storage account name>>.blob.core.windows.net/privatechannel/channel?<<SAS Token>
  - nodefaults 
dependencies: 
  - openssl 
  - ncurses 
```
När du har skapat exempel filen Conda kan du skapa en virtuell Conda-miljö. 

```
conda env create –file sample.yml  
source activate env 
conda list 
```
Nu när du har verifierat din anpassade kanal kan du använda [python-poolens hanterings](./apache-spark-manage-python-packages.md) process för att uppdatera biblioteken i Apache Spark-poolen.

## <a name="next-steps"></a>Nästa steg
- Visa standard biblioteken: [Apache Spark versions stöd](apache-spark-version-support.md)
- Hantera python-paket: [python-paket hantering](./apache-spark-manage-python-packages.md)

