---
title: Vad är Azure-Data Science Virtual Machine
titleSuffix: Azure Data Science Virtual Machine
description: Översikt över Azure Data Science Virtual Machine – en enkel att använda en virtuell dator på Azures moln plattform med förinstallerade och konfigurerade verktyg och bibliotek för data vetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: bd2333d89e4d1789b3464606b49f624609ef67d5
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518767"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Vad är Azure-Data Science Virtual Machine för Linux och Windows?

Data Science Virtual Machine (DSVM) är en anpassad VM-avbildning på Azure Cloud Platform som skapats specifikt för data vetenskap. Det finns många populära verktyg för data vetenskap förinstallerade och förkonfigurerade för att komma igång med att skapa intelligenta program för avancerade analyser.

DSVM finns på:

+ Windows Server 2019
+ Ubuntu 18.04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Jämförelse med Azure Machine Learning

DSVM är en anpassad VM-avbildning för data vetenskap, men [Azure Machine Learning](../overview-what-is-azure-ml.md) (azureml) är en slutpunkt-till-slutpunkt-plattform som omfattar:

+ Fullständigt hanterad beräkning
  + Beräkningsinstanser
  + Beräknings kluster för distribuerade ML-aktiviteter
  + Härlednings kluster för real tids resultat
+ Data lager (till exempel BLOB, ADLS Gen2, SQL DB)
+ Experiment spårning
+ Modellhantering
+ Notebooks
+ Miljöer (hantera Conda-och R-beroenden)
+ Märkning
+ Pipelines (automatisera data vetenskaps arbets flöden från slut punkt till slut punkt)

### <a name="comparison-with-azureml-compute-instances"></a>Jämförelse med AzureML Compute instances

[Azure Machine Learning beräknings instanser](../concept-compute-instance.md) är en fullständigt konfigurerad och __hanterad__ VM-avbildning medan DSVM är en __ohanterad__ virtuell dator.

De viktigaste skillnaderna mellan dessa två produkt erbjudanden beskrivs nedan:


|Funktion |Datavetenskap<br>Virtuell dator |AzureML<br>Compute Instance (Beräkningsinstans)  | 
|---------|---------|---------|
| Fullständigt hanterad | Inga        | Ja        |
|Stöd för språk     |  Python, R, Julia, SQL, C#,<br> Java, Node.js, F #       | Python och R        |
|Operativsystem     | Ubuntu<br>Windows         |    Ubuntu     |
|Förkonfigurerat GPU-alternativ     |  Ja       |    Ja     |
|Alternativet skala upp | Ja | Ja |
|SSH-åtkomst    | Ja        |    Ja     |
|RDP-åtkomst    | Ja        |     Inga    |
|Inbyggd<br>Värdbaserade antecknings böcker     |   Inga<br>(kräver ytterligare konfiguration)      |      Ja   |
|Inbyggd SSO     | Inga <br>(kräver ytterligare konfiguration)         |    Ja     |
|Inbyggt samarbete     | Inga         | Ja        |
|Förinstallerade verktyg     |  Jupyter (labb), RStudio-Server, VSCode,<br> Visual Studio, pycharm med, Juno,<br>Power BI Desktop, SSMS, <br>Microsoft Office 365, Apache-granskning       |     Jupyter (labb)<br> RStudio Server   |

## <a name="sample-use-cases"></a>Exempel på användningsfall

Nedan illustreras några vanliga användnings fall för DSVM-kunder.

### <a name="short-term-experimentation-and-evaluation"></a>Kortsiktig experimentering och utvärdering

Du kan använda DSVM för att utvärdera eller lära dig nya data vetenskaps [verktyg](./tools-included.md), särskilt genom att gå igenom några av våra publicerade [exempel och genom gångar](./dsvm-samples-and-walkthroughs.md).

### <a name="deep-learning-with-gpus"></a>Djupinlärning med GPU:er

I DSVM kan dina utbildnings modeller använda djup inlärnings-algoritmer på maskin vara som baseras på GPU: er (Graphics Processing units). Genom att dra nytta av funktionerna för skalning av virtuella datorer i Azure-plattformen kan DSVM hjälpa dig att använda GPU-baserad maskin vara i molnet efter behov. Du kan växla till en GPU-baserad virtuell dator vid utbildning av stora modeller eller vid behov av snabba beräkningar samtidigt som samma OS-disk behålls. Du kan välja någon av de N-seriens GPU-aktiverade virtuella datorerna SKU: er med DSVM. Anteckna GPU-aktiverade virtuella datorer SKU: er stöds inte på kostnads fria Azure-konton.

Windows-versionerna av DSVM levereras förinstallerade med GPU-drivrutiner, ramverk och GPU-versioner av djup inlärnings ramverk. På Linux-versionerna har djup inlärning på GPU: er Aktiver ATS på Ubuntu-Dsvm. 

Du kan också distribuera Ubuntu-eller Windows-versionerna av DSVM till en virtuell Azure-dator som inte baseras på GPU: er. I det här fallet kommer alla ramverk för djup inlärning att återgå till CPU-läget.

[Lär dig mer om tillgängliga djup inlärnings-och AI-ramverk](dsvm-tools-deep-learning-frameworks.md).

### <a name="data-science-training-and-education"></a>Kurser och utbildning om datavetenskap

Företagsutbildare och lärare som undervisar datavetenskapsklasser brukar tillhandahålla en avbildning av en virtuell dator. Bilden ser till att eleverna har en konsekvent inställning och att exemplen fungerar förutsägbart.

DSVM skapar en miljö på begäran med en konsekvent konfiguration som underlättar support-och inkompatibilitets utmaningarna. Det är mycket användbart i fall där de här miljöerna behöver skapas ofta, särskilt för kortare kurser.


## <a name="whats-included-on-the-dsvm"></a>Vad ingår i DSVM?

Se en fullständig lista med verktyg på både Windows-och Linux-Dsvm [här](tools-included.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer med de här artiklarna:

+ Windows:
  + [Konfigurera en Windows DSVM](provision-vm.md)
  + [Data vetenskap på en Windows-DSVM](vm-do-ten-things.md)

+ Linux:
  + [Konfigurera en Linux-DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Data vetenskap i en Linux-DSVM](linux-dsvm-walkthrough.md)