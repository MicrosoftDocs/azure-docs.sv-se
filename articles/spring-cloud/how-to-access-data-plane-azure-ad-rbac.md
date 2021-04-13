---
title: Åtkomst till konfigurations Server och tjänst register
titleSuffix: Azure Spring Cloud
description: Så här får du åtkomst till konfigurations Server-och tjänst register slut punkter med Azure Active Directory rollbaserad åtkomst kontroll.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java
ms.openlocfilehash: 90ee39039b0734bf434e7339a973358989c08175
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311370"
---
# <a name="access-config-server-and-service-registry"></a>Åtkomst till konfigurations Server och tjänst register

Den här artikeln beskriver hur du får åtkomst till vår moln konfigurations Server och vår moln tjänst register som hanteras av Azure våren Cloud med hjälp av rollbaserad åtkomst kontroll (RBAC) i Azure Active Directory (Azure AD).

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Tilldela roll till Azure AD-användare/-grupp, MSI eller tjänstens huvud namn

Om du vill använda Azure AD och RBAC måste du tilldela rollen *Azure våren Cloud data Reader* till en användare, grupp eller tjänstens huvud namn genom att följa stegen nedan:

1. Gå till tjänst översikts sidan för din tjänst instans.

2. Öppna bladet åtkomst kontroll genom att klicka på **Access Control (IAM)** .

3. Klicka på knappen **Lägg till** och **Lägg till roll tilldelningar** (auktorisering kan krävas för att läggas till).

4. Hitta och välj *Azure våren Cloud data Reader* under **roll**.
5. Tilldela åtkomst till `User, group, or service principal` eller `User assigned managed identity` enligt användar typen. Sök efter och välj användare.  
6. Klicka på `Save`

   ![tilldela – roll](media/access-data-plane-aad-rbac/assign-data-reader-role.png)

## <a name="access-config-server-and-service-registry-endpoints"></a>Åtkomst till konfigurations Server-och tjänst register slut punkter

När rollen Azure våren Cloud data Reader har tilldelats kan kunderna komma åt vår moln konfigurations Server för våren och moln tjänstens slut punkter. Använd följande procedurer:

1. Hämta en åtkomsttoken. När en Azure AD-användare har tilldelats rollen Azure våren Cloud data Reader kan kunderna använda följande kommandon för att logga in på Azure CLI med användare, tjänstens huvud namn eller hanterad identitet för att få en åtkomsttoken. Mer information finns i [autentisera Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Skapa slut punkten. Vi stöder standard slut punkter för vår moln konfigurations Server och vår moln tjänst register som hanteras av Azure våren Cloud. Mer information finns i [produktions klara slut punkter](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints). Kunder kan också få en fullständig lista över slut punkter som stöds av våren Cloud config server och våren Cloud Service Registry som hanteras av Azure våren Cloud genom att komma åt slut punkter:

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/'* 

>[!NOTE]
> Om du använder Azure Kina kan du ersätta `*.azuremicroservices.io` med `*.microservices.azure.cn` , se [mer](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure).

3. Få åtkomst till den sammanslutna slut punkten med åtkomst-token. Lägg till åtkomsttoken i en rubrik för att ge auktorisering.  Endast metoden GET stöds.

    Du kan till exempel få åtkomst till en slut punkt som *" https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health "* för att se hälso status för Eureka.

    Om svaret är *401 obehörig*, kontrollerar du om rollen har tilldelats.  Det tar flera minuter innan rollen börjar gälla eller verifierar att åtkomsttoken inte har upphört att gälla.

## <a name="next-steps"></a>Nästa steg
* [Autentisera Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)
* [Färdiga slut punkter för produktion](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Se även
* [Skapa roller och behörigheter](how-to-permissions.md)
