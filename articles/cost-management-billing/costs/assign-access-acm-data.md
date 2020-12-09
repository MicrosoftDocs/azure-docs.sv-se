---
title: Tilldela åtkomst till Azure Cost Management-data
description: I den här artikeln går vi igenom att tilldela behörighet till Azure Cost Management-data för olika omfång.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.custom: secdec18
ms.openlocfilehash: 660b5751e3b3cbc632331e99d797af3392a8aea4
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2020
ms.locfileid: "91371974"
---
# <a name="assign-access-to-cost-management-data"></a>Tilldela åtkomst till Cost Management-data

För användare med Azure Enterprise-avtal definieras en användares åtkomst till Azure Cost Management-data av en kombination av behörigheter som tilldelats i Azure-portalen och Enterprise-portalen (EA). För användare med andra typer av Azure-konton är det enklare att definiera en användares åtkomstnivå till Cost Management-data via rollbaserad åtkomstkontroll i Azure (Azure RBAC). I den här artikeln går vi igenom att tilldela åtkomst till Cost Management-data. När kombinationen av behörigheter har tilldelats kan användaren visa data i Cost Management baserat på vilket åtkomstomfång användaren har och vilket omfång han eller hon väljer på Azure-portalen.

Det omfång användaren väljer används i hela Cost Management för att ge datakonsolidering och styra åtkomsten till kostnadsinformation. Användare väljer inte flera omfång samtidigt. I stället väljs ett större omfång som de underordnade omfången ingår, och sedan filtrerar användaren ned till den önskade informationen. Det är viktigt att förstå datakonsolidering eftersom vissa användare inte bör ha åtkomst till överordnade omfång som underordnade omfång ingår i.

Se videon om [åtkomstkontroll i Cost Management](https://www.youtube.com/watch?v=_uQzQ9puPyM) om du vill veta mer om hur du tilldelar åtkomst för att visa kostnader och avgifter med rollbaserad åtkomstkontroll i Azure (Azure RBAC). Om du vill titta på andra videor går du till [YouTube-kanalen för Cost Management](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/_uQzQ9puPyM]

## <a name="cost-management-scopes"></a>Cost Management-omfång

Cost Management har stöd för flera olika typer av Azure-konton. Om du vill se hela listan med kontotyper som stöds kan du läsa [Förstå Cost Management-data](understand-cost-mgt-data.md). Kontotypen avgör vilka omfång som är tillgängliga.

### <a name="azure-ea-subscription-scopes"></a>Omfång för Azure EA-prenumerationer

Du måste minst ha skrivskyddad åtkomst till ett eller flera av följande omfång för att visa kostnadsdata för Azure EA-prenumerationer.

| **Omfång** | **Definieras på** | **Nödvändig åtkomst för att visa data** | **Nödvändig EA-inställning** | **Konsoliderar data till** |
| --- | --- | --- | --- | --- |
| Faktureringskonto<sup>1</sup> | [https://ea.azure.com](https://ea.azure.com/) | Företagsadministratör | Ingen | Alla prenumerationer från Enterprise-avtalet |
| Avdelning | [https://ea.azure.com](https://ea.azure.com/) | Avdelningsadministratör | **Visa avgifter för DA** aktiverad | Alla prenumerationer som tillhör ett registreringskonto som är länkade till avdelningen |
| Registreringskonto<sup>2</sup> | [https://ea.azure.com](https://ea.azure.com/) | Kontoägare | **Visa avgifter för AO** aktiverad | Alla prenumerationer från registreringskontot |
| Hanteringsgrupp | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-läsare (eller Läsare) | **Visa avgifter för AO** aktiverad | Alla prenumerationer under hanteringsgruppen |
| Prenumeration | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-läsare (eller Läsare) | **Visa avgifter för AO** aktiverad | Alla resurser/resursgrupper i prenumerationen |
| Resursgrupp | [https://portal.azure.com](https://portal.azure.com/) | Cost Management-läsare (eller Läsare) | **Visa avgifter för AO** aktiverad | Alla resurser i resursgruppen |

<sup>1</sup> Faktureringskontot kallas även för Enterprise-avtal eller registrering.

<sup>2</sup> Registreringskontot kallas också för kontoinnehavare.


## <a name="other-azure-account-scopes"></a>Andra omfång för Azure-konton

Du måste minst ha skrivskyddad åtkomst till ett eller flera av följande omfång för att visa kostnadsdata för andra Azure-prenumerationer:

- Azure-konto
- Hanteringsgrupp
- Resursgrupp

Olika omfång är tillgängliga när en partner har registrerat en kund i ett Microsoft-kundavtal. CSP-kunder kan sedan använda Cost Management-funktioner när de har aktiverats av CSP-partnern. Mer information finns i [Komma igång med Azure Cost Management för partners](get-started-partners.md).

## <a name="enable-access-to-costs-in-the-azure-portal"></a>Ge åtkomst till kostnader på Azure-portalen

Avdelningsomfånget kräver att alternativet **Avdelningsadministratörer kan visa debiteringar** (Visa avgifter för DA) har inställningen **På**. Konfigurera alternativet på Azure-portalen eller EA-portalen. Alla andra omfång kräver att alternativet **Kontoägare kan visa debiteringar** (Visa avgifter för AO) har inställningen **På**.

Så här aktiverar du ett alternativ på Azure-portalen:

1. Logga in på Azure-portalen på https://portal.azure.com med ett administratörskonto.
1. Välj menyalternativet **Kostnadshantering + fakturering**.
1. Välj **Faktureringsomfång** för att visa en lista över tillgängliga faktureringsomfång och faktureringskonton.
1. Välj ditt **faktureringskonto** i listan med tillgängliga faktureringskonton.
1. Under **Inställningar** väljer du menyalternativet **Principer** och konfigurerar sedan inställningen.  
    ![Principer för faktureringsomfång som visar alternativ för att visa debiteringar](./media/assign-access-acm-data/azure-portal-policies-view-charges.png)

Förutom att aktivera alternativen för visning av avgifter måste du även konfigurera rollbaserad åtkomstkontroll i Azure (Azure RBAC) för de flesta omfång i Azure-portalen.

## <a name="enable-access-to-costs-in-the-ea-portal"></a>Ge tillgång till kostnader i EA-portalen

För omfånget Avdelning måste alternativet **Visa avgifter för DA** vara **aktiverat** i EA-portalen. Konfigurera alternativet på Azure-portalen eller EA-portalen. För alla andra omfång måste alternativet **Visa avgifter för OA** vara **aktiverat** i EA-portalen.

Så här aktiverar du ett alternativ på EA-portalen:

1. Logga in i EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett administratörskonto.
2. Välj **Hantera** i fönstret till vänster.
3. Ändra avgiftsalternativet till **Visa avgifter för DA** eller **Visa avgifter för AO** för de Cost Management-omfång du vill ge åtkomst till.  
    ![Fliken Registrering med alternativen Visa avgifter för DA och AO](./media/assign-access-acm-data/ea-portal-enrollment-tab.png)

Förutom att aktivera alternativen för visning av avgifter måste du även konfigurera rollbaserad åtkomstkontroll i Azure (Azure RBAC) för de flesta omfång i Azure-portalen.

## <a name="enterprise-administrator-role"></a>Rollen Företagsadministratör

En företagsadministratör har om standard åtkomst till faktureringskontot (Enterprise-avtal/registrering) och till alla andra underordnade omfång. Företagsadministratören tilldelar åtkomst till omfång för andra användare. Det är en bra regel att säkra kontinuiteten genom att alltid ha två användare med behörighet som företagsadministratör. I följande avsnitt visas ett exempel där företagsadministratören tilldelar åtkomst till omfång för andra användare.

## <a name="assign-billing-account-scope-access"></a>Tilldela åtkomst till omfånget Faktureringskonto

För åtkomst till omfånget Faktureringskonto krävs behörighet som företagsadministratör i EA-portalen. Företagsadministratören kan visa kostnader för hela EA-registreringen eller flera registreringar. Det krävs ingen åtgärd i Azure-portalen för omfånget Faktureringskonto.

1. Logga in i EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett administratörskonto.
2. Välj **Hantera** i fönstret till vänster.
3. Välj registreringen du vill hantera på fliken **Registrering**.  
    ![välj din registrering i EA-portalen](./media/assign-access-acm-data/ea-portal.png)
4. Välj **+ Lägg till administratör**.
5. I rutan Lägg till administratör väljer du autentiseringstypen och skriver in användarens e-postadress.
6. Om användaren ska ha skrivskyddad åtkomst till kostnader och användningsdata går du till **Skrivskyddad** och väljer **Ja**.  Annars väljer du **Nej**.
7. Välj **Lägg till** för att skapa kontot.  
    ![exempelinformation som visas i rutan Lägg till administratör](./media/assign-access-acm-data/add-admin.png)

Det kan ta upp till 30 minuter innan den nya användaren kan komma åt data i Cost Management.

### <a name="assign-department-scope-access"></a>Tilldela åtkomst till omfånget Avdelning

För åtkomst till omfånget Avdelning krävs behörigheten avdelningsadministratör (Visa avgifter för DA) i EA-portalen. Avdelningsadministratören kan visa kostnader och användningsdata kopplade till en eller flera avdelningar. I avdelningsdata ingår alla prenumerationer som tillhör ett registreringskonto som är kopplat till avdelningen. Det krävs ingen åtgärd i Azure-portalen.

1. Logga in i EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett administratörskonto.
2. Välj **Hantera** i fönstret till vänster.
3. Välj registreringen du vill hantera på fliken **Registrering**.
4. Välj fliken **Avdelning** och välj sedan **Lägg till administratör**.
5. I rutan Lägg till avdelningsadministratör väljer du autentiseringstypen och skriver in användarens e-postadress.
6. Om användaren ska ha skrivskyddad åtkomst till kostnader och användningsdata går du till **Skrivskyddad** och väljer **Ja**.  Annars väljer du **Nej**.
7. Välj de avdelningar som du vill ge behörighet som avdelningsadministratör till.
8. Välj **Lägg till** för att skapa kontot.  
    ![ange nödvändig information i rutan Lägg till avdelningsadministratör](./media/assign-access-acm-data/add-depart-admin.png)

## <a name="assign-enrollment-account-scope-access"></a>Tilldela åtkomst till omfånget Registreringskonto

För åtkomst till omfånget Registreringskonto krävs behörighet som kontoinnehavare (Visa avgifter för AO) i EA-portalen. Kontoinnehavare kan visa kostnader och användningsdata för de prenumerationer som skapats via registreringskontot. Det krävs ingen åtgärd i Azure-portalen.

1. Logga in i EA-portalen på [https://ea.azure.com](https://ea.azure.com) med ett administratörskonto.
2. Välj **Hantera** i fönstret till vänster.
3. Välj registreringen du vill hantera på fliken **Registrering**.
4. Välj fliken **Konto** och välj sedan **Lägg till konto**.
5. I rutan Lägg till konto väljer du den **avdelning** som kontot ska kopplas till, eller så lämnar du kontot otilldelat.
6. Välj autentiseringstyp och skriv kontonamnet.
7. Skriv användarens e-postadress och sedan kostnadsställe (valfritt).
8. Välj **Lägg till** för att skapa kontot.  
    ![ange den information som krävs för registreringskontot i rutan Lägg till konto](./media/assign-access-acm-data/add-account.png)

När du har slutfört stegen ovan blir användarkontot ett registreringskonto i Enterprise-portalen och kan skapa prenumerationer. Användaren kan visa information om kostnader och användning för de prenumerationer som skapas.

## <a name="assign-management-group-scope-access"></a>Tilldela åtkomst till omfånget Hanteringsgrupp

För åtkomst till omfånget Hanteringsgrupp krävs behörighet på minst nivån Cost Management-läsare (eller läsare). Du kan konfigurera behörigheter för en hanteringsgrupp i Azure-portalen. Du måste minst ha behörighet som administratör för användaråtkomst (eller ägare) för hanteringsgruppen innan du kan ge åtkomst till andra. För Azure EA-konton måste du också ha aktiverat inställningen **Visa avgifter för AO** i EA-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **Alla tjänster** i sidolisten, sök efter _hanteringsgrupper_ och välj **Hanteringsgrupper**.
3. Välj hanteringsgruppen i hierarkin.
4. Välj **Information** bredvid namnet på hanteringsgruppen.
5. Välj **Åtkomstkontroll (IAM)** i den vänstra rutan.
6. Välj **Lägg till**.
7. Under **Roll** väljer du **Cost Management-läsare**.
8. Under **Bevilja åtkomst till** väljer du **Användare, grupp eller program i Azure AD**.
9. Sök efter och välj användaren för att bevilja åtkomsten.
10. Välj **Spara**.  
    ![exempelinformation i rutan Lägg till behörigheter för en hanteringsgrupp](./media/assign-access-acm-data/add-permissions.png)

## <a name="assign-subscription-scope-access"></a>Tilldela åtkomst till omfånget Prenumeration

För åtkomst till en prenumeration krävs behörighet på minst nivån Cost Management-läsare (eller läsare). Du kan konfigurera behörigheter till prenumerationer i Azure-portalen. Du måste minst ha behörighet som administratör för användaråtkomst (eller ägare) för prenumerationen innan du kan ge åtkomst till andra. För Azure EA-konton måste du också ha aktiverat inställningen **Visa avgifter för AO** i EA-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **Alla tjänster** i sidolisten, sök efter _prenumerationer_ och välj **Prenumerationer**.
3. Välj din prenumeration.
4. Välj **Åtkomstkontroll (IAM)** i den vänstra rutan.
5. Välj **Lägg till**.
6. Under **Roll** väljer du **Cost Management-läsare**.
7. Under **Bevilja åtkomst till** väljer du **Användare, grupp eller program i Azure AD**.
8. Sök efter och välj användaren för att bevilja åtkomsten.
9. Välj **Spara**.

## <a name="assign-resource-group-scope-access"></a>Tilldela åtkomst till omfånget Resursgrupp

För åtkomst till en resursgrupp krävs behörighet på minst nivån Cost Management-läsare (eller läsare). Du kan konfigurera behörigheter till resursgrupper i Azure-portalen. Du måste minst ha behörighet som administratör för användaråtkomst (eller ägare) för resursgruppen innan du kan ge åtkomst till andra. För Azure EA-konton måste du också ha aktiverat inställningen **Visa avgifter för AO** i EA-portalen.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Välj **Alla tjänster** i sidolisten, sök efter _resursgrupper_ och välj **Resursgrupper**.
3. Välj din resursgrupp.
4. Välj **Åtkomstkontroll (IAM)** i den vänstra rutan.
5. Välj **Lägg till**.
6. Under **Roll** väljer du **Cost Management-läsare**.
7. Under **Bevilja åtkomst till** väljer du **Användare, grupp eller program i Azure AD**.
8. Sök efter och välj användaren för att bevilja åtkomsten.
9. Välj **Spara**.

## <a name="cross-tenant-authentication-issues"></a>Problem med autentisering mellan klientorganisationer

Azure Cost Management har för närvarande ett begränsat stöd för autentisering mellan klientorganisationer. När du försöker autentisera mellan klientorganisationer kan du ibland få felet **Åtkomst nekad** i kostnadsanalysen. Det här problemet kan inträffa om du konfigurerar rollbaserad åtkomstkontroll i Azure (Azure RBAC) till en annan klientorganisations prenumeration och sedan försöker visa kostnadsdata.

*Så här löser du problemet*: När du har konfigurerat Azure RBAC mellan klientorganisationer väntar du en timme. Försök sedan att visa kostnader i kostnadsanalysen eller ge Cost Management åtkomst till användarna i båda klientorganisationerna.  


## <a name="next-steps"></a>Nästa steg

- Om du inte redan har slutfört den första Cost Management-snabbstarten kan du gå till [Börja analysera kostnader](quick-acm-cost-analysis.md).
