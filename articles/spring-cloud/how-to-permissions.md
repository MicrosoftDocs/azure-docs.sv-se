---
title: Så här använder du behörigheter i Azure våren Cloud
description: Den här artikeln visar hur du skapar anpassade roller för behörigheter i Azure våren Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878489"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Så här använder du behörigheter i Azure våren Cloud
Den här artikeln visar hur du skapar anpassade roller som delegerar behörigheter till Azure våren Cloud-resurser. Anpassade roller utökar [inbyggda Azure-roller](../role-based-access-control/built-in-roles.md) med olika lager behörigheter.

Vi kommer att implementera följande anpassade roller:

* **Rollen utvecklare**: 
    * Distribuera
    * Test
    * Starta om appar
    * Kan tillämpa och göra ändringar i AppData på git-lagringsplatsen
    * Kan hämta logg strömmen
* Hantering av **Ops-Sites tillförlitlighet**: 
    * Starta om appar
    * Hämta logg strömmar
    * Det går inte att göra ändringar i appar eller konfigurationer
* **Roll för Azure-pipeline/Jenkins/GitHub-åtgärder**:
    * Kan utföra åtgärder för att skapa, läsa, uppdatera och ta bort
    * Kan skapa och konfigurera allt i Azure våren Cloud och appar inom tjänst instansen: Azure-pipeline, Jenkins-eller GitHub-åtgärder med hjälp av terraform-eller ARM-mallar

## <a name="define-developer-role"></a>Definiera rollen utvecklare

Rollen utvecklare innehåller behörigheter för att starta om appar och se deras logg strömmar, men kan inte göra ändringar i appar, konfiguration.

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>Navigera i prenumerations-och resurs grupps åtkomst kontroll (IAM)

Följ de här stegen för att börja definiera en roll.

1. I Azure Portal öppnar du den prenumeration och resurs grupp där du vill att den anpassade rollen ska kunna tilldelas.
2. Öppna **åtkomst kontroll (IAM)**.
3. Klicka på **+ Lägg till**.
4. Klicka på **Lägg till anpassad roll**.
5. Klicka på **Nästa**.

   ![Skapa anpassad roll](media/spring-cloud-permissions/create-custom-role.png)

6. Klicka på **Lägg till behörigheter**.

   ![Lägg till behörigheter start](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Sök efter Azure våren Cloud-behörigheter:
7. Sök efter *Microsoft.app* i sökrutan.
Välj *Microsoft Azure våren Cloud*.

   ![Välj moln för Azure våren](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Välj behörigheter för rollen utvecklare:

Från **Microsoft. AppPlatform/våren** väljer du:
* Skriva: skapa eller uppdatera Azure våren Cloud Service-instansen
* Läs: få Azure våren Cloud Service-instans
* Övrigt: lista test nycklar för Azure våren Cloud Service instance

Från **Microsoft. AppPlatform/våren/Apps** väljer du:
* Läs: Läs Microsoft Azure våren Cloud Application
* Övrigt: Hämta Microsoft Azure fjäder moln program resurs överförings-URL

Från **Microsoft. AppPlatform/våren/Apps/bindningar** väljer du:
* Läs: Läs Microsoft Azure fjäder moln program bindning

Från **Microsoft. AppPlatform/våren/Apps/distributioner** väljer du:
* Skriv: Skriv Microsoft Azure fjäder moln program distribution
* Läsa: Läs Microsoft Azure fjäder moln program distribution
* Övrigt: starta Microsoft Azure fjäder moln program distribution
* Övrigt: stoppa Microsoft Azure fjäder moln program distribution
* Övrigt: starta om Microsoft Azure fjäder moln program distribution
* Övrigt: Hämta Microsoft Azure fjäder moln program distributions logg filens URL

Från **Microsoft. AppPlatform/våren/Apps/Domains** väljer du:
* Läsa: läsa Microsoft Azure vår moln program anpassad domän

Från **Microsoft. AppPlatform/våren/certificates** väljer du:
* Läs: Läs Microsoft Azure våren Cloud Certificate

Från **Microsoft. AppPlatform/locations/operationResults/våren** väljer du:
* Läs: Läs åtgärds resultat

Från **Microsoft. AppPlatform/locations/operationStatus/operationId** väljer du:
* Läsa: Läs åtgärds status

    [![Skapa Developler-behörigheter ](media/spring-cloud-permissions/developer-permissions-box.png)](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Klicka på **Lägg till**.

10. Granska behörigheterna.

11. Klicka på **Granska och skapa**.

## <a name="define-devops-engineer-role"></a>Definiera DevOps-ingenjörs roll
Den här proceduren definierar en roll med behörighet att distribuera, testa och starta om Azure våren Cloud Apps.

1. Upprepa proceduren för att navigera i prenumeration, resurs grupp och åtkomst kontroll (IAM).
2. Välj behörigheter för DevOps-ingenjörs rollen:

Från **Microsoft. AppPlatform/våren** väljer du:
* Skriva: skapa eller uppdatera Azure våren Cloud Service-instansen
* Ta bort: ta bort Azure våren Cloud Service-instansen
* Läs: få Azure våren Cloud Service-instans
* Övrigt: Aktivera test slut punkten Azure våren Cloud Service instance
* Övrigt: inaktivera test slut punkten för Azure våren Cloud Service instance
* Övrigt: lista test nycklar för Azure våren Cloud Service instance
* Övrigt: återskapa test nyckeln för Azure våren Cloud Service instance

Från **Microsoft. AppPlatform/våren/Apps** väljer du:
* Skriv: Skriv Microsoft Azure våren Cloud Application
* Ta bort: ta bort Microsoft Azure våren Cloud Application
* Läs: Läs Microsoft Azure våren Cloud Application
* Övrigt: Hämta Microsoft Azure fjäder moln program resurs överförings-URL
* Övrigt: Verifiera Microsoft Azure vår anpassade domän för moln program

Från **Microsoft. AppPlatform/våren/Apps/bindningar** väljer du:
* Skriv: Skriv Microsoft Azure fjäder moln program bindning
* Ta bort: ta bort Microsoft Azure fjäder moln program bindning
* Läs: Läs Microsoft Azure fjäder moln program bindning

Från **Microsoft. AppPlatform/våren/Apps/distributioner** väljer du:
* Skriv: Skriv Microsoft Azure fjäder moln program distribution
* Ta bort: ta bort Azure våren Cloud Application Deployment
* Läsa: Läs Microsoft Azure fjäder moln program distribution
* Övrigt: starta Microsoft Azure fjäder moln program distribution
* Övrigt: stoppa Microsoft Azure fjäder moln program distribution
* Övrigt: starta om Microsoft Azure fjäder moln program distribution
* Övrigt: Hämta Microsoft Azure fjäder moln program distributions logg filens URL

Från **Microsoft. AppPlatform/våren/Apps/distributioner/SKU: er** väljer du:
* Läs: Visa lista över tillgängliga SKU: er för program distribution

Från **Microsoft. AppPlatform/locations** väljer du:
* Övrigt: kontrol lera namn tillgänglighet

Från Microsoft. AppPlatform/locations/operationResults/våren Select: Read: Läs åtgärds resultat

Från **Microsoft. AppPlatform/locations/operationStatus/operationId** väljer du:
* Läsa: Läs åtgärds status

Från **Microsoft. AppPlatform/SKU: er** väljer du:
* Läsa: lista tillgängliga SKU: er

   [![Behörigheter ](media/spring-cloud-permissions/dev-ops-permissions.png) för dev/op](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. Klicka på **Lägg till**.

4. Granska behörigheterna.

5. Klicka på **Granska och skapa**.

## <a name="define-ops---site-reliability-engineering-role"></a>Definiera rollen som tillförlitlig teknik för OPS-site
Den här proceduren definierar en roll med behörighet att distribuera, testa och starta om Azure våren Cloud Apps.

1. Upprepa proceduren för att navigera i prenumeration, resurs grupp och åtkomst kontroll (IAM).

2. Välj behörigheter för rollen som tillförlitlig teknik för OPS-site:

Från **Microsoft. AppPlatform/våren** väljer du:
* Läs: få Azure våren Cloud Service-instans
* Övrigt: lista test nycklar för Azure våren Cloud Service instance

Från **Microsoft. AppPlatform/våren/Apps** väljer du:
* Läs: Läs Microsoft Azure våren Cloud Application

Från **Microsoft. AppPlatform/Apps/distributioner** väljer du:
* Läsa: Läs Microsoft Azure fjäder moln program distribution
* Övrigt: starta Microsoft Azure fjäder moln program distribution
* Övrigt: stoppa Microsoft Azure fjäder moln program distribution
* Övrigt: starta om Microsoft Azure fjäder moln program distribution

Från **Microsoft. AppPlatform/locations/operationResults/våren** väljer du:
* Läs: Läs åtgärds resultat

Från **Microsoft. AppPlatform/locations/operationStatus/operationId** väljer du:
* Läsa: Läs åtgärds status

   [![Ops/SRE-behörigheter ](media/spring-cloud-permissions/ops-sre-permissions.png)](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Klicka på **Lägg till**.

4. Granska behörigheterna.

5. Klicka på **Granska och skapa**.

## <a name="define-azure-pipelinesprovisioning-role"></a>Definiera Azure-pipeline/etablerings roll
Denna roll för Jenkins/GitHub-åtgärder kan skapa och konfigurera allt i Azure våren Cloud och appar med en tjänst instans. Den här rollen är till för att släppa eller distribuera kod.

1. Upprepa proceduren för att navigera i prenumeration, resurs grupp och åtkomst kontroll (IAM).

2. Öppna **behörighets** alternativen.

3. Välj behörigheter för Azure-pipeline/Provisioning-rollen:
  
Från **Microsoft. AppPlatform/våren** väljer du:
* Skriva: skapa eller uppdatera Azure våren Cloud Service-instansen
* Ta bort: ta bort Azure våren Cloud Service-instansen
* Läs: få Azure våren Cloud Service-instans
* Övrigt: Aktivera test slut punkten Azure våren Cloud Service instance
* Övrigt: inaktivera test slut punkten för Azure våren Cloud Service instance
* Övrigt: lista test nycklar för Azure våren Cloud Service instance
* Övrigt: återskapa test nyckeln för Azure våren Cloud Service instance

Från **Microsoft. AppPlatform/våren/Apps** väljer du:
* Skriv: Skriv Microsoft Azure våren Cloud Application
* Ta bort: ta bort Microsoft Azure våren Cloud Application
* Läs: Läs Microsoft Azure våren Cloud Application
* Övrigt: Hämta Microsoft Azure fjäder moln program resurs överförings-URL
* Övrigt: Verifiera Microsoft Azure vår anpassade domän för moln program

Från **Microsoft. AppPlatform/våren/Apps/bindningar** väljer du:
* Skriv: Skriv Microsoft Azure fjäder moln program bindning
* Ta bort: ta bort Microsoft Azure fjäder moln program bindning
* Läs: Läs Microsoft Azure fjäder moln program bindning

Från **Microsoft. AppPlatform/våren/Apps/distributioner** väljer du:
* Skriv: Skriv Microsoft Azure fjäder moln program distribution
* Ta bort: ta bort Azure våren Cloud Application Deployment
* Läsa: Läs Microsoft Azure fjäder moln program distribution
* Övrigt: starta Microsoft Azure fjäder moln program distribution
* Övrigt: stoppa Microsoft Azure fjäder moln program distribution
* Övrigt: starta om Microsoft Azure fjäder moln program distribution
* Övrigt: Hämta Microsoft Azure fjäder moln program distributions logg filens URL

Från **Microsoft. AppPlatform/SKU: er** väljer du:
* Läsa: lista tillgängliga SKU: er

Från **Microsoft. AppPlatform/locations** väljer du:
* Övrigt: kontrol lera namn tillgänglighet

Från **Microsoft. AppPlatform/locations/operationResults/våren** väljer du:
* Läs: Läs åtgärds resultat

Från **Microsoft. AppPlatform/locations/operationStatus/operationId** väljer du:
* Läsa: Läs åtgärds status

Från **Microsoft. AppPlatform/SKU: er** väljer du:
* Läsa: lista tillgängliga SKU: er

   [![Pipeline-behörigheter ](media/spring-cloud-permissions/pipelines-permissions-box.png)](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Klicka på **Lägg till**.

5. Granska behörigheterna.

6. Klicka på **Granska och skapa**.


## <a name="see-also"></a>Se även
* [Skapa eller uppdatera anpassade roller i Azure med Microsoft Azure-portalen](../role-based-access-control/custom-roles-portal.md)

Mer information om tre metoder som definierar en anpassad behörighet finns i:
* [Klona en roll](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [Börja från början](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [Starta från JSON](../role-based-access-control/custom-roles-portal.md#start-from-json)