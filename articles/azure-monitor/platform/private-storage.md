---
title: Använda kund hanterade lagrings konton i Azure Monitor Log Analytics
description: Använd ditt eget lagrings konto för Log Analytics scenarier
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 0a2439f0ed18cf93691a1d0389e049b1b7993d93
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97732074"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Använda kund hanterade lagrings konton i Azure Monitor Log Analytics

Log Analytics förlitar sig på Azure Storage i många olika scenarier. Den här användningen hanteras vanligt vis automatiskt. Vissa fall kräver dock att du anger och hanterar ditt eget lagrings konto, även kallat ett kundhanterat lagrings konto. Det här dokumentet innehåller information om användningen av kundhanterad lagring för inmatning av WAD/LAD-loggar, privat länk specifika scenarier och CMK-kryptering (Customer Key). 

> [!NOTE]
> Vi rekommenderar att du inte tar ett beroende på innehållet Log Analytics uppladdningar till kundhanterad lagring, eftersom formatering och innehåll kan ändras.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Mata in Azure-diagnostik anknytnings loggar (WAD/LAD)
Azure-diagnostik tilläggs agenter (kallas även WAD och LAD för Windows respektive Linux-agenter) samlar in olika operativ system loggar och lagrar dem på ett kundhanterat lagrings konto. Du kan sedan mata in dessa loggar i Log Analytics för att granska och analysera dem.
Hur du samlar in Azure-diagnostik tilläggs loggar från ditt lagrings konto ansluter lagrings kontot till arbets ytan Log Analytics som en lagrings data källa med hjälp av [Azure Portal](./diagnostics-extension-logs.md#collect-logs-from-azure-storage) eller genom att anropa [API: et för Storage Insights](/rest/api/loganalytics/storage%20insights/createorupdate).

Data typer som stöds:
* Syslog
* Windows-händelser
* Service Fabric
* ETW-händelser
* IIS-loggar

## <a name="using-private-links"></a>Använda privata länkar
Kund hanterade lagrings konton krävs i vissa användnings fall när privata länkar används för att ansluta till Azure Monitor resurser. Ett sådant fall är inmatningen av anpassade loggar eller IIS-loggar. De här data typerna laddas först upp som blobbar till ett mellanliggande Azure Storage konto och hämtas bara till en arbets yta. På samma sätt kan vissa Azure Monitor-lösningar använda lagrings konton för att lagra stora filer, till exempel Azure Security Center (ASC) som kan behöva ladda upp filer. 

##### <a name="private-link-scenarios-that-require-a-customer-managed-storage"></a>Privata länk scenarier som kräver en kundhanterad lagring
* Inmatning av anpassade loggar och IIS-loggar
* Tillåter ASC-lösning att ladda upp filer

### <a name="how-to-use-a-customer-managed-storage-account-over-a-private-link"></a>Använda ett kundhanterat lagrings konto via en privat länk
##### <a name="workspace-requirements"></a>Krav på arbets yta
När du ansluter till Azure Monitor via en privat länk kan Log Analytics agenter bara skicka loggar till arbets ytor som är länkade till nätverket via en privat länk. Den här regeln kräver att du konfigurerar Azure Monitor ett AMPLS-objekt (Private Link scope) korrekt, ansluter det till dina arbets ytor och ansluter AMPLS till nätverket via en privat länk. Mer information om konfigurations proceduren för AMPLS finns i [använda Azure Private Link för att ansluta nätverk på ett säkert sätt till Azure Monitor](./private-link-security.md). 
##### <a name="storage-account-requirements"></a>Krav för lagringskonto
För att lagrings kontot ska kunna ansluta till din privata länk måste det:
* Finnas i ditt VNet eller i ett peer-nätverk och är anslutet till ditt VNet via en privat länk. Detta gör det möjligt för agenter på ditt VNet att skicka loggar till lagrings kontot.
* Finnas i samma region som arbets ytan som den är länkad till.
* Tillåt Azure Monitor åtkomst till lagrings kontot. Om du väljer att endast tillåta Välj nätverk att komma åt ditt lagrings konto bör du välja undantaget: "Tillåt betrodda Microsoft-tjänster för att få åtkomst till det här lagrings kontot".
![Avbildning av lagrings konto förtroende MS Services](./media/private-storage/storage-trust.png)
* Om din arbets yta även hanterar trafik från andra nätverk bör du konfigurera lagrings kontot så att inkommande trafik kommer från relevanta nätverk/Internet.

##### <a name="link-your-storage-account-to-a-log-analytics-workspace"></a>Länka ditt lagrings konto till en Log Analytics-arbetsyta
Du kan länka ditt lagrings konto till arbets ytan via [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) eller [REST API](/rest/api/loganalytics/linkedstorageaccounts). Tillämpliga dataSourceType-värden:
* CustomLogs – om du vill använda lagrings utrymmet för anpassade loggar och IIS-loggar under inmatning.
* AzureWatson – Använd lagringen för filer som laddats upp av ASC-lösningen (Azure Security Center). Mer information om hur du hanterar kvarhållning, ersätter ett länkat lagrings konto och övervakar din lagrings konto aktivitet finns i [Hantera länkade lagrings konton](#managing-linked-storage-accounts). 

## <a name="encrypting-data-with-cmk"></a>Kryptera data med CMK
Azure Storage krypterar alla data i vila i ett lagrings konto. Som standard krypterar den data med Microsoft-hanterade nycklar (MMK). Azure Storage kan dock i stället använda en kundhanterad nyckel (CMK) från Azure Key Vault för att kryptera dina lagrings data. Du kan antingen importera egna nycklar till Azure Key Vault, eller så kan du använda Azure Key Vault-API: er för att generera nycklar.
##### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>CMK-scenarier som kräver ett kundhanterat lagrings konto
* Kryptera logg – aviserings frågor med CMK
* Kryptera sparade frågor med CMK

### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Så här tillämpar du CMK på kund hanterade lagrings konton
##### <a name="storage-account-requirements"></a>Krav för lagringskonto
Lagrings kontot och nyckel valvet måste finnas i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Storage kryptering och nyckel hantering finns i [Azure Storage kryptering för data i vila](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Tillämpa CMK på dina lagrings konton
Om du vill konfigurera ditt Azure Storage-konto så att det använder Kundhanterade nycklar med Azure Key Vault använder du [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) eller [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="managing-linked-storage-accounts"></a>Hantera länkade lagrings konton

Om du vill länka eller ta bort länk till lagrings konton till arbets ytan använder du [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) eller [REST API](/rest/api/loganalytics/linkedstorageaccounts).

##### <a name="create-or-modify-a-link"></a>Skapa eller ändra en länk
När du länkar ett lagrings konto till en arbets yta börjar Log Analytics att använda det i stället för det lagrings konto som ägs av tjänsten. Du kan 
* Registrera flera lagrings konton för att sprida belastningen på loggar mellan dem
* Återanvänd samma lagrings konto för flera arbets ytor

##### <a name="unlink-a-storage-account"></a>Ta bort länk till ett lagrings konto
Om du vill sluta använda ett lagrings konto tar du bort länken till lagringen från arbets ytan. Om du tar bort länken för alla lagrings konton från en arbets yta innebär Log Analytics att de ska förlita sig på tjänst hanterade lagrings konton. Om nätverket har begränsad åtkomst till Internet, kanske dessa lagringar inte är tillgängliga och alla scenarier som förlitar sig på lagringen Miss lyckas.

##### <a name="replace-a-storage-account"></a>Ersätta ett lagrings konto
För att ersätta ett lagrings konto som används för inmatning,
1.  **Skapa en länk till ett nytt lagrings konto.** Loggnings agenterna kommer att hämta den uppdaterade konfigurationen och börja skicka data till den nya lagrings platsen. Processen kan ta några minuter.
2.  **Ta sedan bort länken till det gamla lagrings kontot så att agenter slutar skriva till det borttagna kontot.** Vid inmatnings processen sparas data från det här kontot tills allt har matats in. Ta inte bort lagrings kontot förrän du ser att alla loggar har matats in.

### <a name="maintaining-storage-accounts"></a>Underhålla lagrings konton
##### <a name="manage-log-retention"></a>Hantera logg kvarhållning
När du använder ditt eget lagrings konto är kvarhållning upp till dig. Med andra ord tar Log Analytics inte bort loggar som lagras i din privata lagring. I stället bör du konfigurera en princip för att hantera belastningen enligt dina inställningar.

##### <a name="consider-load"></a>Läs igenom
Lagrings konton kan hantera en viss belastning av Läs-och skriv förfrågningar innan de startar begränsnings begär Anden (se [skalbarhets-och prestanda mål för Blob Storage](../../storage/common/scalability-targets-standard-account.md) för mer information). Begränsningen påverkar hur lång tid det tar att mata in loggar. Om ditt lagrings konto är överbelastat registrerar du ett ytterligare lagrings konto för att sprida belastningen mellan dem. Du övervakar lagrings kontots kapacitet och prestanda genom att granska [insikter i Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Relaterade avgifter
Lagrings konton debiteras av volymen lagrade data, typen av lagring och typen av redundans. Mer information finns i avsnittet om [priser för Block-Blob](https://azure.microsoft.com/pricing/details/storage/blobs) och [Table Storage prissättning](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att [använda Azures privata länk för att ansluta nätverk på ett säkert sätt till Azure Monitor](private-link-security.md)
- Lär dig mer om att [Azure Monitor Kundhanterade nycklar](customer-managed-keys.md)
