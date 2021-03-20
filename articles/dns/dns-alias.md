---
title: Översikt över alias-poster – Azure DNS
description: I den här artikeln får du lära dig mer om stöd för Ali Asets poster i Microsoft Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 08/09/2019
ms.author: rohink
ms.openlocfilehash: 8b2576669357aae7e5fe423515933c2ce4a23a7d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94954485"
---
# <a name="azure-dns-alias-records-overview"></a>Översikt över Azure DNS-aliasposter

Azure DNS Ali Aset-poster är kvalifikationer i en DNS-postuppsättning. De kan referera till andra Azure-resurser inifrån din DNS-zon. Du kan till exempel skapa en post uppsättning för Ali Aset som refererar till en offentlig Azure-IP-adress i stället för en A-post. Ali Asets post uppsättningen pekar på en Azure offentlig IP-tjänstinstans som är dynamiskt. Detta innebär att Ali Aset kan uppdateras sömlöst under DNS-matchning.

En post uppsättning för Ali Aset stöds för följande post typer i en Azure DNS zon: 

- A
- AAAA
- CNAME

> [!NOTE]
> Om du tänker använda en aliasresurspost för post typerna A eller AAAA för att peka på en [Azure Traffic Manager-profil](../traffic-manager/quickstart-create-traffic-manager-profile.md) måste du kontrol lera att Traffic Managers profilen bara har [externa slut punkter](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints). Du måste ange IPv4-eller IPv6-adressen för externa slut punkter i Traffic Manager. Du kan inte använda fullständigt kvalificerade domän namn (FQDN) i slut punkter. Vi rekommenderar att du använder statiska IP-adresser.

## <a name="capabilities"></a>Funktioner

- **Peka på en offentlig IP-resurs från en DNS A/AAAA-postuppsättning.** Du kan skapa en A/AAAA-postuppsättning och göra den till en angiven aliasresurspost som pekar på en offentlig IP-resurs (standard eller Basic). DNS-postuppsättningen ändras automatiskt om den offentliga IP-adressen ändras eller tas bort. Dangling DNS-poster som pekar på felaktiga IP-adresser undviks.

   Det finns en aktuell gräns på 20 alias post uppsättningar per resurs.

- **Peka på en Traffic Manager profil från en post uppsättning för DNS A/AAAA/CNAME.** Du kan skapa en A/AAAA-eller CNAME-postuppsättning och använda Ali Aset för att peka den på en Traffic Manager profil. Det är särskilt användbart när du behöver dirigera trafik i en zon spets eftersom traditionella CNAME-poster inte stöds för en zon Apex. Anta till exempel att din Traffic Manager-profil är myprofile.trafficmanager.net och att företagets DNS-zon är contoso.com. Du kan skapa en post uppsättning av typen A/AAAA för contoso.com (zon Apex) och peka på myprofile.trafficmanager.net.
- **Peka på en Azure Content Delivery Network-slutpunkt (CDN)**. Detta är användbart när du skapar statiska webbplatser med hjälp av Azure Storage och Azure CDN.
- **Peka på en annan DNS-post uppsättning inom samma zon.** Aliasposter kan referera till andra postuppsättningar av samma typ. En DNS CNAME-post uppsättning kan till exempel vara ett alias till en annan CNAME-postuppsättning. Den här ordningen är användbar om du vill att vissa post uppsättningar ska vara alias och vissa icke-alias.

## <a name="scenarios"></a>Scenarier

Det finns några vanliga scenarier för Alian tals poster.

### <a name="prevent-dangling-dns-records"></a>Förhindra Dangling DNS-poster

Ett vanligt problem med traditionella DNS-poster är Dangling-poster. Till exempel DNS-poster som inte har uppdaterats för att återspegla ändringar av IP-adresser. Problemet inträffar särskilt med poster av typen/AAAA eller CNAME.

Om mål-IP eller CNAME inte längre finns med en traditionell DNS-zon post, måste DNS-posten som är kopplad till den uppdateras manuellt. I vissa organisationer kanske en manuell uppdatering inte sker i tid på grund av process problem eller separering av roller och associerade behörighets nivåer. En roll kan till exempel ha behörighet att ta bort en CNAME-eller IP-adress som tillhör ett program. Men det finns inte tillräckligt med behörighet för att uppdatera DNS-posten som pekar på dessa mål. En fördröjning vid uppdatering av DNS-posten kan eventuellt orsaka ett avbrott för användarna.

Ali Asets förhindrar Dangling-referenser genom tätt koppling av livs cykeln för en DNS-post med en Azure-resurs. Anta till exempel att du har en DNS-post som är kvalificerad som en aliasresurspost som pekar på en offentlig IP-adress eller en Traffic Manager-profil. Om du tar bort de underliggande resurserna blir DNS-Ali-posten en tom post uppsättning. Den borttagna resursen är inte längre referenser till den.

### <a name="update-dns-record-set-automatically-when-application-ip-addresses-change"></a>Uppdatera DNS-post – Ställ in automatiskt när program-IP-adresser ändras

Det här scenariot liknar det föregående. Kanske ett program flyttas eller den underliggande virtuella datorn startas om. En aliasresurspost uppdateras sedan automatiskt när IP-adressen ändras för den underliggande offentliga IP-resursen. På så sätt undviker du potentiella säkerhets risker för att dirigera användarna till ett annat program som har tilldelats den gamla offentliga IP-adressen.

### <a name="host-load-balanced-applications-at-the-zone-apex"></a>Belastnings Utjämnings program för värd i zonen Apex

DNS-protokollet förhindrar att CNAME-poster tilldelas i zonens Apex. Till exempel om din domän är contoso.com; Du kan skapa CNAME-poster för somelabel.contoso.com; men du kan inte skapa CNAME för contoso.com.
Den här begränsningen utgör ett problem för program ägare som har belastningsutjämnade program bakom [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md). Eftersom användning av en Traffic Manager profil kräver att en CNAME-post skapas går det inte att peka på Traffic Manager profilen från zonens Apex.

Det här problemet kan lösas med hjälp av Ali Aset-poster. Till skillnad från CNAME-poster skapas Alian slut poster i zonens Apex och program ägare kan använda den för att peka sin zon spetsig post till en Traffic Manager profil som har externa slut punkter. Program ägare pekar på samma Traffic Manager profil som används för alla andra domäner inom sin DNS-zon.

Till exempel kan contoso.com och www- \. contoso.com peka på samma Traffic Manager profil. Mer information om hur du använder Ali Asets poster med Azure Traffic Manager-profiler finns i avsnittet Nästa steg.

### <a name="point-zone-apex-to-azure-cdn-endpoints"></a>Punkt zon spetsigt till Azure CDN slut punkter

Precis som en Traffic Manager-profil kan du också använda Ali Aset för att peka din DNS-zon spets till att Azure CDN slut punkter. Detta är användbart när du skapar statiska webbplatser med hjälp av Azure Storage och Azure CDN. Du kan sedan komma åt webbplatsen utan att vänta "www" till ditt DNS-namn.

Om din statiska webbplats t. ex. heter `www.contoso.com` kan användarna få åtkomst till din webbplats med hjälp av `contoso.com` utan att behöva lägga www till DNS-namnet.

Som tidigare beskrivits stöds inte CNAME-poster i zonens Apex. Därför kan du inte använda en CNAME-post för att peka contoso.com till CDN-slutpunkten. I stället kan du använda en aliasresurspost för att peka zon spetsen mot en CDN-slutpunkt direkt.

> [!NOTE]
> Det finns för närvarande inte stöd för att peka en zon spets till CDN-slutpunkter för Azure CDN från Akamai.

## <a name="next-steps"></a>Nästa steg

Mer information om aliin poster finns i följande artiklar:

- [Självstudie: Konfigurera en aliasresurspost för att referera till en offentlig Azure-IP-adress](tutorial-alias-pip.md)
- [Självstudie: Konfigurera en aliaspost för att hantera apex-domännamn med Traffic Manager](tutorial-alias-tm.md)
- [Vanliga frågor och svar om DNS](./dns-faq.md#alias-records)