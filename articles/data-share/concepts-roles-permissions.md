---
title: Roller och krav för Azure Data Share
description: Läs om de behörigheter som krävs för att dela och ta emot data med Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: a832c8956f7a3d4f8669209d7ed311e7555e1e75
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644247"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Roller och krav för Azure Data Share 

Den här artikeln beskriver roller och behörigheter som krävs för att dela och ta emot data med Azure Data Share-tjänsten. 

## <a name="roles-and-requirements"></a>Roller och krav

Med Azure Data Share-tjänsten kan du dela data utan att utbyta autentiseringsuppgifter mellan data leverantören och konsumenten. För ögonblicks bilds-baserad delning använder Azure Data Sharing-tjänsten hanterade identiteter (kallades tidigare MSIs) för att autentisera till Azure Data Store. Azure Data Share-resursens hanterade identitet måste beviljas åtkomst till Azure Data Store för att läsa eller skriva data.

För att kunna dela eller ta emot data från ett Azure-datalager behöver användaren minst följande behörigheter. 

* Behörighet att skriva till Azure Data Store. Den här behörigheten finns normalt i **deltagar** rollen.

För lagring och data Lake Snapshot-baserad delning måste du också ha behörighet att skapa roll tilldelning i Azure Data Store. Normalt finns behörighet att skapa roll tilldelningar i **ägar** rollen, rollen administratör för användar åtkomst eller en anpassad roll med *Microsoft. auktorisering/roll tilldelningar/Skriv* behörighet tilldelad. Den här behörigheten krävs inte om data resurs resursens hanterade identitet redan har beviljats åtkomst till Azure Data Store. Nedan visas en sammanfattning av de roller som tilldelats till data resursen resursens hanterade identitet:

|**Data lager typ**|**Data lager för DataProvider-källa**|**Data lager för data konsument mål**|
|---|---|---|
|Azure Blob Storage| Storage Blob Data-läsare | Storage Blob Data-deltagare
|Azure Data Lake gen1 | Ägare | Stöds inte
|Azure Data Lake Gen2 | Storage Blob Data-läsare | Storage Blob Data-deltagare
|

För SQL-ögonblicksbild-baserad delning måste en SQL-användare skapas från en extern provider i Azure SQL Database med samma namn som Azure Data Resource-resursen. Azure Active Directory administratörs behörighet krävs för att skapa den här användaren. Nedan visas en sammanfattning av den behörighet som krävs av SQL-användaren.

|**SQL Database typ**|**SQL användar behörighet för DataProvider**|**Användar behörighet för data Consumer SQL**|
|---|---|---|
|Azure SQL Database | db_datareader | db_datareader db_datawriter db_ddladmin
|Azure Synapse Analytics | db_datareader | db_datareader db_datawriter db_ddladmin
|

### <a name="data-provider"></a>Data leverantör
För att kunna lägga till en data uppsättning i Azure-dataresursen i lagrings-och data Lake Snapshot-baserad delning måste du bevilja åtkomst till Azure Data Store-källan. Om lagrings kontot t. ex. är beviljat, beviljas data resursens hanterade identitet rollen *Storage BLOB data Reader* . Detta görs automatiskt av Azure Data Share-tjänsten när användaren lägger till data uppsättningen via Azure Portal och användaren har rätt behörighet. Till exempel är användaren en ägare till Azure Data Store eller är medlem i en anpassad roll som har tilldelats behörigheten *Microsoft. auktorisering/roll tilldelningar/Skriv* . 

Alternativt kan användaren ha ägaren till Azure Data Store och lägga till data resurs resursens hanterade identitet i Azure Data Store manuellt. Den här åtgärden behöver bara utföras en gång per data resurs resurs. Följ stegen nedan om du vill skapa en roll tilldelning för data resurs resursens hanterade identitet manuellt.  

1. Gå till Azure Data Store.
1. Välj **Access Control (IAM)**.
1. Välj **Lägg till en roll tilldelning**.
1. Under *roll* väljer du rollen i roll tilldelnings tabellen ovan (till exempel för lagrings konto väljer du *Storage BLOB data Reader*).
1. Under *Välj* anger du namnet på din Azure Data Resource-resurs.
1. Klicka på *Spara*.

Mer information om roll tilldelning finns i [tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md). Om du delar data med hjälp av REST API: er kan du skapa roll tilldelning med hjälp av API genom att referera till [tilldela Azure-roller med hjälp av REST API](../role-based-access-control/role-assignments-rest.md). 

För SQL-ögonblicksbild-baserad delning måste en SQL-användare skapas från en extern provider i SQL Database med samma namn som Azure Data Resource-resursen vid anslutning till SQL Database med Azure Active Directory autentisering. Den här användaren måste beviljas *db_datareader* -behörighet. Ett exempel skript tillsammans med andra krav för SQL-baserad delning finns i själv studie kursen för att [Dela från Azure SQL Database eller Azure Synapse Analytics](how-to-share-from-sql.md) . 

### <a name="data-consumer"></a>Data konsument
För att kunna ta emot data i lagrings kontot måste konsument data resursens hanterade identitet beviljas åtkomst till mål lagrings kontot. Data resursen resursens hanterade identitet måste beviljas rollen *Storage BLOB data Contributor* . Detta görs automatiskt av Azure Data Share-tjänsten om användaren anger ett mål lagrings konto via Azure Portal och användaren har rätt behörighet. Till exempel är användaren ägare till lagrings kontot eller är medlem i en anpassad roll som har tilldelats behörigheten *Microsoft. auktorisering/roll tilldelningar/Skriv* . 

Alternativt kan användaren ha ägare till lagrings kontot Lägg till data resurs resursens hanterade identitet manuellt i lagrings kontot. Den här åtgärden behöver bara utföras en gång per data resurs resurs. Följ stegen nedan om du vill skapa en roll tilldelning för data resurs resursens hanterade identitet manuellt. 

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