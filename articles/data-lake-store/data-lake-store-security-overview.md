---
title: Översikt över säkerhet i Azure Data Lake Storage Gen1 | Microsoft Docs
description: Lär dig mer om säkerhetsfunktioner i Azure Data Lake Storage Gen1, inklusive autentisering, auktorisering, nätverks isolering, data skydd och granskning.
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: twooley
ms.openlocfilehash: 240018381a3139a6378141d78514e43ae469de5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92146319"
---
# <a name="security-in-azure-data-lake-storage-gen1"></a>Säkerhet i Azure Data Lake Storage Gen1

Många företag drar nytta av stor data analys för affärs insikter för att hjälpa dem att fatta smarta beslut. En organisation kan ha en komplex och reglerad miljö med ett ökande antal olika användare. Det är viktigt för företaget att se till att viktiga affärs data lagras på ett säkert sätt med rätt åtkomst nivå för enskilda användare. Azure Data Lake Storage Gen1 är utformad för att hjälpa till att uppfylla dessa säkerhets krav. I den här artikeln får du lära dig mer om säkerhetsfunktionerna i Data Lake Storage Gen1, inklusive:

* Autentisering
* Auktorisering
* Nätverksisolering
* Dataskydd
* Granskning

## <a name="authentication-and-identity-management"></a>Autentisering och identitets hantering

Autentisering är den process genom vilken en användares identitet verifieras när användaren interagerar med Data Lake Storage Gen1 eller med en tjänst som ansluter till Data Lake Storage Gen1. För identitets hantering och autentisering använder Data Lake Storage Gen1 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), en omfattande identitets-och åtkomst hanterings moln lösning som fören klar hanteringen av användare och grupper.

Varje Azure-prenumeration kan associeras med en instans av Azure Active Directory. Endast användare och tjänst identiteter som har definierats i Azure Active Directorys tjänsten kan komma åt ditt Data Lake Storage Gen1-konto genom att använda Azure Portal, kommando rads verktyg eller via klient program som din organisation skapar med hjälp av Data Lake Storage Gen1 SDK. Viktiga fördelar med att använda Azure Active Directory som en centraliserad mekanism för åtkomst kontroll är:

* Förenklad identitets livs cykel hantering. Identiteten för en användare eller en tjänst (en huvud tjänst identitet) kan snabbt skapas och återkallas genom att du helt enkelt tar bort eller inaktiverar kontot i katalogen.
* Multi-Factor Authentication. [Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md) tillhandahåller ett extra säkerhets lager för användar inloggningar och transaktioner.
* Autentisering från vilken klient som helst via ett öppet standard protokoll, till exempel OAuth eller OpenID.
* Federation med Enterprise Directory-tjänster och moln identitets leverantörer.

## <a name="authorization-and-access-control"></a>Auktorisering och åtkomst kontroll

När Azure Active Directory autentiserat en användare så att användaren kan komma åt Data Lake Storage Gen1, kontrollerar auktorisering åtkomst behörigheterna för Data Lake Storage Gen1. Data Lake Storage Gen1 separerar auktoriseringen för konto-och data relaterade aktiviteter på följande sätt:

* [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) för konto hantering
* POSIX ACL för åtkomst till data i Store

### <a name="azure-rbac-for-account-management"></a>Azure RBAC för konto hantering

Fyra grundläggande roller definieras för Data Lake Storage Gen1 som standard. Rollerna tillåter olika åtgärder på ett Data Lake Storage Gen1 konto via Azure Portal, PowerShell-cmdletar och REST-API: er. Rollen ägare och deltagare kan utföra olika administrations funktioner på kontot. Du kan tilldela rollen läsare rollen till användare som bara visar konto hanterings data.

![Azure-roller](./media/data-lake-store-security-overview/rbac-roles.png "Azure-roller")

Observera att även om roller tilldelas för konto hantering, påverkar vissa roller åtkomsten till data. Du måste använda ACL: er för att kontrol lera åtkomsten till åtgärder som en användare kan utföra i fil systemet. I följande tabell visas en sammanfattning av hanterings rättigheter och data åtkomst rättigheter för standard rollerna.

| Roller | Hanterings rättigheter | Åtkomst behörigheter för data | Förklaring |
| --- | --- | --- | --- |
| Ingen roll har tilldelats |Inget |Styrs av ACL |Användaren kan inte använda Azure Portal eller Azure PowerShell-cmdletar för att bläddra i Data Lake Storage Gen1. Användaren kan bara använda kommando rads verktyg. |
| Ägare |Alla |Alla |Ägar rollen är en superanvändare. Den här rollen kan hantera allt och har fullständig åtkomst till data. |
| Läsare |Skrivskyddad |Styrs av ACL |Rollen läsare kan visa allt om konto hantering, till exempel vilken användare som är tilldelad till vilken roll. Rollen läsare kan inte göra några ändringar. |
| Deltagare |Alla utom Lägg till och ta bort roller |Styrs av ACL |Deltagar rollen kan hantera vissa aspekter av ett konto, till exempel distributioner och skapande och hantering av aviseringar. Deltagar rollen kan inte lägga till eller ta bort roller. |
| Administratör för användaråtkomst |Lägga till och ta bort roller |Styrs av ACL |Rollen administratör för användar åtkomst kan hantera användar åtkomst till konton. |

Instruktioner finns i [tilldela användare eller säkerhets grupper för att data Lake Storage gen1 konton](data-lake-store-secure-data.md#assign-users-or-security-groups-to-data-lake-storage-gen1-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Använda ACL: er för åtgärder i fil system

Data Lake Storage Gen1 är ett hierarkiskt fil system som Hadoop Distributed File System (HDFS) och stöder [POSIX ACL: er](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Den kontrollerar läsa (r), skriva (w) och köra (x) behörigheter för resurser för ägar rollen, för gruppen ägare och för andra användare och grupper. I Data Lake Storage Gen1 kan ACL: er aktive ras i rotmappen, i undermappar och på enskilda filer. Mer information om hur ACL: er fungerar i samband med Data Lake Storage Gen1 finns i [åtkomst kontroll i data Lake Storage gen1](data-lake-store-access-control.md).

Vi rekommenderar att du definierar ACL: er för flera användare med hjälp av [säkerhets grupper](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Lägg till användare i en säkerhets grupp och tilldela sedan ACL: er för en fil eller mapp till den säkerhets gruppen. Detta är användbart när du vill ge tilldelade behörigheter, eftersom du är begränsad till högst 28 poster för tilldelade behörigheter. Mer information om hur du bättre skyddar data som lagras i Data Lake Storage Gen1 genom att använda Azure Active Directory säkerhets grupper finns i [tilldela användare eller säkerhets grupp som ACL: er till data Lake Storage gen1 fil systemet](data-lake-store-secure-data.md#filepermissions).

![Lista åtkomst behörigheter](./media/data-lake-store-security-overview/adl.acl.2.png "Lista åtkomst behörigheter")

## <a name="network-isolation"></a>Nätverksisolering

Använd Data Lake Storage Gen1 för att kontrol lera åtkomsten till ditt data lager på nätverks nivå. Du kan upprätta brand väggar och definiera ett IP-adressintervall för dina betrodda klienter. Med ett IP-adressintervall kan endast klienter som har en IP-adress inom det definierade intervallet ansluta till Data Lake Storage Gen1.

![Brand Väggs inställningar och IP-åtkomst](./media/data-lake-store-security-overview/firewall-ip-access.png "Brand Väggs inställningar och IP-adress")

Service märken för Azure Virtual Networks (VNet) för Data Lake gen 1. En service-tagg representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras. Mer information finns i [Översikt över Azure Service-Taggar](../virtual-network/service-tags-overview.md).

## <a name="data-protection"></a>Dataskydd

Data Lake Storage Gen1 skyddar dina data i hela livs cykeln. För data som överförs använder Data Lake Storage Gen1 protokollet bransch standard Transport Layer Security (TLS 1,2) för att skydda data över nätverket.

![Kryptering i Data Lake Storage Gen1](./media/data-lake-store-security-overview/adls-encryption.png "Kryptering i Data Lake Storage Gen1")

Data Lake Storage Gen1 innehåller även kryptering för data som lagras i kontot. Du kan välja att ha krypterade data eller välja ingen kryptering. Om du väljer att använda kryptering krypteras data som lagras i Data Lake Storage Gen1 innan de lagras på ett beständigt medium. I sådana fall krypterar Data Lake Storage Gen1 automatiskt data innan de bevarar och dekrypterar data före hämtning, så att de är helt transparenta för klienten som har åtkomst till data. Ingen kod ändring krävs på klient sidan för att kryptera/dekryptera data.

För nyckel hantering har Data Lake Storage Gen1 två lägen för att hantera dina huvud krypterings nycklar (MEKs), vilket krävs för att dekryptera data som lagras i Data Lake Storage Gen1. Du kan antingen låta Data Lake Storage Gen1 hantera MEKs åt dig eller välja att behålla ägarskapet för MEKs med ditt Azure Key Vault-konto. Du anger läget för nyckel hantering när du skapar ett Data Lake Storage Gen1-konto. Mer information om hur du tillhandahåller en krypterings-relaterad konfiguration finns i [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Aktivitets- och diagnostikloggar

Du kan använda aktivitets-eller diagnostikloggar, beroende på om du söker efter loggar för konto hanterings aktiviteter eller data relaterade aktiviteter.

* Konto hantering-relaterade aktiviteter använder Azure Resource Manager-API: er och visas i Azure Portal via aktivitets loggar.
* Datarelaterade aktiviteter använder WebHDFS REST API: er och visas i Azure Portal via diagnostikloggar.

### <a name="activity-log"></a>Aktivitetslogg

För att följa regler kan en organisation kräva tillräckliga gransknings historik för konto hanterings aktiviteter om det behövs för att kunna träffa vissa incidenter. Data Lake Storage Gen1 har inbyggd övervakning och loggar alla konto hanterings aktiviteter.

Visa och välj de kolumner som du vill logga i gransknings historiken för konto hantering. Du kan också exportera aktivitets loggar till Azure Storage.

![Aktivitetslogg](./media/data-lake-store-security-overview/activity-logs.png "Aktivitetslogg")

Mer information om hur du arbetar med aktivitets loggar finns i [Visa aktivitets loggar för att granska åtgärder på resurser](../azure-resource-manager/management/view-activity-logs.md).

### <a name="diagnostics-logs"></a>Diagnostikloggar

Du kan aktivera data åtkomst granskning och diagnostisk loggning i Azure Portal och skicka loggarna till ett Azure Blob Storage-konto, en Event Hub-eller Azure Monitor-loggar.

![Diagnostikloggar](./media/data-lake-store-security-overview/diagnostic-logs.png "Diagnostikloggar")

Mer information om hur du arbetar med diagnostikloggar med Data Lake Storage Gen1 finns i [använda diagnostikloggar för data Lake Storage gen1](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Sammanfattning

Företags kunder kräver en moln plattform för data analys som är säker och lätt att använda. Data Lake Storage Gen1 är utformad för att hjälpa dig att åtgärda dessa krav via identitets hantering och autentisering via Azure Active Directory-integrering, ACL-baserad auktorisering, nätverks isolering, data kryptering under överföring och i vila och granskning.

Om du vill se nya funktioner i Data Lake Storage Gen1 skickar du oss feedback i [data Lake Storage gen1 UserVoice-forumet](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Se även

* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Kom igång med Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)