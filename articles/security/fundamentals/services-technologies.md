---
title: Azure Security Services och Technologies | Microsoft Docs
description: Artikeln innehåller en granskad lista över Azures säkerhets tjänster och tekniker.
services: security
documentationcenter: na
author: terrylanfear
manager: barbkess
editor: TomSh
ms.assetid: a5a7f60a-97e2-49b4-a8c5-7c010ff27ef8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/29/2019
ms.author: terrylan
ms.openlocfilehash: 5914d5a54606f49f6fbd809a17e2b47375b58dcd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641976"
---
# <a name="security-services-and-technologies-available-on-azure"></a>Säkerhets tjänster och tekniker som är tillgängliga i Azure

I våra diskussioner med aktuella och framtida Azure-kunder brukar vi ofta bli "vill du ha en lista över alla säkerhetsrelaterade tjänster och tekniker som Azure måste erbjuda?"

När du utvärderar alternativ för moln tjänst leverantörer är det bra att ha den här informationen. Vi har angett den här listan för att komma igång.

Med tiden kommer den här listan att ändras och växa, precis som Azure gör. Se till att kontrol lera den här sidan regelbundet för att hålla dig uppdaterad om våra säkerhetsrelaterade tjänster och tekniker.

## <a name="general-azure-security"></a>Allmän Azure-säkerhet
|Tjänst|Beskrivning|
|--------|--------|
|[Azure &nbsp; Security &nbsp; Center](../../security-center/security-center-introduction.md)| En skydds lösning för moln skydd som tillhandahåller säkerhets hantering och Avancerat skydd för arbets belastningar i hybrid moln.|
|[Azure Key Vault](../../key-vault/general/overview.md)| En säker hemligheter för lösen ord, anslutnings strängar och annan information som du behöver för att hålla dina appar igång. |
|[Azure Monitor-loggar](../../azure-monitor/logs/log-query-overview.md)|En övervaknings tjänst som samlar in telemetri och andra data och tillhandahåller ett frågespråk och analys motor för att leverera Operational Insights för dina appar och resurser. Kan användas separat eller med andra tjänster som Security Center. |
|[Azure dev/test Labs](../../devtest-labs/devtest-lab-overview.md)|En tjänst som hjälper utvecklare och testare att snabbt skapa miljöer i Azure och minimera spill och kontrol lera kostnader.  |

<!---|[Azure&nbsp;Disk&nbsp;Encryption](/azure/azure-security-disk-encryption-overview)| THIS WILL GO TO THE NEW OVERVIEW TOPIC MEGHAN STEWART IS WRITING|--->

## <a name="storage-security"></a>Lagringssäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Kryptering av Azure &nbsp; Storage &nbsp; -tjänsten &nbsp;](../../storage/common/storage-service-encryption.md)|En säkerhetsfunktion som automatiskt krypterar dina data i Azure Storage.   |
|[StorSimple-krypterad hybrid lagring](../../storsimple/storsimple-ova-overview.md)| En integrerad lagrings lösning som hanterar lagrings aktiviteter mellan lokala enheter och Azures moln lagring.|
|[Azure Client-Side-kryptering](../../storage/common/storage-client-side-encryption.md)| En krypterings lösning på klient sidan som krypterar data i klient program innan de överförs till Azure Storage. dekrypterar också data vid hämtning. |
| [Azure Storage signaturer för delad åtkomst](../../storage/common/storage-sas-overview.md)|En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagrings konto.  |
|[Azure Storage konto nycklar](../../storage/common/storage-account-create.md)| En åtkomst kontroll metod för Azure Storage som används för autentisering när lagrings kontot nås. |
|[Azure-filresurser med SMB 3,0-kryptering](../../storage/files/storage-files-introduction.md)|En nätverks säkerhets teknik som aktiverar automatisk nätverks kryptering för fildelnings protokollet SMB (Server Message Block). |
|[Azure-lagringsanalys](/rest/api/storageservices/Storage-Analytics)| En loggning och statistik som genererar teknik för data i ditt lagrings konto. |

<!------>

## <a name="database-security"></a>Databassäkerhet
|Tjänst|Beskrivning|
|------|--------|
| [Azure &nbsp; SQL- &nbsp; brandvägg](../../azure-sql/database/firewall-configure.md)|En funktion för nätverks åtkomst kontroll som skyddar mot nätverksbaserade attacker till databasen. |
|[Kryptering av Azure &nbsp; SQL- &nbsp; cellnivå &nbsp;](/archive/blogs/sqlsecurity/recommendations-for-using-cell-level-encryption-in-azure-sql-database)| En databas säkerhets teknik som tillhandahåller kryptering på en detaljerad nivå.  |
| [Kryptering av Azure &nbsp; SQL- &nbsp; anslutning](../../azure-sql/database/logins-create-manage.md)|För att erbjuda säkerhet, kontrollerar SQL Database åtkomsten med hjälp av brandväggsregler som begränsar anslutning efter IP-adress, autentiseringsmekanismer kräver att användare bevisar sin identitet och auktoriseringsmekanismer begränsar användare till specifika åtgärder och data. |
| [Azure SQL Always Encryption](/sql/relational-databases/security/encryption/always-encrypted-database-engine)|Skyddar känsliga data, t. ex. kreditkorts nummer eller nationella identifierings nummer (t. ex. amerikanskt socialförsäkrings nummer) som lagras i Azure SQL Database-eller SQL Server-databaser.  |
| [Azure &nbsp; SQL- &nbsp; Transparent datakryptering](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)| En databas säkerhets funktion som krypterar lagringen av en hel databas. |
| [Azure SQL Database granskning](../../azure-sql/database/auditing-overview.md)|En databas gransknings funktion som spårar databas händelser och skriver dem till en Gransknings logg på ditt Azure Storage-konto.  |


## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering
|Tjänst|Beskrivning|
|------|--------|
| [&nbsp;Rollbaserad &nbsp; åtkomst kontroll i Azure](../../role-based-access-control/role-assignments-portal.md)|En åtkomst kontroll funktion som är utformad för att tillåta användare att få åtkomst till de resurser som krävs för att få åtkomst baserat på deras roller i organisationen.  |
| [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)|En molnbaserad databas för autentisering som stöder en molnbaserad katalog med flera klienter och flera identitets hanterings tjänster i Azure.  |
| [Azure Active Directory B2C](../../active-directory-b2c/overview.md)|En identitets hanterings tjänst som gör det möjligt att styra hur kunder registrerar, loggar in och hanterar sina profiler när de använder Azure-baserade program.   |
| [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md)| En molnbaserad och hanterad version av Active Directory Domain Services. |
| [Azure AD Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)| En säkerhets bestämmelse som använder flera olika former av autentisering och verifiering innan åtkomst till skyddad information tillåts. |

## <a name="backup-and-disaster-recovery"></a>Säkerhets kopiering och haveri beredskap
|Tjänst|Beskrivning|
|------|--------|
| [Azure &nbsp; Backup](../../backup/backup-overview.md)| En Azure-baserad tjänst som används för att säkerhetskopiera och återställa data i Azure-molnet. |
| [Azure &nbsp; Site &nbsp; Recovery](../../site-recovery/site-recovery-overview.md)|En online tjänst som replikerar arbets belastningar som körs på fysiska och virtuella datorer (VM) från en primär plats till en sekundär plats för att aktivera återställning av tjänster efter ett haveri. |

## <a name="networking"></a>Nätverk
|Tjänst|Beskrivning|
|------|--------|
| [Nätverks &nbsp; säkerhets &nbsp; grupper](../../virtual-network/virtual-network-vnet-plan-design-arm.md)| En nätverks baserad åtkomst kontroll funktion som använder en 5-tupel för att tillåta eller neka beslut.  |
| [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md)| En nätverks enhet som används som en VPN-slutpunkt för att tillåta åtkomst mellan platser till virtuella Azure-nätverk.  |
| [Azure Application Gateway](../../application-gateway/overview.md)|En avancerad webb programs belastningsutjämnare som kan dirigeras baserat på URL och utföra SSL-avlastning. |
|[Brand vägg för webbaserade program](../../web-application-firewall/afds/afds-overview.md) (WAF)|En funktion i Application Gateway som ger centraliserat skydd för dina webb program mot vanliga sårbarheter och sårbarheter|
| [Azure Load Balancer](../../load-balancer/load-balancer-overview.md)|En TCP/UDP-program för nätverks belastnings utjämning. |
| [Azure ExpressRoute](../../expressroute/expressroute-introduction.md)| En dedikerad WAN-länk mellan lokala nätverk och virtuella Azure-nätverk. |
| [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)| En global DNS-belastningsutjämnare.|
| [Azure Application Proxy](../../active-directory/manage-apps/application-proxy.md)| En autentiserande klient del som används för att skydda fjärråtkomst för webb program som finns lokalt. |
|[Azure Firewall](../../firewall/overview.md)|En hanterad, molnbaserad nätverks säkerhets tjänst som skyddar dina Azure Virtual Network-resurser.|
|[Azure DDoS Protection](../../ddos-protection/ddos-protection-overview.md)|Kombinerat med bästa praxis för program design, ger skydd mot DDoS-attacker.|
|[Tjänstslutpunkter för virtuellt nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md)|Utökar det virtuella nätverkets privata adress utrymme och identiteten för ditt VNet till Azure-tjänsterna, via en direkt anslutning.|
