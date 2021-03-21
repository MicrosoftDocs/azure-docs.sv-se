---
title: Azure Storage Explorer säkerhets guide | Microsoft Docs
description: Säkerhets rikt linjer för Azure Storage Explorer
services: storage
author: cralvord
ms.service: storage
ms.topic: best-practice
ms.date: 07/30/2020
ms.author: cralvord
ms.openlocfilehash: 283ec9999f9b4362035b6770383984efb0879d49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783767"
---
# <a name="azure-storage-explorer-security-guide"></a>Azure Storage Explorer säkerhets guide

Med Microsoft Azure Storage Explorer kan du enkelt arbeta med Azure Storage data på ett säkert och säkert sätt på Windows, macOS och Linux. Genom att följa dessa rikt linjer kan du se till att dina data förblir skyddade.

## <a name="general"></a>Allmänt

- **Använd alltid den senaste versionen av Storage Explorer.** Storage Explorer versioner kan innehålla säkerhets uppdateringar. Hålla sig uppdaterad hjälper till att säkerställa allmän säkerhet.
- **Anslut endast till resurser som du litar på.** Data som du hämtar från ej betrodda källor kan vara skadliga, och överföring av data till en obetrodd källa kan leda till förlorade eller stulna data.
- **Använd HTTPS när det är möjligt.** Storage Explorer använder HTTPS som standard. Vissa scenarier låter dig använda HTTP, men HTTP bör endast användas som en sista utväg.
- **Se till att endast de behörigheter som krävs ges till de personer som behöver dem.** Undvik att tillåta att du beviljar någon åtkomst till dina resurser.
- **Var försiktig när du kör kritiska åtgärder.** Vissa åtgärder, t. ex. borttagning och överskrivning, är irreversibla och kan leda till data förlust. Se till att du arbetar med rätt resurser innan du utför de här åtgärderna.

## <a name="choosing-the-right-authentication-method"></a>Välja rätt autentiseringsmetod

Storage Explorer ger till gång till dina Azure Storage resurser på olika sätt. Oavsett vilken metod du väljer här är våra rekommendationer.

### <a name="azure-ad-authentication"></a>Azure AD-autentisering

Det enklaste och säkraste sättet att komma åt dina Azure Storage-resurser är att logga in med ditt Azure-konto. När du loggar in används Azure AD-autentisering, vilket gör att du kan:

- Ge åtkomst till vissa användare och grupper.
- Återkalla åtkomsten till vissa användare och grupper när som helst.
- Framtvinga åtkomst villkor, till exempel kräver Multi-Factor Authentication.

Vi rekommenderar att du använder Azure AD-autentisering närhelst det är möjligt.

I det här avsnittet beskrivs de två Azure AD-baserade teknikerna som kan användas för att skydda dina lagrings resurser.

#### <a name="azure-role-based-access-control-azure-rbac"></a>Azure RBAC (rollbaserad åtkomstkontroll)

[Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) ger dig detaljerad åtkomst kontroll över dina Azure-resurser. Azure-roller och-behörigheter kan hanteras från Azure Portal.

Storage Explorer stöder Azure RBAC-åtkomst till lagrings konton, blobbar och köer. Om du behöver åtkomst till fil resurser eller tabeller måste du tilldela Azure-roller som ger behörighet att lista lagrings konto nycklar.

#### <a name="access-control-lists-acls"></a>Åtkomstkontrollistor (ACL)

Med [åtkomst kontrol listor (ACL: er)](../blobs/data-lake-storage-access-control.md) kan du kontrol lera åtkomst till filer och mappar i ADLS Gen2 BLOB-behållare. Du kan hantera dina ACL: er med hjälp av Storage Explorer.

### <a name="shared-access-signatures-sas"></a>Signaturer för delad åtkomst (SAS)

Om du inte kan använda Azure AD-autentisering rekommenderar vi att du använder signaturer för delad åtkomst. Med signaturer för delad åtkomst kan du:

- Ge anonym åtkomst till säkra resurser.
- Återkalla en SAS omedelbart om den genereras från en princip för delad åtkomst (SAP).

Men med signaturer för delad åtkomst kan du inte:

- Begränsa vem som kan använda en SAS. En giltig SAS kan användas av alla som har den.
- Återkalla en SAS om den inte genereras från en princip för delad åtkomst (SAP).

När du använder SAS i Storage Explorer rekommenderar vi följande rikt linjer:

- **Begränsa distributionen av SAS-token och URI: er.** Distribuera bara SAS-token och URI: er till betrodda individer. Att begränsa SAS-distribution minskar risken för att en SAS kan vara felanvänd.
- **Använd bara SAS-token och URI: er från entiteter som du litar på.**
- **Använd principer för delad åtkomst (SAP) när du genererar SAS-token och URI: er om det är möjligt.** En SAS som baseras på en delad åtkomst princip är säkrare än en icke-tillförlitlig säkerhets Association, eftersom SAS kan återkallas genom att ta bort SAP.
- **Skapa token med minimal resurs åtkomst och behörigheter.** Minimal behörighet begränsar de potentiella skador som kan utföras om ett SAS-värde används.
- **Skapa tokens som bara är giltiga för så länge som behövs.** En kort livs längd är särskilt viktig för Bare SAS, eftersom det inte finns något sätt att återkalla dem när de har skapats.

> [!IMPORTANT]
> När du delar SAS-token och URI: er i fel söknings syfte, till exempel i tjänst begär Anden eller fel rapporter, måste du alltid redigera minst signaturen i SAS-delen.

### <a name="storage-account-keys"></a>Lagringskontonycklar

Lagrings konto nycklar ger obegränsad åtkomst till tjänster och resurser i ett lagrings konto. Därför rekommenderar vi att du begränsar användningen av nycklar för att få åtkomst till resurser i Storage Explorer. Använd Azure RBAC-funktioner eller SAS för att ge åtkomst i stället.

Vissa Azure-roller ger behörighet att hämta lagrings konto nycklar. Individer med dessa roller kan effektivt kringgå behörigheter som beviljas eller nekas av Azure RBAC. Vi rekommenderar inte att du beviljar den här behörigheten om det inte är nödvändigt.

Storage Explorer försöker använda lagrings konto nycklar, om de är tillgängliga, för att autentisera begär Anden. Du kan inaktivera den här funktionen i inställningar (**tjänster > lagrings konton > inaktivera användning av nycklar**). Vissa funktioner har inte stöd för Azure RBAC, till exempel att arbeta med klassiska lagrings konton. Sådana funktioner kräver fortfarande nycklar och påverkas inte av den här inställningen.

Om du måste använda nycklar för att komma åt dina lagrings resurser rekommenderar vi följande rikt linjer:

- **Dela inte dina konto nycklar med någon.**
- **Hantera dina lagrings konto nycklar som lösen ord.** Om du måste göra dina nycklar tillgängliga kan du använda säkra lagrings lösningar som [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

> [!NOTE]
> Om du tror att en lagrings konto nyckel har delats eller distribuerats av misstag kan du generera nya nycklar för ditt lagrings konto från Azure Portal.

### <a name="public-access-to-blob-containers"></a>Offentlig åtkomst till BLOB-behållare

Med Storage Explorer kan du ändra åtkomst nivån för dina Azure Blob Storage-behållare. Icke-privata BLOB-behållare låter alla anonym Läs åtkomst till data i dessa behållare.

När du aktiverar offentlig åtkomst för en BLOB-behållare rekommenderar vi följande rikt linjer:

- **Aktivera inte offentlig åtkomst till en BLOB-behållare som kan innehålla potentiellt känsliga data.** Se till att BLOB-behållaren är fri från alla privata data.
- **Ladda inte upp potentiellt känsliga data till en BLOB-behållare med BLOB-eller container åtkomst.** 

## <a name="next-steps"></a>Nästa steg

- [Säkerhetsrekommendationer](../blobs/security-recommendations.md)