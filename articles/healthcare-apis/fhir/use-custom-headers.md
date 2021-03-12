---
title: Lägga till data i gransknings loggar med anpassade huvuden – Azure API för FHIR
description: Den här artikeln beskriver hur du lägger till data i gransknings loggar med hjälp av anpassade HTTP-huvuden i Azure API för FHIR.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: 937be72bfec96119474e7effe9ba88a2cf253444
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019515"
---
# <a name="add-data-to-audit-logs-by-using-custom-http-headers"></a>Lägga till data i gransknings loggar med hjälp av anpassade HTTP-huvuden

I Azures FHIR-API (Azure fast sjukvård) kan en användare vilja inkludera ytterligare information i loggarna, som kommer från det anropande systemet.

Om till exempel användaren av API: t autentiseras av ett externt system vidarebefordrar det systemet anropet till FHIR-API: et. På FHIR API-skiktet har informationen om den ursprungliga användaren förlorats, eftersom anropet vidarebefordrades. Det kan vara nödvändigt att logga och behålla denna användar information för gransknings-eller hanterings syfte. Det anropande systemet kan tillhandahålla användar identitet, uppringnings plats eller annan nödvändig information i HTTP-huvudena som kommer att utföras när anropet vidarebefordras.

Du kan se det här data flödet i följande diagram:

:::image type="content" source="media/custom-headers/custom-headers-diagram.png" alt-text="Diagram över anpassade sidhuvuden":::

Du kan använda anpassade rubriker för att avbilda flera typer av information. Exempel:

* Information om identitet eller auktorisering
* Anroparens ursprung
* Ursprunglig organisation
* Klient system information (elektronisk hälso post, patient Portal)

> [!IMPORTANT]
> Tänk på att informationen som skickas i anpassade sidhuvuden lagras i ett internt Microsoft-loggnings system i 30 dagar efter att den är tillgänglig i Azure logg övervakning. Vi rekommenderar att du krypterar all information innan du lägger till den i anpassade sidhuvuden. Du bör inte skicka någon PHI-information via kund huvuden.

Du måste använda följande namngivnings konvention för dina HTTP-huvuden: X-MS-AZUREFHIR-AUDIT- \<name> .

Dessa HTTP-huvuden ingår i en egenskaps uppsättning som läggs till i loggen. Exempel:

* X-MS-AZUREFHIR-AUDIT-USERID: 1234 
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: XXXX
* X-MS-AZUREFHIR-AUDIT-XYZ: 1234

Den här informationen serialiseras sedan till JSON när den läggs till i kolumnen egenskaper i loggen. Exempel:

```json
{ "X-MS-AZUREFHIR-AUDIT-USERID" : "1234",
"X-MS-AZUREFHIR-AUDIT-USERLOCATION" : "XXXX",
"X-MS-AZUREFHIR-AUDIT-XYZ" : "1234" }
```
 
Som med valfritt HTTP-huvud kan samma rubrik namn upprepas med olika värden. Exempel:

* X-MS-AZUREFHIR-AUDIT-USERLOCATION: sjukhusa
* X-MS-AZUREFHIR-AUDIT-USERLOCATION: nödfall

När de läggs till i loggen kombineras värdena med en kommaavgränsad lista. Exempel:

{"X-MS-AZUREFHIR-AUDIT-USERLOCATION": "sjukhusa, nödfall"}
 
Du kan lägga till högst 10 unika huvuden (repetitioner av samma rubrik med olika värden räknas bara som en). Den totala maximala längden för värdet för en rubrik är 2048 tecken.

Om du använder Firefly C#-klientens API-bibliotek ser koden ut ungefär så här:

```C#
FhirClient client;
client = new FhirClient(serverUrl);
client.OnBeforeRequest += (object sender, BeforeRequestEventArgs e) =>
{
    // Add custom headers to be added to the logs
    e.RawRequest.Headers.Add("X-MS-AZUREFHIR-AUDIT-UserLocation", "HospitalA");
};
client.Get("Patient");
```
## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du lägger till data i gransknings loggar med hjälp av anpassade huvuden i Azure API för FHIR. Läs sedan mer om andra ytterligare inställningar som du kan konfigurera i Azure API för FHIR.
 
>[!div class="nextstepaction"]
>[Ytterligare inställningar](azure-api-for-fhir-additional-settings.md)
