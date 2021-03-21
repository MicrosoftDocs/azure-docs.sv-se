---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 08/27/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9e4d60e501ffc5b61c87a80b8dd13698cca28737
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97934548"
---
Key Vault har stöd för upp till 1024 åtkomst princip poster, med varje post som beviljar en särskild uppsättning behörigheter till ett visst säkerhets objekt. På grund av den här begränsningen rekommenderar vi att du tilldelar åtkomst principer till grupper av användare, där det är möjligt, i stället för enskilda användare. Med hjälp av grupper blir det mycket enklare att hantera behörigheter för flera personer i din organisation. Mer information finns i [Hantera app-och resurs åtkomst med hjälp av Azure Active Directory grupper](../articles/active-directory/fundamentals/active-directory-manage-groups.md)

Fullständig information om Key Vault åtkomst kontroll finns i [Azure Key Vault säkerhet: identitets-och åtkomst hantering](../articles/key-vault/general/security-overview.md#identity-management).