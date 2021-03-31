---
title: Azure Schema Registry i Event Hubs (förhandsversion)
description: Den här artikeln innehåller en översikt över stöd för schema registret av Azure Event Hubs (för hands version).
ms.topic: overview
ms.date: 09/22/2020
ms.custom: references_regions
ms.openlocfilehash: a876651b76aa259754623854b8fc4a7c6c8a939e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92330503"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Azure Schema Registry i Event Hubs (förhandsversion)
I många händelse strömnings-och meddelande scenarier innehåller händelse-och meddelande nytto Last strukturerad data som antingen serialiseras eller avserialiseras med hjälp av ett schema drivet format som Apache Avro. Både avsändare och mottagare kan vilja verifiera integriteten för data med ett schema dokument som med JSON-schema. För schema drivna format är det ett krav för att konsumenten ska kunna deserialisera data för att göra schemat tillgängligt för meddelande konsumenten. 

**Azure schema Registry** är en funktion i Event Hubs, som tillhandahåller en central lagrings plats för schema dokument för händelse drivna och meddelandebaserade program. Det ger dig flexibiliteten för dina tillverkare och konsument program att utbyta data utan att behöva hantera och dela schemat mellan dem och även för att utveckla i olika takt. Schema registret ger också ett enkelt styrnings ramverk för återanvändbara scheman och definierar relationen mellan scheman genom en grupperings-konstruktion (schema grupper).

> [!NOTE]
> - **Schema register** funktionen är för närvarande en för **hands version** och rekommenderas inte för produktions arbets belastningar.
> - Funktionen är endast tillgänglig på **standard** -och **dedikerade** nivåer, inte i **Basic** -nivån.

Med schema drivna serialiserade ramverk som Apache Avro, kan externa serialiserings-metadata till delade scheman också hjälpa till att avsevärt minska antalet per meddelande för typ information och fält namn som ingår i varje data uppsättning som det är fallet med taggade format som JSON. Om du har scheman lagrade i intill händelserna och inifrån händelse infrastrukturen säkerställer du att de metadata som krävs för serialisering/deserialisering alltid finns i räckvidden och att scheman inte får plats. 

## <a name="event-hubs-namespace"></a>Event Hubs-namnområde
En Event Hubs namnrymd kan nu vara värd för schema grupper tillsammans med Event Hub (eller Kafka-ämnen). Den är värd för ett schema register och kan ha flera schema grupper. Trots att de finns i Azure Event Hubs, kan schema registret användas i universellt med alla Azure Messaging-tjänster och andra meddelande-eller händelse koordinatorer. Var och en av dessa schema grupper är en separat skydds plats för en uppsättning scheman. Grupper kan justeras med ett visst program eller en organisationsenhet. 

## <a name="schema-groups"></a>Schema grupper
Schema gruppen är en logisk grupp med liknande scheman baserat på dina affärs kriterier. En schema grupp kan innehålla flera versioner av ett schema. Inställningen för kompatibilitet för en schema grupp kan hjälpa till att se till att nyare schema versioner är bakåtkompatibla.

Den säkerhets gräns som införs av mekanismen hjälper till att säkerställa att affärs hemligheter inte oavsiktligt läcker genom metadata i situationer där namn området delas mellan flera partner. Det gör det också möjligt för program ägare att hantera scheman oberoende av andra program som delar samma namnrymd.


## <a name="schemas"></a>Scheman
Scheman definierar avtalet mellan producenterna och konsumenterna. Ett schema som definierats i ett Event Hubs schema register hjälper till att hantera kontraktet utanför händelse data, vilket innebär att nytto lasten tas bort. Ett schema har ett namn, en typ (exempel: post, array osv.), kompatibilitetsläge (ingen, vidarebefordra, baklänges, fullständig) och serialiserings typ (endast Avro för tillfället). Du kan skapa flera versioner av ett schema och hämta och använda en speciell version av ett schema. 

## <a name="client-sdks"></a>Klient-SDK: er
Du kan använda något av följande bibliotek som innehåller en Avro-serialiserare, som du kan använda för att serialisera och deserialisera nytto laster som innehåller schema-ID: n och Avro-kodade data.

- [.NET-Microsoft. Azure. data. SchemaRegistry. ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java-Azure-Data-schemaregistry-Avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python – Azure-schemaregistry – avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [Java @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/) -köra Kafka-integrerade Apache Avro-serialiseringar och deserialiseringar som backas upp av Azure schema Registry. Java-klientens Apache Kafka klientens serialisering för Azure schema Registry kan användas i alla Apache Kafka-scenarier och med alla Apache Kafka®-baserade distributioner eller moln tjänster. 

Följande bild visar informations flödet i schema registret med Event Hubs: 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="Flödes diagram":::

## <a name="standard-vs-dedicated-limits"></a>Standard kontra dedikerade gränser
För gränser (till exempel: antalet schema grupper i ett namn område) som är samma och olika för standard-och dedikerade nivåer av Event Hubs, se [schema register gränser](../azure-resource-manager/management/azure-subscription-service-limits.md#schema-registry-limitations)

## <a name="azure-role-based-access-control"></a>Rollbaserad Azure-åtkomstkontroll
När du har åtkomst till schema registret program mässigt måste du registrera ett program i Azure Active Directory (Azure AD) och lägga till programmets säkerhets objekt i någon av rollbaserade roller för Azure-rollbaserad åtkomst kontroll (Azure RBAC):

| Roll | Beskrivning | 
| ---- | ----------- | 
| Ägare | Läsa, skriva och ta bort schema register grupper och scheman. |
| Deltagare | Läsa, skriva och ta bort schema register grupper och scheman. |
| [Schema register läsare (för hands version)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Läs och Visa lista över schema register grupper och scheman. |
| [Schema register deltagare (för hands version)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Läsa, skriva och ta bort schema register grupper och scheman. |

Anvisningar om hur du skapar registrering av ett program med hjälp av Azure Portal finns i [Registrera en app med Azure AD](../active-directory/develop/quickstart-register-app.md). Anteckna klient-ID (program-ID), klient-ID och hemlighet som ska användas i koden. 

## <a name="next-steps"></a>Nästa steg

- Information om hur du skapar ett schema register med hjälp av Azure Portal finns i [skapa ett Event Hubs schema register med hjälp av Azure Portal](create-schema-registry.md).
- Se följande **schema register Avro-klient biblioteks** exempel.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Kafka Avro-integrering för Azure schema Registry](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
