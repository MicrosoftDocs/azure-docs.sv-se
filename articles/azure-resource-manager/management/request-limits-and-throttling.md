---
title: Begärandebegränsningar och begränsningar
description: Beskriver hur du använder begränsning med Azure Resource Manager begär anden när prenumerations gränserna har nåtts.
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: seodec18
ms.openlocfilehash: c1fd707bf9f13ed88bf9acb5d2ad70d56c453a90
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024633"
---
# <a name="throttling-resource-manager-requests"></a>Begränsning av Resource Manager-förfrågningar

I den här artikeln beskrivs hur Azure Resource Manager begränsnings begär Anden. Det visar hur du spårar antalet begär Anden som återstår innan gränsen nås och hur du svarar när du har nått gränsen.

Begränsning sker på två nivåer. Azure Resource Manager begränsar begär Anden för prenumerationen och klienten. Om begäran är under begränsnings gränserna för prenumerationen och klient organisationen dirigerar Resource Manager begäran till resurs leverantören. Resurs leverantören tillämpar begränsnings begränsningar som är skräddarsydda för dess åtgärder. Följande bild visar hur begränsningen används som en begäran går från användaren till Azure Resource Manager och resurs leverantören.

![Begär begränsning](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>Prenumerations-och klient gränser

Alla åtgärder på prenumerations nivå och klient nivå är föremål för begränsning av gränser. Prenumerations förfrågningar är sådana som innebär att du skickar ditt prenumerations-ID, till exempel att hämta resurs grupperna i din prenumeration. Klient begär Anden omfattar inte ditt prenumerations-ID, t. ex. att ta emot giltiga Azure-platser.

Standard begränsningarna för begränsning per timme visas i följande tabell.

| Omfång | Operations | Gräns |
| ----- | ---------- | ------- |
| Prenumeration | databasläsningar | 12000 |
| Prenumeration | text | 15 000 |
| Prenumeration | skriver | 1200 |
| Klientorganisation | databasläsningar | 12000 |
| Klientorganisation | skriver | 1200 |

De här gränserna gäller det säkerhetsobjekt (användare eller program) som skickar förfrågningarna och prenumerationens eller klientorganisationens ID. Om dina förfrågningar kommer från fler än ett säkerhetsobjekt är begränsningen för prenumerationen eller klientorganisationen högre än 12 000 respektive 1 200 per timme.

Dessa gränser gäller för varje Azure Resource Manager instans. Det finns flera instanser i varje Azure-region och Azure Resource Manager distribueras till alla Azure-regioner.  I praktiken är gränserna högre än dessa gränser. Begär Anden från en användare hanteras vanligt vis av olika instanser av Azure Resource Manager.

## <a name="resource-provider-limits"></a>Resource Provider-gränser

Resurs leverantörer tillämpar egna begränsnings gränser. Eftersom resurs hanterarens begränsningar efter ägar-ID och en instans av Resource Manager, kan det hända att resurs leverantören får fler förfrågningar än standard gränserna i föregående avsnitt.

I det här avsnittet beskrivs begränsnings gränserna för vissa vanliga resurs leverantörer.

### <a name="storage-throttling"></a>Lagringsbegränsning

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>Nätverksbegränsning

Microsoft. Network Resource-providern tillämpar följande begränsningar:

| Åtgärd | Gräns |
| --------- | ----- |
| Skriv/ta bort (placering) | 1000 per 5 minuter |
| läsa (Hämta) | 10000 per 5 minuter |

### <a name="compute-throttling"></a>Beräknings begränsning

Information om hur du begränsar gränserna för beräknings åtgärder finns i [FELSÖKA API begränsnings fel – beräkna](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors).

Använd [Virtual Machine Scale Sets åtgärder](/rest/api/compute/virtualmachinescalesetvms)för att kontrol lera virtuella dator instanser i en skalnings uppsättning för virtuella datorer. Använd till exempel [virtuella datorer för skalnings uppsättning för virtuella datorer – lista](/rest/api/compute/virtualmachinescalesetvms/list) med parametrar för att kontrol lera energi läget för virtuella dator instanser. Detta API minskar antalet begär Anden.

### <a name="azure-resource-graph-throttling"></a>Begränsning av Azure-resurs diagram

[Azure Resource Graph](../../governance/resource-graph/overview.md) begränsar antalet begär anden till åtgärder. Stegen i den här artikeln för att fastställa de återstående förfrågningarna och hur du svarar när gränsen nås gäller även för resurs diagram. Resurs diagram anger dock sin egen gräns och återställnings takt. Mer information finns i avsnittet om [begränsnings rubriker för resurs diagram](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers).

### <a name="other-resource-providers"></a>Andra resurs leverantörer

Information om begränsning i andra resurs leverantörer finns i:

* [Riktlinjer för begränsning i Azure Key Vault](../../key-vault/general/overview-throttling.md)
* [AKS-felsökning](../../aks/troubleshooting.md#im-receiving-429---too-many-requests-errors)

## <a name="error-code"></a>Felkod

När du når gränsen får du HTTP-statuskoden **429 för många begär Anden**. Svaret innehåller ett värde för **återförsök efter** , som anger antalet sekunder som programmet ska vänta (eller vila) innan nästa förfrågan skickas. Om du skickar en begäran innan värdet för återförsök har förflutit, bearbetas inte din begäran och ett nytt värde för återförsök returneras.

När du har väntat den angivna tiden kan du också stänga och öppna anslutningen till Azure igen. Genom att återställa anslutningen kan du ansluta till en annan instans av Azure Resource Manager.

Om du använder en Azure SDK kan SDK: n ha en automatisk omförsöks konfiguration. Mer information finns i [vägledning för återförsök för Azure-tjänster](/azure/architecture/best-practices/retry-service-specific).

Vissa resurs leverantörer returnerar 429 för att rapportera ett tillfälligt problem. Problemet kan vara ett överbelastnings tillstånd som inte direkt orsakas av din begäran. Eller så kan det representera ett tillfälligt fel meddelande om status för mål resursen eller beroende resursen. Nätverks resurs leverantören returnerar till exempel 429 med **RetryableErrorDueToAnotherOperation** -felkoden när mål resursen är låst av en annan åtgärd. Ta reda på om felet kommer från begränsning eller ett tillfälligt tillstånd genom att visa fel informationen i svaret.

## <a name="remaining-requests"></a>Återstående förfrågningar

Du kan fastställa antalet återstående förfrågningar genom att undersöka svarshuvuden. Läs begär Anden returnerar ett värde i rubriken för antalet återstående Läs begär Anden. Skriv förfrågningar innehåller ett värde för antalet återstående Skriv förfrågningar. I följande tabell beskrivs de svarshuvuden som du kan undersöka för dessa värden:

| Svars huvud | Beskrivning |
| --- | --- |
| x-MS-ratelimit-återstående-prenumeration-läsningar |Återstående läsning av prenumerations omfång. Det här värdet returneras vid Läs åtgärder. |
| x-MS-ratelimit-återstående – prenumeration-skrivningar |Prenumerationens omfångs skrivningar är kvar. Det här värdet returneras vid Skriv åtgärder. |
| x-MS-ratelimit-rest-Tenant-läsningar |Återstående läsningar av klient organisations område |
| x-MS-ratelimit-rest-Tenant-skrivningar |Återstående skrivningar för klient omfånget |
| x-MS-ratelimit-återstående – prenumeration-resurs-begär Anden |Prenumerationens omfattning av resurs typ begär Anden återstår.<br /><br />Detta huvud värde returneras endast om en tjänst har åsidosatt standard gränsen. Resource Manager lägger till det här värdet i stället för prenumerationen läser eller skriver. |
| x-MS-ratelimit-återstående – prenumeration-Resource-entities-läsa |Prenumerationens omfattning av resurs typs samlings begär Anden återstår.<br /><br />Detta huvud värde returneras endast om en tjänst har åsidosatt standard gränsen. Det här värdet anger antalet återstående samlings begär Anden (list resurser). |
| x-MS-ratelimit-återstående-klient-resurs-begär Anden |Klient omfånget resurs typ begär Anden återstår.<br /><br />Den här rubriken läggs bara till för begär Anden på klient nivå och endast om en tjänst har åsidosatt standard gränsen. Resource Manager lägger till det här värdet i stället för klienten som läser eller skriver. |
| x-MS-ratelimit-rest-Tenant-Resource-entities-Read |Klient delens webbprogramsomfattande resurs typ samling begär Anden återstår.<br /><br />Den här rubriken läggs bara till för begär Anden på klient nivå och endast om en tjänst har åsidosatt standard gränsen. |

Resurs leverantören kan även returnera svarshuvuden med information om återstående förfrågningar. Information om svarshuvuden som returneras av Compute Resource providern finns i svarshuvuden för [anrops frekvens information](/troubleshoot/azure/virtual-machines/troubleshooting-throttling-errors#call-rate-informational-response-headers).

## <a name="retrieving-the-header-values"></a>Hämtar rubrik värden

Att hämta dessa rubrik värden i din kod eller ditt skript skiljer sig inte från att hämta något huvud värde. 

I **C#** kan du till exempel hämta huvudet från ett **HttpWebResponse** -objekt med namnet **Response** med följande kod:

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

I **PowerShell** hämtar du huvud-värdet från en Invoke-WebRequest-åtgärd.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

Ett fullständigt PowerShell-exempel finns i [kontrol lera Resource Manager-gränser för en prenumeration](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).

Om du vill se återstående begär Anden för fel sökning kan du ange parametern **-Debug** i **PowerShell** -cmdleten.

```powershell
Get-AzResourceGroup -Debug
```

Detta returnerar många värden, inklusive följande svars värde:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

Använd en Skriv åtgärd för att få Skriv gränser: 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

Detta returnerar många värden, inklusive följande värden:

```output
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

I **Azure CLI** hämtar du huvud-värdet med hjälp av mer utförligt alternativ.

```azurecli
az group list --verbose --debug
```

Detta returnerar många värden, inklusive följande värden:

```output
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

Använd en Skriv åtgärd för att få Skriv gränser: 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

Detta returnerar många värden, inklusive följande värden:

```output
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>Nästa steg

* Ett fullständigt PowerShell-exempel finns i [kontrol lera Resource Manager-gränser för en prenumeration](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI).
* Mer information om gränser och kvoter finns i [Azure-prenumerationer, tjänst gränser, kvoter och begränsningar](../../azure-resource-manager/management/azure-subscription-service-limits.md).
* Information om hur du hanterar asynkrona REST-begäranden finns i [spåra asynkrona Azure-åtgärder](async-operations.md).