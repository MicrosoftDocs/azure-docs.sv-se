---
author: mattchenderson
ms.service: app-service
ms.topic: include
ms.date: 04/20/2020
ms.author: mahender
ms.openlocfilehash: 3b282a99bb7f6f107717d9c265a46d285d03b849
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83649108"
---
Med en hanterad identitet från Azure Active Directory (Azure AD) kan din app enkelt komma åt andra Azure AD-skyddade resurser som Azure Key Vault. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter i Azure AD finns i [hanterade identiteter för Azure-resurser](../articles/active-directory/managed-identities-azure-resources/overview.md).

Ditt program kan beviljas två typer av identiteter:

- En **systemtilldelad identitet** är kopplad till ditt program och tas bort om din app tas bort. En app kan bara ha en tilldelad identitet.
- En **användardefinierad identitet** är en fristående Azure-resurs som kan tilldelas till din app. En app kan ha flera användare tilldelade identiteter.