---
title: Data som ska samlas in när ett ärende öppnas för Microsoft Azure Automation | Microsoft Docs
description: Den här artikeln beskriver den information som ska samlas in innan du öppnar ett ärende för Azure Automation med Microsoft Azure support.
services: automation
ms.subservice: ''
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.openlocfilehash: 3d2c0cf780b992d7bb464969c50d60ffa5093e8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896570"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Data som ska samlas in när du öppnar ett ärende för Microsoft Azure Automation

I den här artikeln beskrivs en del av den information som du ska samla in innan du öppnar ett ärende för Azure Automation med Microsoft Azure stöd. Den här informationen krävs inte för att öppna ärendet. Det kan dock hjälpa Microsoft att lösa problemet snabbare. Du kan också uppmanas att utföra dessa data av support teknikern när du har öppnat ärendet.

## <a name="basic-data"></a>Grundläggande data

Samla in de grundläggande data som beskrivs i Knowledge Base [-artikeln 4034605 – hur du samlar in Azure Automation-skriptbaserad diagnostik](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Data för Uppdateringshantering problem i Linux

1. Utöver de objekt som listas i KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)kör du följande verktyg för logg insamling:

   [Logg insamlare för OMS Linux-Agent](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Komprimera innehållet i mappen **/var/opt/Microsoft/omsagent/Run/automationworker/** och skicka sedan den komprimerade filen till Azure-supporten.
 
3. Kontrol lera att ID: t för arbets ytan som Log Analytics agent för Linux-rapporter är samma som ID: t för arbets ytan som övervakas för uppdateringar.

## <a name="data-for-update-management-issues-on-windows"></a>Data för Uppdateringshantering problem i Windows

1. Samla in data för objekten som anges i [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Exportera följande händelse loggar till EVTX-formatet:

   * System
   * Program
   * Säkerhet
   * Operations Manager
   * Microsoft-SMA/Operational

3. Kontrol lera att ID: t för arbets ytan som agenten rapporterar till är samma som ID: t för arbets ytan som övervakas av Windows-uppdateringar.

## <a name="data-for-job-issues"></a>Data för jobb problem

1. Samla in data för objekten som anges i [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Samla in jobb-ID för jobbet som har ett problem:

   1. I Azure Portal går du till **Automation-konton**.
   2. Välj det Automation-konto som du felsöker och Anteckna namnet.
   3. Välj **jobb**.
   4. Välj det jobb som du felsöker.
   5. I fönstret jobb Sammanfattning söker du efter GUID-värdet i **jobb-ID**.

   ![Jobb-ID i fönstret jobb Sammanfattning](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Samla in ett exempel på det skript som du kör.

4. Samla in loggfilerna:

   1. I Azure Portal går du till **Automation-konton**.
   2. Välj det Automation-konto som du felsöker.
   3. Välj **jobb**.
   4. Välj det jobb som du felsöker.
   5. Välj **alla loggar**.
   6. Samla in data i fönstret som visas.

   ![Data som listas under alla loggar](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Data för modul problem

Förutom de [grundläggande data posterna](#basic-data)samlar du in följande information:

* De steg du har följt, så att problemet kan reproduceras.
* Skärm dum par av eventuella fel meddelanden.
* Skärm bilder av aktuella moduler och deras versions nummer.

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp:

* Få svar från Azure-experter via [Azure-forum](https://azure.microsoft.com/support/forums/).
* Anslut till [@AzureSupport](https://twitter.com/azuresupport) , det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
* Filen en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
