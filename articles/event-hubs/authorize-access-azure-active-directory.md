---
title: Ge åtkomst med Azure Active Directory
description: Den här artikeln innehåller information om hur du auktoriserar åtkomst till Event Hubs resurser med Azure Active Directory.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: d794b03fdbb5429983788c74cbb05a7c13bf2d76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92910805"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Ge åtkomst till Event Hubs resurser med Azure Active Directory
Azure Event Hubs stöder användning av Azure Active Directory (Azure AD) för att godkänna begär anden till Event Hubs resurser. Med Azure AD kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge behörighet till ett säkerhets objekt, som kan vara en användare eller ett huvud namn för en program tjänst. Mer information om roller och roll tilldelningar finns i [förstå de olika rollerna](../role-based-access-control/overview.md).

## <a name="overview"></a>Översikt
När ett säkerhets objekt (en användare eller ett program) försöker få åtkomst till en Event Hubs-resurs måste begäran vara auktoriserad. Med Azure AD är åtkomst till en resurs en två stegs process. 

 1. Först autentiseras säkerhets objektets identitet och en OAuth 2,0-token returneras. Resurs namnet för att begära en token är `https://eventhubs.azure.net/` , och det är detsamma för alla moln/klienter. För Kafka-klienter är resursen att begära en token `https://<namespace>.servicebus.windows.net` .
 1. Därefter skickas token som en del av en begäran till tjänsten Event Hubs för att ge åtkomst till den angivna resursen.

Autentiserings steget kräver att en programbegäran innehåller en OAuth 2,0-åtkomsttoken vid körning. Om ett program körs i en Azure-entitet, till exempel en virtuell Azure-dator, en skalnings uppsättning för virtuella datorer eller en Azure Function-app, kan den använda en hanterad identitet för att få åtkomst till resurserna. Information om hur du autentiserar begär Anden som görs av en hanterad identitet i Event Hubs-tjänsten finns i [autentisera åtkomst till azure Event Hubs-resurser med Azure Active Directory och hanterade identiteter för Azure-resurser](authenticate-managed-identity.md). 

Auktoriserings steget kräver att en eller flera Azure-roller tilldelas säkerhets objekt. Azure Event Hubs tillhandahåller Azure-roller som omfattar uppsättningar med behörigheter för Event Hubs resurser. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Mer information om Azure-roller finns i [inbyggda Azure-roller i azure Event Hubs](#azure-built-in-roles-for-azure-event-hubs). 

Interna program och webb program som gör förfrågningar till Event Hubs kan också auktoriseras med Azure AD. Information om hur du begär en åtkomsttoken och använder den för att auktorisera begär Anden för Event Hubs resurser finns i [autentisera åtkomst till Azure Event Hubs med Azure AD från ett program](authenticate-application.md). 

## <a name="assign-azure-roles-for-access-rights"></a>Tilldela Azure-roller för åtkomst rättigheter
Azure Active Directory (Azure AD) tillåter åtkomst rättigheter till skyddade resurser via [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md). Azure Event Hubs definierar en uppsättning inbyggda Azure-roller som omfattar vanliga uppsättnings behörigheter som används för att komma åt Event Hub-data och du kan även definiera anpassade roller för åtkomst till data.

När en Azure-roll tilldelas till ett säkerhets objekt för Azure AD ger Azure åtkomst till dessa resurser för säkerhets objekt. Åtkomst kan begränsas till prenumerations nivån, resurs gruppen, Event Hubs namn området eller någon annan resurs under den. Ett säkerhets objekt i Azure AD kan vara en användare eller ett huvud namn för program tjänsten eller en [hanterad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Inbyggda Azure-roller för Azure Event Hubs
Azure tillhandahåller följande inbyggda Azure-roller för att auktorisera åtkomst till Event Hubs data med hjälp av Azure AD och OAuth:

| Roll | Beskrivning | 
| ---- | ----------- | 
| [Azure Event Hubs data ägare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | Använd den här rollen för att ge fullständig åtkomst till Event Hubs resurser. |
| [Azure Event Hubs data avsändare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | Använd den här rollen för att ge åtkomst till Event Hubs-resurser. |
| [Azure Event Hubs data mottagare](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | Använd den här rollen för att ge den förbrukar/får åtkomst till Event Hubs resurser. |

Inbyggda roller för schema register finns i [schema register roller](schema-registry-overview.md#azure-role-based-access-control).

## <a name="resource-scope"></a>Resursomfång 
Innan du tilldelar en Azure-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det alltid är bäst att bara bevilja det begränsande möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Event Hubs resurser, från och med det smala omfång:

- **Konsument grupp**: i det här området gäller roll tilldelningen enbart för den här entiteten. För närvarande har Azure Portal inte stöd för att tilldela en Azure-roll till ett säkerhets objekt på den här nivån. 
- **Event Hub**: roll tilldelningen gäller för entiteten Event Hub och gruppen konsument under den.
- **Namnrymd**: roll tilldelningen sträcker sig över hela topologin av Event Hubs under namn området och till den konsument grupp som är kopplad till den.
- **Resurs grupp**: roll tilldelningen gäller för alla Event Hubs resurser under resurs gruppen.
- **Prenumeration**: roll tilldelningen gäller för alla Event Hubs resurser i alla resurs grupper i prenumerationen.

> [!NOTE]
> - Tänk på att det kan ta upp till fem minuter för Azure Role-tilldelningar att spridas. 
> - Det här innehållet gäller för både Event Hubs och Event Hubs för Apache Kafka. Mer information om Event Hubs för Kafka-support finns i [Event Hubs för Kafka-säkerhet och autentisering](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication).


Mer information om hur inbyggda roller definieras finns i [förstå roll definitioner](../role-based-access-control/role-definitions.md#management-and-data-operations). Information om hur du skapar anpassade Azure-roller finns i [Azure-anpassade roller](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Exempel
- [Microsoft. Azure. EventHubs-exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    I de här exemplen används det gamla biblioteket **Microsoft. Azure. EventHubs** , men du kan enkelt uppdatera det så att det använder det senaste **Azure. Messaging. EventHubs** -biblioteket. Om du vill flytta exemplet från att använda det gamla biblioteket till ett nytt, se [guiden för att migrera från Microsoft. Azure. EventHubs till Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Exempel för Azure. Messaging. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Det här exemplet har uppdaterats för att använda det senaste **Azure. Messaging. EventHubs** -biblioteket.
- [Event Hubs för Kafka-OAuth-exempel](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Nästa steg
- Lär dig hur du tilldelar en inbyggd Azure-roll till ett säkerhets objekt finns i [autentisera åtkomst till Event Hubs resurser med Azure Active Directory](authenticate-application.md).
- Lär dig [hur du skapar anpassade roller med Azure RBAC](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Lär dig [hur du använder Azure Active Directory med händelsehubbnamnområde](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Se följande relaterade artiklar:

- [Autentisera begär anden till Azure Event Hubs från ett program med Azure Active Directory](authenticate-application.md)
- [Autentisera en hanterad identitet med Azure Active Directory för att få åtkomst till Event Hubs resurser](authenticate-managed-identity.md)
- [Autentisera begär anden till Azure Event Hubs med signaturer för delad åtkomst](authenticate-shared-access-signature.md)
- [Ge åtkomst till Event Hubs resurser med signaturer för delad åtkomst](authorize-access-shared-access-signature.md)
