---
title: Roller och krav för Azure Data Share
description: Läs om de behörigheter som krävs för att dela och ta emot data med Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: f5c5d6da239d302b57bdb37e9d49116a29c1ccb4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100558122"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Roller och krav för Azure Data Share 

Den här artikeln beskriver roller och behörigheter som krävs för att dela och ta emot data med Azure Data Share-tjänsten. 

## <a name="roles-and-requirements"></a>Roller och krav

Med Azure Data Share-tjänsten kan du dela data utan att utbyta autentiseringsuppgifter mellan data leverantören och konsumenten. Azure Data Share-tjänsten använder hanterade identiteter (kallades tidigare MSIs) för att autentisera till Azure Data Store. 

Azure Data Share-resursens hanterade identitet måste beviljas åtkomst till Azure Data Store. Azure Data Sharing-tjänsten använder sedan den här hanterade identiteten för att läsa och skriva data för ögonblicks bilds delning och för att upprätta en symbolisk länk för delning på plats. 

För att kunna dela eller ta emot data från ett Azure-datalager behöver användaren minst följande behörigheter. Ytterligare behörigheter krävs för SQL-baserad delning.

* Behörighet att skriva till Azure Data Store. Den här behörigheten finns normalt i **deltagar** rollen.
* Behörighet att skapa roll tilldelning i Azure Data Store. Normalt finns behörighet att skapa roll tilldelningar i **ägar** rollen, rollen administratör för användar åtkomst eller en anpassad roll med Microsoft. auktorisering/roll tilldelningar/Skriv behörighet tilldelad. Den här behörigheten krävs inte om data resurs resursens hanterade identitet redan har beviljats åtkomst till Azure Data Store. Se tabellen nedan för den obligatoriska rollen.

Nedan visas en sammanfattning av de roller som tilldelats till data resursen resursens hanterade identitet:

|**Data lager typ**|**Data lager för DataProvider-källa**|**Data lager för data konsument mål**|
|---|---|---|
|Azure Blob Storage| Storage Blob Data-läsare | Storage Blob Data-deltagare
|Azure Data Lake gen1 | Ägare | Stöds inte
|Azure Data Lake Gen2 | Storage Blob Data-läsare | Storage Blob Data-deltagare
|Azure Data Explorer-kluster | Deltagare | Deltagare
|

För SQL-baserad delning måste en SQL-användare skapas från en extern provider i Azure SQL Database med samma namn som data resurs resursen i Azure. Azure Active Directory administratörs behörighet krävs för att skapa den här användaren. Nedan visas en sammanfattning av den behörighet som krävs av SQL-användaren.

|**SQL Database typ**|**SQL användar behörighet för DataProvider**|**Användar behörighet för data Consumer SQL**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader db_datawriter db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader db_datawriter db_ddladmin
|

### <a name="data-provider"></a>Data leverantör

Om du vill lägga till en data uppsättning i Azure Data-resursen måste providerns hanterade identitet beviljas åtkomst till Azure Data Store-källan. Om lagrings kontot t. ex. är beviljat, beviljas data resursens hanterade identitet rollen Storage BLOB data Reader. 

Detta görs automatiskt av Azure Data Share-tjänsten när användaren lägger till data uppsättningen via Azure Portal och användaren har rätt behörighet. Till exempel är användaren en ägare till Azure Data Store eller är medlem i en anpassad roll som har tilldelats behörigheten Microsoft. auktorisering/roll tilldelningar/Skriv. 

Alternativt kan användaren ha ägaren till Azure Data Store och lägga till data resurs resursens hanterade identitet i Azure Data Store manuellt. Den här åtgärden behöver bara utföras en gång per data resurs resurs.

Följ stegen nedan om du vill skapa en roll tilldelning för data resurs resursens hanterade identitet manuellt.  

1. Gå till Azure Data Store.
1. Välj **Access Control (IAM)**.
1. Välj **Lägg till en roll tilldelning**.
1. Under *roll* väljer du rollen i roll tilldelnings tabellen ovan (till exempel för lagrings konto väljer du *Storage BLOB data Reader*).
1. Under *Välj* anger du namnet på din Azure Data Resource-resurs.
1. Klicka på *Spara*.

Mer information om roll tilldelning finns i [tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md). Om du delar data med hjälp av REST API: er kan du skapa roll tilldelning med hjälp av API genom att referera till [tilldela Azure-roller med hjälp av REST API](../role-based-access-control/role-assignments-rest.md). 

För SQL-baserade källor måste en SQL-användare skapas från en extern provider i SQL Database med samma namn som Azure Data Resource-resursen vid anslutning till SQL Database med Azure Active Directory autentisering. Den här användaren måste beviljas *db_datareader* -behörighet. Ett exempel skript tillsammans med andra krav för SQL-baserad delning finns i själv studie kursen för att [Dela från Azure SQL Database eller Azure Synapse Analytics](how-to-share-from-sql.md) . 

### <a name="data-consumer"></a>Data konsument
För att kunna ta emot data måste klient organisationens hanterade identitet beviljas åtkomst till Azure Data Store-målet. Om lagrings kontot till exempel är beviljat, beviljas rollen Storage BLOB data Contributor av data resursens hanterade identitet. 

Detta görs automatiskt av Azure Data Share-tjänsten om användaren anger ett mål data lager via Azure Portal och användaren har rätt behörighet. Till exempel är användaren en ägare till Azure Data Store eller är medlem i en anpassad roll som har tilldelats behörigheten Microsoft. auktorisering/roll tilldelningar/Skriv. 

Alternativt kan användaren ha ägaren till Azure Data Store och lägga till data resurs resursens hanterade identitet i Azure Data Store manuellt. Den här åtgärden behöver bara utföras en gång per data resurs resurs.

Följ stegen nedan om du vill skapa en roll tilldelning för data resurs resursens hanterade identitet manuellt. 

1. Gå till Azure Data Store.
1. Välj **Access Control (IAM)**.
1. Välj **Lägg till en roll tilldelning**.
1. Under *roll* väljer du rollen i roll tilldelnings tabellen ovan (till exempel för lagrings konto väljer du *Storage BLOB data Reader*).
1. Under *Välj* anger du namnet på din Azure Data Resource-resurs.
1. Klicka på *Spara*.

Mer information om roll tilldelning finns i [tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md). Om du tar emot data med hjälp av REST API: er kan du skapa roll tilldelning med hjälp av API genom att referera till [tilldela Azure-roller med hjälp av REST API](../role-based-access-control/role-assignments-rest.md). 

För SQL-baserat mål måste en SQL-användare skapas från en extern provider i SQL Database med samma namn som Azure Data Resource-resursen vid anslutning till SQL Database med Azure Active Directory autentisering. Den här användaren måste beviljas *db_datareader db_datawriter db_ddladmin* behörighet. Ett exempel skript tillsammans med andra krav för SQL-baserad delning finns i själv studie kursen för att [Dela från Azure SQL Database eller Azure Synapse Analytics](how-to-share-from-sql.md) . 

## <a name="resource-provider-registration"></a>Registrering av resurs leverantör 

Du kan behöva registrera Microsoft. DataShare-resurs leverantören manuellt i din Azure-prenumeration i följande scenarier: 

* Visa Azure Data Share-inbjudan för första gången i din Azure-klient
* Dela data från ett Azure-datalager i en annan Azure-prenumeration från din Azure Data Share-resurs
* Ta emot data till ett Azure-datalager i en annan Azure-prenumeration från din Azure Data Resource-resurs

Följ de här stegen för att registrera Microsoft. DataShare-resurs leverantören i din Azure-prenumeration. Du behöver *deltagar* åtkomst till Azure-prenumerationen för att registrera resurs leverantören.

1. I Azure Portal navigerar du till **prenumerationer**.
1. Välj den prenumeration som du använder för Azure Data-resursen.
1. Klicka på **resurs leverantörer**.
1. Sök efter Microsoft. DataShare.
1. Klicka på **Registrera**.
 
Mer information om Resource Provider finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om roller i Azure – [förstå Azures roll definitioner](../role-based-access-control/role-definitions.md)