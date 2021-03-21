---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 9750eabf2aa5af4f431f2db17e113b07d3bce863
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017701"
---
Använd alltid [Azure AD-säkerhetsgrupper](../articles/active-directory/fundamentals/active-directory-manage-groups.md) som det tilldelade huvudobjektet i en ACL-post. Motstå möjligheten att direkt tilldela enskilda användare eller tjänst huvud namn. Med den här strukturen kan du lägga till och ta bort användare eller tjänstens huvud namn utan att behöva tillämpa ACL: er på en hel katalog struktur. I stället kan du bara lägga till eller ta bort användare och tjänstens huvud namn från rätt Azure AD-säkerhetsgrupp. 

Det finns många olika sätt att konfigurera grupper. Anta till exempel att du har en katalog med namnet **/LogData** som innehåller loggdata som genereras av servern. Azure Data Factory (ADF) matar in data i den mappen. Vissa användare från service teknik teamet laddar upp loggar och hanterar andra användare av den här mappen och olika Databricks-kluster kommer att analysera loggar från den mappen. 

Om du vill aktivera dessa aktiviteter kan du skapa en `LogsWriter` grupp och en `LogsReader` grupp. Sedan kan du tilldela behörigheter enligt följande:

- Lägg till `LogsWriter` gruppen i ACL: en för **/LogData** -katalogen med `rwx` behörigheter.
- Lägg till `LogsReader` gruppen i ACL: en för **/LogData** -katalogen med `r-x` behörigheter.
- Lägg till tjänstens huvud objekt eller Hanterad tjänstidentitet (MSI) för ADF i `LogsWriters` gruppen.
- Lägg till användare i service Engineering-teamet i `LogsWriter` gruppen.
- Lägg till tjänstens huvud objekt eller MSI för Databricks i `LogsReader` gruppen.

Om en användare i service Engineering-teamet lämnar företaget kan du bara ta bort dem från `LogsWriter` gruppen. Om du inte har lagt till användaren i en grupp, men i stället lade till en dedikerad ACL-post för användaren, skulle du behöva ta bort ACL-posten från **/LogData** -katalogen. Du måste också ta bort posten från alla under kataloger och filer i hela katalogens hierarki i **/LogData** -katalogen. 

Information om hur du skapar en grupp och lägger till medlemmar finns i [skapa en grundläggande grupp och lägga till medlemmar med hjälp av Azure Active Directory](../articles/active-directory/fundamentals/active-directory-groups-create-azure-portal.md).