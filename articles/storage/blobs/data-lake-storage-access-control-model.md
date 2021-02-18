---
title: Åtkomst kontroll modell för Azure Data Lake Storage Gen2 | Microsoft Docs
description: Lär dig hur du konfigurerar behållare, katalog och åtkomst på filnivå i konton som har ett hierarkiskt namn område.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: normesta
ms.openlocfilehash: e5f62456b85791bad5bb66f3abf67e523558d76e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650396"
---
# <a name="access-control-model-in-azure-data-lake-storage-gen2"></a>Åtkomst kontroll modell i Azure Data Lake Storage Gen2

Data Lake Storage Gen2 stöder följande mekanismer för auktorisering:

- Auktorisering av delad nyckel
- Auktorisering av signatur för delad åtkomst (SAS)
- Rollbaserad åtkomst kontroll (Azure RBAC)
- Åtkomstkontrollistor (ACL)

[Delad nyckel och SAS-auktorisering](#shared-key-and-shared-access-signature-sas-authorization) beviljar åtkomst till en användare (eller ett program) utan att behöva ha en identitet i Azure Active Directory (Azure AD). Med dessa två typer av autentisering har Azure RBAC och ACL: er ingen påverkan.

Både Azure RBAC och ACL kräver att användaren (eller programmet) har en identitet i Azure AD.  Med Azure RBAC kan du ge "grov kornig" åtkomst till lagrings konto data, t. ex. Läs-och skriv åtkomst till **alla** data i ett lagrings konto, medan ACL: er låter dig bevilja "detaljerad" åtkomst, till exempel skriv åtkomst till en katalog eller fil.  

Den här artikeln fokuserar på Azure RBAC och ACL: er och hur systemet utvärderar dem tillsammans för att fatta auktoriseringsbeslut för lagrings konto resurser.

<a id="role-based-access-control"></a>

## <a name="role-based-access-control-azure-rbac"></a>Rollbaserad åtkomst kontroll (Azure RBAC)

Azure RBAC använder roll tilldelningar för att tillämpa uppsättningar av behörigheter för [säkerhets objekt](../../role-based-access-control/overview.md#security-principal). Ett säkerhets objekt är ett objekt som representerar en användare, grupp, tjänstens huvud namn eller en hanterad identitet som definieras i Azure Active Directory (AD). En behörighets uppsättning kan ge ett säkerhets objekt en "grov kornig" åtkomst nivå, till exempel Läs-eller skriv åtkomst till **alla** data i ett lagrings konto eller **alla** data i en behållare. 

Följande roller tillåter ett säkerhets objekt att komma åt data i ett lagrings konto.

|Roll|Beskrivning|
|--|--|
| [Storage Blob Data-ägare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Fullständig åtkomst till Blob Storage-behållare och data. Med den här åtkomsten kan säkerhetsobjektet ange ägare ett objekt och ändra ACL: er för alla objekt. |
| [Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) | Läsa, skriva och ta bort åtkomst till Blob Storage-behållare och blobbar. Den här åtkomsten tillåter inte att säkerhetsobjektet anger ägarskapet för ett objekt, men det kan ändra ACL för objekt som ägs av säkerhets objekt. |
| [Storage Blob Data-läsare](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) | Läsa och lista Blob Storage-behållare och blobbar. |

Roller som [ägare](../../role-based-access-control/built-in-roles.md#owner), [deltagare](../../role-based-access-control/built-in-roles.md#contributor), [läsare](../../role-based-access-control/built-in-roles.md#reader)och [lagrings konto deltagare](../../role-based-access-control/built-in-roles.md#storage-account-contributor) tillåter ett säkerhets objekt att hantera ett lagrings konto, men ger inte åtkomst till data i det kontot. Dessa roller (exklusive **läsare**) kan dock få åtkomst till lagrings nycklarna som kan användas i olika klient verktyg för att få åtkomst till data.

## <a name="access-control-lists-acls"></a>Åtkomstkontrollistor (ACL)

ACL: er ger dig möjlighet att använda "finare kornig het"-åtkomst till kataloger och filer. En *ACL* är en behörighets konstruktion som innehåller en serie *ACL-poster*. Varje ACL-post kopplar säkerhets objekt till en åtkomst nivå.  Mer information finns [i åtkomst kontrol listor (ACL: er) i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

## <a name="how-permissions-are-evaluated"></a>Hur behörigheter utvärderas

Under säkerhetsobjektbaserade auktorisering utvärderas behörigheter i följande ordning.

: en: &nbsp; &nbsp; Azure roles-tilldelningar utvärderas först och prioriteras över alla ACL-tilldelningar.

: två: &nbsp; &nbsp; om åtgärden är fullständigt auktoriserad baserat på roll tilldelningen i Azure utvärderas ACL: er inte alls.

: tre: &nbsp; &nbsp; om åtgärden inte är fullständigt auktoriserad utvärderas ACL: er.

> [!div class="mx-imgBorder"]
> ![behörighets flöde för data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-flow.png)

På grund av hur åtkomst behörigheterna utvärderas av systemet **kan du inte** använda en ACL för att **begränsa** åtkomsten som redan har beviljats av en roll tilldelning. Det beror på att systemet utvärderar Azure Role-tilldelningar först, och om tilldelningen ger tillräcklig åtkomst behörighet ignoreras ACL: er. 

Följande diagram visar behörighets flödet för tre vanliga åtgärder: Visa katalog innehåll, läsa en fil och skriva en fil.

> [!div class="mx-imgBorder"]
> ![exempel på behörighets flöde för data Lake Storage](./media/control-access-permissions-data-lake-storage/data-lake-storage-permissions-example.png)

## <a name="permissions-table-combining-azure-rbac-and-acl"></a>Behörighets tabell: kombinera Azure RBAC och ACL

I följande tabell visas hur du kombinerar Azure-roller och ACL-poster så att ett säkerhets objekt kan utföra de åtgärder som anges i kolumnen **operation** . I den här tabellen visas en kolumn som representerar varje nivå i en fiktiv katalog-hierarki. Det finns en kolumn för behållarens rot Katalog ( `/` ), en under katalog med namnet " Göteborg", en under katalog till katalogen Göteborg, som heter **Göteborg** och en textfil i katalogen Göteborg med namnet **Data.txt**. De som visas i dessa kolumner är [korta formulär](data-lake-storage-access-control.md#short-forms-for-permissions) som representeras av ACL-posten som krävs för att bevilja behörigheter. **Ej** _tillämpligt (ej tillämpligt_) visas i kolumnen om det inte krävs någon ACL-post för att utföra åtgärden.

|    Åtgärd             | Tilldelad Azure-roll               |    /        | Oregon     | Portland | Data.txt |             
|--------------------------|----------------------------------|-------------|-------------|-----------|----------|
| Läs Data.txt            |   Storage Blob Data-ägare        | Saknas      | Saknas      | Saknas       | Saknas    |  
|                          |   Storage Blob Data-deltagare  | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-läsare       | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Inget                           | `--X`    | `--X`    | `--X`     | `R--`  |
| Lägg till i Data.txt       |   Storage Blob Data-ägare        | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-deltagare  | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-läsare       | `--X`    | `--X`    | `--X`     | `-W-`  |
|                          |   Inget                           | `--X`    | `--X`    | `--X`     | `RW-`  |
| Ta bort Data.txt          |   Storage Blob Data-ägare        | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-deltagare  | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-läsare       | `--X`    | `--X`    | `-WX`     | Ej tillämpligt    |
|                          |   Inget                           | `--X`    | `--X`    | `-WX`     | Ej tillämpligt    |
| Skapa Data.txt          |   Storage Blob Data-ägare        | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-deltagare  | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-läsare       | `--X`    | `--X`    | `-WX`     | Ej tillämpligt    |
|                          |   Inget                           | `--X`    | `--X`    | `-WX`     | Ej tillämpligt    |
| Lista                   |   Storage Blob Data-ägare        | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-deltagare  | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-läsare       | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Inget                           | `R-X`    | Saknas      | Saknas       | Saknas    |
| Visa lista/Oregon/            |   Storage Blob Data-ägare        | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-deltagare  | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-läsare       | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Inget                           | `--X`    | `R-X`    | Saknas       | Saknas    |
| Visa lista/Oregon/Portland/   |   Storage Blob Data-ägare        | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-deltagare  | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Storage Blob Data-läsare       | Saknas      | Saknas      | Saknas       | Saknas    |
|                          |   Inget                           | `--X`    | `--X`    | `R-X`     | Ej tillämpligt    |


> [!NOTE] 
> Om du vill visa innehållet i en behållare i Azure Storage Explorer måste säkerhets objekt [Logga in på Storage Explorer med hjälp av Azure AD](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#add-a-resource-via-azure-ad)och (minst) ha Läs åtkomst (R--) till rotmappen ( `\` ) i en behållare. Den här behörighets nivån ger dem möjlighet att visa innehållet i rotmappen. Om du inte vill att innehållet i rotmappen ska visas kan du tilldela dem rollen [läsare](../../role-based-access-control/built-in-roles.md#reader) . Med den rollen kan de lista behållarna i kontot, men inte container innehåll. Sedan kan du bevilja åtkomst till vissa kataloger och filer med hjälp av ACL: er.   

## <a name="security-groups"></a>Säkerhetsgrupper

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-groups.md)]

## <a name="limits-on-azure-role-assignments-and-acl-entries"></a>Gränser för roll tilldelningar i Azure och ACL-poster

Genom att använda grupper kan du minska det högsta antalet roll tilldelningar per prenumeration och maximalt antal ACL-poster per fil eller katalog. I följande tabell beskrivs dessa gränser.

[!INCLUDE [Security groups](../../../includes/azure-storage-data-lake-rbac-acl-limits.md)] 

## <a name="shared-key-and-shared-access-signature-sas-authorization"></a>Auktorisering av delad nyckel och signatur för delad åtkomst (SAS)

Azure Data Lake Storage Gen2 också stöd för [delad nyckel](/rest/api/storageservices/authorize-with-shared-key) och [SAS](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) -metoder för autentisering. En egenskap hos dessa autentiseringsmetoder är att ingen identitet är kopplad till anroparen och därför kan inte behörigheten för säkerhets objekts behörighet utföras.

I händelse av en delad nyckel får anroparen effektiv åtkomst till "Super-User", vilket innebär fullständig åtkomst till alla åtgärder på alla resurser, inklusive data, inställnings ägare och ändring av ACL: er.

SAS-token inkluderar tillåtna behörigheter som en del av token. Behörigheterna som ingår i SAS-token tillämpas effektivt på alla auktoriseringsbeslut, men inga ytterligare ACL-kontroller utförs.

## <a name="next-steps"></a>Nästa steg

Mer information om åtkomst kontrol listor finns  [i åtkomst kontrol listor (ACL: er) i Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).