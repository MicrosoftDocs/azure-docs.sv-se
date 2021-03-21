---
title: ta med fil
description: ta med fil
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: b0be074c995fcc62f63a4a2ebf4149d3040e7b6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750603"
---
## <a name="best-practices"></a>Bästa praxis

Det här avsnittet innehåller rikt linjer för bästa praxis för att ställa in ACL: er rekursivt. 

#### <a name="handling-runtime-errors"></a>Hantera körnings fel

Ett körnings fel kan uppstå i många olika orsaker (till exempel: ett avbrott eller problem med klient anslutning). Om du stöter på ett körnings fel startar du om den rekursiva ACL-processen. ACL: er kan återanvändas på objekt utan att orsaka negativ påverkan. 

#### <a name="handling-permission-errors-403"></a>Hantering av behörighets fel (403)

Om du stöter på ett åtkomst kontroll undantag när du kör en rekursiv ACL-process kanske ditt [säkerhets objekt](../articles/role-based-access-control/overview.md#security-principal) i AD inte har tillräcklig behörighet för att tillämpa en ACL på ett eller flera av de underordnade objekten i mapphierarkin. När ett behörighets fel inträffar stoppas processen och en fortsättnings-token anges. Åtgärda behörighets problemet och Använd sedan den fortsättnings-token för att bearbeta den återstående data mängden. De kataloger och filer som redan har bearbetats behöver inte bearbetas igen. Du kan också välja att starta om den rekursiva ACL-processen. ACL: er kan återanvändas på objekt utan att orsaka negativ påverkan. 

#### <a name="credentials"></a>Autentiseringsuppgifter 

Vi rekommenderar att du etablerar ett säkerhets objekt för Azure AD som har tilldelats rollen som [lagrings-BLOB-dataägare](../articles/role-based-access-control/built-in-roles.md#storage-blob-data-owner) i omfånget för mål lagrings kontot eller behållaren. 

#### <a name="performance"></a>Prestanda 

För att minska svars tiden rekommenderar vi att du kör den rekursiva ACL-processen på en virtuell Azure-dator (VM) som finns i samma region som ditt lagrings konto. 

#### <a name="acl-limits"></a>ACL-gränser

Det maximala antalet ACL: er som du kan tillämpa på en katalog eller fil är 32 åtkomst-ACL: er och 32 standard-ACL: er. Mer information finns i [Åtkomstkontroll i Azure Data Lake Storage Gen2](../articles/storage/blobs/data-lake-storage-access-control.md).