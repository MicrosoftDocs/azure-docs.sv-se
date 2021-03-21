---
title: Azure cache för Redis alternativ för nätverks isolering
description: I den här artikeln får du lära dig hur du fastställer den bästa nätverks isolerings lösningen för dina behov. Vi går igenom grunderna i Azures privata länk, Azure Virtual Network-injektering (VNet) och Azure brand Väggs regler med deras fördelar och begränsningar.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: ef284661d44f700cf0b5282efcd2e6f7b94fa3b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96621526"
---
# <a name="azure-cache-for-redis-network-isolation-options"></a>Azure cache för Redis alternativ för nätverks isolering 
I den här artikeln får du lära dig hur du fastställer den bästa nätverks isolerings lösningen för dina behov. Vi går igenom grunderna i Azures privata länk, Azure Virtual Network-injektering (VNet) och Azure brand Väggs regler med deras fördelar och begränsningar.  

## <a name="azure-private-link-public-preview"></a>Privat Azure-länk (offentlig för hands version) 
Azures privata länk ger privat anslutning från ett virtuellt nätverk till Azure PaaS Services. Det fören klar nätverks arkitekturen och skyddar anslutningen mellan slut punkter i Azure genom att eliminera data exponeringen för det offentliga Internet. 

### <a name="advantages"></a>Fördelar
* Stöds på Basic-, standard-och Premium Azure-cache för Redis-instanser. 
* Med hjälp av en [privat Azure-länk](../private-link/private-link-overview.md)kan du ansluta till en Azure-cache-instans från det virtuella nätverket via en privat slut punkt, som tilldelas en privat IP-adress i ett undernät i det virtuella nätverket. Med det här alternativet är cache-instanser tillgängliga både i VNet och offentligt.  
* När en privat slut punkt har skapats kan åtkomst till det offentliga nätverket begränsas via- `publicNetworkAccess` flaggan. Den här flaggan är inställd på som `Disabled` standard, vilket bara tillåter åtkomst till privat länk. Du kan ställa in värdet på `Enabled` eller `Disabled` med en patch-begäran. Mer information finns i [Azure cache för Redis med Azure Private Link (för hands version)](cache-private-link.md). 
* Alla externa cache-beroenden påverkar inte VNets NSG-regler.

### <a name="limitations"></a>Begränsningar 
* Nätverks säkerhets grupper (NSG) har inaktiverats för privata slut punkter. Men om det finns andra resurser i under nätet kommer NSG tvång att gälla för dessa resurser.
* Geo-replikering, brand Väggs regler, stöd för Portal konsol, flera slut punkter per klustrad cache, persistence till brand vägg och VNet-inmatnings cache stöds inte än. 
* För att ansluta till en klustrad cache `publicNetworkAccess` måste anges till `Disabled` och det får bara finnas en privat slut punkts anslutning.

> [!NOTE]
> När du lägger till en privat slut punkt till en cache-instans flyttas all Redis-trafik till den privata slut punkten på grund av DNS.
> Se till att tidigare brand Väggs regler justeras före.  
>
>

## <a name="azure-virtual-network-injection"></a>Azure Virtual Network insprutning 
VNet är det grundläggande Bygg blocket för ditt privata nätverk i Azure. VNet möjliggör att många Azure-resurser kan kommunicera på ett säkert sätt med varandra, Internet och lokala nätverk. VNet är som ett traditionellt nätverk som du kan använda i ditt eget Data Center, men med fördelarna med Azure-infrastruktur, skalning, tillgänglighet och isolering. 

### <a name="advantages"></a>Fördelar
* När en Azure-cache för Redis-instans har kon figurer ATS med ett VNet, är den inte offentligt adresserad och kan endast nås från virtuella datorer och program i VNet.  
* När VNet kombineras med begränsade NSG-principer bidrar det till att minska risken för data exfiltrering. 
* VNet-distribution ger förbättrad säkerhet och isolering för Azure cache för Redis, samt undernät, åtkomst kontroll principer och andra funktioner för att ytterligare begränsa åtkomst. 
* Geo-replikering stöds. 

### <a name="limitations"></a>Begränsningar
* Virtuella VNet-inmatade cacheminnen är bara tillgängliga för Premium Azure-cache för Redis. 
* När du använder ett VNet-inmatat cacheminne måste du öppna ditt VNet för att cachelagra beroenden, till exempel CRL/PKI, AKV, Azure Storage, Azure Monitor med mera.  


## <a name="azure-firewall-rules"></a>Regler för Azure-brandvägg
[Azure Firewall](../firewall/overview.md) är en hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure VNet-resurser. Det är en fullständigt tillstånds känslig brand vägg som en tjänst med inbyggd hög tillgänglighet och obegränsad moln skalbarhet. Du kan centralt skapa, framtvinga och logga principer för tillämpning och nätverksanslutning över prenumerationer och virtuella nätverk.  

### <a name="advantages"></a>Fördelar
* När brand Väggs regler har kon figurer ATS kan endast klient anslutningar från de angivna IP-adressintervall ansluta till cacheminnet. Anslutningar från Azure cache för Redis övervaknings system tillåts alltid, även om brand Väggs regler har kon figurer ATS. NSG-regler som du definierar tillåts också.  

### <a name="limitations"></a>Begränsningar
* Brand Väggs regler kan användas tillsammans med VNet-inmatade cacheminnen, men inte för privata slut punkter. 


## <a name="next-steps"></a>Nästa steg
* Lär dig hur du konfigurerar ett [VNet-inmatat cacheminne för en Premium Azure-cache för Redis-instansen](cache-how-to-premium-vnet.md).  
* Lär dig hur du konfigurerar [brand Väggs regler för alla Azure-cache för Redis-nivåer](cache-configure.md#firewall). 
* Lär dig hur du [konfigurerar privata slut punkter för alla Azure-cache för Redis-nivåer](cache-private-link.md).