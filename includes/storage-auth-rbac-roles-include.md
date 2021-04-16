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
ms.openlocfilehash: 18f64defbc4222b46d858cb9cfd3e9b56866a4d0
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513551"
---
Azure tillhandahåller följande inbyggda Azure-roller för auktorisering av åtkomst till blob- och ködata med hjälp av Azure AD och OAuth:

- [Storage Blob Data-ägare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner): Används för att ange ägarskap och hantera POSIX-åtkomstkontroll för Azure Data Lake Storage Gen2. Mer information finns i [Åtkomstkontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).
- [Storage Blob Data-deltagare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-contributor): Används för att bevilja läs-/skriv-/borttagningsbehörigheter till bloblagringsresurser.
- [Storage Blob Data-läsare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-reader): Används för att bevilja skrivskyddade behörigheter till bloblagringsresurser.
- [Storage Blob-delegerare](../articles/role-based-access-control/built-in-roles.md#storage-blob-delegator): Hämtar en användardelegeringsnyckel som ska användas för att skapa en signatur för delad åtkomst som är signerad med Azure AD-autentiseringsuppgifter för en container eller blob.
- [Storage Queue Data-deltagare](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-contributor): Används för att bevilja läs-/skrivbehörigheter till Azure-köer.
- [Storage Queue Data-läsare](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-reader): Används för att bevilja skrivskyddade behörigheter till Azure-köer.
- [Storage Queue Data-meddelandeprocessor](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-processor): Används för att ge behörighet att granska, hämta och ta bort meddelanden i Azure Storage-köer.
- [Storage Queue Data-meddelandeavsändare](../articles/role-based-access-control/built-in-roles.md#storage-queue-data-message-sender): Används för att ge tilläggsbehörighet för meddelanden i Azure Storage-köer.

Endast roller som uttryckligen definierats för dataåtkomst tillåter att ett säkerhetsobjekt får åtkomst till blob- eller ködata. Inbyggda roller som **Ägare,** Deltagare och **Lagringskontodeltagare** tillåter ett säkerhetsobjekt att hantera ett lagringskonto, men ger inte åtkomst till blob- eller ködata i det kontot via Azure AD.  Men om en roll innehåller **Microsoft.Storage/storageAccounts/listKeys/action** kan en användare som rollen har tilldelats åtkomst till data i lagringskontot via auktorisering med delad nyckel med kontots åtkomstnycklar. Mer information finns i Använda [Azure Portal åtkomst till blob- eller ködata.](../articles/storage/blobs/authorize-data-operations-portal.md)

Detaljerad information om inbyggda Azure-roller för Azure Storage för både datatjänsterna och  hanteringstjänsten finns i avsnittet Lagring i Inbyggda roller i [Azure för Azure RBAC.](../articles/role-based-access-control/built-in-roles.md#storage) Mer information om de olika typer av roller som ger behörigheter i Azure finns i Administratörsroller för klassiska [prenumerationer, Azure-roller och Azure AD-roller.](../articles/role-based-access-control/rbac-and-directory-admin-roles.md)

> [!IMPORTANT]
> Azure-rolltilldelningar kan ta upp till 30 minuter att spridas.
