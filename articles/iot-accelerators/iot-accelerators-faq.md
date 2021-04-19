---
title: Vanliga frågor och svar om IoT-lösningsacceleratorer – Azure | Microsoft Docs
description: Den här artikeln besvarar vanliga frågor för IoT-lösningsacceleratorer. Den innehåller länkar till GitHub-lagringsplatsen.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 61993df77b0831926f16339a741a2553e80c2a0d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713848"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Vanliga frågor och svar om IoT-lösningsacceleratorer

Se även Vanliga frågor och [svar om ansluten fabrik.](iot-accelerators-faq-cf.md)

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Var hittar jag källkoden för lösningsacceleratorerna?

Källkoden lagras i följande GitHub-lagringsplatsen:

* [Lösningsacceleratorn Ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory)
* [Lösningsaccelerator för enhetssimulering](https://github.com/Azure/azure-iot-pcs-device-simulation)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Var hittar jag lösningsacceleratorer för fjärrövervakning och förutsägande underhåll?

Från och med 10 december 2020 har acceleratorerna för fjärrövervakning och förutsägande underhåll tagits bort från [Azure IoT-lösningsacceleratorer-platsen](https://www.azureiotsolutions.com/Accelerators) och är inte längre tillgängliga för nya distributioner. GitHub-lagringsplatsen för båda acceleratorerna har arkiverats. Koden är fortfarande tillgänglig för alla att komma åt, men lagringsplatsen tar inte emot några nya bidrag.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Vad händer med mina befintliga distributioner av fjärrövervakning och förutsägande underhåll?

Befintliga distributioner påverkas inte av borttagningen av lösningsacceleratorerna för fjärrövervakning och förutsägande underhåll och fortsätter att fungera. Förked lagringsplatsen påverkas inte heller. Huvudlagringsplatsen på GitHub har arkiverats.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Hur gör jag för att du lösningsacceleratorn för enhetssimulering?

Om du vill distribuera lösningsacceleratorn för enhetssimulering kan du gå till GitHub-lagringsplatsen [för](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) enhetssimulering.

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Var hittar jag information om de borttagna lösningsacceleratorerna?

Se följande sidor på webbplatsen för tidigare versioner:

* [Fjärrövervakning](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Förebyggande underhåll](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Vilka SDK:er kan jag använda för att utveckla enhetsklienter för lösningsacceleratorer?

Du hittar länkar till IoT-enhets-SDK:er för olika språk (C, .NET, Java, Node.js, Python) [i GitHub-lagringsplatsen för Microsoft Azure IoT-SDK:er.](https://github.com/Azure/azure-iot-sdks)

Om du använder DevKit-enheten hittar du resurser och exempel på GitHub-lagringsplatsen [för IoT DevKit SDK.](https://github.com/Microsoft/devkit-sdk)

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jag är tjänstadministratör och vill ändra katalogmappningen mellan min prenumeration och en specifik Azure AD-klientorganisation. Hur gör jag för att du slutföra den här uppgiften?

Se [Lägga till en befintlig prenumeration i Azure AD-katalogen](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Jag vill ändra en tjänstadministratör eller Co-Administrator när jag är inloggad med ett organisationskonto

Se supportartikeln [Ändra tjänstadministratör och Co-Administrator när du är inloggad med ett organisationskonto](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Varför ser jag det här felet? "Ditt konto har inte rätt behörighet för att skapa en lösning. Kontakta din kontoadministratör eller försök med ett annat konto."

Titta på följande diagram för vägledning:

![Flödesschema för behörigheter](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Om du fortsätter att se felet när du har verifierat att du är global administratör för Azure AD-klienten och en medadministratör för prenumerationen, be kontoadministratören ta bort användaren och tilldela om nödvändiga behörigheter i den här ordningen. Lägg först till användaren som global administratör och lägg sedan till användaren som medadministratör för Azure-prenumerationen. Om problemet kvarstår kontaktar du [Hjälp & support .](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Varför visas det här felet när jag har en Azure-prenumeration? "En Azure-prenumeration krävs för att skapa förkonfigurerade lösningar. Du kan skapa ett kostnadsfritt utvärderingskonto på bara några minuter."

Om du är säker på att du har en Azure-prenumeration verifierar du klientmappningen för din prenumeration och kontrollerar att rätt klient har valts i listrutan. Om du har verifierat att klientorganisationen är korrekt följer du diagrammet ovan och validerar mappningen av din prenumeration och den här Azure AD-klienten.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Vad är skillnaden mellan att ta bort en resursgrupp i Azure Portal klicka på Ta bort på en lösningsaccelerator i azureiotsolutions.com?

* Om du tar bort lösningsacceleratorn [i azureiotsolutions.com](https://www.azureiotsolutions.com/)tar du bort alla resurser som distribuerades när du skapade lösningsacceleratorn. Om du har lagt till ytterligare resurser i resursgruppen tas även dessa resurser bort.
* Om du tar bort resursgruppen i [Azure Portal](https://portal.azure.com)tar du bara bort resurserna i den resursgruppen. Du måste också ta bort den Azure Active Directory som är associerad med lösningsacceleratorn.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan jag fortsätta att utnyttja mina befintliga investeringar i Azure IoT-lösningsacceleratorer?

Ja. Alla lösningar som finns i dag fortsätter att fungera i din Azure-prenumeration och källkoden förblir tillgänglig i GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hur många IoT Hub instanser kan jag etablera i en prenumeration?

Som standard kan du etablera [10 IoT-hubbar per prenumeration.](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits) Du kan skapa en [Azure-supportbiljett för](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att höja den här gränsen. Eftersom varje lösningsaccelerator etablerar en ny IoT Hub kan du därför bara etablera upp till 10 lösningsacceleratorer i en viss prenumeration.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hur många Azure Cosmos DB instanser kan jag etablera i en prenumeration?

Femtio. Du kan skapa en [Azure-supportbiljett](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att höja den här gränsen, men som standard kan du bara etablera 50 Cosmos DB instanser per prenumeration.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jag skapa en lösningsaccelerator om jag Microsoft Azure för DreamSpark?

> [!NOTE]
> Microsoft Azure för DreamSpark kallas nu Microsoft Imagine för studenter.

För närvarande kan du inte skapa en lösningsaccelerator med [ett Microsoft Azure för Ett DreamSpark-konto.](https://azure.microsoft.com/pricing/member-offers/imagine/) Du kan dock skapa ett kostnadsfritt [utvärderingskonto för Azure](https://azure.microsoft.com/free/) på bara några minuter så att du kan skapa en lösningsaccelerator.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hur gör jag för att ta bort en Azure AD-klient?

Mer information finns i Eric Golpes blogginlägg Walkthrough of Deleting an Azure AD Tenant (Ta [bort en Azure AD-klientorganisation).](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Distribuera lösningsacceleratorn Ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](../iot-fundamentals/iot-security-ground-up.md)