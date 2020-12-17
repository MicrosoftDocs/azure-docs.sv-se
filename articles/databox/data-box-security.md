---
title: Översikt över Microsoft Azure Data Box-säkerhet – översikt| Microsoft Docs i data
description: Beskriver Azure Data Box säkerhetsfunktioner i enheten, tjänsten och data som finns på Data Box-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: alkohli
ms.openlocfilehash: 4d6c77b3e8920cabc397cdcbc235baefa031e5ab
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655500"
---
# <a name="azure-data-box-security-and-data-protection"></a>Säkerhet och dataskydd i Azure Data Box

Data Box är en säker lösning för dataskydd som garanterar att endast behöriga entiteter kan visa, ändra eller ta bort dina data. Den här artikeln beskriver säkerhetsfunktionerna i Azure Data Box som skyddar komponenterna i Data Box-lösningen och de data som lagras i dem.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="data-flow-through-components"></a>Dataflöde genom komponenter

Microsoft Azure Data Box-lösningen består av fyra huvudkomponenter som interagerar med varandra:

- **Azure Data Box-tjänsten i Azure** – Hanteringstjänsten som du använder för att skapa enhetsbeställningen, konfigurera enheten och spåra ordern från början till slut.
- **Data Box-enhet** – Överföringsenheten som levereras till dig för att importera dina lokala data till Azure.
- **Klienter/värdar som är anslutna till enheten** – Klienterna i din infrastruktur som ansluter till Data Box-enheten via USB och som innehåller data som måste skyddas.
- **Molnlagring** – Platsen i Azure-molnet där data lagras. Den platsen är vanligt vis det lagrings konto som är kopplat till Azure Data Box resurs som du har skapat.

Följande diagram visar data flödet genom Azure Data Box lösning från lokal plats till Azure och de olika säkerhetsfunktionerna på plats när data flödar genom lösningen. Det här flödet är för en import ordning för din Data Box-enhet.

![Data Box-enhet importera säkerhet](media/data-box-security/data-box-security-import.png)

Följande diagram är för export ordningen för Data Box-enhet.

![Data Box-enhet exportera säkerhet](media/data-box-security/data-box-security-export.png)

När data flödar genom den här lösningen loggas händelser och loggar skapas. Mer information finns på:

- [Spårning och händelse loggning för dina Azure Data Box import order](data-box-logs.md).
- [Spårning och händelse loggning för dina Azure Data Box-export order](data-box-export-logs.md)

## <a name="security-features"></a>Säkerhetsfunktioner

Data Box är en säker lösning för dataskydd som garanterar att endast behöriga entiteter kan visa, ändra eller ta bort dina data. Säkerhetsfunktionerna i den här lösningen omfattar disken och den associerade tjänsten, och skyddar de data som lagras på disken och i tjänsten.

### <a name="data-box-device-protection"></a>Data Box-enhetsskydd

Data Box-enheten skyddas med hjälp av följande funktioner:

- Ett robust hölje som motstår stötar, felaktig transport och miljöförhållanden. 
- Identifiering av manipulation av maskin- och programvara som förhindrar vidare enhetsåtgärder.
- Kör endast Data Box-specifik programvara.
- Startar i låst tillstånd.
- Kontrollerar enhets åtkomst via en enhets upplåsnings nyckel. Den här nyckeln skyddas av en krypterings nyckel. Du kan använda din egen Kundhanterade nyckel för att skydda nyckeln. Mer information finns i [använda Kundhanterade nycklar i Azure Key Vault för Azure Data Box](data-box-customer-managed-encryption-key-portal.md).
- Autentiseringsuppgifter för åtkomst till att kopiera data till och från enheten. Varje åtkomst till sidan **autentiseringsuppgifter för enheten** i Azure Portal loggas i [aktivitets loggarna](data-box-logs.md#query-activity-logs-during-setup).
- Du kan använda dina egna lösen ord för enhets-och resurs åtkomst. Mer information finns i [Självstudier: beställ Azure Data Box](data-box-deploy-ordered.md).

### <a name="establish-trust-with-the-device-via-certificates"></a>Upprätta förtroende med enheten via certifikat

Med en Data Box-enhet enhet kan du ta med dina egna certifikat och installera dem som ska användas för att ansluta till det lokala webb gränssnittet och Blob Storage. Mer information finns i [använda dina egna certifikat med data Box-enhet och data Box Heavy enheter](data-box-bring-your-own-certificates.md).

### <a name="data-box-data-protection"></a>Data Box-dataskydd

Data som flödar in och ut från Data Box skyddas med hjälp av följande funktioner:

- AES 256-bitars kryptera för data i vila. I en miljö med hög säkerhet kan du använda programvarubaserad dubbel kryptering. Mer information finns i [Självstudier: beställ Azure Data Box](data-box-deploy-ordered.md).
- Krypterade protokoll kan användas för data i flykt. Vi rekommenderar att du använder SMB 3,0 med kryptering för att skydda data när du kopierar till den från dina data servrar.
- Säker radering av data från enheten när uppladdning till Azure har slutförts. Data radering är i enlighet med rikt linjerna i [bilaga A för ATA Hard Disk-enheter i NIST 800-88r1-standarder](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf). Händelsen radering av data registreras i [order historiken](data-box-logs.md#download-order-history).

### <a name="data-box-service-protection"></a>Skydd av Data Box-tjänsten

Data Box-tjänsten skyddas med hjälp av följande funktioner.

- Åtkomst till tjänsten Data Box-enhet kräver att din organisation har en Azure-prenumeration som innehåller Data Box-enhet. Prenumerationen styr vilka funktioner du kan komma åt på Azure Portal.
- Eftersom Data Box-tjänsten finns i Azure skyddas den med hjälp av säkerhetsfunktionerna i Azure. Läs mer om säkerhetsfunktionerna i Microsoft Azure på [Microsoft Azure Säkerhetscenter](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- Åtkomst till Data Box-enhets ordningen kan styras via användning av Azure-roller. Mer information finns i [Konfigurera åtkomst kontroll för data Box-enhet order](data-box-logs.md#set-up-access-control-on-the-order)
- Tjänsten Data Box-enhet lagrar det upplåsnings lösen ord som används för att låsa upp enheten i tjänsten.
- Data Box-tjänsten lagrar orderinformationen och orderstatusen i tjänsten. Den här informationen tas bort när ordern tas bort.

## <a name="managing-personal-data"></a>Hantera personliga data

Azure Data Box samlar in och visar personlig information i följande viktiga instanser i tjänsten:

- **Meddelandeinställningar** – När du skapar en order konfigurerar du e-postadressen till användare i meddelandeinställningarna. Administratörer har tillgång till den här informationen. Den här informationen tas bort av tjänsten när jobbet har slutförts eller när du tar bort ordern.

- **Beställnings information** – när ordern har skapats lagras leverans adress, e-post och kontakt information för användare i Azure Portal. Informationen som sparas omfattar:

  - Kontaktnamn
  - Telefonnummer
  - E-post
  - Gatuadress
  - City
  - Postnummer
  - Tillstånd
  - Land/region
  - Transportföretagets kontonummer
  - Spårningsnummer för leveransen

    Orderinformationen tas bort av Data Box-tjänsten när jobbet har slutförts eller när du tar bort ordern.

- **Leveransadress** – När beställningen har gjorts uppger Data Box-tjänsten leveransadressen till tredjepartsleverantörer, till exempel UPS eller DHL. 

Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).


## <a name="security-guidelines-reference"></a>Referens för säkerhetsriktlinjer

Följande säkerhetsriktlinjer implementeras i Data Box:

|Riktlinjer   |Beskrivning   |
|---------|---------|
|[IEC 60529 IP52](https://www.iec.ch/)    | För skydd mot vatten och damm         |
|[ISTA 2A](https://ista.org/docs/2Aoverview.pdf)     | För tålighet mot felaktiga transportsförhållanden          |
|[NIST SP 800-147](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-147.pdf)      | För säker uppdatering av inbyggd programvara         |
|[FIPS 140-2 nivå 2](https://csrc.nist.gov/csrc/media/publications/fips/140/2/final/documents/fips1402.pdf)      | För dataskydd         |
|Bilaga A, för ATA-hårddiskar i [NIST SP 800-88r1](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-88r1.pdf)      | För datasanering         |

## <a name="next-steps"></a>Nästa steg

- Läs [kraven för Data Box](data-box-system-requirements.md).
- Förstå [Data Box-begränsningarna](data-box-limits.md).
- Distribuera snabbt [Azure Data Box](data-box-quickstart-portal.md) i Azure-portalen.
