---
title: Översikt – Azure Files identitets-baserad auktorisering
description: Azure Files stöder identitets-baserad autentisering över SMB (Server Message Block) via Azure Active Directory Domain Services (AD DS) och Active Directory. Dina domänanslutna virtuella Windows-datorer (VM) kan sedan komma åt Azure-filresurser med hjälp av Azure AD-autentiseringsuppgifter.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 05/29/2020
ms.author: rogarana
ms.openlocfilehash: 8887243f953a7bb000033a2e56b4655b93cd7ca8
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519800"
---
# <a name="overview-of-azure-files-identity-based-authentication-options-for-smb-access"></a>Översikt över Azure Files Identity-baserade autentiseringsalternativ för SMB-åtkomst
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Information om hur du aktiverar lokal Active Directory Domain Services autentisering för Azure-filresurser finns i [Aktivera lokal Active Directory Domain Services autentisering över SMB för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md).

Information om hur du aktiverar Azure AD DS-autentisering för Azure-filresurser finns i [aktivera Azure Active Directory Domain Services autentisering på Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md).

## <a name="glossary"></a>Ordlista 
Det är bra att förstå vissa viktiga villkor som rör Azure AD Domain Service-autentisering över SMB för Azure-filresurser:

-   **Kerberos-autentisering**

    Kerberos är ett autentiseringsprotokoll som används för att verifiera en användares eller värds identitet. Mer information om Kerberos finns i [Översikt över Kerberos-autentisering](/windows-server/security/kerberos/kerberos-authentication-overview).

-  **SMB-protokoll (Server Message Block)**

    SMB är ett standardiserat nätverks fil delnings protokoll. SMB kallas även common Internet File System eller CIFS. Mer information om SMB finns i [Översikt över Microsoft SMB-protokoll och CIFS-protokoll](/windows/desktop/FileIO/microsoft-smb-protocol-and-cifs-protocol-overview).

-   **Azure Active Directory (Azure AD)**

    Azure Active Directory (Azure AD) är Microsofts molnbaserade katalog-och identitets hanterings tjänst för flera innehavare. Azure AD kombinerar kärn katalog tjänster, program åtkomst hantering och identitets skydd i en enda lösning. Azure AD-anslutna virtuella Windows-datorer (VM) kan inte komma åt Azure-filresurser med dina autentiseringsuppgifter för Azure AD. Mer information finns i [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)

-   **Azure Active Directory Domain Services (Azure AD DS)**

    Azure AD DS tillhandahåller hanterade domän tjänster som domän anslutning, grup principer, LDAP och Kerberos/NTLM-autentisering. Dessa tjänster är helt kompatibla med Active Directory Domain Services. Mer information finns i [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md).

- **Lokala Active Directory Domain Services (AD DS)**

    Den lokala Active Directory Domain Services (AD DS)-integration med Azure Files innehåller metoder för att lagra katalog data och göra dem tillgängliga för nätverks användare och administratörer. Säkerheten är integrerad med AD DS via inloggningsautentisering och åtkomst kontroll till objekt i katalogen. Med en enda nätverks inloggning kan administratörer hantera katalog data och organisationer över hela nätverket och auktoriserade nätverks användare kan komma åt resurser var som helst i nätverket. AD DS antas ofta av företag i lokala miljöer och AD DS-autentiseringsuppgifter används som identitet för åtkomst kontroll. Mer information finns i [Active Directory Domain Services översikt](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

-   **Azure RBAC (rollbaserad åtkomstkontroll)**

    Rollbaserad åtkomst kontroll i Azure (Azure RBAC) möjliggör detaljerad åtkomst hantering för Azure. Med hjälp av Azure RBAC kan du hantera åtkomst till resurser genom att ge användarna minst de behörigheter som krävs för att utföra sina jobb. Mer information om Azure RBAC finns i [Vad är Azure rollbaserad åtkomst kontroll (Azure RBAC)?](../../role-based-access-control/overview.md).

## <a name="common-use-cases"></a>Vanliga användarsituationer

Identitetsbaserade autentisering och stöd för Windows ACL: er på Azure Files är bäst för följande användnings fall:

### <a name="replace-on-premises-file-servers"></a>Ersätta lokala fil servrar

Föråldrade och ersättande lokala fil servrar är ett vanligt problem som varje företag stöter på i IT-modernisering resan. Azure-filresurser med lokal AD DS-autentisering är den bästa anpassningen här, när du kan migrera data till Azure Files. Med en fullständig migrering kan du dra nytta av fördelarna med hög tillgänglighet och skalbarhet samtidigt som du minimerar ändringar på klient sidan. Det ger en sömlös migrering till slutanvändare, så de kan fortsätta att komma åt sina data med samma autentiseringsuppgifter med hjälp av sina befintliga domänanslutna datorer.

### <a name="lift-and-shift-applications-to-azure"></a>Lyft och växla program till Azure

När du lyfter och växlar program till molnet, vill du behålla samma autentiserings modell för dina data. När vi utökar den identitetsbaserade åtkomst kontrollen till Azure-filresurser, eliminerar den behovet av att ändra programmet till modern auth-metoder och påskynda moln införande. Azure-filresurser ger möjlighet att integrera med antingen Azure AD DS eller lokal AD DS för autentisering. Om din plan ska vara 100% Cloud Native och minimera ansträngningarna att hantera moln infrastrukturer, är Azure AD DS en bättre anpassning som en fullständigt hanterad domän tjänst. Om du behöver fullständig kompatibilitet med AD DS-funktioner kan det vara bra att utöka din AD DS-miljö till molnet med hjälp av självvärdbaserade domänkontrollanter på virtuella datorer. I båda fallen ger vi flexibiliteten att välja de domän tjänster som passar dina affärs behov.

### <a name="backup-and-disaster-recovery-dr"></a>Säkerhets kopiering och haveri beredskap (DR)

Om du behåller din primära fil lagring kan Azure-filresurser fungera som ett idealiskt lagrings utrymme för säkerhets kopiering eller DR för att förbättra affärs kontinuiteten. Du kan använda Azure-filresurser för att säkerhetskopiera dina data från befintliga fil servrar, samtidigt som du behåller Windows-DACL-filer. För DR-scenarier kan du konfigurera ett autentiseringsalternativ som stöd för korrekt åtkomst kontroll vid redundansväxling.

## <a name="supported-scenarios"></a>Scenarier som stöds

I följande tabell sammanfattas de Azure-filresurser som stöds för Azure AD DS och lokala AD DS. Vi rekommenderar att du väljer den domän tjänst som du har antagit för din klient miljö för integrering med Azure Files. Om du redan har installerat AD DS lokalt eller i Azure där enheterna är domänanslutna till din AD, bör du välja att använda AD DS för autentisering med Azure-filresurser. Om du redan har antagit Azure AD DS bör du använda det för att autentisera till Azure-filresurser.


|Azure AD DS-autentisering  | Lokal AD DS-autentisering  |
|---------|---------|
|Azure AD DS-anslutna Windows-datorer har åtkomst till Azure-filresurser med Azure AD-autentiseringsuppgifter över SMB.     |Lokala AD DS-anslutna eller Azure AD DS-anslutna Windows-datorer kan komma åt Azure-filresurser med lokala Active Directory autentiseringsuppgifter som synkroniseras med Azure AD över SMB. Din klient måste ha detaljerad information om din AD DS.        |

### <a name="restrictions"></a>Begränsningar

- Azure AD DS och lokal AD DS-autentisering stöder inte autentisering mot dator konton. Du kan använda ett tjänst inloggnings konto i stället.
- Varken Azure AD DS-autentisering eller lokal AD DS-autentisering stöds för Azure AD-anslutna enheter eller Azure AD-registrerade enheter.
- Azure-filresurser har endast stöd för identitetsbaserade autentisering mot någon av följande domän tjänster, antingen [Azure Active Directory Domain Services (Azure AD DS)](#azure-ad-ds) eller [lokalt Active Directory Domain Services (AD DS)](#ad-ds).
- Varken identitetsbaserade autentiseringsmetoder stöds med Network File System (NFS), som är en för hands version.

## <a name="advantages-of-identity-based-authentication"></a>Fördelar med Identity-baserad autentisering
Identitetsbaserade autentisering för Azure Files ger flera fördelar jämfört med autentisering med delade nycklar:

-   **Utöka den traditionella identitetsbaserade fil resurs åtkomst upplevelsen till molnet med lokal AD DS och Azure AD DS**  
    Om du planerar att lyfta och flytta ditt program till molnet, ersätta traditionella fil servrar med Azure-filresurser, kanske du vill att ditt program ska autentiseras med antingen lokala AD DS-eller Azure AD DS-autentiseringsuppgifter för att komma åt fildata. Azure Files stöder användning av både lokala AD DS-eller Azure AD DS-autentiseringsuppgifter för att få åtkomst till Azure-filresurser via SMB från antingen lokala AD DS-eller Azure AD DS-domänanslutna virtuella datorer.

-   **Använd detaljerad åtkomst kontroll på Azure-filresurser**  
    Du kan bevilja behörigheter till en speciell identitet på resurs-, katalog-eller filnivå. Anta till exempel att du har flera team som använder en enda Azure-filresurs för projekt samarbete. Du kan ge alla team åtkomst till icke-känsliga kataloger, samtidigt som du begränsar åtkomsten till kataloger som innehåller känslig finansiell information till ditt finans team. 

-   **Säkerhetskopiera Windows ACL: er (kallas även NTFS) tillsammans med dina data**  
    Du kan använda Azure-filresurser för att säkerhetskopiera befintliga lokala fil resurser. Azure Files bevarar dina ACL: er tillsammans med dina data när du säkerhetskopierar en fil resurs till Azure-filresurser över SMB.

## <a name="how-it-works"></a>Så här fungerar det

Azure-filresurser utnyttjar Kerberos-protokollet för autentisering med antingen lokalt AD DS eller Azure AD DS. När en identitet som är kopplad till en användare eller ett program som körs på en klient försöker komma åt data i Azure-filresurser, skickas begäran till domän tjänsten, antingen AD DS eller Azure AD DS, för att autentisera identiteten. Om autentiseringen lyckas returneras en Kerberos-token. Klienten skickar en begäran som inkluderar Kerberos-token och Azure-filresurser använder denna token för att auktorisera begäran. Azure-filresurser tar bara emot Kerberos-token, inte åtkomst till autentiseringsuppgifter.

Innan du kan aktivera Identity-baserad autentisering på Azure-filresurser måste du först konfigurera din domän miljö.

### <a name="ad-ds"></a>AD DS

För lokal AD DS-autentisering måste du konfigurera AD-domänkontrollanter och domän anslutning till dina datorer eller virtuella datorer. Du kan vara värd för dina domänkontrollanter på virtuella Azure-datorer eller lokalt. Oavsett hur måste domänanslutna klienter ha detaljerad information om domän tjänsten, så de måste ligga inom företagets nätverk eller virtuella nätverk (VNET) i domän tjänsten.

Följande diagram visar lokal AD DS-autentisering till Azure-filresurser över SMB. AD DS för lokal måste synkroniseras med Azure AD med hjälp av Azure AD Connect Sync. Endast Hybrid användare som finns i både lokala AD DS och Azure AD kan autentiseras och auktoriseras för åtkomst till Azure-filresurser. Detta beror på att behörigheten på resurs nivå har kon figurer ATS mot den identitet som representeras i Azure AD där behörigheten katalog-/filnivå tillämpas med den i AD DS. Se till att du konfigurerar behörigheterna korrekt mot samma Hybrid användare.

:::image type="content" source="media/storage-files-active-directory-overview/Files-on-premises-AD-DS-Diagram.png" alt-text="Diagram som visar lokal AD DS-autentisering till Azure-filresurser över SMB.":::

### <a name="azure-ad-ds"></a>Azure AD DS

För Azure AD DS-autentisering bör du aktivera Azure AD Domain Services och domän anslutning till de virtuella datorer som du planerar att komma åt fil data från. Din domänanslutna virtuella dator måste finnas i samma virtuella nätverk (VNET) som Azure AD DS. 

Följande diagram visar arbets flödet för Azure AD DS-autentisering till Azure-filresurser över SMB. Det följer ett liknande mönster för lokal AD DS-autentisering till Azure-filresurser. Det finns två större skillnader:

- Först behöver du inte skapa identiteten i Azure AD DS för att representera lagrings kontot. Detta utförs av aktiverings processen i bakgrunden.

- För det andra kan alla användare som finns i Azure AD autentiseras och auktoriseras. Användaren kan endast vara moln eller hybrid. Synkroniseringen från Azure AD till Azure AD DS hanteras av plattformen utan att det krävs någon användar konfiguration. Klienten måste dock vara domänansluten till Azure AD DS, den kan inte vara Azure AD-ansluten eller registrerad. 

:::image type="content" source="media/storage-files-active-directory-overview/Files-Azure-AD-DS-Diagram.png" alt-text="Venndiagram":::

### <a name="enable-identity-based-authentication"></a>Aktivera identitets baserad autentisering

Du kan aktivera identitets baserad autentisering med antingen Azure AD DS eller lokala AD DS för Azure-filresurser på dina nya och befintliga lagrings konton. Det går bara att använda en domän tjänst för autentisering av fil åtkomst på lagrings kontot, som gäller för alla fil resurser i kontot. Detaljerad vägledning om hur du konfigurerar dina fil resurser för autentisering med Azure AD DS i vår artikel [aktivera Azure Active Directory Domain Services autentisering på Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md) och rikt linjer för lokal AD DS i vår andra artikel, [Aktivera lokal Active Directory Domain Services autentisering över SMB för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md).

### <a name="configure-share-level-permissions-for-azure-files"></a>Konfigurera behörigheter på resurs nivå för Azure Files

När antingen Azure AD DS eller lokal AD DS-autentisering har Aktiver ATS kan du använda inbyggda Azure-roller eller konfigurera anpassade roller för Azure AD-identiteter och tilldela åtkomst behörigheter till alla fil resurser i dina lagrings konton. Den tilldelade behörigheten tillåter att den beviljade identiteten endast får åtkomst till resursen, inget annat, inte ens rot katalogen. Du måste fortfarande konfigurera katalog-eller fil nivå behörigheter separat för Azure-filresurser.

### <a name="configure-directory-or-file-level-permissions-for-azure-files"></a>Konfigurera katalog-eller fil nivå behörigheter för Azure Files

Azure-filresurser tillämpar standard behörigheter för Windows-filer både på katalog-och filnivå, inklusive rot katalogen. Konfiguration av katalog-eller fil nivå behörigheter stöds över både SMB och REST. Montera mål fil resursen från den virtuella datorn och konfigurera behörigheter med Windows Utforskaren, Windows- [icacls](/windows-server/administration/windows-commands/icacls)eller kommandot [set-ACL](/powershell/module/microsoft.powershell.security/get-acl) .

### <a name="use-the-storage-account-key-for-superuser-permissions"></a>Använd lagrings konto nyckeln för behörigheter för superanvändare

En användare med lagrings konto nyckeln kan komma åt Azure-filresurser med behörigheten Superanvändare. Behörigheter för superanvändare kringgå alla begränsningar för åtkomst kontroll.

> [!IMPORTANT]
> Vi rekommenderar att du inte delar dina lagrings konto nycklar och använder identitets baserad autentisering närhelst det är möjligt.

### <a name="preserve-directory-and-file-acls-when-importing-data-to-azure-file-shares"></a>Bevara katalog-och fil åtkomst kontrol listor när du importerar data till Azure-filresurser

Azure Files stöder bevarande av katalog-eller filnivå-ACL: er vid kopiering av data till Azure-filresurser. Du kan kopiera ACL: er på en katalog eller fil till Azure-filresurser med hjälp av antingen Azure File Sync eller vanliga verktyg för fil rörelse. Du kan till exempel använda [Robocopy](/windows-server/administration/windows-commands/robocopy) med `/copy:s` flaggan för att kopiera data samt ACL: er till en Azure-filresurs. ACL: er bevaras som standard, du behöver inte aktivera Identity-baserad autentisering på ditt lagrings konto för att bevara ACL: er.

## <a name="pricing"></a>Prissättning
Det finns ingen ytterligare service avgift för att aktivera identitets baserad autentisering över SMB på ditt lagrings konto. Mer information om priser finns i [Azure Files priser](https://azure.microsoft.com/pricing/details/storage/files/) och [Azure AD Domain Services priser](https://azure.microsoft.com/pricing/details/active-directory-ds/).

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files-och identitets-baserad autentisering över SMB finns i följande resurser:

- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Aktivera lokal Active Directory Domain Services autentisering över SMB för Azure-filresurser](storage-files-identity-auth-active-directory-enable.md)
- [Aktivera Azure Active Directory Domain Services autentisering på Azure Files](storage-files-identity-auth-active-directory-domain-service-enable.md)
- [Vanliga frågor och svar](storage-files-faq.md)