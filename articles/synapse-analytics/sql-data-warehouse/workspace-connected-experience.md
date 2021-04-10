---
title: Aktivera Synapse-arbetsytans funktioner på en dedikerad SQL-pool (tidigare SQL DW)
description: I det här dokumentet beskrivs hur en kund kan komma åt och använda sin befintliga SQL DW-fristående instans i arbets ytan.
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/23/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 6d71d9889701ec834747e4bec1dd111157c3206e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694622"
---
# <a name="enabling-synapse-workspace-features-on-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Aktivera Synapse-arbetsytans funktioner på en befintlig dedikerad SQL-pool (tidigare SQL DW)

Alla SQL Data Warehouse-kunder kan nu komma åt och använda en befintlig dedikerad SQL-pool (tidigare SQL DW)-instans via Synapse Studio och arbets ytan, utan att det påverkar automatisering, anslutningar eller verktyg. I den här artikeln förklaras hur en befintlig Azure Synapse Analytics-kund kan bygga på och utöka sin befintliga analys lösning genom att dra nytta av de nya funktioner som nu finns tillgängliga via arbets ytan Synapse och Studio.   

## <a name="experience"></a>Erfarenhet
 
Nu när Synapse-arbetsytan är GA är en ny funktion tillgänglig i översikts avsnittet för DW-portalen där du kan skapa en Synapse-arbetsyta för din befintliga dedikerade SQL-pool (tidigare SQL DW-instanser). Med den här nya funktionen kan du ansluta den logiska servern som är värd för dina befintliga informations lager instanser till en ny Synapse-arbetsyta. Anslutningen säkerställer att alla data lager som finns på den servern görs tillgängliga från arbets ytan och Studio och kan användas tillsammans med Synapse-partner tjänster (Server lös SQL-pool, Apache Spark pool och ADF). Du kan börja komma åt och använda dina resurser så snart etablerings stegen har slutförts och anslutningen har upprättats till den nyligen skapade arbets ytan.  
:::image type="content" source="media/workspace-connected-overview/workspace-connected-dw-portal-overview-pre-create.png" alt-text="Ansluten Synapse-arbetsyta":::

## <a name="using-synapse-workspace-and-studio-features-to-access-and-use-a-dedicated-sql-pool-formerly-sql-dw"></a>Använda Synapse-arbetsytan och Studio funktioner för att komma åt och använda en dedikerad SQL-pool (tidigare SQL DW)
 
Följande information gäller när du använder ett dedikerat SQL DW (tidigare SQL DW) med funktionerna för Synapse-arbetsytan aktiverade: 
- **SQL-funktioner** Alla SQL-funktioner kommer att fortsätta med den logiska SQL-servern när funktionen Synapse Workspace har Aktiver ATS. Det går fortfarande att komma åt servern via SQL Resource Provider när arbets ytan har Aktiver ATS. Alla hanterings funktioner kan initieras via arbets ytan och åtgärden utförs på den logiska SQL Server som är värd för dina SQL-pooler. Ingen befintlig automatisering, verktyg eller anslutningar kommer att brytas eller avbrytas när en arbets yta aktive ras.  
- **Resurs flytt**  Om du initierar en resurs förflyttning på en server med funktionen för Synapse-arbetsyta aktive rad kommer länken mellan servern och arbets ytan att brytas och du kommer inte längre att kunna komma åt din befintliga dedikerade SQL-pool (tidigare SQL DW) från arbets ytan. För att säkerställa att anslutningen bevaras rekommenderar vi att båda resurserna finns kvar i samma prenumeration och resurs grupp. 
- **Övervakning** SQL-begäranden som skickats via Synapse Studio i en dedikerad arbets yta som är aktiverade dedikerad SQL-pool (tidigare SQL DW) kan visas i Monitor Hub. För alla andra övervaknings aktiviteter kan du gå till Azure Portal dedikerad SQL-pool (tidigare SQL DW) övervakning. 
- **Säkerhets** -och **åtkomst kontroller** som anges ovan fortsätter alla hanterings funktioner för SQL Server-och dedikerade SQL-pooler (tidigare SQL DW) att finnas kvar på den logiska SQL-servern. Dessa funktioner omfattar, hantering av brand Väggs regler, inställning av serverns Azure AD-administratör och all åtkomst kontroll för data i din dedikerade SQL-pool (tidigare SQL DW). Följande steg måste utföras för att säkerställa att din dedikerade SQL-pool (tidigare SQL DW) är tillgänglig och kan användas via Synapse-arbetsytan. Medlemskap i arbets ytans roll ger inte användare behörighet till data i dedikerad SQL-pool (tidigare SQL DW). Följ dina principer för normal [SQL-autentisering](sql-data-warehouse-authentication.md) för att se till att användarna kan komma åt den dedikerade SQL-poolen (tidigare SQL DW)-instanser på den logiska servern. Om den dedikerade SQL-poolen (tidigare SQL DW) har en hanterad identitet som redan har tilldelats till den, kommer den här hanterade identitetens namn att vara samma som för den hanterade identiteten för arbets ytan som skapas automatiskt för att stödja arbets ytans partner tjänster (t. ex. ADF-pipeliner).  Två hanterade identiteter med samma namn kan finnas i ett anslutet scenario. De hanterade identiteterna kan särskiljas med sina Azure AD-objekt-ID: n och funktioner för att skapa SQL-användare som använder objekt-ID: n kommer snart.

    ```sql
    CREATE USER [<workspace managed identity] FROM EXTERNAL PROVIDER 
    GRANT CONTROL ON DATABASE:: <dedicated SQL pool name> TO [<workspace managed identity>
    ```

    > [!NOTE] 
    > Den anslutna arbets ytan Synapse Studio visar namnen på dedikerade pooler baserat på de behörigheter som användaren har i Azure. Objekt under pooler kommer inte att vara tillgängliga om användaren inte har behörighet för SQL-pooler. 

- **Nätverks säkerhet** Om arbets ytan Synapse som du aktiverade på den befintliga dedikerade SQL-poolen (tidigare SQL DW) är aktive rad för data intrånget-skydd. Skapa en hanterad privat slut punkts anslutning från arbets ytan till den logiska SQL-servern. Godkänn anslutnings förfrågan för privat slutpunkt för att tillåta kommunikation mellan servern och arbets ytan.
- **Studio** SQL-pooler i **data hubben** en dedikerad SQL-pool (tidigare SQL DW) kan identifieras via verktygs tipset i data hubben. 
- **Skapa en ny dedikerad SQL-pool (tidigare SQL DW)** Nya dedikerade SQL-pooler kan skapas via arbets ytan Synapse och Studio när funktionen arbets yta har Aktiver ATS och etableringen av en ny pool sker på den logiska SQL-servern. De nya resurserna kommer att visas i portalen och Studio när etableringen har slutförts.      

## <a name="next-steps"></a>Nästa steg
Aktivera [Synapse-arbetsytans funktioner](workspace-connected-create.md) på din befintliga dedikerade SQL-pool (tidigare SQL DW)
