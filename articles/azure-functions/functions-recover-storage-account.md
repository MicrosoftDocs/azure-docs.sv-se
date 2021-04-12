---
title: 'Fel söknings fel: Körmiljö för Azure Functions kan inte kontaktas'
description: Lär dig hur du felsöker ett ogiltigt lagrings konto.
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: 392882fc2f0394e61aee973a20479d8f1fa9bc92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606981"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>Fel söknings fel: "Körmiljö för Azure Functions kan inte kontaktas"

Den här artikeln hjälper dig att felsöka följande fel sträng som visas i Azure Portal:

> "Fel: Körmiljö för Azure Functions kan inte kontaktas. Klicka här om du vill ha mer information om lagrings konfiguration. "

Det här problemet uppstår när Functions-körningen inte kan starta. Den vanligaste orsaken till detta är att Function-appen har förlorat åtkomst till sitt lagrings konto. Mer information finns i [krav för lagrings konton](storage-considerations.md#storage-account-requirements).

Resten av den här artikeln hjälper dig att felsöka vissa orsaker till det här felet, inklusive hur du identifierar och löser varje ärende.

## <a name="storage-account-was-deleted"></a>Lagrings kontot har tagits bort

Alla Functions-appar kräver ett lagrings konto för att fungera. Om kontot tas bort fungerar inte dina funktioner.

Börja med att leta upp ditt lagrings konto namn i dina program inställningar. Antingen `AzureWebJobsStorage` eller `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` innehåller namnet på ditt lagrings konto som en del av en anslutnings sträng. Mer information finns i [referens för app-inställningar för Azure Functions](./functions-app-settings.md#azurewebjobsstorage).

Sök efter ditt lagrings konto i Azure Portal för att se om det fortfarande finns. Om den har tagits bort återskapar du lagrings kontot och ersätter lagrings anslutnings strängarna. Funktions koden förloras och du måste distribuera den igen.

## <a name="storage-account-application-settings-were-deleted"></a>Program inställningarna för lagrings kontot har tagits bort

I föregående steg, om du inte kan hitta någon anslutnings sträng för lagrings konton, har det förmodligen tagits bort eller skrivits över. Att ta bort program inställningar vanligaste inträffar när du använder distributions fack eller Azure Resource Manager skript för att ange program inställningar.

### <a name="required-application-settings"></a>Nödvändiga program inställningar

* Kunna
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* Krävs för funktioner i Premium plan:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

Mer information finns i [referens för app-inställningar för Azure Functions](./functions-app-settings.md).

### <a name="guidance"></a>Vägledning

* Kontrol lera inte **plats inställningen** för någon av dessa inställningar. Om du växlar distributions platser bryts funktionens app.
* Ändra inte inställningarna som en del av automatiserade distributioner.
* De här inställningarna måste tillhandahållas och vara giltiga vid skapande tillfället. En automatiserad distribution som inte innehåller de här inställningarna resulterar i en Function-app som inte körs, även om inställningarna läggs till senare.

## <a name="storage-account-credentials-are-invalid"></a>Autentiseringsuppgifterna för lagrings kontot är ogiltiga

De tidigare diskuterade lagrings kontots anslutnings strängar måste uppdateras om du återskapar lagrings nycklar. Mer information om hantering av lagrings nycklar finns i [skapa ett Azure Storage konto](../storage/common/storage-account-create.md).

## <a name="storage-account-is-inaccessible"></a>Lagrings kontot är inte tillgängligt

Din Function-app måste kunna komma åt lagrings kontot. Vanliga problem som blockerar en funktions programmets åtkomst till ett lagrings konto är:

* Function-appen distribueras till din App Service-miljön (ASE) utan rätt nätverks regler för att tillåta trafik till och från lagrings kontot.

* Lagrings kontots brand vägg är aktive rad och inte konfigurerad för att tillåta trafik till och från funktioner. Mer information finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
* Kontrol lera att `allowSharedKeyAccess` inställningen är inställd på `true` som är standardvärdet. Mer information finns i [förhindra auktorisering av delad nyckel för ett Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/shared-key-authorization-prevent?tabs=portal#verify-that-shared-key-access-is-not-allowed). 

## <a name="daily-execution-quota-is-full"></a>Kvoten för daglig körning är full

Om du har konfigurerat en daglig körnings kvot inaktive ras funktions appen tillfälligt, vilket gör att många av Portal kontrollerna blir otillgängliga. 

Om du vill verifiera kvoten i [Azure Portal](https://portal.azure.com)väljer du **plattforms funktioner**  >  **Funktionsapp inställningar** i din Function-app. Om du är över den **dagliga användnings kvoten** som du har angett visas följande meddelande:

  > "Funktionsapp har nått kvoten för daglig användning och har stoppats till nästa tidsram på 24 timmar."

Lös problemet genom att ta bort eller öka den dagliga kvoten och starta sedan om appen. Annars blockeras körningen av appen till nästa dag.

## <a name="app-is-behind-a-firewall"></a>Appen ligger bakom en brand vägg

Din Function-app kan vara oåtkomlig av någon av följande orsaker:

* Din Function-app finns i ett [internt belastningsutjämnad App Service-miljön](../app-service/environment/create-ilb-ase.md) som har kon figurer ATS för att blockera inkommande Internet trafik.

* Din Function-app har [inkommande IP-begränsningar](functions-networking-options.md#inbound-access-restrictions) som är konfigurerade för att blockera Internet åtkomst. 

Azure Portal gör anrop direkt till appen som körs för att hämta listan över funktioner, och det gör HTTP-anrop till kudu-slutpunkten. Inställningarna på plattforms nivå på fliken **plattforms funktioner** är fortfarande tillgängliga.

Så här verifierar du din ASE-konfiguration:
1. Gå till nätverks säkerhets gruppen (NSG) för under nätet där ASE finns.
1. Validera inkommande regler för att tillåta trafik som kommer från den offentliga IP-adressen för den dator där du använder programmet. 
   
Du kan också använda portalen från en dator som är ansluten till det virtuella nätverk som kör din app eller till en virtuell dator som körs i det virtuella nätverket. 

Mer information om konfiguration av inkommande regel finns i avsnittet "nätverks säkerhets grupper" i [nätverks överväganden för en app service-miljön](../app-service/environment/network-info.md#network-security-groups).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att övervaka dina funktions appar:

> [!div class="nextstepaction"]
> [Övervaka Azure Functions](functions-monitoring.md)
