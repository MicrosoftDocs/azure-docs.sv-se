---
title: Azure Storage-kryptering av vilande data
description: Azure Storage skyddar dina data genom att automatiskt kryptera dem innan du sparar dem i molnet. Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av data i ditt lagrings konto, eller så kan du hantera kryptering med dina egna nycklar.
services: storage
author: tamram
ms.service: storage
ms.date: 03/23/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 0688e14b77d885132d6c3fbaa44bed117cc7cf9d
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641121"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage-kryptering av vilande data

Azure Storage använder SSE (Server Side Encryption) för att automatiskt kryptera dina data när de sparas i molnet. Azure Storage kryptering skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden.

## <a name="about-azure-storage-encryption"></a>Om Azure Storage kryptering

Data i Azure Storage krypteras och dekrypteras transparent med 256-bitars AES- [kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), en av de starkaste block chiffer som är tillgängliga och är FIPS 140-2-kompatibel. Azure Storage kryptering liknar BitLocker-kryptering på Windows.

Azure Storage kryptering har Aktiver ATS för alla lagrings konton, inklusive både Resource Manager och klassiska lagrings konton. Azure Storage kryptering kan inte inaktive ras. Eftersom dina data är säkra som standard behöver du inte ändra koden eller programmen för att dra nytta av Azure Storage kryptering.

Data i ett lagrings konto är krypterade oavsett prestanda nivå (standard eller Premium), åtkomst nivå (frekvent eller låg frekvent) eller distributions modell (Azure Resource Manager eller klassisk). Alla blobbar på Arkiv nivån krypteras också. Alla Azure Storage alternativ för redundans stöder kryptering och alla data i både den primära och den sekundära regionen krypteras när geo-replikering är aktiverat. Alla Azure Storage-resurser krypteras, inklusive blobbar, diskar, filer, köer och tabeller. Alla metadata för objekt krypteras också. Det kostar inget extra att Azure Storage kryptering.

Varje Block-Blob, Lägg till BLOB eller sid-blob som skrevs till Azure Storage efter den 20 oktober 2017 är krypterad. Blobbar som skapats före detta datum fortsätter att krypteras med en bakgrunds process. Om du vill framtvinga kryptering av en blob som skapades före den 20 oktober 2017 kan du skriva om blobben. Information om hur du kontrollerar krypterings statusen för en BLOB finns i [kontrol lera krypterings statusen för en BLOB](../blobs/storage-blob-encryption-status.md).

Mer information om de kryptografiska modulerna underliggande Azure Storage kryptering finns i [Cryptography-API: nästa generation](/windows/desktop/seccng/cng-portal).

Information om kryptering och nyckel hantering för Azure Managed disks finns i [kryptering på Server sidan för Azure Managed disks](../../virtual-machines/disk-encryption.md).

## <a name="about-encryption-key-management"></a>Om hantering av krypterings nyckel

Data i ett nytt lagrings konto krypteras med Microsoft-hanterade nycklar som standard. Du kan fortsätta att förlita dig på Microsoft-hanterade nycklar för kryptering av dina data, eller så kan du hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar har du två alternativ. Du kan använda båda typerna av nyckel hantering eller båda:

- Du kan ange en *kundhanterad nyckel* som ska användas för att kryptera och dekryptera data i Blob Storage och i Azure Files. <sup>1, 2</sup> Kundhanterade nycklar måste lagras i Azure Key Vault eller Azure Key Vault-hanterad maskin varu säkerhets modell (HSM) (för hands version). Mer information om Kundhanterade nycklar finns i [använda Kundhanterade nycklar för Azure Storage kryptering](./customer-managed-keys-overview.md).
- Du kan ange en *anpassad nyckel* för Blob Storage-åtgärder. En klient som gör en Läs-eller skrivbegäran mot Blob Storage kan inkludera en krypterings nyckel på begäran om detaljerad kontroll över hur BLOB-data krypteras och dekrypteras. Mer information om kundspecifika nycklar finns i [Ange en krypterings nyckel på en begäran till Blob Storage](../blobs/encryption-customer-provided-keys.md).

I följande tabell jämförs nyckel hanterings alternativ för Azure Storage kryptering.

| Nyckel hanterings parameter | Microsoft-hanterade nycklar | Kundhanterade nycklar | Kund-tillhandahållna nycklar |
|--|--|--|--|
| Kryptering/dekryptering | Azure | Azure | Azure |
| Azure Storage tjänster som stöds | Alla | Blob Storage, Azure Files<sup>1, 2</sup> | Blob Storage |
| Nyckel lagring | Microsoft nyckel lager | Azure Key Vault eller Key Vault HSM | Kundens egna nyckel lager |
| Största ansvar för nyckel rotation | Microsoft | Kund | Kund |
| Nyckel kontroll | Microsoft | Kund | Kund |

<sup>1</sup> information om hur du skapar ett konto som stöder användning av Kundhanterade nycklar med Queue Storage finns i [skapa ett konto som stöder Kundhanterade nycklar för köer](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> mer information om hur du skapar ett konto som stöder användning av Kundhanterade nycklar med Table Storage finns i [skapa ett konto som stöder Kundhanterade nycklar för tabeller](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

> [!NOTE]
> Microsoft-hanterade nycklar roteras på rätt sätt enligt kraven för efterlevnad. Om du har specifika krav för nyckel rotation rekommenderar Microsoft att du flyttar till Kundhanterade nycklar så att du kan hantera och granska rotationen själv.

## <a name="doubly-encrypt-data-with-infrastructure-encryption"></a>Dubblera kryptering av data med infrastruktur kryptering

Kunder som kräver höga garantier för att deras data är säkra kan också aktivera 256-bitars AES-kryptering på Azure Storage infrastruktur nivå. När infrastruktur kryptering har Aktiver ATS krypteras data i ett lagrings konto två gånger &mdash; på tjänst nivå och en gång på infrastruktur nivå &mdash; med två olika krypteringsalgoritmer och två olika nycklar. Dubbel kryptering av Azure Storage data skyddar mot ett scenario där en av krypteringsalgoritmerna eller nycklarna kan komprometteras. I det här scenariot fortsätter det ytterligare lagret med kryptering att skydda dina data.

Kryptering på tjänst nivå stöder användning av antingen Microsoft-hanterade nycklar eller kund hanterade nycklar med Azure Key Vault. Kryptering på infrastruktur nivå är beroende av Microsoft-hanterade nycklar och använder alltid en separat nyckel.

Mer information om hur du skapar ett lagrings konto som möjliggör infrastruktur kryptering finns i [skapa ett lagrings konto med infrastruktur kryptering aktiverat för dubbel kryptering av data](infrastructure-encryption-enable.md).

## <a name="next-steps"></a>Nästa steg

- [Vad är Azure Key Vault?](../../key-vault/general/overview.md)
- [Kundhanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
- [Krypterings omfång för Blob Storage](../blobs/encryption-scope-overview.md)
- [Ange en krypterings nyckel för en begäran till Blob Storage](../blobs/encryption-customer-provided-keys.md)
