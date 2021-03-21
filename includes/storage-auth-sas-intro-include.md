---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/20/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: aec1faa4de1149f08fb6fbc1cc5bf3aa2ab6becd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "75371792"
---
Med en signatur för delad åtkomst (SAS) kan du ge begränsad åtkomst till behållare och blobbar i ditt lagrings konto. När du skapar en SAS anger du dess begränsningar, inklusive vilka Azure Storage resurser som en klient har åtkomst till, vilka behörigheter de har på dessa resurser och hur länge SAS är giltig.

Varje SAS signeras med en nyckel. Du kan signera en SAS på ett av två sätt:

- Med en nyckel som skapats med hjälp av Azure Active Directory (autentiseringsuppgifter för Azure AD). En SAS som är signerad med Azure AD-autentiseringsuppgifter är en *användar Delegerings* -SAS.
- Med lagrings konto nyckeln. Både en *tjänst-SAS* och ett *konto säkerhets associationer* signeras med lagrings konto nyckeln.

En användar delegering SAS ger överlägsen säkerhet till en SAS som är signerad med lagrings konto nyckeln. Microsoft rekommenderar att du använder en användar Delegerings-SAS när det är möjligt. Mer information finns i [bevilja begränsad åtkomst till data med signaturer för delad åtkomst (SAS)](../articles/storage/common/storage-sas-overview.md).
