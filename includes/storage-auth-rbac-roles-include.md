---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f014ce55dc40723faf1b60f908814f9fa0428b8e
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99213580"
---
Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till blob-och Queue-data med hjälp av Azure AD och OAuth:

- [Storage BLOB data-ägare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Använd för att ange ägarskap och hantera POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2. Mer information finns i [åtkomst kontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage BLOB data-deltagare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Använd för att bevilja läs-/skriv-/borttagnings behörigheter till Blob Storage-resurser.
- [Storage BLOB data Reader](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): används för att bevilja Läs behörighet till Blob Storage-resurser.
- [Storage BLOB-delegerare](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Hämta en användar Delegerings nyckel som ska användas för att skapa en signatur för delad åtkomst som är signerad med Azure AD-autentiseringsuppgifter för en behållare eller BLOB.
- [Data deltagare i Storage Queue](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Använd för att bevilja läs-/skriv-/borttagnings behörigheter till Azure-köer.
- [Data läsare för lagrings köer](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Använd för att bevilja Läs behörighet till Azure-köer.
- [Processor för lagrings data meddelande](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Använd för att ge behörighet att granska, hämta och ta bort meddelanden i Azure Storage köer.
- [Avsändare av data meddelande i lagrings](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender)köer: Använd för att bevilja Lägg till behörigheter till meddelanden i Azure Storage köer.

Endast roller som uttryckligen definierats för data åtkomst tillåter ett säkerhets objekt att komma åt BLOB-eller Queue data. Inbyggda roller som **ägare**, **deltagare** och **lagrings konto deltagare** tillåter ett säkerhets objekt att hantera ett lagrings konto, men ger inte till gång till BLOB-eller Queue-data inom kontot via Azure AD. Men om en roll innehåller **Microsoft. Storage/storageAccounts/listnycklar/åtgärd**, kan en användare som rollen har tilldelats åtkomst till data i lagrings kontot via autentisering med delad nyckel med konto åtkomst nycklarna. Mer information finns i [använda Azure Portal för att få åtkomst till BLOB-eller Queue-data](../articles/storage/blobs/authorize-data-operations-portal.md).

Detaljerad information om inbyggda Azure-roller för Azure Storage för både data tjänsterna och hanterings tjänsten finns i avsnittet **lagring** i [inbyggda Azure-roller för Azure RBAC](../articles/role-based-access-control/built-in-roles.md#storage). Dessutom finns information om de olika typerna av roller som ger behörigheter i Azure i de klassiska rollerna för [prenumerationer, Azure-roller och Azure AD-roller](../articles/role-based-access-control/rbac-and-directory-admin-roles.md).

> [!IMPORTANT]
> Det kan ta upp till 30 minuter innan Azure-roll tilldelningar sprids.
