---
title: Komma åt data planet med Azure AD, RBAC
description: Så här kommer du åt data planet med Azure Active Directory rollbaserad åtkomst kontroll.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 2909d40fbc7cb5b310ea17f17a6e683ce47638ce
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220139"
---
# <a name="access-the-data-plane-with-azure-active-directory-and-role-based-access-control"></a>Komma åt data planet med Azure Active Directory och rollbaserad Access Control

Den här artikeln beskriver hur du kommer åt Azure våren Cloud config server och service Registry-slutpunkter med hjälp av Azure Active Directory (AAD) rollbaserad åtkomst kontroll (RBAC).

## <a name="assign-role-to-aad-usergroup-msi-or-service-principal"></a>Tilldela roll till AAD användare/grupp, MSI eller tjänstens huvud namn

Om du vill använda AAD och RBAC måste du tilldela rollen *Azure våren Cloud data Reader* till en användare, grupp eller tjänstens huvud namn genom att följa stegen nedan:

1. Gå till tjänst översikts sidan för din tjänst instans.

2. Öppna bladet åtkomst kontroll genom att klicka på **Access Control (IAM)** .

3. Klicka på knappen **Lägg till** och **Lägg till roll tilldelningar** (auktorisering kan krävas för att läggas till).

4. Hitta och välj *Azure våren Cloud data Reader* under **roll**.
5. Tilldela åtkomst till `User, group, or service principal` eller `User assigned managed identity` enligt användar typen. Sök efter och välj användare.  
6. Klicka på `Save`

   ![tilldela – roll](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-data-plane"></a>Kom åt data planet

När en AAD-användare har tilldelats rollen *Azure våren Cloud data Reader* kan kunderna logga in på Azure CLI med användare, tjänstens huvud namn eller hanterad identitet.  Se [autentisera Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli) för att hämta en åtkomsttoken.  Använd sedan följande kommandon.

```azurecli
az login
az account get-access-token
```

För närvarande stöder CLI standard slut punkter för konfigurations servern och tjänst registret. Mer information finns i [produktions klara slut punkter](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints). 

Kunder kan också få en fullständig lista över slut punkter som stöds av konfigurations servern och tjänst registret genom att komma åt slut punkter:
* *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/*
* *https://SERVICE_NAME.Root_Endpoint/config/actuator/* 

Åtkomsttoken i rubriken ger auktorisering. Endast metoden GET stöds.

Du kan till exempel få åtkomst till en slut punkt som *https://SERVICE_NAME.Root_Endpoint/eureka/actuator/health* visar hälso status för Eureka.

Olika rot slut punkter visas nedan beroende på olika moln typer.

| Moln          | Rot slut punkt              |
| -------------- | -------------------------- |
| Offentliga         | svc.azuremicroservices.io  |
| Mooncake/Kina | svc.microservices.azure.cn |

Om svaret är *401 obehörig*, kontrollerar du om rollen har tilldelats.  Det tar flera minuter innan rollen börjar gälla eller verifierar att åtkomsttoken inte har upphört att gälla.

## <a name="next-steps"></a>Nästa steg
* [Autentisera Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Färdiga slut punkter för produktion](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Se även
* [Skapa roller och behörigheter](spring-cloud-howto-permissions.md)