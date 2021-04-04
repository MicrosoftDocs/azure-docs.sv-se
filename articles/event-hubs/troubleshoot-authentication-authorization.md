---
title: Felsök problem med autentisering och auktorisering – Azure Event Hubs
description: Den här artikeln innehåller information om hur du felsöker problem med autentisering och auktorisering med Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 368fd8efda1b828f99bc41da0743768989c1a601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329618"
---
# <a name="troubleshoot-authentication-and-authorization-issues---azure-event-hubs"></a>Felsök problem med autentisering och auktorisering – Azure Event Hubs
Artikeln [Felsöka anslutnings problem](troubleshooting-guide.md) innehåller tips för fel sökning av anslutnings problem med Azure Event Hubs. Den här artikeln innehåller tips och rekommendationer för att felsöka problem med autentisering och auktorisering med Azure Event Hubs. 

## <a name="if-you-are-using-azure-active-directory"></a>Om du använder Azure Active Directory
Om du använder Azure Active Directory (Azure AD) för att autentisera och auktorisera med Azure Event Hubs bekräftar du att identiteten som har åtkomst till händelsehubben är medlem av rätt Azure- **roll** i rätt **resurs omfång** (konsument grupp, händelsehubben, namnrymd, resurs grupp eller prenumeration).

### <a name="azure-roles"></a>Azure-roller
- [Azure Event Hubs data ägare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) för fullständig åtkomst till Event Hubs resurser.
- [Azure Event Hubs data avsändare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) för sändnings åtkomst.
- [Azure Event Hubs data mottagare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) för mottagnings åtkomst.

Inbyggda roller för schema register finns i [schema register roller](schema-registry-overview.md#azure-role-based-access-control).

### <a name="resource-scopes"></a>Resurs omfång
- **Konsument grupp**: i det här området gäller roll tilldelningen enbart för den här entiteten. För närvarande har Azure Portal inte stöd för att tilldela en Azure-roll till ett säkerhets objekt på den här nivån. 
- **Event Hub**: roll tilldelningen gäller för entiteten Event Hub och gruppen konsument under den.
- **Namnrymd**: roll tilldelningen sträcker sig över hela topologin av Event Hubs under namn området och till den konsument grupp som är kopplad till den.
- **Resurs grupp**: roll tilldelningen gäller för alla Event Hubs resurser under resurs gruppen.
- **Prenumeration**: roll tilldelningen gäller för alla Event Hubs resurser i alla resurs grupper i prenumerationen.

Mer information finns i följande artiklar:

- [Autentisera ett program med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-application.md)
- [Ge åtkomst till Event Hubs resurser med Azure Active Directory](authorize-access-azure-active-directory.md)

## <a name="if-you-are-using-shared-access-signatures-sas"></a>Om du använder signaturer för delad åtkomst (SAS)
Följ dessa steg om du använder [SAS](authenticate-shared-access-signature.md): 

- Se till att den SAS-nyckel som du använder är korrekt. Om inte använder du rätt SAS-nyckel.
- Kontrol lera att nyckeln har rätt behörigheter (skicka, ta emot eller hantera). Om inte, använder du en nyckel som har den behörighet du behöver. 
- Kontrol lera om nyckeln har upphört att gälla. Vi rekommenderar att du förnyar SAS-välbefinnandet innan det upphör att gälla. Om det finns en klock lutning mellan klienten och Event Hubs-tjänstenoderna kan autentiseringstoken förfalla innan klienten realiserar den. Aktuella implementerings konton klock sneda upp till 5 minuter, det vill säga att klienten förnyar token 5 minuter innan den upphör att gälla. Om klock skillnaden är större än 5 minuter kan klienten därför studera tillfälliga autentiseringsfel.
- Om **Start tiden för SAS** har angetts till **nu** kan du se tillfälliga haverier för de första minuterna på grund av klock skevningen (skillnader i aktuell tid på olika datorer). Ange start tiden som minst 15 minuter tidigare eller ange inte alls. Samma gäller vanligt vis förfallo tiden. 

Mer information finns i följande artiklar: 

- [Autentisera med signaturer för delad åtkomst (SAS)](authenticate-shared-access-signature.md). 
- [Auktorisera åtkomst till Event Hubs resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar:

* [Felsök anslutningsproblem](troubleshooting-guide.md)
