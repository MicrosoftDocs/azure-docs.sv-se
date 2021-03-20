---
title: Microsoft Azure Data Box Disk säkerhetsfunktioner | Microsoft Docs i data
description: Beskriver säkerhetsfunktioner som implementeras för Azure Data Box Disk, en moln lösning som gör att du kan överföra stora mängder data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: alkohli
ms.openlocfilehash: 59cce6d89bc863ad489a8ed37c7f284d3085c775
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "79117291"
---
# <a name="azure-data-box-disk-security-and-data-protection"></a>Säkerhet och dataskydd i Azure Data Box Disk

Den här artikeln beskriver säkerhetsfunktionerna i Azure Data Box Disk som skyddar komponenterna i Data Box-lösningen och de data som lagras i dem. 

## <a name="data-flow-through-components"></a>Dataflöde genom komponenter

Microsoft Azure Data Box-lösningen består av fyra huvudkomponenter som interagerar med varandra:

- **Azure Data Box-tjänsten i Azure** – Hanteringstjänsten som du använder för att skapa diskbeställningen, konfigurera diskarna och spåra ordern från början till slut.
- **Data Box-diskar** – De fysiska diskarna som skickas till dig så att du kan importera dina lokala data till Azure. 
- **Klienter/värdar som är anslutna till diskarna** – Klienterna i din infrastruktur som ansluter till Data Box-disken via USB och som innehåller data som måste skyddas.
- **Molnlagring** – Platsen i Azure-molnet där data lagras. Det här är vanligtvis lagringskontot som är länkat till den Azure Data Box-resurs som du skapade.

Följande diagram visar hur data flödar genom Azure Data Box Disk-lösningen från din lokala infrastruktur till Azure.

![Data Box Disk-säkerhet](media/data-box-disk-security/data-box-disk-security-1.png)

## <a name="security-features"></a>Säkerhetsfunktioner

Data Box Disk är en säker lösning för dataskydd som garanterar att endast behöriga entiteter kan visa, ändra eller ta bort dina data. Säkerhetsfunktionerna i den här lösningen omfattar disken och den associerade tjänsten, och skyddar de data som lagras på disken och i tjänsten.

### <a name="data-box-disk-protection"></a>Data Box Disk-skydd

Data Box Disk-enheter skyddas med följande funktioner:

- BitLocker AES-128-bitarskryptering för disken under hela processen.
- Säkra uppdateringsfunktioner för diskarna.
- Diskarna är låsta när de skickas och kan endast låsas upp med hjälp av ett Data Box Disk-upplåsningsverktyg. Upplåsningsverktyget är tillgängligt på portalen för Data Box Disk-tjänsten.

### <a name="data-box-disk-data-protection"></a>Data Box Disk-dataskydd

Data som flödar in och ut från Data Box Disk skyddas med hjälp av följande funktioner:

- BitLocker-kryptering av data under hela processen.
- Data raderas från disken när dataöverföringen till Azure är klar. Dataraderingen följer NIST 800-88r1-standarder.

### <a name="data-box-service-protection"></a>Skydd av Data Box-tjänsten

Data Box-tjänsten skyddas med hjälp av följande funktioner.

- Åtkomst till Data Box Disk-tjänsten kräver att din organisation har en Azure-prenumeration som omfattar Data Box Disk. Prenumerationen styr vilka funktioner du kan komma åt på Azure Portal.
- Eftersom Data Box-tjänsten finns i Azure skyddas den med hjälp av säkerhetsfunktionerna i Azure. Läs mer om säkerhetsfunktionerna i Microsoft Azure på [Microsoft Azure Säkerhetscenter](https://www.microsoft.com/TrustCenter/Security/default.aspx).
- Data Box Disk lagrar disknyckeln som används för att låsa upp disken i tjänsten. 
- Data Box Disk-tjänsten lagrar orderinformationen och orderstatusen i tjänsten. Den här informationen tas bort när ordern tas bort.

## <a name="managing-personal-data"></a>Hantera personliga data

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Azure Data Box Disk samlar in och visar personlig information i följande viktiga instanser i tjänsten:

- **Meddelandeinställningar** – När du skapar en order konfigurerar du e-postadressen till användare i meddelandeinställningarna. Administratörer har tillgång till den här informationen. Den här informationen tas bort av tjänsten när jobbet har slutförts eller när du tar bort ordern.

- **Orderdetaljer** – När ordern har skapats lagras leveransadressen, e-postadressen och kontaktuppgifterna för användare på Azure Portal. Informationen som sparas omfattar:

  - Kontaktnamn
  - Telefonnummer
  - E-post
  - Gatuadress
  - City
  - Postnummer
  - Tillstånd
  - Land/region
  - Enhets-ID
  - Transportföretagets kontonummer
  - Spårningsnummer för leveransen

    Orderinformationen tas bort av Data Box-tjänsten när jobbet har slutförts eller när du tar bort ordern.

- **Leveransadress** – När beställningen har gjorts uppger Data Box-tjänsten leveransadressen till tredjepartsleverantörer, till exempel UPS eller DHL. 

Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).


## <a name="next-steps"></a>Nästa steg

- Gå igenom [kraven för Data Box Disk](data-box-disk-system-requirements.md).
- Förstå [Data Box Disk-begränsningarna](data-box-disk-limits.md).
- Distribuera snabbt [Azure Data Box Disk](data-box-disk-quickstart-portal.md) på Azure-portalen.
