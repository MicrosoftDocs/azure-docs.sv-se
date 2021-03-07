---
title: Förbered för Azure Stack Edge Pro-enhets problem
description: Beskriver hur du får en ersättning för en Azure Stack Edge Pro-enhet som misslyckades.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 60469dc834c28bd8dbc73a1883ea01b06797c01f
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102442986"
---
# <a name="prepare-for-an-azure-stack-edge-pro-gpu-device-failure"></a>Förbered för ett Azure Stack Edge Pro GPU-enhets problem

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Den här artikeln hjälper dig att förbereda ett enhets fel genom detaljerad information om hur du sparar och säkerhetskopierar enhets konfigurationen och data på din Azure Stack Edge Pro GPU-enhet. 

Artikeln innehåller inte steg för att säkerhetskopiera Kubernetes-och IoT-behållare som distribuerats på Azure Stack Edge Pro GPU-enhet. 

## <a name="understand-device-failures"></a>Förstå enhets problem

Din Azure Stack Edge Pro GPU-enhet kan uppleva två typer av maskin varu problem.

- Tillåtna problem som kräver att du ersätter en maskin varu komponent. Med dessa problem kan du använda enheten i ett degraderat tillstånd. Exempel på dessa fel är en enskild havererad enhet (PSU) eller en enskild havererad disk på enheten. I vart och ett av dessa fall kan enheten fortsätta att arbeta. Kontakta Microsoft Support så snart som möjligt för att ersätta de komponenter som misslyckades.

- Icke-tillåtna fel som kräver att du ersätter hela enheten, till exempel när två diskar har misslyckats på enheten. I dessa fall kontaktar du Microsoft Support omedelbart. Efter att ha fastställt att en enhet behöver bytas ut bidrar stödet till att ersätta din Azure Stack Edge-enhet.

För att förbereda för uteblivna haverier måste du säkerhetskopiera följande saker på din enhet:

- Information om enhets konfigurationen
- Data i Edge lokala resurser och gräns moln resurser
- Filer och mappar som är kopplade till de virtuella datorer som körs på enheten


## <a name="back-up-device-configuration"></a>Säkerhetskopiera enhets konfigurationen

Under den inledande konfigurationen av enheten är det viktigt att ha en kopia av enhetens konfigurations information som beskrivs i [Check listan för distribution](azure-stack-edge-gpu-deploy-checklist.md). Under återställningen används den här konfigurations informationen för den nya ersättnings enheten. 

## <a name="protect-device-data"></a>Skydda data på enheter

Enhets data kan vara av någon av följande typer:

- Data i gräns moln resurser
- Data i lokala resurser
- Filer och mappar på virtuella datorer

I följande avsnitt beskrivs stegen och rekommendationerna för att skydda var och en av dessa typer av data på enheten.

## <a name="protect-data-in-edge-cloud-shares"></a>Skydda data i gräns moln resurser

Du kan skapa gräns moln resurser som nivå data från din enhet till Azure. Beroende på vilken nätverks bandbredd som är tillgänglig konfigurerar du bandbredden på enheten för att minimera eventuell data förlust om ett fel uppstår.

> [!IMPORTANT]
> Om det finns ett icke-anslaget haveri på enheten kan lokala data som inte är i nivå av-enhet till Azure gå förlorade. 

## <a name="protect-data-in-edge-local-shares"></a>Skydda data i lokala Edge-resurser

Om du distribuerar Kubernetes eller IoT Edge konfigurerar du för att spara program data på enheten lokalt och synkroniserar inte med Azure Storage. Data lagras på en resurs på enheten. Du kanske tycker det är viktigt att säkerhetskopiera data i dessa resurser.

Följande data skydds lösningar från tredje part kan tillhandahålla en säkerhets kopierings lösning för data i de lokala SMB-eller NFS-resurserna. 

| Tredjepartsprogram           | Referens till lösningen                               |
|--------------------------------|---------------------------------------------------------|
| Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Kontakta Cohesity om du vill ha mer information.          |
| CommVault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Kontakta CommVault om du vill ha mer information.          |
| Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Kontakta Veritas om du vill ha mer information.   |
| Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Kontakta Veeam om du vill ha mer information. |


## <a name="protect-files-and-folders-on-vms"></a>Skydda filer och mappar på virtuella datorer

Azure Stack Edge fungerar med Azure Backup och andra data skydds lösningar från tredje part för att tillhandahålla en lösning för säkerhets kopiering för att skydda data som finns i de virtuella datorer som distribueras på enheten. I följande tabell visas referenser till tillgängliga lösningar som du kan välja mellan.


| Säkerhets kopierings lösningar        | Operativsystem som stöds   | Referens                                                                |
|-------------------------|----------------|--------------------------------------------------------------------------|
| Microsoft Azure Recovery Services MARS-agenten för Azure Backup | Windows        | [Om MARS-agent](../backup/backup-azure-about-mars.md)    |
| Cohesity                | Windows, Linux | [Microsoft Azure-integrering, säkerhetskopiera & återställnings lösning kort](https://www.cohesity.com/solution/cloud/azure) <br>Kontakta Cohesity om du vill ha mer information.                          |
| CommVault               | Windows, Linux | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br>Kontakta CommVault om du vill ha mer information.                          |
| Veritas                 | Windows, Linux | [https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370](https://vox.veritas.com/t5/Protection/Protecting-Azure-Stack-Edge-with-NetBackup/ba-p/883370) <br> Kontakta Veritas om du vill ha mer information.                    |
| Veeam                   | Windows, Linux | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Kontakta Veeam om du vill ha mer information. |


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [återställer från en misslyckad Azure Stack Edge Pro GPU-enhet](azure-stack-edge-gpu-recover-device-failure.md).