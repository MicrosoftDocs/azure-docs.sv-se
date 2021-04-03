---
title: Så här anpassar du en Synkroniseringsregel i Azure AD Connect | Microsoft Docs
description: Lär dig hur du använder synkroniseringsregeln för att redigera eller skapa en ny Synkroniseringsregel.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2bb86988454141dc692b4a9967997c4ff7574a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90530496"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Så här skapar och anpassar du en synkroniseringsregel

## <a name="recommended-steps"></a>**Rekommenderade åtgärder**

Du kan redigera eller skapa en ny Synkroniseringsregel med hjälp av regel redigeraren för synkronisering. Du måste vara en avancerad användare för att kunna ändra regler för synkronisering. Eventuella fel ändringar kan leda till att objekt tas bort från mål katalogen. Läs [rekommenderade dokument](#recommended-documents) för att få expert kunskaper i formateringsregler. Om du vill ändra en Synkroniseringsregel går du igenom följande steg:

* Starta redigeraren för synkronisering från program-menyn i Skriv bordet enligt nedan:

    ![Redigerings meny för Synkroniseringsregel](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Om du vill anpassa en standard regel för synkronisering ska du klona den befintliga regeln genom att klicka på knappen Redigera i redigeraren för regler för synkronisering, som skapar en kopia av standard standard regeln och inaktiverar den. Spara den klonade regeln med prioriteten mindre än 100.  Prioritet avgör vilken regel WINS (lägre numeriska värden) en konflikt lösning om det finns en konflikt mellan olika flöden.

    ![Redigerare för Synkroniseringsregel](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* När du ändrar ett särskilt attribut bör du helst behålla attributet ändra i den klonade regeln.  Aktivera sedan standard regeln så att det ändrade attributet kommer från den klonade regeln och andra attribut hämtas från standard regeln. 

* Observera att om det beräknade värdet för det ändrade attributet är NULL i din klonade regel och inte är NULL i standard standard regeln, kommer värdet NOT NULL att vinnas och kommer att ersätta värdet NULL. Om du inte vill att ett NULL-värde ska ersättas med ett värde som inte är NULL tilldelar du AuthoritativeNull i din klonade regel.

* Om du vill ändra en **utgående** regel ändrar du filtret i redigeraren för synkroniseringsregeln.

## <a name="recommended-documents"></a>**Rekommenderade dokument**
* [Azure AD Connect synkronisering: tekniska begrepp](./how-to-connect-sync-technical-concepts.md)
* [Azure AD Connect synkronisering: förstå arkitekturen](./concept-azure-ad-connect-sync-architecture.md)
* [Azure AD Connect synkronisering: förstå deklarativ etablering](./concept-azure-ad-connect-sync-declarative-provisioning.md)
* [Azure AD Connect synkronisering: förstå uttryck för deklarativ etablering](./concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)
* [Azure AD Connect-synkronisering: Förstå standardkonfigurationen](./concept-azure-ad-connect-sync-default-configuration.md)
* [Azure AD Connect synkronisering: förstå användare, grupper och kontakter](./concept-azure-ad-connect-sync-user-and-contacts.md)
* [Azure AD Connect synkronisering: skugg-attribut](./how-to-connect-syncservice-shadow-attributes.md)

## <a name="next-steps"></a>Nästa steg
- [Azure AD Connect synkronisering](how-to-connect-sync-whatis.md).
- [Vad är hybrid identitet?](whatis-hybrid-identity.md).