---
title: Översikt över partner-onboarding (Azure Event Grid)
description: Innehåller en översikt över hur du kan registrera dig Event Grid partner.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 40d0afe0aaeb40412948eb304a36a3627566551b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869697"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Översikt över partner-onboarding (Azure Event Grid)

Den här artikeln beskriver hur du privat använder Azure Event Grid partnerresurser och hur du blir en offentligt tillgänglig partnerämnestyp.

Du behöver ingen särskild behörighet för att börja använda de Event Grid som är associerade med publiceringshändelser som en Event Grid partner. I själva verket kan du använda dem i dag för att publicera händelser privat till dina egna Azure-prenumerationer och testa resursmodellen om du överväger att bli en partner.

> [!NOTE]
> Stegvisa instruktioner om hur du kommer igång som Event Grid-partner med hjälp av Azure Portal finns i Registrera som [en Event Grid partner (Azure Portal).](onboard-partner.md) 

## <a name="how-partner-events-work"></a>Så här fungerar partnerhändelser
Funktionen Partnerhändelser använder den befintliga arkitektur som Event Grid redan använder för att publicera händelser från Azure-resurser, till exempel Azure Storage och Azure IoT Hub, och gör dessa verktyg offentligt tillgängliga för alla att använda. Att använda dessa verktyg är som standard endast privat för din Azure-prenumeration. Om du vill göra dina händelser offentligt tillgängliga fyller du i formuläret [och kontaktar Event Grid teamet](mailto:gridpartner@microsoft.com).

Med funktionen Partnerhändelser kan du publicera händelser till Azure Event Grid för användning i flera samma år.

## <a name="onboarding-and-event-publishing-overview"></a>Översikt över publicering av registrering och händelser

### <a name="partner-flow"></a>Partnerflöde

1. Skapa en Azure-klientorganisation om du inte redan har en.
1. Använd Azure CLI för att skapa en ny Event Grid `partnerRegistration` . Den här resursen innehåller information som visningsnamn, beskrivning, installations-URI och så vidare.

    ![Skapa ett partnerämne](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Skapa en eller flera partnernamnrymder i varje region där du vill publicera händelser. Tjänsten Event Grid till att skapa en publiceringsslutpunkt (till exempel `https://contoso.westus-1.eventgrid.azure.net/api/events` ) och åtkomstnycklar.

    ![Skapa ett partnernamnområde](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Ge kunderna ett sätt att registrera sig i systemet för att de vill ha ett partnerämne.
1. Kontakta Event Grid för att meddela att du vill att din typ av partnerämne ska bli offentlig.

### <a name="customer-flow"></a>Kundflöde

1. Kunden besöker den virtuella Azure Portal att notera det Azure-prenumerations-ID och den resursgrupp som de vill att partnerämnet ska skapas i.
1. Kunden begär ett partnerämne via ditt system. Som svar skapar du en händelsetunnel till ditt partnernamnområde.
1. Event Grid skapar ett **väntande** partnerämne i kundens Azure-prenumeration och resursgrupp.

    ![Skapa en händelsekanal](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Kunden aktiverar partnerämnet via Azure Portal. Händelser kan nu flöda från din tjänst till kundens Azure-prenumeration.

    ![Aktivera ett partnerämne](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Resursmodell
Följande resursmodell är för Partnerhändelser.

### <a name="partner-registrations"></a>Partnerregistreringar
* Resurs: `partnerRegistrations`
* Används av: Partner
* Beskrivning: Samlar in globala metadata för SaaS-partnern (programvara som en tjänst) (till exempel namn, visningsnamn, beskrivning, konfigurations-URI).
    
    Att skapa eller uppdatera en partnerregistrering är en självbetjäning för partnern. Den här självbetjäningsförmågan gör det möjligt för partner att skapa och testa det fullständiga flödet från slutet till slut.
    
    Endast Microsoft-godkända partnerregistreringar kan upptäckas av kunder.
* Omfång: Skapas i partnerns Azure-prenumeration. Metadata är synliga för kunder när de har gjorts offentliga.

### <a name="partner-namespaces"></a>Partnernamnrymder
* Resurs: `partnerNamespaces`
* Används av: Partner
* Beskrivning: Innehåller en regional resurs för publicering av kundhändelser till. Varje partnernamnområde har en publiceringsslutpunkt och autentiseringsnycklar. Namnområdet är också hur partnern begär ett partnerämne för en viss kund och visar en lista över aktiva kunder.
* Omfång: Finns i partnerns prenumeration.

### <a name="event-channel"></a>Händelsekanal
* Resurs: `partnerNamespaces/eventChannels`
* Används av: Partner
* Beskrivning: Händelsekanalerna är en spegling av kundens partnerämne. Genom att skapa en händelsekanal och ange kundens Azure-prenumeration och resursgrupp i metadata signalerar du till Event Grid för att skapa ett partnerämne för kunden. Event Grid ett Azure Resource Manager-anrop för att skapa ett motsvarande partnerämne i kundens prenumeration. Partnerämnet skapas i ett väntande tillstånd. Det finns en en-till-en-länk mellan varje händelsekanal och partnerämne.
* Omfång: Finns i partnerns prenumeration.

### <a name="partner-topics"></a>Partnerämnen
* Resurs: `partnerTopics`
* Används av: Kunder
* Beskrivning: Partnerämnen liknar anpassade ämnen och systemämnen i Event Grid. Varje partnerämne är associerat med en specifik källa (till exempel ) och `Contoso:myaccount` en specifik partnerämnestyp (till exempel Contoso). Kunder skapar händelseprenumerationer på partnerämnet för att dirigera händelser till olika händelsehanterare.

    Kunder kan inte skapa den här resursen direkt. Det enda sättet att skapa ett partnerämne är via en partneråtgärd som skapar en händelsekanal.
* Omfång: Finns i kundens prenumeration.

### <a name="partner-topic-types"></a>Typer av partnerämne
* Resurs: `partnerTopicTypes`
* Används av: Kunder
* Beskrivning: Typer av partnerämne är resurstyper för hela klientorganisationen som gör det möjligt för kunder att identifiera listan över godkända partnerämnestyper. URL:en ser ut så här: https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Omfång: Global

## <a name="publish-events-to-event-grid"></a>Publicera händelser till Event Grid
När du skapar ett partnernamnområde i en Azure-region får du en regional slutpunkt och motsvarande autentiseringsnycklar. Publicera batchar med händelser till den här slutpunkten för alla kundhändelsekanaler i det namnområdet. Baserat på källfältet i händelsen mappar Azure Event Grid varje händelse med motsvarande partnerämnen.

### <a name="event-schema-cloudevents-v10"></a>Händelseschema: CloudEvents v1.0
Publicera händelser för Azure Event Grid med hjälp av CloudEvents 1.0-schemat. Event Grid har stöd för både strukturerat läge och batchläge. CloudEvents 1.0 är det enda händelseschema som stöds för partnernamnområden.

### <a name="example-flow"></a>Exempelflöde

1.  Publiceringstjänsten gör en HTTP POST till `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
1.  I begäran inkluderar du ett huvudvärde med namnet aeg-sas-key som innehåller en nyckel för autentisering. Den här nyckeln etableras när partnernamnrymden skapas. Ett giltigt huvudvärde är till exempel aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
1.  Ange Content-Type-huvudet till "application/cloudevents-batch+json; charset=UTF-8a".
1.  Kör en HTTP POST-fråga till publicerings-URL:en med en batch med händelser som motsvarar den regionen. Exempel:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

När du har publicerar till partnernamnrymdens slutpunkt får du ett svar. Svaret är en vanlig HTTP-svarskod. Några vanliga svar är:

| Resultat                             | Svarsåtgärder              |
|------------------------------------|-----------------------|
| Klart                            | 200 OK                |
| Händelsedata har felaktigt format    | 400 – Felaktig begäran       |
| Ogiltig åtkomstnyckel                 | 401 – Ej behörig      |
| Felaktig slutpunkt                 | 404 – Hittades inte         |
| Matrisen eller händelsen överskrider storleksgränserna | 413 Nyttolasten är för stor |

## <a name="references"></a>Referenser

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM-mall](/azure/templates/microsoft.eventgrid/allversions)
  * [SCHEMA FÖR ARM-mall](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API:er](/azure/templates/microsoft.eventgrid/2020-04-01-preview/partnernamespaces)
  * [CLI-tillägg](/cli/azure/)

### <a name="sdks"></a>SDK:er
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [Js](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Kör](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Nästa steg
- [Översikt över partnerämnen](partner-events-overview.md)
- [Onboarding-formulär för partnerämnen](https://aka.ms/gridpartnerform)
- [Auth0-partnerämne](auth0-overview.md)
- [Använda Auth0-partnerämnet](auth0-how-to.md)
