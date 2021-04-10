---
title: Använda kundhanterade lagringskonton i Azure Monitor-logganalys
description: Använd ditt eget lagrings konto för Log Analytics scenarier
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: a6d4c5811c08aa8c4de2eeea5f5f53967c3006b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025364"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Använda kundhanterade lagringskonton i Azure Monitor-logganalys

Log Analytics förlitar sig på Azure Storage i olika scenarier. Den här användningen hanteras vanligt vis automatiskt. Vissa fall kräver dock att du anger och hanterar ditt eget lagrings konto, även kallat ett kundhanterat lagrings konto. Det här dokumentet beskriver användningen av kundhanterad lagring för WAD/LAD-loggar, privat länk och CMK-kryptering (Customer Key). 

> [!NOTE]
> Vi rekommenderar att du inte tar ett beroende på innehållet Log Analytics uppladdningar till kundhanterad lagring, eftersom formatering och innehåll kan ändras.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Mata in Azure-diagnostik anknytnings loggar (WAD/LAD)
Azure-diagnostik tilläggs agenter (kallas även WAD och LAD för Windows respektive Linux-agenter) samlar in olika operativ system loggar och lagrar dem på ett kundhanterat lagrings konto. Du kan sedan mata in dessa loggar i Log Analytics för att granska och analysera dem.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Samla in Azure-diagnostik tilläggs loggar från ditt lagrings konto
Anslut lagrings kontot till din Log Analytics-arbetsyta som en lagrings data källa med hjälp av [Azure Portal](../agents/diagnostics-extension-logs.md#collect-logs-from-azure-storage) eller genom att anropa [API: et för Storage Insights](/rest/api/loganalytics/storage%20insights/createorupdate).

Data typer som stöds:
* Syslog
* Windows-händelser
* Service Fabric
* ETW-händelser
* IIS-loggar

## <a name="using-private-links"></a>Använda privata länkar
Kund hanterade lagrings konton används för att mata in anpassade loggar eller IIS-loggar när privata länkar används för att ansluta till Azure Monitor resurser. Vid inmatnings processen för dessa data typer laddas först upp loggar till ett mellanliggande Azure Storages konto och matas sedan in dem till en arbets yta. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Använda ett kundhanterat lagrings konto via en privat länk
#### <a name="workspace-requirements"></a>Krav på arbets yta
När du ansluter till Azure Monitor via en privat länk kan Log Analytics agenter bara skicka loggar till arbets ytor som är tillgängliga via en privat länk. Detta krav innebär att du bör:
* Konfigurera ett Azure Monitor-objekt (Private Link scope) AMPLS
* Anslut den till dina arbets ytor
* Anslut AMPLS till nätverket via en privat länk. 

Mer information om konfigurations proceduren för AMPLS finns i [använda Azure Private Link för att ansluta nätverk på ett säkert sätt till Azure Monitor](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Krav för lagringskonto
För att lagrings kontot ska kunna ansluta till din privata länk måste det:
* Finnas i ditt VNet eller ett peer-nätverk, och är anslutet till ditt VNet via en privat länk.
* Finnas i samma region som arbets ytan som den är länkad till.
* Tillåt Azure Monitor åtkomst till lagrings kontot. Om du väljer att endast tillåta Välj nätverk att komma åt ditt lagrings konto bör du välja undantaget: "Tillåt betrodda Microsoft-tjänster för att få åtkomst till det här lagrings kontot".
![Avbildning av lagrings konto förtroende MS Services](./media/private-storage/storage-trust.png)
* Om din arbets yta även hanterar trafik från andra nätverk bör du konfigurera lagrings kontot så att inkommande trafik kommer från relevanta nätverk/Internet.
* Koordinera TLS-versionen mellan agenterna och lagrings kontot – vi rekommenderar att du skickar data till Log Analytics med TLS 1,2 eller högre. Granska [plattformsspecifik vägledning](./data-security.md#sending-data-securely-using-tls-12)och [Konfigurera dina agenter att använda TLS 1,2 om det](../agents/agent-windows.md#configure-agent-to-use-tls-12)behövs. Om det av någon anledning inte är möjligt konfigurerar du lagrings kontot så att det accepterar TLS 1,0.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Använda ett kundhanterat lagrings konto för CMK data kryptering
Azure Storage krypterar alla data i vila i ett lagrings konto. Som standard använder den Microsoft-hanterade nycklar (MMK) för att kryptera data. Azure Storage kan dock också använda CMK från Azure Key Vault för att kryptera dina lagrings data. Du kan antingen importera egna nycklar till Azure Key Vault, eller så kan du använda Azure Key Vault-API: er för att generera nycklar.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>CMK-scenarier som kräver ett kundhanterat lagrings konto
* Kryptera logg – aviserings frågor med CMK
* Kryptera sparade frågor med CMK

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Så här tillämpar du CMK på kund hanterade lagrings konton
##### <a name="storage-account-requirements"></a>Krav för lagringskonto
Lagrings kontot och nyckel valvet måste finnas i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Storage kryptering och nyckel hantering finns i [Azure Storage kryptering för data i vila](../../storage/common/storage-service-encryption.md).

##### <a name="apply-cmk-to-your-storage-accounts"></a>Tillämpa CMK på dina lagrings konton
Om du vill konfigurera Azure Storage-kontot för att använda CMK med Azure Key Vault använder du [Azure Portal](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)eller [CLI](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json). 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Länka lagrings konton till din Log Analytics-arbetsyta
### <a name="using-the-azure-portal"></a>Använda Azure Portal
Öppna din arbets yta-meny på Azure Portal och välj *länkade lagrings konton*. Ett blad öppnas med de länkade lagrings kontona enligt de användnings fall som nämns ovan (inmatning via privat länk, användning av CMK till sparade frågor eller aviseringar).
![Blad avbildningen länkade lagrings konton om ](./media/private-storage/all-linked-storage-accounts.png) du markerar ett objekt i tabellen öppnas dess lagrings konto information, där du kan ange eller uppdatera det länkade lagrings kontot för den här typen. 
![Länka en blad avbildning för lagrings konton ](./media/private-storage/link-a-storage-account-blade.png) du kan använda samma konto för olika användnings fall om du föredrar det.

### <a name="using-the-azure-cli-or-rest-api"></a>Använda Azure CLI eller REST API
Du kan också länka ett lagrings konto till din arbets yta via [Azure CLI](/cli/azure/monitor/log-analytics/workspace/linked-storage) eller [REST API](/rest/api/loganalytics/linkedstorageaccounts).

Tillämpliga dataSourceType-värden är:
* CustomLogs – om du vill använda lagrings kontot för anpassade loggar och inmatning av IIS-loggar
* Fråga – Använd lagrings kontot för att lagra sparade frågor (krävs för CMK-kryptering)
* Aviseringar – att använda lagrings kontot för att lagra loggbaserade aviseringar (krävs för CMK-kryptering)


## <a name="managing-linked-storage-accounts"></a>Hantera länkade lagrings konton

### <a name="create-or-modify-a-link"></a>Skapa eller ändra en länk
När du länkar ett lagrings konto till en arbets yta börjar Log Analytics att använda det i stället för det lagrings konto som ägs av tjänsten. Du kan 
* Registrera flera lagrings konton för att sprida belastningen på loggar mellan dem
* Återanvänd samma lagrings konto för flera arbets ytor

### <a name="unlink-a-storage-account"></a>Ta bort länk till ett lagrings konto
Om du vill sluta använda ett lagrings konto tar du bort länken till lagringen från arbets ytan. Om du tar bort länken för alla lagrings konton från en arbets yta innebär Log Analytics att de ska förlita sig på tjänst hanterade lagrings konton. Om nätverket har begränsad åtkomst till Internet, kanske dessa lagringar inte är tillgängliga och alla scenarier som förlitar sig på lagringen Miss lyckas.

### <a name="replace-a-storage-account"></a>Ersätta ett lagrings konto
För att ersätta ett lagrings konto som används för inmatning,
1.  **Skapa en länk till ett nytt lagrings konto.** Loggnings agenterna kommer att hämta den uppdaterade konfigurationen och börja skicka data till den nya lagrings platsen. Processen kan ta några minuter.
2.  **Ta sedan bort länken till det gamla lagrings kontot så att agenter slutar skriva till det borttagna kontot.** Vid inmatnings processen sparas data från det här kontot tills allt har matats in. Ta inte bort lagrings kontot förrän du ser att alla loggar har matats in.

### <a name="maintaining-storage-accounts"></a>Underhålla lagrings konton
#### <a name="manage-log-retention"></a>Hantera logg kvarhållning
När du använder ditt eget lagrings konto är kvarhållning upp till dig. Log Analytics tar inte bort loggar som lagras i din privata lagring. I stället bör du konfigurera en princip för att hantera belastningen enligt dina inställningar.

#### <a name="consider-load"></a>Läs igenom
Lagrings konton kan hantera en viss belastning av Läs-och skriv förfrågningar innan de startar begränsnings begär Anden (mer information finns i [skalbarhets-och prestanda mål för Blob Storage](../../storage/common/scalability-targets-standard-account.md)). Begränsningen påverkar hur lång tid det tar att mata in loggar. Om ditt lagrings konto är överbelastat registrerar du ett ytterligare lagrings konto för att sprida belastningen mellan dem. Du övervakar lagrings kontots kapacitet och prestanda genom att granska [insikter i Azure Portal]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Relaterade avgifter
Lagrings konton debiteras av volymen lagrade data, typen av lagring och typen av redundans. Mer information finns i avsnittet om [priser för Block-Blob](https://azure.microsoft.com/pricing/details/storage/blobs) och [Table Storage prissättning](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att [använda Azures privata länk för att ansluta nätverk på ett säkert sätt till Azure Monitor](private-link-security.md)
- Lär dig mer om att [Azure Monitor Kundhanterade nycklar](../logs/customer-managed-keys.md)