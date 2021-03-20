---
title: Hantera åtkomst till Azure-hantering med villkorlig åtkomst i Azure AD
description: Lär dig mer om att använda villkorlig åtkomst i Azure AD för att hantera åtkomst till Azure-hantering.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: skwan
ms.assetid: 0adc8b11-884e-476c-8c43-84f9bf12a34b
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/15/2019
ms.author: rolyon
ms.reviewer: skwan
ms.openlocfilehash: 2a52635dbaa7a76034f3a535b099320a901e8c07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "83758783"
---
# <a name="manage-access-to-azure-management-with-conditional-access"></a>Hantera åtkomst till Azure-hantering med villkorlig åtkomst

> [!CAUTION]
> Se till att du förstår hur villkorlig åtkomst fungerar innan du konfigurerar en princip för hantering av åtkomst till Azure-hantering. Se till att du inte skapar villkor som kan blockera din egen åtkomst till portalen.

Villkorlig åtkomst i Azure Active Directory (Azure AD) styr åtkomsten till molnappar baserat på särskilda villkor som du anger. För att tillåta åtkomst skapar du principer för villkorlig åtkomst som tillåter eller blockerar åtkomst baserat på om kraven i principen är uppfyllda. 

Normalt använder du villkorlig åtkomst för att kontrol lera åtkomsten till dina molnappar. Du kan också konfigurera principer för att styra åtkomsten till Azure-hantering baserat på vissa villkor (till exempel inloggnings risk, plats eller enhet) och för att genomdriva krav som Multi-Factor Authentication.

Om du vill skapa en princip för Azure-hantering väljer du **Microsoft Azure hantering** under **molnappar** när du väljer den app som principen ska tillämpas på.

![Villkorlig åtkomst för hantering av Azure](./media/conditional-access-azure-management/conditional-access-azure-mgmt.png)

Principen som du skapar gäller för alla slut punkter för Azure-hantering, inklusive följande:

- Azure Portal
- Azure Resource Manager Provider
- Klassiska tjänst hanterings-API: er
- Azure PowerShell
- Administratörs Portal för Visual Studio-prenumerationer
- Azure DevOps
- Azure Data Factory Portal

Observera att principen gäller för Azure PowerShell, som anropar Azure Resource Manager API. Den gäller inte för [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2), som anropar Microsoft Graph.

Mer information om hur du konfigurerar en exempel princip för att aktivera villkorlig åtkomst för Microsoft Azure hantering finns i artikeln [villkorlig åtkomst: KRÄV MFA för Azure-hantering](../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).
