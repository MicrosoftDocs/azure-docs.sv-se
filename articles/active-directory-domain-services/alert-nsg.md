---
title: Lösa aviseringar om nätverks säkerhets grupper i Azure AD DS | Microsoft Docs
description: Lär dig hur du felsöker och löser konfigurations aviseringar för nätverks säkerhets grupper för Azure Active Directory Domain Services
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: justinha
ms.openlocfilehash: 5b48d326efad889adbcf25d487ee27b8200f558f
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693916"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Kända problem: aviseringar om nätverks konfiguration i Azure Active Directory Domain Services

För att program och tjänster ska kunna kommunicera med en Azure Active Directory Domain Services (Azure AD DS)-hanterad domän, måste särskilda nätverks portar vara öppna för att tillåta trafik att flöda. I Azure styr du trafik flödet med hjälp av nätverks säkerhets grupper. Hälso status för en Azure AD DS-hanterad domän visar en avisering om de nödvändiga reglerna för nätverks säkerhets grupper inte finns på plats.

Den här artikeln hjälper dig att förstå och lösa vanliga aviseringar för konfigurations problem med nätverks säkerhets grupper.

## <a name="alert-aadds104-network-error"></a>Aviserings AADDS104: nätverks fel

### <a name="alert-message"></a>Aviserings meddelande

*Microsoft kan inte komma åt domän kontrol Lanterna för den här hanterade domänen. Detta kan inträffa om en nätverks säkerhets grupp (NSG) som kon figurer ATS i ditt virtuella nätverk blockerar åtkomsten till den hanterade domänen. En annan möjlig orsak är om det finns en användardefinierad väg som blockerar inkommande trafik från Internet.*

Ogiltiga regler för nätverks säkerhets grupper är den vanligaste orsaken till nätverks fel för Azure AD DS. Nätverks säkerhets gruppen för det virtuella nätverket måste tillåta åtkomst till vissa portar och protokoll. Om dessa portar blockeras kan Azure-plattformen inte övervaka eller uppdatera den hanterade domänen. Synkroniseringen mellan Azure AD-katalogen och Azure AD DS påverkas också. Se till att du behåller standard portarna öppna för att undvika avbrott i tjänsten.

## <a name="default-security-rules"></a>Standardsäkerhetsregler

Följande standard säkerhets regler för inkommande och utgående trafik tillämpas på nätverks säkerhets gruppen för en hanterad domän. De här reglerna skyddar Azure AD DS och ger Azure-plattformen möjlighet att övervaka, hantera och uppdatera den hanterade domänen.

### <a name="inbound-security-rules"></a>Ingående säkerhetsregler

| Prioritet | Namn | Port | Protokoll | Källa | Mål | Åtgärd |
|----------|------|------|----------|--------|-------------|--------|
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Valfri | Tillåt |
| 201      | AllowRD | 3389 | TCP | CorpNetSaw | Valfri | Neka<sup>1</sup> |
| 65000    | AllVnetInBound | Valfri | Valfri | VirtualNetwork | VirtualNetwork | Tillåt |
| 65001    | AllowAzureLoadBalancerInBound | Valfri | Valfri | AzureLoadBalancer | Valfri | Tillåt |
| 65500    | DenyAllInBound | Valfri | Valfri | Valfri | Valfri | Neka |


<sup>1</sup> Valfritt för fel sökning. Tillåt när det behövs för avancerad fel sökning.

> [!NOTE]
> Du kan också ha en ytterligare regel som tillåter inkommande trafik om du [konfigurerar säker LDAP][configure-ldaps]. Denna ytterligare regel krävs för korrekt LDAP-kommunikation.

### <a name="outbound-security-rules"></a>Säkerhetsregler för utgående trafik

| Prioritet | Namn | Port | Protokoll | Källa | Mål | Åtgärd |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | AllVnetOutBound | Valfri | Valfri | VirtualNetwork | VirtualNetwork | Tillåt |
| 65001    | AllowAzureLoadBalancerOutBound | Valfri | Valfri |  Valfri | Internet | Tillåt |
| 65500    | DenyAllOutBound | Valfri | Valfri | Valfri | Valfri | Neka |

>[!NOTE]
> Azure AD DS behöver obegränsad utgående åtkomst från det virtuella nätverket. Vi rekommenderar inte att du skapar några ytterligare regler som begränsar utgående åtkomst för det virtuella nätverket.

## <a name="verify-and-edit-existing-security-rules"></a>Verifiera och redigera befintliga säkerhets regler

För att kontrol lera de befintliga säkerhets reglerna och se till att standard portarna är öppna, utför du följande steg:

1. Sök efter och välj **nätverks säkerhets grupper** i Azure Portal.
1. Välj den nätverks säkerhets grupp som är kopplad till din hanterade domän, t. ex. *AADDS-contoso.com-NSG*.
1. På sidan **Översikt** visas befintliga inkommande och utgående säkerhets regler.

    Granska reglerna för inkommande och utgående trafik och jämför med listan över nödvändiga regler i föregående avsnitt. Om det behövs markerar du och tar bort eventuella anpassade regler som blockerar nödvändig trafik. Om någon av de nödvändiga reglerna saknas lägger du till en regel i nästa avsnitt.

    När du har lagt till eller tagit bort regler för att tillåta den nödvändiga trafiken uppdaterar den hanterade domänens hälsa automatiskt inom två timmar och tar bort aviseringen.

### <a name="add-a-security-rule"></a>Lägg till en säkerhetsregel

Slutför följande steg för att lägga till en säkerhets regel som saknas:

1. Sök efter och välj **nätverks säkerhets grupper** i Azure Portal.
1. Välj den nätverks säkerhets grupp som är kopplad till din hanterade domän, t. ex. *AADDS-contoso.com-NSG*.
1. Under **Inställningar** i den vänstra panelen klickar du på *inkommande säkerhets regler* eller *utgående säkerhets regler* , beroende på vilken regel du behöver lägga till.
1. Välj **Lägg till** och skapa sedan den nödvändiga regeln baserat på porten, protokollet, riktningen osv. När du är klar väljer du **OK**.

Det tar en stund för säkerhets regeln att läggas till och visas i listan.

## <a name="next-steps"></a>Nästa steg

Om du fortfarande har problem [öppnar du en support förfrågan för Azure][azure-support] om du behöver ytterligare fel sökning.

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
