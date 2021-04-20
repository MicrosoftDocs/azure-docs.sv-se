---
title: Felsöka åtkomst till Azure EA-portalen
description: Den här artikeln beskriver några vanliga problem som kan uppstå med ett Azure Enterprise-avtal (EA) i Azure EA-portalen.
author: bandersmsft
ms.author: banders
ms.date: 03/26/2021
ms.topic: troubleshooting
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 852fb85607318772870a8b6826c934997b84ff6d
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726474"
---
# <a name="troubleshoot-azure-ea-portal-access"></a>Felsöka åtkomst till Azure EA-portalen

Den här artikeln beskriver några vanliga problem som kan uppstå med ett Azure Enterprise-avtal (EA). Azure EA-portalen används för att hantera användare av och kostnader för Enterprise-avtal. Dessa problem kan uppstå när du konfigurerar eller uppdaterar åtkomst till Azure EA-portalen.

## <a name="issues-adding-an-admin-to-an-enrollment"></a>Problem med att lägga till en administratör till en registrering

Det finns olika typer av autentiseringsnivåer för företagsregistreringar. När autentiseringsnivåer tillämpas felaktigt kan det uppstå problem när du försöker logga in på Azure EA-portalen.

Du kan använda Azure EA-portalen för att ge åtkomst till användare med olika autentiseringsnivåer. En företagsadministratör kan uppdatera autentiseringsnivån för att uppfylla organisationens säkerhetskrav.

### <a name="authentication-level-types"></a>Typer av autentiseringsnivå

- Endast Microsoft-konto – för organisationer som vill använda, skapa och hantera användare via Microsoft-konton.
- Arbets- eller skolkonto – för organisationer som har konfigurerat Active Directory med federation till molnet och där alla konton finns i en enda klientorganisation.
- Arbets- eller skolkonto mellan klientorganisationer – för organisationer som har konfigurerat Active Directory med federation till molnet och kommer att ha konton i flera klientorganisationer.
- Blandat konto – gör att du kan lägga till användare med Microsoft-konto och/eller ett arbets- eller skolkonto.

Det första arbets- eller skolkonto som läggs till i registreringen blir _standarddomänen_. För att lägga till ett arbets- eller skolkonto med en annan klientorganisation måste du ändra autentiseringsnivån under registreringen till autentisering mellan klientorganisationer.

Så här uppdaterar du autentiseringsnivån:

1. Logga in på Azure EA-portalen som företagsadministratör.
2. Klicka på **Hantera** i det vänstra navigeringsfönstret.
3. Klicka på fliken **Registrering**.
4. Under **Registreringsinformation** väljer du **Autentiseringsnivå**.
5. Klicka på pennsymbolen.
6. Klicka på **Spara**.

![Exempel som visar autentiseringsnivåer ](./media/ea-portal-troubleshoot/create-ea-authentication-level-types.png)

Microsoft-konton måste ha ett associerat ID som skapas på [https://signup.live.com](https://signup.live.com/).

Arbets- eller skolkonton är tillgängliga för organisationer som har konfigurerat Active Directory med federation och där alla konton finns i en enda klientorganisation. Användare kan läggas till med autentisering av extern användare i arbete eller skola om företagets interna Active Directory är federerad.

Om din organisation inte använder Active Directory-federation kan du inte använda din e-postadress för arbete eller skola. I stället registrerar eller skapar du en ny e-postadress och registrerar den som ett Microsoft-konto.

## <a name="unable-to-access-the-azure-ea-portal"></a>Det går inte att komma åt Azure EA-portalen

Om du får ett felmeddelande när du försöker logga in på Azure EA-portalen använder du följande felsökningssteg:

- Se till att du använder rätt URL för Azure EA-portalen, som är https://ea.azure.com.
- Ta reda på om din åtkomst till Azure EA-portalen lades till som ett arbets- eller skolkonto eller som ett Microsoft-konto.
  - Om du använder ditt arbetskonto anger du din e-postadress och ditt lösenord för arbete. Ditt arbetslösenord tillhandahålls av din organisation. Du kan höra med din IT-avdelning om hur du återställer lösenordet om det är problem med det.
  - Om du använder ett Microsoft-konto anger du e-postadressen och lösenordet för ditt Microsoft-konto. Om du har glömt lösenordet till Microsoft-kontot kan du återställa det på [https://account.live.com/password/reset](https://account.live.com/password/reset).
- Använd en privat eller inkognito webbläsarsession för att logga in så att inga cookies eller cachelagrad information från tidigare eller befintliga sessioner sparas. Rensa webbläsarens cache och använd ett privat eller inkognito fönster för att öppna https://ea.azure.com.
- Om du ser felet _Ogiltig användare_ när du använder ett Microsoft-konto kan det bero på att du har flera Microsoft-konton. Det som du försöker logga in med är inte den primära e-postadressen.
Eller om du får felet _Ogiltig användare_ så kan det bero på att fel kontotyp användes när användaren lades till i registreringen. Det kan till exempel ha varit ett arbets- eller skolkonto i stället för ett Microsoft-konto. I det här exemplet bör du be en annan EA-administratör att lägga till rätt konto, eller så behöver du kontakta [supporten](https://support.microsoft.com/supportforbusiness/productselection?sapId=cf791efa-485b-95a3-6fad-3daf9cd4027c).
  - Om du behöver kontrollera det primära aliaset går du till [https://account.live.com](https://account.live.com). Klicka sedan på **Din information** och sedan på **Hantera hur du loggar in på Microsoft**. Följ anvisningarna för att verifiera en alternativ e-postadress och hämta en kod för att få åtkomst till känslig information. Ange säkerhetskoden. Välj **Konfigurera senare** om du inte vill konfigurera tvåfaktorautentisering.
  - Du ser sidan **Hantera hur du loggar in på Microsoft** där du kan visa dina kontoalias. Kontrollera att det primära aliaset är det som du använder för att logga in på Azure-EA-portalen. Om det inte är det kan du göra aliaset till ditt primära alias. Eller så kan du använda det primära aliaset för Azure EA-portalen i stället.

## <a name="next-steps"></a>Nästa steg

- Administratörer i Azure EA-portalen bör läsa [Administration i Azure EA-portalen](ea-portal-administration.md). Där finns mer information om vanliga administrativa uppgifter.
- Läs vanliga [Cost Management + Billing och](../cost-management-billing-faq.yml) svar om vanliga problem med Azure EA-aktivering.
