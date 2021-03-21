---
title: Felsök anslutning mellan Synapse Studio och lagring
description: Felsök anslutning mellan Synapse Studio och lagring
author: saveenr
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d570b4a8df5d59cf8828985bee20852d6bc79b1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98117069"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Felsöka anslutningen mellan Azure Synapse Analytics Synapse Studio och lagring

I Synapse Studio kan du utforska data resurser som finns i den länkade lagringen. Den här guiden hjälper dig att lösa anslutnings problem när du försöker komma åt dina data resurser. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Fall #1: lagrings kontot saknar rätt behörighet

Om ditt lagrings konto saknar rätt behörighet kan du inte expandera lagrings strukturen via "data"--> "länkad" i Synapse Studio. Se skärm bilden med problemets symptom nedan. 

Det detaljerade fel meddelandet kan variera, men den allmänna innebörden av fel meddelandet är: "den här begäran har inte behörighet att utföra den här åtgärden."

I den länkade lagrings noden:  
![Problem med lagrings anslutning 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

I noden lagrings behållare:  
![Lagrings anslutnings problem 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**Lösning**: Mer information om hur du tilldelar ditt konto till rätt roll finns i [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Ärende #2: det gick inte att skicka begäran till lagrings servern

När du väljer pilen för att expandera lagrings strukturen i "data"--> "länkad" i Synapse Studio kan du se problemet "REQUEST_SEND_ERROR" i den vänstra panelen. Se skärm bilden för problemets symptom nedan:

I den länkade lagrings noden:  
![Problem med lagrings anslutning 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

I noden lagrings behållare:  
![Lagrings anslutnings problem 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Det kan finnas flera möjliga orsaker till det här problemet:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>Lagrings resursen finns bakom ett vNet och en privat lagrings plats måste konfigureras

**Lösning**: i det här fallet måste du konfigurera den privata lagrings slut punkten för ditt lagrings konto. Information om hur du konfigurerar den privata lagrings slut punkten för vNet finns i [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../security/how-to-connect-to-workspace-from-restricted-network.md).

Du kan använda kommandot "nslookup \<storage-account-name\> . DFS.Core.Windows.net" för att kontrol lera anslutningen när den privata lagrings slut punkten har kon figurer ATS. Den ska returnera en sträng som liknar: " \<storage-account-name\> . privatelink.DFS.Core.Windows.net".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>Lagrings resursen är inte bakom ett vNet, men Blob Service (Azure AD) kan inte nås på grund av en brand vägg som kon figurer ATS

**Lösning**: i så fall måste du öppna ditt lagrings konto i Azure Portal. Rulla ned i det vänstra navigerings fältet för att **stödja + fel sökning** och välj **anslutnings kontroll** för att kontrol lera anslutnings statusen för **BLOB service (Azure AD)** . Om den inte är tillgänglig följer du den upphöjda guiden för att kontrol lera konfigurationen av **brand väggar och virtuella nätverk** på sidan för ditt lagrings konto. Mer information om lagrings brand väggar finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../../storage/common/storage-network-security.md).

### <a name="other-issues-to-check"></a>Andra problem att kontrol lera 

* Lagrings resursen som du försöker komma åt är Azure Data Lake Storage Gen2 och ligger bakom en brand vägg och ett virtuellt nätverk (med den privata lagrings platsen konfigurerad) på samma gång.
* Behållar resursen som du använder har tagits bort eller finns inte.
* Korsande klient organisation: den arbets plats innehavare som användaren använde för att logga in är inte samma som innehavaren av lagrings kontot. 


## <a name="next-steps"></a>Nästa steg
Om föregående steg inte hjälper till att lösa problemet kan du [skapa ett support ärende](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md).