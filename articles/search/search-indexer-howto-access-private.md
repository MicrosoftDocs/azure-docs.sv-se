---
title: Indexerare anslutningar via en privat slut punkt
titleSuffix: Azure Cognitive Search
description: Konfigurera indexerare-anslutningar för att komma åt innehåll från andra Azure-resurser som skyddas via en privat slut punkt.
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b30cb1767e733861d8418ea29e564bc90a5bc70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676517"
---
# <a name="make-indexer-connections-through-a-private-endpoint"></a>Gör indexerare anslutningar via en privat slut punkt

Många Azure-resurser, till exempel Azure Storage-konton, kan konfigureras för att godkänna anslutningar från en lista över virtuella nätverk och neka externa anslutningar som härstammar från ett offentligt nätverk. Om du använder en indexerare för att indexera data i Azure Kognitiv sökning och data källan finns i ett privat nätverk kan du skapa en utgående [privat slut punkts anslutning](../private-link/private-endpoint-overview.md) för att komma åt data.

Den här anslutnings metoden för indexerare omfattas av följande två krav:

+ Den Azure-resurs som innehåller innehåll eller kod måste registreras tidigare med [tjänsten Azure Private Link](https://azure.microsoft.com/services/private-link/).

+ Azure Kognitiv sökning-tjänsten måste finnas på Basic-nivån eller högre. Funktionen är inte tillgänglig på den kostnads fria nivån. Dessutom måste nivån vara standard 2 (S2) eller högre om indexeraren har en färdigheter. Mer information finns i [tjänst begränsningar](search-limits-quotas-capacity.md#shared-private-link-resource-limits).

## <a name="shared-private-link-resources-management-apis"></a>Delade privat länk resurser hanterings-API: er

Privata slut punkter för skyddade resurser som skapas via Azure Kognitiv sökning API: er kallas *delade privata länk resurser*. Detta beror på att du är "delning"-åtkomst till en resurs, till exempel ett lagrings konto, som har integrerats med [Azure Private Link-tjänsten](https://azure.microsoft.com/services/private-link/).

Med hjälp av hanterings REST API tillhandahåller Azure Kognitiv sökning en [CreateOrUpdate](/rest/api/searchmanagement/sharedprivatelinkresources/createorupdate) -åtgärd som du kan använda för att konfigurera åtkomst från en Azure kognitiv sökning-indexerare.

Du kan bara skapa privata slut punkts anslutningar till vissa resurser genom att använda för hands versionen av search Management-API: et (version *2020-08-01-Preview* eller senare), som är avsedd för *hands* version i följande tabell. Resurser utan för *hands* versions beteckning kan skapas med antingen för hands versionen eller en allmänt tillgänglig API-version (*2020-08-01* eller senare).

I följande tabell visas Azure-resurser för vilka du kan skapa utgående privata slut punkter från Azure Kognitiv sökning. Om du vill skapa en delad privat länk resurs anger du **gruppens ID-** värden exakt så som de skrivs i API: et. Värdena är Skift läges känsliga.

| Azure-resurs | Grupp-ID |
| --- | --- |
| Azure Storage-BLOB (eller) ADLS gen 2 | `blob`|
| Azure Storage-tabeller | `table`|
| Azure Cosmos DB-SQL API | `Sql`|
| Azure SQL Database | `sqlServer`|
| Azure Database for MySQL (för hands version) | `mysqlServer`|
| Azure Key Vault | `vault` |
| Azure Functions (för hands version) | `sites` |

Du kan också fråga de Azure-resurser för vilka utgående anslutningar för privata slut punkter stöds genom att använda [listan över API: er som stöds](/rest/api/searchmanagement/privatelinkresources/listsupported).

I resten av den här artikeln är en blandning av [Azure CLI](/cli/azure/) (eller [ARMClient](https://github.com/projectkudu/ARMClient) om du föredrar) och [Postman](https://www.postman.com/) (eller någon annan http-klient som [vändning](https://curl.se/) om du föredrar) som används för att demonstrera REST API samtal.

> [!NOTE]
> Exemplen i den här artikeln baseras på följande antaganden:
> * Namnet på Sök tjänsten är _contoso-search_, som finns i _contoso_ -resurs gruppen för en prenumeration med prenumerations-ID _00000000-0000-0000-0000-000000000000_. 
> * Resurs-ID för den här Sök tjänsten är _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.search/searchServices/contoso-search_.

Resten av exemplen visar hur tjänsten _contoso-search_ kan konfigureras så att dess indexerare kan komma åt data från _/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-Storage_ för säker lagrings konto.

## <a name="secure-your-storage-account"></a>Skydda ditt lagrings konto

Konfigurera lagrings kontot så att [endast åtkomst från vissa undernät tillåts](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network). Om du väljer det här alternativet i Azure Portal och lämnar inställningen tom, innebär det att ingen trafik från virtuella nätverk tillåts.

   ![Skärm bild av fönstret "brand väggar och virtuella nätverk" som visar alternativet för att tillåta åtkomst till valda nätverk. ](media\search-indexer-howto-secure-access\storage-firewall-noaccess.png)

> [!NOTE]
> Du kan använda den [betrodda Microsoft-lösningen](../storage/common/storage-network-security.md#trusted-microsoft-services) för att kringgå virtuella nätverk eller IP-begränsningar för ett lagrings konto. Du kan också aktivera Sök tjänsten för att komma åt data i lagrings kontot. För att göra det, se [indexerare åtkomst till Azure Storage med undantag för betrott tjänst](search-indexer-howto-access-trusted-service-exception.md). 
>
> Men när du använder den här metoden sker kommunikationen mellan Azure Kognitiv sökning och ditt lagrings konto via den offentliga IP-adressen för lagrings kontot, över det säkra Microsoft stamnät nätverket.

### <a name="step-1-create-a-shared-private-link-resource-to-the-storage-account"></a>Steg 1: skapa en delad privat länk resurs till lagrings kontot

Om du vill begära Azure Kognitiv sökning att skapa en utgående privat slut punkts anslutning till lagrings kontot, gör du följande API-anrop, till exempel med [Azure CLI](/cli/azure/): 

`az rest --method put --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 --body @create-pe.json`

Eller om du föredrar att använda [ARMClient](https://github.com/projectkudu/ARMClient):

`armclient PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01 create-pe.json`

Innehållet i *create-pe.jsi* filen, som representerar begär ande texten till API: et, är följande:

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve"
      }
}
```

Ett `202 Accepted` svar returnerades vid lyckad åtgärd. Processen för att skapa en utgående privat slut punkt är en tids krävande (asynkron) åtgärd. Det innebär att du distribuerar följande resurser:

* En privat slut punkt som tilldelas med en privat IP-adress i ett `"Pending"` tillstånd. Den privata IP-adressen hämtas från det adress utrymme som har allokerats till det virtuella nätverket i körnings miljön för Sök tjänstens angivna privata indexerare. Vid godkännande av den privata slut punkten kommer all kommunikation från Azure Kognitiv sökning till lagrings kontot från den privata IP-adressen och en säker kanal för privata länkar.

* En privat DNS-zon för resurs typen, baserat på `groupId` . Genom att distribuera den här resursen ser du till att alla DNS-sökningar till den privata resursen använder den IP-adress som är associerad med den privata slut punkten.

Se till att ange rätt `groupId` för den typ av resurs som du skapar den privata slut punkten för. Eventuella matchnings fel resulterar i ett svarsmeddelande som inte lyckades.

Som i alla asynkrona Azure-åtgärder `PUT` returnerar anropet ett `Azure-AsyncOperation` huvud värde som ser ut ungefär så här:

`"Azure-AsyncOperation": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

Du kan söka i denna URI regelbundet för att hämta status för åtgärden. Innan du fortsätter rekommenderar vi att du väntar tills statusen för den delade privata länk resurs åtgärden har nått ett Terminal-tillstånd (det vill säga åtgärdens status har *slutförts*).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01`

Eller med ARMClient:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe/operationStatuses/08586060559526078782?api-version=2020-08-01"`

```json
{
    "status": "running" | "succeeded" | "failed"
}
```

### <a name="step-2a-approve-the-private-endpoint-connection-for-the-storage-account"></a>Steg 2a: Godkänn den privata slut punkts anslutningen för lagrings kontot

> [!NOTE]
> I det här avsnittet använder du Azure Portal för att gå igenom godkännande flödet för en privat slut punkt till lagringen. Alternativt kan du använda de [REST API](/rest/api/storagerp/privateendpointconnections) som är tillgängliga via Storage Resource Provider.
>
> Andra leverantörer, till exempel Azure Cosmos DB eller Azure SQL Server, erbjuder liknande API: er för Storage Resource Provider för hantering av privata slut punkts anslutningar.

1. I Azure Portal väljer du fliken **anslutningar för privata slut punkter** för ditt lagrings konto. När den asynkrona åtgärden har slutförts bör det finnas en begäran om en privat slut punkts anslutning med begär ande meddelandet från det tidigare API-anropet.

   ![Skärm bild av Azure Portal som visar fönstret "anslutningar för privata slut punkter".](media\search-indexer-howto-secure-access\storage-privateendpoint-approval.png)

1. Välj den privata slut punkt som Azure Kognitiv sökning skapat. I kolumnen **privat slut punkt** identifierar du den privata slut punkts anslutningen med namnet som har angetts i föregående API, väljer **Godkänn** och anger sedan ett lämpligt meddelande. Meddelande innehållet är inte signifikant. 

   Se till att den privata slut punkts anslutningen visas som visas på följande skärm bild. Det kan ta en till två minuter innan statusen uppdateras i portalen.

   ![Skärm bild av Azure Portal som visar statusen "godkänd" i fönstret "anslutningar för privata slut punkter".](media\search-indexer-howto-secure-access\storage-privateendpoint-after-approval.png)

När den privata slut punkts anslutnings förfrågan har *godkänts kan trafiken* flöda genom den privata slut punkten. När den privata slut punkten har godkänts skapar Azure Kognitiv sökning nödvändiga mappningar för DNS-zon i den DNS-zon som skapats för den.

### <a name="step-2b-query-the-status-of-the-shared-private-link-resource"></a>Steg 2b: fråga efter status för den delade privata länk resursen

Kontrol lera att den delade privata länk resursen har uppdaterats efter godkännande genom att hämta dess status med hjälp av [Get-API: et](/rest/api/searchmanagement/sharedprivatelinkresources/get).

`az rest --method get --uri https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Eller med ARMClient:

`armclient GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Search/searchServices/contoso-search/sharedPrivateLinkResources/blob-pe?api-version=2020-08-01`

Om `properties.provisioningState` resursen är `Succeeded` och `properties.status` är `Approved` , innebär det att den delade privata länk resursen fungerar och att indexeraren kan konfigureras för att kommunicera över den privata slut punkten.

```json
{
      "name": "blob-pe",
      "properties": {
        "privateLinkResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Storage/storageAccounts/contoso-storage",
        "groupId": "blob",
        "requestMessage": "please approve",
        "status": "Approved",
        "resourceRegion": null,
        "provisioningState": "Succeeded"
      }
}

```

### <a name="step-3-configure-the-indexer-to-run-in-the-private-environment"></a>Steg 3: Konfigurera indexeraren att köras i den privata miljön

> [!NOTE]
> Du kan utföra det här steget innan anslutningen till den privata slut punkten godkänns. Tills den privata slut punkts anslutningen har godkänts, kommer alla indexerare som försöker kommunicera med en säker resurs (till exempel lagrings kontot) att bli i ett tillfälligt haveri läge. Det går inte att skapa nya indexerare. Så snart anslutningen till den privata slut punkten har godkänts kan indexerare komma åt det privata lagrings kontot.

1. [Skapa en data källa](/rest/api/searchservice/create-data-source) som pekar på det säkra lagrings kontot och en lämplig behållare i lagrings kontot. Följande skärm bild visar den här begäran i Postman.

   ![Skärm bild som visar hur en data källa skapas i Postman-användargränssnittet.](media\search-indexer-howto-secure-access\create-ds.png )

1. [Skapa ett index](/rest/api/searchservice/create-index) och om du vill kan du även [skapa en färdigheter](/rest/api/searchservice/create-skillset) med hjälp av REST API.

1. [Skapa en indexerare](/rest/api/searchservice/create-indexer) som pekar på data källan, indexet och färdigheter som du skapade i föregående steg. Dessutom tvingar indexeraren att köras i den privata körnings miljön genom att ställa in `executionEnvironment` konfigurations egenskapen indexerare till `private` .

   ![Skärm bild som visar hur en indexerare skapas i Postman-användargränssnittet.](media\search-indexer-howto-secure-access\create-idr.png)

   När indexeraren har skapats måste den börja indexera innehåll från lagrings kontot via anslutningen till den privata slut punkten. Du kan övervaka status för indexeraren med hjälp av API: [t för indexerings status](/rest/api/searchservice/get-indexer-status).

> [!NOTE]
> Om du redan har befintliga indexerare kan du uppdatera dem via [API: et för placering](/rest/api/searchservice/create-indexer) genom att ställa in på `executionEnvironment` `private` .

## <a name="troubleshooting"></a>Felsökning

- Om det inte går att skapa indexeraren med ett fel meddelande som "autentiseringsuppgifter för data källa är ogiltiga" innebär det att antingen statusen för den privata slut punkts anslutningen inte har *godkänts* än eller att anslutningen inte fungerar. För att åtgärda problemet: 
  * Hämta statusen för den delade privata länk resursen med hjälp av [Get-API: et](/rest/api/searchmanagement/sharedprivatelinkresources/get). Om statusen är *godkänd* kontrollerar du `properties.provisioningState` för resursen. Om statusen här är `Incomplete` innebär det att vissa underliggande beroenden för resursen inte kunde konfigureras. `PUT`Åtgärda problemet genom att utfärda begäran för att återskapa den delade privata länk resursen. Ett omgodkännande kan vara nödvändigt. Kontrol lera statusen för resursen på nytt för att kontrol lera att problemet är löst.

- Om du skapar indexeraren utan att ange dess `executionEnvironment` egenskap kan skapandet lyckas, men körnings historiken visar att indexeraren körs Miss lyckas. För att åtgärda problemet:
   * [Uppdatera indexeraren](/rest/api/searchservice/update-indexer) för att ange körnings miljön.

- Om du har skapat indexeraren utan att ange `executionEnvironment` egenskapen och den körs utan problem, innebär det att Azure kognitiv sökning har beslutat att dess körnings miljö är den Sök tjänst som är speciell *privat* miljö. Detta kan ändras, beroende på vilka resurser som förbrukas av indexeraren, belastningen på Sök tjänsten och andra faktorer, och det kan gå sönder senare. För att åtgärda problemet:
  * Vi rekommenderar starkt att du ställer in `executionEnvironment` egenskapen så att `private` den inte kan köras i framtiden.

[Kvoter och gränser](search-limits-quotas-capacity.md) avgör hur många delade privata länk resurser som kan skapas och är beroende av SKU: n för Sök tjänsten.

## <a name="next-steps"></a>Nästa steg

Läs mer om privata slut punkter:

- [Vad är privata slut punkter?](../private-link/private-endpoint-overview.md)
- [DNS-konfigurationer som behövs för privata slut punkter](../private-link/private-endpoint-dns.md)