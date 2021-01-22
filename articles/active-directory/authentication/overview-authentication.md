---
title: Översikt över Azure Active Directory autentisering
description: Lär dig mer om olika autentiseringsmetoder och säkerhetsfunktioner för användar inloggningar med Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae7ae239493c999d98100bf9dee4f4f0183addf2
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660821"
---
# <a name="what-is-azure-active-directory-authentication"></a>Vad är Azure Active Directory autentisering?

En av huvud funktionerna i en identitets plattform är att verifiera, eller *autentisera*, autentiseringsuppgifter när en användare loggar in på en enhet, ett program eller en tjänst. I Azure Active Directory (Azure AD) omfattar autentiseringen mer än bara verifieringen av ett användar namn och lösen ord. Azure AD-autentisering innehåller följande komponenter för att förbättra säkerheten och minska behovet av support support:

* Återställning av lösenord för självbetjäning
* Azure AD-multifaktorautentisering
* Hybrid integrering för att skriva lösen ords ändringar tillbaka till en lokal miljö
* Hybrid integrering som tillämpar principer för lösen ords skydd för en lokal miljö
* Lösenordsfri autentisering

Ta en titt på vår korta video för att lära dig mer om de här komponenterna för autentisering.

## <a name="improve-the-end-user-experience"></a>Förbättra slut användar upplevelsen

Azure AD hjälper till att skydda en användares identitet och förenkla inloggnings upplevelsen. Funktioner som självbetjäning för återställning av lösen ord gör att användare kan uppdatera eller ändra sina lösen ord med hjälp av en webbläsare från vilken enhet som helst. Den här funktionen är särskilt användbar när användaren har glömt sitt lösen ord eller om kontot är låst. Utan att behöva vänta på supportavdelningen eller administratören för att få support kan en användare avblockera sig själva och fortsätta att fungera.

Med Azure AD Multi-Factor Authentication kan användarna välja ytterligare en form av autentisering vid inloggning, till exempel ett telefonsamtal eller ett meddelande om mobilapp. Den här möjligheten minskar kravet på en enskild, fast form av sekundär autentisering som en maskinvaru-token. Om användaren inte har en form av ytterligare autentisering kan de välja en annan metod och fortsätta att fungera.

![Autentiseringsmetoder som används på inloggnings skärmen](media/concept-authentication-methods/overview-login.png)

Med lösen ords lös autentisering elimineras behovet av att användaren skapar och kommer ihåg ett säkert lösen ord. Funktioner som Windows Hello för företag eller FIDO2 säkerhets nycklar låter användarna logga in på en enhet eller ett program utan lösen ord. Den här möjligheten kan minska komplexiteten med att hantera lösen ord i olika miljöer.

## <a name="self-service-password-reset"></a>Återställning av lösenord för självbetjäning

Lösen ords återställning via självbetjäning ger användarna möjlighet att ändra eller återställa sitt lösen ord, utan administratörs-eller support inblandning. Om ett användar konto är låst eller om det glömmer sitt lösen ord, kan de följa prompter för att avblockera sig själva och komma tillbaka till arbetet. Detta minskar risken för support och produktivitets förlust när en användare inte kan logga in på sin enhet eller ett program.

Lösen ords återställning via självbetjäning fungerar i följande scenarier:

* **Lösen ords ändring –** när en användare känner till sitt lösen ord men vill ändra det till något nytt.
* **Lösen ords återställning –** när en användare inte kan logga in, t. ex. när de glömmer bort lösen ordet, och vill återställa sina lösen ord.
* **Upplåsning av konto –** när en användare inte kan logga in eftersom deras konto är utelåst och vill låsa upp sitt konto.

När en användare uppdaterar eller återställer sitt lösen ord med hjälp av lösen ords återställning via självbetjäning, kan lösen ordet också skrivas tillbaka till en lokal Active Directory miljö. Tillbakaskrivning av lösen ord ser till att en användare omedelbart kan använda sina uppdaterade autentiseringsuppgifter med lokala enheter och program.

## <a name="azure-ad-multi-factor-authentication"></a>Azure AD-multifaktorautentisering

Multifaktorautentisering är en process där en användare under inloggningen uppmanas att ange ytterligare en typ av identifiering, till exempel att ange en kod på sin mobiltelefon eller ange ett fingeravtryck.

Om du bara använder ett lösen ord för att autentisera en användare lämnar den osäker vektor för angrepp. Om lösen ordet är svagt eller har exponerats någon annan stans, är det verkligen användaren som loggar in med användar namnet och lösen ordet, eller är det en angripare? När du behöver en andra form av autentisering, ökar säkerheten eftersom den här ytterligare faktorn inte är något som är lätt för en angripare att hämta eller duplicera.

![Konceptuell bild av de olika formerna av Multi-Factor Authentication](./media/concept-mfa-howitworks/methods.png)

Azure AD Multi-Factor Authentication fungerar genom att kräva två eller fler av följande autentiseringsmetoder:

* Något du känner, vanligt vis ett lösen ord.
* Något du har, till exempel en betrodd enhet som inte är enkel att duplicera, till exempel en telefon eller maskin varu nyckel.
* Något du är-biometrik som ett finger avtryck eller en ansikts skanning.

Användare kan registrera sig själva för lösen ords återställning via självbetjäning och Azure AD Multi-Factor Authentication i ett steg för att förenkla den här upplevelsen. Administratörer kan definiera vilka former av sekundär autentisering som kan användas. Azure AD Multi-Factor Authentication kan också krävas när användare utför en lösen ords återställning via självbetjäning för att ytterligare skydda processen.

## <a name="password-protection"></a>Lösenordsskydd

Som standard blockerar Azure AD svaga lösen ord som *Password1*. En global lista över blockerade lösen ord uppdateras automatiskt och tillämpas som innehåller kända svaga lösen ord. Om en Azure AD-användare försöker ange sitt lösen ord till något av dessa svaga lösen ord får de ett meddelande om att välja ett säkrare lösen ord.

För att öka säkerheten kan du definiera anpassade lösen ords skydds principer. Dessa principer kan använda filter för att blockera alla varianter av ett lösen ord som innehåller ett namn som *contoso* eller en plats som *London*, till exempel.

För hybrid säkerhet kan du integrera Azure AD Password Protection med en lokal Active Directory miljö. En komponent som installeras i lokal-miljön tar emot den globala listan över förbjudna lösen ord och anpassade lösen ords skydds principer från Azure AD, och domän kontrol Lanterna använder dem för att bearbeta lösen ords ändrings händelser. Den här hybrid metoden ser till att det oavsett hur eller var en användare ändrar sina autentiseringsuppgifter, att du tvingar användningen av starka lösen ord.

## <a name="passwordless-authentication"></a>Lösenordsfri autentisering

Slut målet för många miljöer är att ta bort användningen av lösen ord som en del av inloggnings händelser. Funktioner som Azure Password Protection eller Azure AD Multi-Factor Authentication hjälpa till att förbättra säkerheten, men ett användar namn och lösen ord är inte en svag form av autentisering som kan exponeras eller resultera i brutet angrepp.

![Säkerhet och bekvämlighet med autentiseringsprocessen som leder till lösen ords lös](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

När du loggar in med en metod för lösen ords skydd tillhandahålls autentiseringsuppgifterna med metoder som biometrik med Windows Hello för företag, eller en säkerhets nyckel för FIDO2. Dessa autentiseringsmetoder kan inte enkelt dupliceras av en angripare.

Azure AD tillhandahåller olika sätt att autentisera med hjälp av lösen ords lös metoder för att förenkla inloggnings upplevelsen för användare och minska risken för attacker.

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång finns i [självstudien för självbetjäning för återställning av lösen ord (SSPR)][tutorial-sspr] och [Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Mer information om koncept för lösen ords återställning via självbetjäning finns i [så här fungerar lösen ords återställning i Azure AD][concept-sspr].

Mer information om Multi-Factor Authentication-koncept finns i [hur Azure AD Multi-Factor Authentication fungerar][concept-mfa].

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
