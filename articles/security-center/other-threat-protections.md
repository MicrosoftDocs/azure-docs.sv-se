---
title: Ytterligare skydd mot hot i Azure Security Center
description: Lär dig mer om hot skyddet som är tillgängligt från Azure Security Center utanför Azure Defender
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: overview
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 5b9e9f5cc5184a083f45999eaeb031eb83b17c58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754342"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Ytterligare hot skydd i Azure Security Center
I och med de inbyggda [Azure Defender-skydden](azure-defender.md)erbjuder Azure Security Center även följande hot skydds funktioner.

> [!TIP]
> Om du vill aktivera Security Center hot skydds funktioner måste du aktivera Azure Defender på prenumerationen som innehåller de aktuella arbets belastningarna.
>
> Du kan aktivera skydd mot hot för **Azure Database for MariaDB/MySQL/postgresql** endast på resurs nivå.


## <a name="threat-protection-for-azure-network-layer"></a>Hot skydd för Azure nätverks lager <a name="network-layer"></a>
Security Center Network Layer Analytics baseras på [IPFIX data](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), som är paket rubriker som samlas in av Azure Core-routrar. Baserat på den här datafeeden använder Security Center maskin inlärnings modeller för att identifiera och flagga skadliga trafik aktiviteter. Security Center använder också Microsoft Threat Intelligence-databasen för att utöka IP-adresser.

Vissa nätverkskonfigurationer begränsar Security Center från att generera aviseringar om misstänkt nätverks aktivitet. För att Security Center generera nätverks aviseringar, se till att:
- Den virtuella datorn har en offentlig IP-adress (eller finns på en belastningsutjämnare med en offentlig IP-adress).
- Den virtuella datorns utgående trafik blockeras inte av en extern ID-lösning.

En lista över aviseringar för Azure nätverks lager finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Skydd mot Azure Resource Manager (för hands version)<a name ="management-layer"></a>
Security Center skydds skikt baserat på Azure Resource Manager är för närvarande en för hands version.

Security Center erbjuder ett extra skydds lager genom att använda Azure Resource Manager händelser, som anses vara kontroll planet för Azure. Genom att analysera Azure Resource Manager poster Security Center identifiera ovanliga eller potentiellt skadliga åtgärder i Azure-prenumerationens miljö.

En lista över Azure Defender för Resource Manager-aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-resourcemanager).


>[!NOTE]
> Flera av föregående analyser drivs av Microsoft Cloud App Security. Om du vill dra nytta av dessa analyser måste du aktivera en Cloud App Security-licens. Om du har en Cloud App Security licens är de här aviseringarna aktiverade som standard. Så här inaktiverar du aviseringarna:
>
> 1. Från Security Center menyn väljer du **pris & inställningar**.
> 1. Välj den prenumeration som du vill ändra.
> 1. Välj **hot identifiering**.
> 1. Rensa **tillåt Microsoft Cloud App Security att komma åt mina data** och välj **Spara**.


>[!NOTE]
>Security Center lagrar säkerhetsrelaterad kund information i samma geo som dess resurs. Om Microsoft ännu inte har distribuerat Security Center i resursens geografiska område, lagrar data i USA. När Cloud App Security är aktive rad lagras informationen i enlighet med reglerna för geo-platsen för Cloud App Security. Mer information finns i [data lagring för icke-regionala tjänster](https://azuredatacentermap.azurewebsites.net/).

1. Ange den arbets yta som du vill installera agenten på. Kontrol lera att arbets ytan finns i samma prenumeration som du använder i Security Center och att du har Läs-/Skriv behörighet på arbets ytan.

1. Aktivera **Azure Defender** och välj **Spara**.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Skydd mot Azure Cosmos DB (för hands version)<a name="cosmos-db"></a>

Azure Cosmos DB-aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja Azure Cosmos DB-konton.

Mer information finns i:

* [Avancerat skydd för Azure Cosmos DB (för hands version)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Listan med aviseringar för skydd mot Azure Cosmos DB (för hands version)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Skydd mot hot för andra Microsoft-tjänster <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Skydd mot hot för Azure-WAF <a name="azure-waf"></a>

Azure Application Gateway erbjuder en WAF (brandvägg för webbaserade program) som ger ett centraliserat skydd för dina webbappar mot vanliga kryphål och säkerhetsproblem.

Webb program är alltmer riktade mot skadliga attacker som utnyttjar ofta kända sårbarheter. Application Gateway-WAF baseras på kärn regel uppsättningen 3,0 eller 2.2.9 från det öppna säkerhets projektet för webb program. WAF uppdateras automatiskt för att skydda mot nya sårbarheter. 

Om du har en licens för Azure WAF strömmas dina WAF-aviseringar till Security Center utan ytterligare konfiguration som behövs. Mer information om de aviseringar som genererats av WAF finns i [regel grupper och regler för webb programs brand vägg](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Skydd mot hot för Azure DDoS Protection <a name="azure-ddos"></a>

DDoS-attacker (distributed denial of Service) är kända för att vara lätta att köra. De har blivit ett bra säkerhets problem, särskilt om du flyttar dina program till molnet. 

Ett DDoS-angrepp försöker att belasta ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan rikta in sig på alla slut punkter som kan nås via Internet.

Du kan skydda dig mot DDoS-attacker genom att köpa en licens för Azure DDoS Protection och se till att du följer metod tips för program design. DDoS Protection tillhandahåller olika tjänst nivåer. Mer information finns i [Azure DDoS Protection översikt](../ddos-protection/ddos-protection-overview.md).

En lista över Azure DDoS Protection aviseringar finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Nästa steg
Mer information om säkerhets aviseringar från dessa hot Protection-funktioner finns i följande artiklar:

* [Referens tabell för alla Azure Security Center aviseringar](alerts-reference.md)
* [Säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md)
* [Hantera och besvara säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)
* [Exportera Security Center data kontinuerligt](continuous-export.md)