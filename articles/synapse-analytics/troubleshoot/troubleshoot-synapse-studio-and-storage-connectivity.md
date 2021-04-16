---
title: Felsök anslutning mellan Synapse Studio och lagring
description: Felsök anslutning mellan Synapse Studio och lagring
author: saveenr
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: xujiang1
ms.reviewer: jrasnick
ms.openlocfilehash: d5f79131608315f7e1c05cbfc0117300eea6c511
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566281"
---
# <a name="troubleshoot-connectivity-between-azure-synapse-analytics-synapse-studio-and-storage"></a>Felsöka anslutningen mellan Azure Synapse Analytics Synapse Studio och lagring

I Synapse Studio kan du utforska dataresurser som finns i din länkade lagring. Den här guiden hjälper dig att lösa anslutningsproblem när du försöker komma åt dina dataresurser. 

## <a name="case-1-storage-account-lacks-proper-permissions"></a>Fall #1: Lagringskontot saknar rätt behörigheter

Om ditt lagringskonto saknar rätt behörigheter kan du inte expandera lagringsstrukturen via "Data" --> "Linked" (Länkad) i Synapse Studio. Se skärmbilden av problemets symptom nedan. 

Det detaljerade felmeddelandet kan variera, men den allmänna innebörden av felmeddelandet är: "Den här begäran har inte behörighet att utföra den här åtgärden.".

I den länkade lagringsnoden:  
![Problem med lagringsanslutning 1](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1.png)

I noden för lagringscontainern:  
![Problem med lagringsanslutning 1a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-1a.png)

**LÖSNING:** Information om hur du tilldelar ditt konto till rätt roll finns i Använda Azure Portal för att tilldela en Azure-roll för åtkomst till [blob- och ködata](../../storage/common/storage-auth-aad-rbac-portal.md)


## <a name="case-2-failed-to-send-the-request-to-storage-server"></a>Fall #2: Det gick inte att skicka begäran till lagringsservern

När du väljer pilen för att expandera lagringsstrukturen i "Data" --> "Länkad" i Synapse Studio kan du se problemet "REQUEST_SEND_ERROR" i den vänstra panelen. Se skärmbilden av problemets symptom nedan:

I den länkade lagringsnoden:  
![Problem med lagringsanslutning 2](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2.png)

I noden för lagringscontainern:  
![Problem med lagringsanslutning 2a](media/troubleshoot-synapse-studio-and-storage-connectivity/storage-connectivity-issue-2a.png)

Det kan finnas flera möjliga orsaker till det här problemet:

### <a name="the-storage-resource-is-behind-a-vnet-and-a-storage-private-endpoint-needs-to-configure"></a>Lagringsresursen finns bakom ett vNet och en privat lagringsslutpunkt måste konfigureras

**LÖSNING:** I det här fallet måste du konfigurera lagringens privata slutpunkt för ditt lagringskonto. Information om hur du konfigurerar den privata lagringsslutpunkten för vNet finns i Använda Azure Portal för att tilldela en Azure-roll för åtkomst till [blob- och ködata.](../security/how-to-connect-to-workspace-from-restricted-network.md)

Du kan använda kommandot "nslookup .dfs.core.windows.net" för att kontrollera anslutningen när den privata \<storage-account-name\> lagringsslutpunkten har konfigurerats. Den bör returnera en sträng som liknar: \<storage-account-name\> ".privatelink.dfs.core.windows.net".

### <a name="the-storage-resource-is-not-behind-a-vnet-but-the-blob-service-azure-ad-endpoint-is-not-accessible-due-to-firewall-configured"></a>Lagringsresursen finns inte bakom ett vNet, men Blob Service-slutpunkten (Azure AD) är inte tillgänglig på grund av att brandväggen har konfigurerats

**LÖSNING:** I det här fallet måste du öppna ditt lagringskonto i Azure Portal. I det vänstra navigeringsfönstret bläddrar du **ned till Support + felsökning** och väljer **Anslutningskontroll** för att kontrollera status för **Blob Service-anslutning (Azure AD).** Om den inte är tillgänglig följer du den uppflyttade guiden för att kontrollera konfigurationen av **brandväggar och virtuella** nätverk på sidan för ditt lagringskonto. Mer information om lagringsbrandväggen finns [i Konfigurera Azure Storage och virtuella nätverk.](../../storage/common/storage-network-security.md)

### <a name="other-issues-to-check"></a>Andra problem att kontrollera 

* Den lagringsresurs som du använder Azure Data Lake Storage Gen2 och finns bakom en brandvägg och ett vNet (med privat slutpunkt för lagring konfigurerad) samtidigt.
* Containerresursen som du använder har tagits bort eller finns inte.
* Korsande klientorganisation: arbetsytans klientorganisation som användaren använde för att logga in är inte samma som klientorganisationen för lagringskontot. 


## <a name="next-steps"></a>Nästa steg
Om de föregående stegen inte hjälper dig att lösa problemet skapar du [en supportbiljett.](../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md)