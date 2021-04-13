---
title: Aktivera funktioner för Synapse-arbetsyta
description: Det här dokumentet beskriver hur en användare kan aktivera Synapse-arbetsytans funktioner på en befintlig dedikerad SQL-pool (tidigare SQL DW).
services: synapse-analytics
author: antvgski
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/25/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 605f495fbc5913663499f5411abcaa531cea97ee
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312220"
---
# <a name="enabling-synapse-workspace-features-for-a-dedicated-sql-pool-formerly-sql-dw"></a>Aktivera Synapse-arbetsytans funktioner för en dedikerad SQL-pool (tidigare SQL DW)

Alla SQL Data Warehouse-användare kan nu komma åt och använda en befintlig dedikerad SQL-pool (tidigare SQL DW)-instans via Synapse Studio och arbets ytan. Användare kan använda Synapse Studio och arbets ytan utan att påverka automatisering, anslutningar eller verktyg. Den här artikeln förklarar hur en befintlig Azure Synapse Analytics-användare kan aktivera Synapse-arbetsytans funktioner för en befintlig dedikerad SQL-pool (tidigare SQL DW). Användaren kan utöka sin befintliga analys lösning genom att dra nytta av de nya funktionerna för funktioner som nu är tillgängliga via Synapse-arbetsytan och Studio.   

## <a name="prerequisites"></a>Förutsättningar
Innan du aktiverar Synapse-arbetsytan på ditt data lager måste du kontrol lera att du har följande
- Behörighet att skapa och hantera de SQL-resurser som finns på den logiska SQL-servern.
- Skriv behörighet för värden SQL Server. 
- Rättigheter för att skapa Azure Synapse-resurser.
- En Azure Active Directory administratör som identifierats på den logiska servern

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="enabling-synapse-workspace-features-for-an-existing-dedicated-sql-pool-formerly-sql-dw"></a>Aktivera Synapse-arbetsytans funktioner för en befintlig dedikerad SQL-pool (tidigare SQL DW)

Synapse-arbetsytans funktioner kan aktive ras på alla befintliga dedikerade SQL-pooler (tidigare SQL DW) i en region som stöds. Den här funktionen är endast tillgänglig via Azure Portal.

Följ dessa steg om du vill skapa en Synapse-arbetsyta för ditt befintliga data lager.
1. Välj en befintlig dedikerad SQL-pool (tidigare SQL DW) och öppna översikts sidan.
2. Välj **ny Synapse-arbetsyta** i det översta meny fältet eller meddelandet direkt nedan.
3. När du har granskat listan över informations lager som gjorts tillgängliga via den nya Synapse-arbetsytan på sidan **Skapa ny Azure Synapse-arbetsyta** . Välj **Fortsätt** om du vill fortsätta.
4. På sidan grundläggande visas den befintliga dedikerade SQL-poolen ( **projekt** information-avsnittet måste vara ifyllt med samma **prenumeration** och **resurs grupp** som distribueras under den logiska servern. Och under **arbets ytans information** fylls **arbets ytans** namn i förväg med samma namn och region som den logiska SQL-servern för att säkerställa anslutningen mellan din nya Synapse-arbetsyta och den logiska servern kan skapas under etablerings processen. Efter etablering av den här anslutningen måste behållas för att säkerställa fortsatt åtkomst till den dedikerade SQL-poolen (tidigare SQL DW)-instanser via arbets ytan och Studio.
5. Gå till Välj Data Lake Storage gen 2.
6. Välj **Skapa ny** eller Välj en befintlig **data Lake Storage Gen2** innan du väljer **Nästa: nätverk**.
7. Välj ett **lösen ord för SQL-administratören** för **Server lös instansen**. Om du ändrar lösen ordet uppdateras eller ändras inte SQL-autentiseringsuppgifterna för den logiska servern. Lämna fälten tomt om du föredrar ett Systemdefinierat lösen ord. Det här lösen ordet kan ändras när som helst inom den nya arbets ytan. Administratörs namnet måste vara detsamma som används på SQL Server.
8. Välj önskade **nätverks inställningar** och välj **Granska + skapa** för att starta arbets ytans etablering.
9. Välj **goto resurs** för att öppna den nya arbets ytan.

    > [!NOTE]
    > Alla dedikerade SQL-pooler (tidigare SQL DW) som finns på den logiska servern är tillgängliga via den nya arbets ytan.

## <a name="post-provisioning-steps"></a>Publicera etablerings steg
Följande steg måste utföras för att se till att din befintliga dedikerade SQL-pool (tidigare SQL DW)-instanser kan nås via Synapse Studio.
1. På sidan Översikt över Synapse-arbetsyta väljer du **ansluten server**. Den **anslutna servern** tar dig till den anslutna SQL logiska server som är värd för dina informations lager. I den grundläggande menyn väljer du **ansluten server**.
2. Öppna **brand väggar och virtuella nätverk** och se till att klientens IP-adress eller ett fördefinierat IP-intervall har åtkomst till den logiska servern.
3. Öppna **Active Directory admin** och kontrol lera att en AAD-administratör har ställts in på den logiska servern.
4. Välj en av de dedikerade SQL-poolerna (tidigare SQL DW) som finns på den logiska servern. På sidan Översikt väljer du **Starta Synapse Studio** eller går till logga in [på Synapse Studio](https://web.azuresynapse.net) och loggar in på din arbets yta.

5. Öppna **data hubben** och expandera den DEDIKERADe SQL-poolen i Object Explorer för att se till att du har åtkomst till och kan fråga ditt informations lager.

    > [!NOTE] 
    > En ansluten arbets yta kan tas bort när som helst. Om du tar bort arbets ytan tas inte den anslutna dedikerade SQL-poolen bort (tidigare SQL DW). Arbets ytans funktion kan återaktiveras på den dedikerade SQL-poolen (tidigare SQL DW) när borttagnings åtgärden har slutförts.

## <a name="next-steps"></a>Nästa steg
Komma igång med [Synapse-arbetsyta och Studio](../get-started.md).
