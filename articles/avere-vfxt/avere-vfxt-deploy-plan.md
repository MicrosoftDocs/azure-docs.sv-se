---
title: Planera ditt AVERT vFXT-system – Azure
description: Planera ett AVERT vFXT för Azure-kluster som passar dina behov. Lär dig frågor innan du går till Azure Marketplace eller skapar virtuella datorer.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: e5c33144dcebe491757dfafb2879fe51545827ce
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272798"
---
# <a name="plan-your-avere-vfxt-system"></a>Planera för ditt Avere vFXT-system

I den här artikeln förklaras hur du planerar en ny vFXT för Azure-kluster som är placerade och lämplig storlek för dina behov.

Innan du går till Azure Marketplace eller skapar virtuella datorer bör du tänka på följande:

* Hur fungerar klustret med andra Azure-resurser?
* Var ska kluster element finnas i privata nätverk och undernät?
* Vilken typ av backend-lagring kommer du att använda och hur kommer klustret att få åtkomst till det?
* Hur kraftfulla måste dina klusternoder vara för att stödja ditt arbets flöde?

Läs vidare om du vill veta mer.

## <a name="learn-the-components-of-the-system"></a>Lär dig mer om systemets komponenter

Det kan vara bra att förstå komponenterna i AVERT vFXT för Azure-systemet när du börjar planera.

* Klusternoder – klustret består av tre eller flera virtuella datorer som har kon figurer ATS som klusternoder. Fler noder ger systemet högre data flöde och ett större cacheminne.

* Cache – cache-kapaciteten delas jämnt mellan klusternoderna. Ange cache-storlek per nod när du skapar klustret. Node-storlekarna läggs till för att bli den totala cachestorleken.

* Kluster styrenhet – kluster styrenheten är en ytterligare virtuell dator som finns i samma undernät som klusternoderna. Kontrollanten behövs för att skapa klustret och för pågående hanterings uppgifter.

* Backend-lagring – de data som du vill att cache-lagra är långsiktigt i ett maskin varu lagrings system eller en Azure Blob-behållare. Du kan lägga till lagring när du har skapat AVERT vFXT för Azure-kluster, eller om du använder Blob Storage kan du lägga till och konfigurera behållaren när du skapar klustret.

* Klienter-klient datorer som använder de cachelagrade filerna ansluter till klustret med hjälp av en virtuell fil Sök väg i stället för att komma åt lagrings systemen direkt. (Läs mer i [montera vFXT-klustret för AVERT](avere-vfxt-mount-clients.md).)

## <a name="subscription-resource-group-and-network-infrastructure"></a>Prenumeration, resurs grupp och nätverks infrastruktur

Överväg var elementen i ditt AVERT vFXT för Azure-distribution ska vara. Diagrammet nedan visar ett möjligt arrangemang för det Avera vFXT för Azure-komponenter:

![Diagram som visar kluster styrenheten och virtuella kluster datorer i ett undernät. Runt under nätets gränser är en VNet-gränser. Inuti VNet är en sexhörning som representerar lagrings tjänstens slut punkt. den är ansluten med en streckad pil till en blob-lagring utanför VNet.](media/avere-vfxt-components-option.png)

Följ dessa rikt linjer när du planerar ditt AVERT vFXT-klusters nätverks infrastruktur:

* Skapa en ny prenumeration för varje AVERT vFXT för Azure-distribution. Hantera alla komponenter i den här prenumerationen.

  Fördelarna med att använda en ny prenumeration för varje distribution är:
  * Enklare kostnads uppföljning – Visa och granska alla kostnader från resurser, infrastruktur och Compute-cykler i en prenumeration.
  * Enklare rensning – du kan ta bort hela prenumerationen när du är klar med projektet.
  * Bekväm partitionering av resurs kvoter – isolera de AVERT vFXT-klienter och-kluster i en enda prenumeration för att skydda andra kritiska arbets belastningar från möjlig resurs begränsning. Den här separationen förhindrar en konflikt när du hämtar ett stort antal klienter för ett arbets flöde med hög prestanda beräkning.

* Leta upp dina klient beräknings system nära vFXT-klustret. Backend-lagring kan vara mer fjärran sluten.  

* Leta upp vFXT-klustret och den virtuella kluster styrenheten – särskilt bör de vara:

  * I samma virtuella nätverk
  * I samma resurs grupp
  * Använda samma lagrings konto
  
  Mallen för att skapa kluster hanterar den här konfigurationen för de flesta situationer.

* Klustret måste finnas i sitt eget undernät för att undvika IP-adress konflikter med klienter eller andra beräknings resurser.

* Använd mallen skapa kluster för att skapa de flesta av de nödvändiga infrastruktur resurserna för klustret, inklusive resurs grupper, virtuella nätverk, undernät och lagrings konton.

  Om du vill använda resurser som redan finns kontrollerar du att de uppfyller kraven i den här tabellen.

  | Resurs | Använd befintlig? | Krav |
  |----------|-----------|----------|
  | Resursgrupp | Ja, om det är tomt | Måste vara tom|
  | Lagringskonto | **Ja** om du ansluter en befintlig BLOB-behållare efter att klustret har skapats <br/>  **Nej** om du skapar en ny BLOB-behållare när klustret skapas | Den befintliga BLOB-behållaren måste vara tom <br/> &nbsp; |
  | Virtuellt nätverk | Ja | Måste inkludera en Storage Service-slutpunkt om du skapar en ny Azure Blob-behållare |
  | Undernät | Ja | Kan inte innehålla andra resurser |

## <a name="ip-address-requirements"></a>Krav för IP-adress

Kontrol lera att klustrets undernät har ett stort tillräckligt IP-adressintervall för att stödja klustret.

The aver vFXT-klustret använder följande IP-adresser:

* En IP-adress för kluster hantering. Den här adressen kan flyttas från nod till nod i klustret efter behov, så att den alltid är tillgänglig. Använd den här adressen för att ansluta till konfigurations verktyget AVERT på kontroll panelen.
* För varje klusternod:
  * Minst en IP-adress som riktas mot klienter. (Alla klient adresser hanteras av klustrets *vserver*, som kan flytta IP-adresserna mellan noder efter behov.)
  * En IP-adress för kluster kommunikation
  * En instans-IP-adress (tilldelad till den virtuella datorn)

Om du använder Azure Blob Storage kan det även krävas IP-adresser från klustrets virtuella nätverk:  

* Ett Azure Blob Storage-konto måste ha minst fem IP-adresser. Behåll detta krav i åtanke om du hittar Blob Storage i samma virtuella nätverk som klustret.
* Om du använder Azure Blob Storage som ligger utanför klustrets virtuella nätverk skapar du en lagrings tjänst slut punkt i det virtuella nätverket. Slut punkten använder ingen IP-adress.

Du kan välja att hitta nätverks resurser och Blob Storage (om det används) i olika resurs grupper från klustret.

## <a name="vfxt-node-size"></a>vFXT Node-storlek

De virtuella datorer som fungerar som klusternoder bestämmer hur data flödet och lagrings kapaciteten för din cache är. <!-- The instance type offered has been chosen for its memory, processor, and local storage characteristics. You can choose from two instance types, with different memory, processor, and local storage characteristics. -->

Varje vFXT-nod kommer att vara identisk. Det innebär att om du skapar ett kluster med tre noder kommer du att ha tre virtuella datorer av samma typ och storlek.

| Instans typ | Virtuella processorer | Minne  | Lokal SSD-lagring  | Maximalt antal datadiskar | Ej cachelagrad disk data flöde | NÄTVERKSKORT (antal) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_E32s_v3 | 32  | 256 GiB | 512 GiB  | 32 | 51 200 IOPS <br/> 768 Mbit/s | 16 000 Mbit/s (8)  |

Disk-cache per nod kan konfigureras och kan Rage från 1000 GB till 8000 GB. 4 TB per nod är den rekommenderade cachestorleken för Standard_E32s_v3 noder.

Mer information om de här virtuella datorerna finns i Microsoft Azure-dokumentationen: [minnesoptimerade storlekar för virtuella datorer](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)

## <a name="account-quota"></a>Konto kvot

Se till att prenumerationen har kapacitet att köra det AVERT vFXT-kluster och alla data behandlings-eller klient system som används. Läs [kvoten för vFXT-klustret](avere-vfxt-prereqs.md#quota-for-the-vfxt-cluster) för mer information.

## <a name="back-end-data-storage"></a>Data lagring på Server Sidan

Server dels lagrings system tillhandahåller filer till klustrets cache och tar även emot ändrade data från cachen. Bestäm om din arbets uppsättning ska lagras på lång sikt i en ny BLOB-behållare eller i ett befintligt lagrings system (moln eller maskin vara). Dessa Server dels lagrings system kallas *Core*-.

### <a name="hardware-core-filers"></a>Maskin varu core-filer

Lägg till maskin varu lagrings system i vFXT-klustret när du har skapat klustret. Du kan använda en mängd olika populära maskin varu system, inklusive lokala system, så länge lagrings systemet kan nås från klustrets undernät.

Läs [Konfigurera lagring](avere-vfxt-add-storage.md) för detaljerade instruktioner om hur du lägger till ett befintligt lagrings system i det AVERT vFXT-klustret.

### <a name="cloud-core-filers"></a>Cloud core-filer

AVERT vFXT för Azure-systemet kan använda tomma BLOB-behållare för Server dels lagring. Behållare måste vara tomma när de läggs till i klustret – vFXT-systemet måste kunna hantera objekt arkivet utan att befintliga data behöver bevaras.

> [!TIP]
> Om du vill använda Azure Blob Storage för Server delen skapar du en ny behållare som en del av att skapa vFXT-klustret. Mallen för att skapa kluster kan skapa och konfigurera en ny BLOB-behållare så att den är redo att användas så fort klustret är tillgängligt. Att lägga till en behållare senare är mer komplicerat.
>
> Läs [skapa AVERT vFXT för Azure](avere-vfxt-deploy.md#create-the-avere-vfxt-for-azure) för mer information.

När du har lagt till en tom Blob Storage-behållare som en kärna, kan du kopiera data till den via klustret. Använd en parallell och flertrådad kopierings funktion. Läs [Flytta data till vFXT-klustret](avere-vfxt-data-ingest.md) om du vill lära dig att kopiera data till klustrets nya behållare effektivt genom att använda klient datorer och vFXT-cachen aver.

## <a name="cluster-access"></a>Kluster åtkomst

AVERT vFXT för Azure-kluster finns i ett privat undernät och klustret har ingen offentlig IP-adress. Du måste ha något sätt att komma åt det privata under nätet för kluster administration och klient anslutningar.

Åtkomst alternativ är:

* Hopp värd – tilldela en offentlig IP-adress till en separat virtuell dator i det privata nätverket och Använd den för att skapa en TLS-tunnel till klusternoderna.

  > [!TIP]
  > Om du anger en offentlig IP-adress på kluster styrenheten kan du använda den som hopp värd. Mer information finns [i kluster styrenheten som hopp värd](#cluster-controller-as-jump-host) .

* Virtuellt privat nätverk (VPN) – Konfigurera en punkt-till-plats-eller plats-till-plats-VPN mellan ditt privata nätverk i Azure och företags nätverk.

* Azure-ExpressRoute – konfigurera en privat anslutning via en ExpressRoute-partner.

Mer information om de här alternativen finns i [Azure Virtual Network-dokumentationen om Internet kommunikation](../virtual-network/virtual-networks-overview.md#communicate-with-the-internet).

### <a name="cluster-controller-as-jump-host"></a>Kluster styrenhet som hopp värd

Om du anger en offentlig IP-adress på kluster styrenheten kan du använda den som en hopp värd för att kontakta det vFXT-kluster som finns utanför det privata under nätet. Men eftersom styrenheten har åtkomst behörighet för att ändra klusternoder skapar detta en liten säkerhets risk.

För att förbättra säkerheten för en kontrollant med en offentlig IP-adress skapar distributions skriptet automatiskt en nätverks säkerhets grupp som begränsar inkommande åtkomst till port 22. Du kan skydda systemet ytterligare genom att låsa åtkomsten till ditt utbud av IP-adresser – det vill säga bara tillåta anslutningar från datorer som du vill använda för kluster åtkomst.

När du skapar klustret kan du välja om du vill skapa en offentlig IP-adress på kluster styrenheten.

* Om du skapar ett **nytt virtuellt nätverk** eller ett **Nytt undernät**, tilldelas kluster styrenheten en **offentlig** IP-adress.
* Om du väljer ett befintligt virtuellt nätverk och undernät får kluster styrenheten bara **privata** IP-adresser.

## <a name="vm-access-roles"></a>Åtkomst roller för virtuella datorer

Azure använder [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/index.yml) för att auktorisera de virtuella datorerna i klustret för att utföra vissa uppgifter. Till exempel behöver kluster styrenheten auktorisering för att skapa och konfigurera klusternodens virtuella datorer. Klusternoder måste kunna tilldela eller omtilldela IP-adresser till andra klusternoder.

Två inbyggda Azure-roller används för de AVERT vFXT virtuella datorerna:

* Kluster styrenheten använder den inbyggda rollen " [avere](../role-based-access-control/built-in-roles.md#avere-contributor)".
* Klusternoder använder den inbyggda rollen " [aver operator](../role-based-access-control/built-in-roles.md#avere-operator)".

Om du behöver anpassa åtkomst roller för AVERT vFXT-komponenter måste du definiera en egen roll och sedan tilldela den till de virtuella datorerna när de skapas. Du kan inte använda distributions mal len i Azure Marketplace. Kontakta Microsofts kund tjänst och support genom att öppna ett ärende i Azure Portal enligt beskrivningen i [få hjälp med systemet](avere-vfxt-open-ticket.md).

## <a name="next-steps"></a>Nästa steg

[Distributions översikten](avere-vfxt-deploy-overview.md) ger en överblick över de steg som krävs för att skapa ett AVERT VFXT för Azure-systemet och bli redo att hantera data.
