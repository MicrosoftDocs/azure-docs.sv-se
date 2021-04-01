---
title: Fakturerings modell för Azure Active Directory B2C
description: Lär dig mer om fakturerings modellen för Azure AD B2C's Monthly Active Users (MAU), så här länkar du en Azure AD B2C-klient till en Azure-prenumeration och hur du väljer rätt pris nivå för Premium nivån.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: a54ed9dc6557d9b613485bf28e74af0c59fc9e5e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99225216"
---
# <a name="billing-model-for-azure-active-directory-b2c"></a>Fakturerings modell för Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) Priset baseras på månatliga aktiva användare (MAU), vilket är antalet unika användare med autentiserings aktivitet inom en kalender månad. Den här fakturerings modellen gäller för både Azure AD B2C klienter och [Azure AD gäst användar samarbete (B2B)](../active-directory/external-identities/external-identities-pricing.md). MAU-faktureringen hjälper dig att minska kostnaderna genom att erbjuda en kostnads fri nivå och flexibel, förutsägbar prissättning. I den här artikeln får du lära dig om MAU-fakturering, länkar Azure AD B2C-klienter till en prenumeration och hur du ändrar pris nivån.

> [!IMPORTANT]
> Den här artikeln innehåller ingen pris information. Den senaste informationen om fakturering och prissättning för användning finns [Azure Active Directory B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Se även [Azure AD B2C regions tillgänglighet och data placering](data-residency.md) för information om var Azure AD B2C tjänsten är tillgänglig och var användar data lagras.

## <a name="what-do-i-need-to-do"></a>Vad behöver jag göra?

För att kunna dra nytta av MAU-fakturering måste din Azure AD B2C klient vara länkad till en Azure-prenumeration. Du kan också behöva byta Azure AD B2C-klient till en annan pris nivå om du vill använda Azure AD B2C Premium P2-funktioner, t. ex. riskfylld villkorlig åtkomst.

|Om din klient organisation är:  |Du måste:  |
|---------|---------|
| En Azure AD B2C klient organisation har redan fakturerats per MAU-basis     | Gör ingenting. När användare autentiserar till din Azure AD B2C-klient debiteras du automatiskt med hjälp av den MAU fakturerings modellen.        |
| En Azure AD B2C klient organisation som ännu inte har länkats till en prenumeration     |  [Länka Azure AD B2C-klienten till en prenumeration](#link-an-azure-ad-b2c-tenant-to-a-subscription) för att aktivera Mau fakturering.     |
| En Azure AD B2C klient som var länkad till en prenumeration före den 1 november 2019    | [Växla till Mau fakturering (rekommenderas)](#switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants)eller Fortsätt till fakturerings modellen per autentisering.     |
| En Azure AD B2C klient och du vill använda Premium funktioner (t. ex. riskfylld villkorlig åtkomst)    | [Ändra till en pris nivå för Azure AD](#change-your-azure-ad-pricing-tier) som stöder de funktioner som du vill använda.        |
|  |  |

## <a name="about-the-monthly-active-users-mau-billing-model"></a>Om fakturerings modellen för månatliga aktiva användare (MAU)

MAU-faktureringen pågick i kraft för Azure AD B2C-klienter den **1 November 2019**. Alla Azure AD B2C klienter som du har skapat och kopplat till en prenumeration på eller efter det datumet har fakturerats per MAU-basis. Om du har en Azure AD B2C klient som inte har länkats till en prenumeration måste du göra det nu. Om du har en befintlig Azure AD B2C klient som var länkad till en prenumeration före den 1 november 2019 rekommenderar vi att du uppgraderar till fakturerings modellen för månatliga aktiva användare (MAU), eller så kan du hålla koll på fakturerings modellen per autentisering.
  
Din Azure AD B2C klient måste också vara länkad till lämplig pris nivå för Azure baserat på de funktioner som du vill använda. Premium-funktioner kräver Azure AD B2C [Premium P1-eller P2-prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/). Du kan behöva uppgradera pris nivån när du använder nya funktioner. Till exempel måste du välja Azure AD B2C Premium P2 pris nivå för din klient organisation.

## <a name="link-an-azure-ad-b2c-tenant-to-a-subscription"></a>Länka en Azure AD B2C-klient till en prenumeration

Användnings kostnader för Azure Active Directory B2C (Azure AD B2C) faktureras till en Azure-prenumeration. Du måste uttryckligen länka en Azure AD B2C-klient till en Azure-prenumeration genom att skapa en Azure AD B2C- *resurs* i Azure-prenumerationen. Flera Azure AD B2C-resurser kan skapas i en enda Azure-prenumeration, tillsammans med andra Azure-resurser som virtuella datorer, lagrings konton och Logic Apps. Du kan se alla resurser i en prenumeration genom att gå till den Azure Active Directory (Azure AD) som prenumerationen är associerad med.

En prenumeration som är länkad till en Azure AD B2C-klient kan användas för fakturering av Azure AD B2C användning eller andra Azure-resurser, inklusive ytterligare Azure AD B2C resurser. Det går inte att använda för att lägga till andra Azure licensbaserade tjänster eller Office 365-licenser inom Azure AD B2C klient organisationen.

### <a name="prerequisites"></a>Förutsättningar

* [Azure-prenumeration](https://azure.microsoft.com/free/)
* [Azure AD B2C klient](tutorial-create-tenant.md) som du vill länka till en prenumeration
  * Du måste vara klient administratör
  * Klienten får inte redan vara länkad till en prenumeration

### <a name="create-the-link"></a>Skapa länken

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller den Azure-prenumeration som du vill använda (*inte* den katalog som innehåller Azure AD B2C klienten).
3. Välj **skapa en resurs**, ange `Active Directory B2C` i fältet **Sök i Marketplace** och välj sedan **Azure Active Directory B2C**.
4. Välj **Skapa**.
5. Välj **Länka en befintlig Azure AD B2C-klient till min Azure-prenumeration**.
6. Välj en **Azure AD B2C klient** i list rutan. Endast innehavare som du är global administratör för och som inte redan är länkade till en prenumeration visas. Fältet **Azure AD B2C resurs namn** fylls i med domän namnet för den Azure AD B2C klient som du väljer.
7. Välj en aktiv Azure- **prenumeration** som du är administratör för.
8. Under **resurs grupp** väljer du **Skapa ny** och anger sedan **resurs gruppens plats**. Resurs grupps inställningarna här påverkar inte Azure AD B2C klient plats, prestanda eller fakturerings status.
9. Välj **Skapa**.

    ![Sidan Azure AD B2C resurs skapande i Azure Portal](./media/billing/portal-01-create-b2c-resource-page.png)

När du har slutfört de här stegen för en Azure AD B2C klient faktureras din Azure-prenumeration baserat på din Azure Direct-eller Enterprise-avtal-information, om så är tillämpligt.

## <a name="change-your-azure-ad-pricing-tier"></a>Ändra din pris nivå för Azure AD

En klient måste vara länkad till lämplig pris nivå för Azure baserat på de funktioner som du vill använda med din Azure AD B2C klient. Premium-funktioner kräver Azure AD B2C Premium P1 eller P2, enligt beskrivningen i [Azure Active Directory B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/). I vissa fall måste du uppgradera pris nivån när du använder nya funktioner. Om du till exempel vill använda identitets skydd, riskfylld villkorlig åtkomst och alla framtida Premium P2-funktioner med Azure AD B2C måste du välja pris nivån Azure AD B2C Premium P2 för din klient.

Följ dessa steg om du vill ändra pris nivån.

1. Logga in på Azure-portalen.

2. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller Azure-prenumerationen som din Azure B2C-klient är länkad till (Välj *inte* själva Azure AD B2C klienten).

3. I rutan Sök högst upp i portalen anger du namnet på din Azure AD B2C klient. Välj sedan klienten i Sök resultaten under **resurser**.

4. På sidan resurs **Översikt** väljer du **ändra** under **pris nivå**.

   ![Ändra prisnivå](media/billing/change-pricing-tier.png)
 
5. Välj den pris nivå som innehåller de funktioner som du vill aktivera.

   ![Välj pris nivå](media/billing/select-tier.png)

## <a name="switch-to-mau-billing-pre-november-2019-azure-ad-b2c-tenants"></a>Växla till MAU fakturering (före november 2019 Azure AD B2C klienter)

Om du har länkat Azure AD B2C-klienten till en prenumeration före den **1 November 2019** används den tidigare fakturerings modellen per autentisering. Vi rekommenderar att du uppgraderar till fakturerings modellen för månatliga aktiva användare (MAU). Fakturerings alternativ konfigureras i din Azure AD B2C-resurs.

MAU-faktureringen växlar till månatliga aktiva användare () kan inte **ångras**. När du har konverterat en Azure AD B2C resurs till den MAU fakturerings modellen kan du inte återställa resursen till fakturerings modellen per autentisering.

Så här gör du för att växla till MAU fakturering för en befintlig Azure AD B2C-resurs:

1. Logga in på [Azure Portal](https://portal.azure.com) som prenumerations ägare med administrativ åtkomst till Azure AD B2C resursen.

2. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den Azure AD B2C katalog som du vill uppgradera till Mau fakturering.<br/>

    ![Katalog-och prenumerations filter i Azure Portal](./media/billing/portal-mau-01-select-b2c-directory.png)

3. På den vänstra menyn väljer du **Azure AD B2C**. Eller Välj **alla tjänster** och Sök efter och välj **Azure AD B2C**.

4. På sidan **Översikt** i Azure AD B2C klient organisationen väljer du länken under **resurs namn**. Du dirigeras till Azure AD B2C resursen i din Azure AD-klient.<br/>

    ![Azure AD B2C resurs länk markerad i Azure Portal](./media/billing/portal-mau-02-b2c-resource-link.png)

5. På sidan **Översikt** i Azure AD B2C resurs, under **fakturerbara enheter**, väljer du länken **per autentisering (ändra till Mau)** .<br/>

    ![Ändra till MAU-länken är markerad i Azure Portal](./media/billing/portal-mau-03-change-to-mau-link.png)

6. Välj **Bekräfta** för att slutföra uppgraderingen till Mau-fakturering.<br/>

    ![MAU fakturerings bekräftelse dialog ruta i Azure Portal](./media/billing/portal-mau-04-confirm-change-to-mau.png)


### <a name="what-to-expect-when-you-transition-to-mau-billing-from-per-authentication-billing"></a>Vad ska förväntas när du övergår till MAU debitering från fakturering per autentisering

MAU-baserad avläsning aktive ras så snart du, prenumerationen/resurs ägaren, bekräftar ändringen. Din månads faktura visar de enheter med autentisering som faktureras tills ändringen och nya enheter av MAU börjar med ändringen.

Användare är inte dubbelt räknade under över gångs perioden. Unika aktiva användare som autentiseras innan ändringen debiteras per autentisering under en kalender månad. Samma användare ingår inte i MAU-beräkningen för resten av prenumerationens fakturerings period. Exempel:

* Contoso B2C-klienten har 1 000 användare. 250 användare är aktiva under en månad. Prenumerations administratören ändras från per autentisering till månatliga aktiva användare (MAU) den 10: a i månaden.
* Faktureringen för 1 – 10 – 10 faktureras med modellen per autentisering.
  * Om 100 användare loggar in under den här perioden (1st-tionde) är dessa användare märkta som *betalda för månaden*.
* Fakturering från den tionde (den effektiva tiden för över gång) debiteras enligt MAU-priset.
  * Om ytterligare 150 användare loggar in under denna tid (10 – 30) debiteras bara ytterligare 150.
  * Den fortsatta aktiviteten hos de första 100 användarna påverkar inte faktureringen för resten av kalender månaden.

Under över gångens fakturerings period kommer prenumerations ägaren troligen att se poster för båda metoderna (per autentisering och per-MAU) i fakturerings instruktionen för Azure-prenumerationen:

* En post för användningen fram till datum/tid för ändring som reflekterar per autentisering.
* En post för användningen efter den ändring som visar månatliga aktiva användare (MAU).

Den senaste informationen om fakturering och priser för användning för Azure AD B2C finns i [Azure Active Directory B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Hantera dina Azure AD B2C klient resurser

När du har skapat Azure AD B2C resursen i en Azure-prenumeration bör du se en ny resurs av typen "B2C Tenant" med dina andra Azure-resurser.

Du kan använda den här resursen för att:

* Gå till prenumerationen för att granska fakturerings information
* Hämta Azure AD B2C innehavarens klient-ID i GUID-format
* Gå till din Azure AD B2C-klient
* Skicka en support förfrågan
* Flytta din Azure AD B2C klient resurs till en annan Azure-prenumeration eller resurs grupp

### <a name="regional-restrictions"></a>Regionala begränsningar

Om du har fastställt regionala begränsningar för att skapa Azure-resurser i din prenumeration kan denna begränsning förhindra att du skapar den Azure AD B2C resursen.

Minska det här problemet genom att minska dina regionala begränsningar.

## <a name="azure-cloud-solution-providers-csp-subscriptions"></a>Prenumerationer på Azure Cloud solution providers (CSP)

Azure Cloud solution providers (CSP)-prenumerationer stöds i Azure AD B2C. Funktionerna är tillgängliga med API: er eller Azure Portal för Azure AD B2C och för alla Azure-resurser. Administratörer av CSP-prenumerationen kan länka, flytta och ta bort relationer med Azure AD B2C som gjorts med andra Azure-resurser.

Hanteringen av Azure AD B2C med hjälp av rollbaserad åtkomst kontroll påverkas inte av associationen mellan Azure AD B2C klienten och en Azure CSP-prenumeration. Rollbaserad åtkomst kontroll uppnås med hjälp av klientbaserade roller, inte prenumerations-baserade roller.

## <a name="change-the-azure-ad-b2c-tenant-billing-subscription"></a>Ändra Azure AD B2C klient fakturerings prenumeration

### <a name="move-using-azure-resource-manager"></a>Flytta med Azure Resource Manager

Azure AD B2C klienter kan flyttas till en annan prenumeration med Azure Resource Manager om käll-och mål prenumerationer finns inom samma Azure Active Directory klient organisation.

Information om hur du flyttar Azure-resurser som din Azure AD B2C-klient till en annan prenumeration finns i [Flytta resurser till en ny resurs grupp eller prenumeration](../azure-resource-manager/management/move-resource-group-and-subscription.md).

Innan du börjar flytta måste du läsa hela artikeln för att kunna förstå begränsningarna och kraven för en sådan flytt. Förutom instruktioner för att flytta resurser innehåller den viktig information, till exempel en check lista för förflyttning och hur du verifierar flytt åtgärden.

### <a name="move-by-unlinking-and-relinking"></a>Flytta genom att ta bort länkar och länka om

Om käll-och mål prenumerationerna är kopplade till olika Azure Active Directory klienter kan du inte utföra flytten via Azure Resource Manager enligt beskrivningen ovan. Du kan dock fortfarande uppnå samma resultat genom att ta bort kopplingen mellan Azure AD B2C klient organisationen och käll prenumerationen och länka om den till mål prenumerationen. Den här metoden är säker eftersom det enda objekt du tar bort är *fakturerings länken*, inte själva Azure AD B2C själva klienten. Ingen av användarna, apparna, användar flödena, etc. kommer att påverkas.

1. I själva katalogen Azure AD B2C kan du [bjuda in en gäst användare](user-overview.md#guest-user) från Azure AD-klienten (det som är mål Azure-prenumerationen länkad till) och se till att användaren har rollen **Global administratör** i Azure AD B2C.
1. Gå till *Azure-resursen* som representerar Azure AD B2C i din käll Azure-prenumeration enligt beskrivningen i avsnittet [Hantera Azure AD B2C klient resurser](#manage-your-azure-ad-b2c-tenant-resources) ovan. Växla inte till den faktiska Azure AD B2C-klienten.
1. Välj knappen **ta bort** på sidan **Översikt** . Detta tar *inte* bort relaterade Azure AD B2C klientens användare eller program. Den tar bara bort fakturerings länken från käll prenumerationen.
1. Logga in på Azure Portal med det användar konto som har lagts till som administratör i Azure AD B2C i steg 1. Gå sedan till mål Azure-prenumerationen, som är länkad till mål Azure Active Directory klient organisationen. 
1. Återupprätta fakturerings länken i mål prenumerationen genom att följa anvisningarna för att [Skapa länken](#create-the-link) ovan.
1. Din Azure AD B2C-resurs har nu flyttats till mål Azure-prenumerationen (länkad till mål Azure Active Directory) och debiteras via den här prenumerationen från nu.

## <a name="next-steps"></a>Nästa steg

Den senaste pris informationen finns i [Azure Active Directory B2C prissättning](https://azure.microsoft.com/pricing/details/active-directory-b2c/).