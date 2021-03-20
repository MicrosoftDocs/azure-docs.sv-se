---
title: Hantering av användar etablering för företags program i Azure Active Directory
description: Lär dig hur du hanterar användar konto etablering för företags program med hjälp av Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5dceeb11ed9a4d6af88650a6146f58db412748d9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579424"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>Hantera användar konto etablering för företags program i Azure Portal

I den här artikeln beskrivs de allmänna stegen för att hantera automatisk etablering av användar konton och avetablering för program som stöder det. *Etablering av användar konto* är en handling som används för att skapa, uppdatera och/eller inaktivera användar konto poster i ett programs lokala användar profil arkiv. De flesta moln-och SaaS-program lagrar användarens roll och behörigheter i användarens egna lokala användar profil Arkiv och förekomst av en sådan användar post i användarens lokala arkiv *krävs* för enkel inloggning och åtkomst till arbete. Läs mer om automatisk etablering av användar konton i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](user-provisioning.md).

> [!IMPORTANT]
> Azure Active Directory (Azure AD) har ett galleri som innehåller tusentals förintegrerade program som har Aktiver ATS för automatisk etablering med Azure AD. Du bör börja med att hitta självstudien för etablerings installation som är specifik för ditt program i [listan med självstudier om hur du integrerar SaaS-appar med Azure Active Directory](../saas-apps/tutorial-list.md). Du kommer troligen att hitta steg-för-steg-anvisningar om hur du konfigurerar både appen och Azure AD för att skapa en etablerings anslutning.

## <a name="finding-your-apps-in-the-portal"></a>Hitta dina appar i portalen

Använd Azure Active Directory Portal för att visa och hantera alla program som är konfigurerade för enkel inloggning i en katalog. Företags program är appar som distribueras och används i din organisation. Följ dessa steg om du vill visa och hantera dina företags program:

1. Öppna [Azure Active Directory-portalen](https://aad.portal.azure.com).
1. Välj **företags program** i det vänstra fönstret. En lista över alla konfigurerade appar visas, inklusive appar som har lagts till från galleriet.
1. Välj en app för att läsa in resurs fönstret där du kan visa rapporter och hantera inställningar för appar.
1. Välj **etablering** för att hantera inställningar för etablering av användar konto för den valda appen.

   ![Etablerings skärmen för att hantera inställningar för användar konto etablering](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>Etablerings lägen

**Etablerings** fönstret börjar med menyn **läge** , som visar de etablerings lägen som stöds för ett företags program och låter dig konfigurera dem. De tillgängliga alternativen är:

* **Automatiskt** – det här alternativet visas om Azure AD stöder automatisk API-baserad etablering eller inaktive ring av användar konton i det här programmet. Välj det här läget för att visa ett gränssnitt som hjälper administratörerna:

  * Konfigurera Azure AD för att ansluta till programmets API för användar hantering
  * Skapa konto mappningar och arbets flöden som definierar hur användar konto data ska flöda mellan Azure AD och appen
  * Hantera Azure AD Provisioning-tjänsten

* **Manuellt** – det här alternativet visas om Azure AD inte stöder automatisk etablering av användar konton i det här programmet. I det här fallet måste användar konto poster som lagras i programmet hanteras med hjälp av en extern process, baserat på användar hanterings-och etablerings funktionerna i programmet (som kan inkludera SAML just-in-Time-etablering).

## <a name="configuring-automatic-user-account-provisioning"></a>Konfigurera automatisk etablering av användar konto

Välj det **automatiska** alternativet om du vill ange inställningar för administratörs behörighet, mappningar, starta och stoppa och synkronisering.

### <a name="admin-credentials"></a>Admin-autentiseringsuppgifter

Expandera **administratörsautentiseringsuppgifter** för att ange de autentiseringsuppgifter som krävs för att Azure AD ska kunna ansluta till programmets användar hanterings-API. Vilka indatatyper som krävs varierar beroende på programmet. Information om vilka typer av autentiseringsuppgifter som krävs för specifika program finns i [konfigurations guiden för det specifika programmet](user-provisioning.md).

Välj **test anslutning** för att testa autentiseringsuppgifterna genom att låta Azure AD försöka ansluta till appens etablerings app med de angivna autentiseringsuppgifterna.

### <a name="mappings"></a>Mappningar

Expandera **mappningar** för att visa och redigera användarattribut som flödar mellan Azure AD och mål programmet när användar konton har skapats eller uppdaterats.

Det finns en förkonfigurerad uppsättning mappningar mellan användar objekt i Azure AD och varje SaaS-Apps användar objekt. Vissa appar hanterar även grupp objekt. Välj en mappning i tabellen för att öppna mappnings redigeraren, där du kan visa och anpassa dem.

Anpassningar som stöds är:

* Aktivera och inaktivera mappningar för särskilda objekt, till exempel Azure AD-användarobjektet till SaaS-appens användar objekt.
* Redigera attributen som flödar från Azure AD-användarobjektet till appens User-objekt. Mer information om mappning av attribut finns i [förstå attribut mappnings typer](customize-application-attributes.md#understanding-attribute-mapping-types).
* Filtrera de etablerings åtgärder som Azure AD kör i mål programmet. I stället för att låta Azure AD synkronisera objekt fullständigt kan du begränsa åtgärderna som körs.

  Om du till exempel bara väljer **Uppdatera** och Azure AD uppdateras bara befintliga användar konton i ett program, men inga nya skapas. Välj endast **skapa** och Azure skapar bara nya användar konton, men uppdatera inte befintliga. Med den här funktionen kan administratörer skapa olika mappningar för skapande av konton och uppdatera arbets flöden.

* Lägger till en ny attributmappning. Välj **Lägg till ny mappning** längst ned i fönstret **Mappning av attribut** . Fyll i formuläret **Redigera attribut** och välj **OK** för att lägga till den nya mappningen i listan.

### <a name="settings"></a>Inställningar

Expandera **inställningarna** för att ange en e-postadress för att ta emot meddelanden och om du vill ta emot aviseringar om fel. Du kan också välja omfånget för de användare som ska synkroniseras. Du kan välja att synkronisera alla användare och grupper eller bara de som har tilldelats.

### <a name="provisioning-status"></a>Etablerings status 

Om etableringen aktive ras för första gången för ett program aktiverar du tjänsten genom att ändra **etablerings statusen** till **på**. Den här ändringen gör att Azure AD Provisioning-tjänsten kör en första cykel. Den läser de användare som tilldelats i avsnittet **användare och grupper** , frågar mål programmet för dem och kör sedan de etablerings åtgärder som definierats i avsnittet Azure AD- **mappningar** . Under den här processen lagrar etablerings tjänsten cachelagrade data om vilka användar konton den hanterar, så icke-hanterade konton i mål programmen som aldrig fanns i omfattningen för tilldelningen påverkas inte av inetablerings åtgärder. Efter den första cykeln synkroniserar etablerings tjänsten automatiskt användar-och grupp objekt i ett intervall om 40 minuter.

Ändra **etablerings statusen** till **av**  för att pausa etablerings tjänsten. I det här läget skapar inte Azure, uppdaterar eller tar bort användar-eller grupp objekt i appen. Ändra statusen tillbaka till **på** och tjänsten hämtar var den slutade.
