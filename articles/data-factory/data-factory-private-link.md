---
title: Azure Private Link för Azure Data Factory
description: Lär dig mer om hur Azures privata länkar fungerar i Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/01/2020
ms.openlocfilehash: a1b5ba56d30124bea7a814c2ffcf0cfff28903aa
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062196"
---
# <a name="azure-private-link-for-azure-data-factory"></a>Azure Private Link för Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

Med hjälp av en privat Azure-länk kan du ansluta till olika plattformar som en tjänst (PaaS) distributioner i Azure via en privat slut punkt. En privat slut punkt är en privat IP-adress inom ett särskilt virtuellt nätverk och undernät. En lista över PaaS-distributioner som stöder funktionen för privat länk finns i [dokumentationen för privat länk](../private-link/index.yml). 

## <a name="secure-communication-between-customer-networks-and-azure-data-factory"></a>Säker kommunikation mellan kund nätverk och Azure Data Factory 
Du kan konfigurera ett virtuellt Azure-nätverk som en logisk representation av ditt nätverk i molnet. Detta ger följande fördelar:
* Du hjälper till att skydda dina Azure-resurser mot attacker i offentliga nätverk.
* Du låter nätverken och Data Factory kommunicera på ett säkert sätt med varandra. 

Du kan också ansluta ett lokalt nätverk till ditt virtuella nätverk genom att konfigurera en Internet Protocol security (IPsec) VPN-anslutning (plats-till-plats) eller en Azure-ExpressRoute (privat peering). 

Du kan också installera en integration runtime med egen värd på en lokal dator eller en virtuell dator i det virtuella nätverket. På så sätt kan du:
* Kör kopierings aktiviteter mellan ett moln data lager och ett data lager i ett privat nätverk.
* Skicka transformerings aktiviteter mot beräknings resurser i ett lokalt nätverk eller i ett virtuellt Azure-nätverk. 

Flera kommunikations kanaler krävs mellan Azure Data Factory och det virtuella kund nätverket, som du ser i följande tabell:

| Domain | Port | Description |
| ---------- | -------- | --------------- |
| `adf.azure.com` | 443 | Ett kontroll plan, som krävs för Data Factory redigering och övervakning. |
| `*.{region}.datafactory.azure.net` | 443 | Krävs av integration runtime med egen värd för att ansluta till tjänsten Data Factory. |
| `*.servicebus.windows.net` | 443 | Krävs av integration runtime med egen värd för interaktiv redigering. |
| `download.microsoft.com` | 443 | Krävs av den egna värdbaserade integrerings körningen för att ladda ned uppdateringarna. |

Med stöd för privat länk för Azure Data Factory kan du:
* Skapa en privat slut punkt i det virtuella nätverket.
* Aktivera den privata anslutningen till en speciell Data Factory-instans. 

Kommunikationen med att Azure Data Factory tjänsten går via en privat länk och ger säker privat anslutning. 

![Diagram över privat länk för Azure Data Factory arkitektur.](./media/data-factory-private-link/private-link-architecture.png)

Att aktivera tjänsten för privata Länkar för var och en av de föregående kommunikations kanalerna har följande funktioner:
- **Stöds**:
   - Du kan skapa och övervaka data fabriken i det virtuella nätverket, även om du blockerar all utgående kommunikation.
   - Kommando kommunikationen mellan den lokala integrerings körningen och tjänsten Azure Data Factory kan utföras på ett säkert sätt i en privat nätverks miljö. Trafiken mellan den lokala integrerings körningen och Azure Data Factory tjänsten går via en privat länk. 
- **Stöds inte för närvarande**:
   - Interaktiv redigering som använder en lokal integrerings körning, till exempel testa anslutning, bläddra i Mapplista och tabell lista, Hämta schema och förhandsgranska data, går via en privat länk.
   - Den nya versionen av den egna värdbaserade integrerings körningen kan hämtas automatiskt från Microsoft Download Center om du aktiverar automatisk uppdatering.

   > [!NOTE]
   > För funktioner som inte stöds för närvarande måste du fortfarande konfigurera den tidigare nämnda domänen och porten i det virtuella nätverket eller företagets brand vägg. 

   > [!NOTE]
   > Anslutning till Azure Data Factory via privat slut punkt gäller endast för integration runtime med egen värd i Data Factory. Det stöds inte i Synapse.

> [!WARNING]
> När du skapar en länkad tjänst ser du till att dina autentiseringsuppgifter lagras i ett Azure Key Vault. Annars fungerar inte autentiseringsuppgifterna när du aktiverar privat länk i Azure Data Factory.

## <a name="dns-changes-for-private-endpoints"></a>DNS-ändringar för privata slut punkter
När du skapar en privat slut punkt uppdateras DNS CNAME-resursposten för Data Factory till ett alias i en under domän med prefixet "privatelink". Som standard skapar vi också en [privat DNS-zon](../dns/private-dns-overview.md)som motsvarar under domänen "privatelink" med DNS a-resursposter för de privata slut punkterna.

När du löser slut punkts-URL: en för Data Factory från en plats utanför VNet med den privata slut punkten, matchas den mot den offentliga slut punkten för Data Factory-tjänsten. Vid matchning från det VNet som är värd för den privata slut punkten matchas slut punktens URL-adress till den privata slut punktens IP-adress.

För det illustrerat exemplet ovan kommer DNS-resursposterna för Data Factory ' DataFactoryA ', när de löses från utanför det virtuella nätverk som är värd för den privata slut punkten, att vara:

| Namn | Typ | Värde |
| ---------- | -------- | --------------- |
| DataFactoryA. {region}. DataFactory. Azure. net | CNAME   | DataFactoryA. {region}. privatelink. DataFactory. Azure. net |
| DataFactoryA. {region}. privatelink. DataFactory. Azure. net | CNAME   | Den offentliga slut punkten för < Data Factory-tjänst > |
| Den offentliga slut punkten för < Data Factory-tjänst >  | A | Den offentliga IP-adressen för < Data Factory-tjänsten > |

DNS-resursposterna för DataFactoryA, när de löses i det VNet som är värd för den privata slut punkten, blir:

| Namn | Typ | Värde |
| ---------- | -------- | --------------- |
| DataFactoryA. {region}. DataFactory. Azure. net | CNAME   | DataFactoryA. {region}. privatelink. DataFactory. Azure. net |
| DataFactoryA. {region}. privatelink. DataFactory. Azure. net   | A | IP-adress för < privat slutpunkt > |

Om du använder en anpassad DNS-server i nätverket måste klienterna kunna matcha FQDN för Data Factory slut punkten till den privata slut punktens IP-adress. Du bör konfigurera DNS-servern så att den delegerar din privata länk under domän till den privata DNS-zonen för det virtuella nätverket, eller konfigurera A-poster för ' DataFactoryA. {region}. privatelink. DataFactory. Azure. net med den privata slut punktens IP-adress.

Mer information om hur du konfigurerar en egen DNS-server för att stödja privata slut punkter finns i följande artiklar:
- [Namnmatchning för resurser i virtuella nätverk i Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)
- [DNS-konfiguration för privata slut punkter](../private-link/private-endpoint-overview.md#dns-configuration)


## <a name="set-up-private-link-for-azure-data-factory"></a>Konfigurera en privat länk för Azure Data Factory
Du kan skapa privata slut punkter med hjälp av [Azure Portal](../private-link/create-private-endpoint-portal.md).

Du kan välja om du vill ansluta den lokala integrerings körningen till Azure Data Factory via en offentlig slut punkt eller privat slut punkt. 

![Skärm bild som blockerar offentlig åtkomst till Integration Runtime med egen värd.](./media/data-factory-private-link/disable-public-access-shir.png)


Du kan också gå till Azure Data Factory i Azure Portal och skapa en privat slut punkt, som du ser här:

![Skärm bild av fönstret "privat slut punkts anslutningar" för att skapa en privat slut punkt.](./media/data-factory-private-link/create-private-endpoint.png)

I **resurs** steget väljer du **Microsoft. DataFactory/factors** as **resurs typ**. Och om du vill skapa en privat slut punkt för kommando kommunikation mellan den lokala integrerings körningen och Azure Data Factory tjänsten väljer du **DataFactory** som **mål under resurs**.

![Skärm bild av fönstret "privat slut punkts anslutningar" för att välja resurs.](./media/data-factory-private-link/private-endpoint-resource.png)

> [!NOTE]
> Inaktive ring av offentlig nätverks åtkomst gäller endast för integration runtime med egen värd, inte för att Azure Integration Runtime och SQL Server Integration Services (SSIS) Integration Runtime.

Om du vill skapa en privat slut punkt för att redigera och övervaka data fabriken i det virtuella nätverket väljer du **Portal** som **mål under resurs**.

> [!NOTE]
> Du kan fortfarande komma åt Azure Data Factory Portal via ett offentligt nätverk när du har skapat en privat slut punkt för portalen.

## <a name="next-steps"></a>Nästa steg

- [Skapa en data fabrik med hjälp av Azure Data Factory gränssnittet](quickstart-create-data-factory-portal.md)
- [Introduktion till Azure Data Factory](introduction.md)
- [Visuell redigering i Azure Data Factory](author-visually.md)