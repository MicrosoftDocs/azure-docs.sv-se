---
title: Vanliga frågor om IoT Solution Accelerators – Azure | Microsoft Docs
description: I den här artikeln besvaras vanliga frågor och svar om IoT Solution-acceleratorer. Den innehåller länkar till GitHub-databaserna.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903997"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>Vanliga frågor och svar om IoT-lösningsacceleratorer

Se även de [vanliga frågorna om de anslutna fabriken](iot-accelerators-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>Var hittar jag käll koden för lösnings acceleratorerna?

Käll koden lagras i följande GitHub-databaser:

* [Lösnings Accelerator för ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory)
* [Lösnings Accelerator för enhets simulering](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>Var kan jag hitta acceleratorer för fjärrövervakning och förebyggande underhålls lösningar?

Från och med den 10 december 2020 har fjärrövervakning och förebyggande underhålls acceleratorer tagits bort från webbplatsen [Azure IoT Solution Accelerators](https://www.azureiotsolutions.com/Accelerators) och är inte längre tillgängliga för nya distributioner. GitHub-databaserna för båda acceleratorerna har arkiverats. Koden är fortfarande tillgänglig för alla som har åtkomst till, men databaserna tar inga nya bidrag.

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>Vad händer med mina befintliga fjärrövervakninger och förebyggande underhålls distributioner?

Befintliga distributioner påverkas inte av att du tar bort fjärrövervakning och förebyggande underhålls lösningar och fortsätter att fungera. Förgrenade databaser påverkas inte heller. Huvud databaserna på GitHub har arkiverats.

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>Hur gör jag för att distribuerar du lösnings Accelerator för enhets simulering?

Information om hur du distribuerar lösnings acceleratorn för enhets simulering finns i GitHub-lagringsplatsen för [enhets simulering](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) .

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>Var hittar jag information om de borttagna lösnings acceleratorerna?

Se följande sidor på webbplatsen för föregående versioner:

* [Fjärrövervakning](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [Förebyggande underhåll](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>Vilka SDK: er kan jag använda för att utveckla enhets klienter för lösnings acceleratorer?

Du hittar länkar till olika språk (C, .NET, Java, Node.js, python) IoT-enhets-SDK: er i [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) : er GitHub-databaser.

Om du använder DevKit-enheten kan du hitta resurser och exempel i [IoT DEVKIT SDK](https://github.com/Microsoft/devkit-sdk) GitHub-lagringsplatsen.

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>Jag är tjänst administratör och vill ändra katalog mappningen mellan min prenumeration och en särskild Azure AD-klient. Vill du Hur gör jag för att slutföra den här uppgiften?

Se [så här lägger du till en befintlig prenumeration i din Azure AD-katalog](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>Jag vill ändra en tjänst administratör eller Co-Administrator när du är inloggad med ett organisations konto

Se Support artikeln [ändra tjänst administratör och Co-Administrator när du är inloggad med ett organisations konto](https://azure.microsoft.com/support/changing-service-admin-and-co-admin).

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Varför ser jag det här felet? "Ditt konto har inte rätt behörighet för att skapa en lösning. Kontakta konto administratören eller försök med ett annat konto. "

Se följande diagram för vägledning:

![Behörighets flöde](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> Om du fortsätter att se felet när du har verifierat att du är global administratör för Azure AD-klienten och en medadministratör för prenumerationen, har du konto administratören som tar bort användaren och tilldelar om nödvändig behörighet i den här ordningen. Lägg först till användaren som global administratör och Lägg sedan till användare som en medadministratör för Azure-prenumerationen. Kontakta [hjälp & support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om problemen kvarstår.

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>Varför ser jag det här felet när jag har en Azure-prenumeration? "En Azure-prenumeration krävs för att skapa förkonfigurerade lösningar. Du kan skapa ett kostnads fritt utvärderings konto på bara några minuter. "

Om du är säker på att du har en Azure-prenumeration verifierar du klient mappningen för din prenumeration och kontrollerar att rätt klient organisation är markerad i list rutan. Om du har verifierat att klienten är korrekt följer du föregående diagram och validerar mappningen av prenumerationen och den här Azure AD-klienten.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>Vad är skillnaden mellan att ta bort en resurs grupp i Azure Portal och klicka på ta bort på en lösnings Accelerator i azureiotsolutions.com?

* Om du tar bort Solution Accelerator i [azureiotsolutions.com](https://www.azureiotsolutions.com/)tar du bort alla resurser som distribuerades när du skapade Solution Accelerator. Om du har lagt till ytterligare resurser i resurs gruppen tas även dessa resurser bort.
* Om du tar bort resurs gruppen i [Azure Portal](https://portal.azure.com)tar du bara bort resurserna i den resurs gruppen. Du måste också ta bort det Azure Active Directory program som är kopplat till Solution Accelerator.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>Kan jag fortsätta att använda mina befintliga investeringar i Azure IoT Solution-acceleratorer?

Ja. Alla lösningar som finns idag fortsätter att fungera i din Azure-prenumeration och käll koden förblir tillgänglig i GitHub.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Hur många IoT Hub-instanser kan jag etablera i en prenumeration?

Som standard kan du etablera [10 IoT-hubbar per prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits). Du kan skapa ett [support ärende för Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att höja den här gränsen. Därför kan du, eftersom varje lösnings Accelerator etablerar en ny IoT Hub, bara etablera upp till 10 lösnings acceleratorer i en specifik prenumeration.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Hur många Azure Cosmos DB-instanser kan jag etablera i en prenumeration?

50. Du kan skapa ett [support ärende för Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) för att höja den här gränsen, men som standard kan du bara etablera 50 Cosmos DB-instanser per prenumeration.

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>Kan jag skapa en lösnings Accelerator om jag har Microsoft Azure för DreamSpark?

> [!NOTE]
> Microsoft Azure för DreamSpark kallas nu Microsoft Imagine för studenter.

För närvarande kan du inte skapa en lösnings Accelerator med ett [Microsoft Azure för DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) -konto. Du kan dock skapa ett [kostnads fritt utvärderings konto för Azure](https://azure.microsoft.com/free/) på bara några minuter som gör det möjligt att skapa en lösnings Accelerator.

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>Hur gör jag för att ta bort en Azure AD-klient?

Se Eric Golpess blogg inlägg [genom att ta bort en Azure AD-klient](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant).

### <a name="next-steps"></a>Nästa steg

Du kan även utforska andra funktioner och möjligheter i IoT-lösningsacceleratorerna:

* [Distribuera lösnings Accelerator för ansluten fabrik](quickstart-connected-factory-deploy.md)
* [IoT-säkerhet från grunden](../iot-fundamentals/iot-security-ground-up.md)