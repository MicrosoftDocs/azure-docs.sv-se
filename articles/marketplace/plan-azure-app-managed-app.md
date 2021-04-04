---
title: Planera ett Azure-hanterat program för ett erbjudande för Azure-program
description: Ta reda på vad som krävs för att skapa en hanterad program plan för ett nytt Azure-program med hjälp av den kommersiella Marketplace-portalen i Microsoft Partner Center.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96621424"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>Planera ett Azure-hanterat program för ett erbjudande för Azure-program

En Azure- _hanterad program_ plan är ett sätt att publicera ett erbjudande om Azure-program på Azure Marketplace. Om du inte redan har gjort det kan du läsa [planera ett Azure Application erbjudande för den kommersiella marknads platsen](plan-azure-application-offer.md).

Hanterade program är Transact-erbjudanden som distribueras och debiteras via Azure Marketplace. Det List alternativ som en användare ser är att hämta det nu.

Använd en Azure Application: hanterad program plan när följande villkor är uppfyllda:

- Du kommer att distribuera en prenumerations-baserad lösning för din kund som använder antingen en virtuell dator (VM) eller en hel infrastruktur som en tjänst (IaaS)-baserad lösning.
- Du eller din kund kräver att lösningen hanteras av en partner. En partner kan till exempel vara en System Integrator eller en hanterad tjänst leverantör (MSP).

## <a name="managed-application-offer-requirements"></a>Krav för hanterade program erbjudanden

| Krav | Information |
| ------------ | ------------- |
| En Azure-prenumeration | Hanterade program måste distribueras till en kunds prenumeration, men de kan hanteras av en tredje part. |
| Fakturering och mätning | Resurserna finns i en kunds Azure-prenumeration. Virtuella datorer som använder betalnings modellen betala per användning samverkar med kunden via Microsoft och faktureras via kundens Azure-prenumeration. <br><br> För att få en egen licens för virtuella datorer, fakturerar Microsoft alla infrastruktur kostnader som uppstår i kund prenumerationen, men du debiteras licens avgifterna för Transact-programvaran med kunden direkt. |
| Azure-kompatibel virtuell hård disk (VHD) | Virtuella datorer måste byggas på Windows eller Linux. Mer information finns i:<br> • [Skapa en teknisk till gång för Azure VM](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (för Windows-VHD: er).<br> •  [Linux-distributioner](../virtual-machines/linux/endorsed-distros.md) som har godkänts på Azure (för Linux-VHD: er). |
| Spårning av kundanvändning | Alla nya erbjudanden för Azure-program måste också innehålla ett GUID för [Azure-partner kund användnings behörighet](azure-partner-customer-usage-attribution.md) . Mer information om kund användnings behörighet och hur du aktiverar det finns i [Azure-partner kund användnings behörighet](azure-partner-customer-usage-attribution.md). |
| Distributions paket | Du behöver ett distributions paket som gör det möjligt för kunderna att distribuera planen. Om du skapar flera planer som kräver samma tekniska konfiguration kan du använda samma paket. Mer information finns i nästa avsnitt: distributions paket. |
|||

> [!NOTE]
> Hanterade program måste kunna distribueras via Azure Marketplace. Om kund kommunikationen är ett problem kan du kontakta intresserade kunder när du har aktiverat delning av lead.

## <a name="deployment-package"></a>Distributions paket

Distributions paketet innehåller alla mallfiler som behövs för den här planen, samt ytterligare resurser, paketerade som en. zip-fil.

Alla Azure-program måste innehålla de här två filerna i rotmappen för ett zip-arkiv:

- En mall för en Resource Manager-mall med namnet [mainTemplate.jspå](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). Den här mallen definierar de resurser som ska distribueras till kundens Azure-prenumeration. Exempel på Resource Manager-mallar finns i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/) eller motsvarande [GitHub: Azure Resource Manager snabb starts mallar](https://github.com/azure/azure-quickstart-templates) lagrings platsen.
- En definition av användar gränssnittet för att skapa Azure-program med namnet [createUiDefinition.jspå](../azure-resource-manager/managed-applications/create-uidefinition-overview.md). I användargränssnittet anger du element som ger konsumenterna möjlighet att ange parametervärden.

Högsta antal fil storlekar som stöds är:

- Upp till 1 GB totalt komprimerat zip-arkiv storlek
- Upp till 1 GB för varje enskild okomprimerad fil i zip-arkivet

Alla nya erbjudanden för Azure-program måste också innehålla ett GUID för [Azure-partner kund användnings behörighet](azure-partner-customer-usage-attribution.md) .

## <a name="azure-regions"></a>Azure-regioner

Du kan publicera planen till den offentliga Azure-regionen, Azure Government region eller både och. Innan du publicerar till [Azure Government](../azure-government/documentation-government-manage-marketplace-partners.md)ska du testa och validera planen i miljön eftersom vissa slut punkter kan skilja sig. Ställ in och testa planen genom att begära ett utvärderings konto från [Microsoft Azure Government utvärderings version](https://azure.microsoft.com/global-infrastructure/government/request/).

Du, som utgivare, är ansvariga för alla kontroller, säkerhets åtgärder och bästa praxis. Azure Government använder fysiskt isolerade Data Center och nätverk (som finns i USA).

En lista över länder och regioner som stöds av den kommersiella marknads platsen finns i [stöd för geografisk tillgänglighet och valuta](marketplace-geo-availability-currencies.md).

Azure Government Services hanterar data som omfattas av vissa myndighets bestämmelser och krav. Till exempel FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS. För att få kännedom om dina certifieringar för dessa program kan du ge upp till 100 länkar som beskriver dem. De kan antingen vara länkar till din lista över programmet direkt eller länkar till beskrivningar av dina efterlevnad med dem på dina egna webbplatser. Dessa länkar är bara synliga för Azure Government kunder.

## <a name="choose-who-can-see-your-plan"></a>Välj vem som kan se din plan

Du kan konfigurera varje plan så att den blir synlig för alla (offentlig) eller endast till en speciell publik (privat). Du kan skapa upp till 100-planer och upp till 45 av dem som kan vara privata. Du kanske vill skapa en privat plan för att erbjuda olika pris alternativ eller tekniska konfigurationer för vissa kunder.

Du beviljar åtkomst till en privat plan med Azure-prenumerations-ID: n med alternativet att inkludera en beskrivning av varje prenumerations-ID som du tilldelar. Du kan lägga till högst 10 prenumerations-ID: n manuellt eller upp till 10 000 prenumerations-ID med hjälp av en. CSV-fil. ID: n för Azure-prenumerationen visas som GUID och bokstäver måste vara gemena.

Privata planer stöds inte med Azure-prenumerationer som upprättats via en åter försäljare av Cloud Solution Provider-programmet (CSP). Mer information finns [i privata erbjudanden på Microsofts kommersiella marknads](private-offers.md)plats.

> [!NOTE]
> Om du publicerar en privat plan kan du ändra dess synlighet till offentlig senare. Men när du har publicerat ett offentligt abonnemang kan du inte ändra dess synlighet till privat.

## <a name="define-pricing"></a>Definiera priser

Du måste ange priset per månad för varje plan. Detta pris är utöver alla Azure-infrastrukturer eller program kostnader som du betalar per användning av de resurser som distribueras av den här lösningen.

Utöver priset per månad kan du också ange priser för förbrukning av enheter som inte är standard med hjälp av [avgiftsbelagd fakturering](partner-center-portal/azure-app-metered-billing.md). Du kan ställa in priset per månad på noll och debitera exklusivt genom att fakturera faktureringen om du vill.

Priserna anges i USD (USD = USA dollar) konverteras till den lokala valutan för alla valda marknader med de aktuella växelkurserna när de sparas. Men du kan välja att ange kund priser för varje marknad.

## <a name="just-in-time-jit-access"></a>JIT-åtkomst (just in Time)

Med JIT-åtkomst kan du begära utökad åtkomst till ett hanterat programs resurser för fel sökning eller underhåll. Du har alltid skrivskyddad åtkomst till resurserna, men under en viss tids period kan du ha större åtkomst. Mer information finns i [Aktivera och begär just-in-Time-åtkomst för Azure Managed Applications](../azure-resource-manager/managed-applications/request-just-in-time-access.md).

> [!NOTE]
> Se till att uppdatera din `createUiDefinition.json` fil för att stödja den här funktionen.

## <a name="deployment-mode"></a>Distributionsläge

Du kan konfigurera en hanterad program plan att använda antingen det **fullständiga** eller **stegvisa** distributions läget. I komplett läge leder en omdistribution av programmet av kunden till att resurser i den hanterade resurs gruppen tas bort om resurserna inte har definierats i [mainTemplate.jspå](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template). I stegvist läge lämnar en omdistribution av programmet befintliga resurser oförändrade. Läs mer i [Azure Resource Manager distributions lägen](../azure-resource-manager/templates/deployment-modes.md).

## <a name="notification-endpoint-url"></a>URL för meddelande slut punkt

Du kan också ange en HTTPS webhook-slutpunkt för att ta emot aviseringar om alla CRUD-åtgärder på hanterade program instanser av en plan.

## <a name="customize-allowed-customer-actions-optional"></a>Anpassa tillåtna kund åtgärder (valfritt)

Du kan också ange vilka åtgärder som kunder kan utföra på de hanterade resurserna förutom de `*/read` åtgärder som är tillgängliga som standard.

Om du väljer det här alternativet måste du ange antingen kontroll åtgärder eller tillåtna data åtgärder, eller båda. Mer information finns i [förstå neka tilldelningar för Azure-resurser](../role-based-access-control/deny-assignments.md). Information om tillgängliga åtgärder finns i [Azure Resource Manager Resource Provider-åtgärder](../role-based-access-control/resource-provider-operations.md). Om du till exempel vill tillåta att användare startar om virtuella datorer kan du lägga till dem `Microsoft.Compute/virtualMachines/restart/action` i de tillåtna åtgärderna.

## <a name="choose-who-can-manage-the-application"></a>Välj vem som kan hantera programmet

Du måste ange vem som kan hantera ett hanterat program i vart och ett av de valda molnen: _offentliga Azure_ och _Azure Government molnet_. Samla in följande information:

- **Azure Active Directory klient-ID** – Azure AD-klient-ID: t (även kallat katalog-ID) som innehåller identiteterna för de användare, grupper eller program som du vill ge behörighet till. Du hittar ditt Azure AD-klient-ID på Azure Portal i [Egenskaper för Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).
- **Auktorisering** – lägg till Azure Active Directory objekt-ID för varje användare, grupp eller program som du vill ska beviljas behörighet till den hanterade resurs gruppen. Identifiera användaren med sitt huvud-ID, som finns på [bladet Azure Active Directory användare på Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

För varje huvud-ID associerar du en av de inbyggda Azure AD-rollerna (ägare eller deltagare). Rollen du väljer beskriver de behörigheter som huvud kontot kommer att ha på resurserna i kund prenumerationen. Mer information finns i [Inbyggda roller i Azure](../role-based-access-control/built-in-roles.md). Mer information om rollbaserad åtkomst kontroll (RBAC) finns i [Kom igång med RBAC i Azure Portal](../role-based-access-control/overview.md).

> [!NOTE]
> Även om du kan lägga till upp till 100-auktoriseringar per Azure-region är det oftast enklare att skapa en Active Directory användar grupp och ange dess ID i "huvud-ID". På så sätt kan du lägga till fler användare i hanterings gruppen när planen har distribuerats och minska behovet av att uppdatera planen bara för att lägga till fler auktoriseringar.

## <a name="policy-settings"></a>Principinställningar

Du kan använda [Azure-principer](../governance/policy/index.yml) för det hanterade programmet för att ange krav för kompatibilitet för den distribuerade lösningen. Du kan läsa om principdefinitioner och parametervärdenas format i [Azure Policy-exempel](../governance/policy/samples/index.md).

Du kan konfigurera högst fem principer och bara en instans av varje typ av princip. Vissa princip typer kräver ytterligare parametrar.

| Principtyp | Princip parametrar krävs |
| ------------ | ------------- |
| Azure SQL Database kryptering | Inga |
| Gransknings inställningar för Azure SQL Server | Ja |
| Azure Data Lake Store kryptering | Inga |
| Granska diagnostikinställningar | Ja |
| Granska kompatibilitet för resurs plats | Inga |
|||

För varje princip typ som du lägger till måste du associera standard-eller kostnads fria princip-SKU. Standard-SKU: n krävs för gransknings principer. Princip namn är begränsade till 50 tecken.

## <a name="next-steps"></a>Nästa steg

- [Så här skapar du ett erbjudande för Azure-program på den kommersiella Marketplace](create-new-azure-apps-offer.md)
