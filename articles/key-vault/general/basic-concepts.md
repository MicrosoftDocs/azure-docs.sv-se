---
title: Vad är Azure Key Vault? | Microsoft Docs
description: Lär dig Azure Key Vault skyddar kryptografiska nycklar och hemligheter som används av molnprogram och molntjänster.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 863f98e643a7978856c03f5efe95736e6787f977
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814414"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault grundläggande begrepp

Azure Key Vault är en molntjänst för säker lagring och åtkomst till hemligheter. En hemlighet är allt som du vill kontrollera åtkomsten till, till exempel API-nycklar, lösenord, certifikat eller kryptografiska nycklar. Key Vault-tjänsten stöder två typer av containrar: valv och HSM-pooler (Managed Hardware Security Module). Valv stöder lagring av programvara och HSM-säkerhetskopierade nycklar, hemligheter och certifikat. Hanterade HSM-pooler stöder endast HSM-säkerhetskopierade nycklar. Se [Azure Key Vault REST API översikt](about-keys-secrets-certificates.md) för fullständig information.

Här är några andra viktiga termer:

- **Klientorganisation**: en klientorganisation är den organisation som äger och hanterar en specifik instans av Microsoft-molntjänster. Den används oftast för att referera till azure- och Microsoft 365 tjänster för en organisation.

- **Valvägare**: valvägaren kan skapa ett nyckelvalv och få fullständig åtkomst till och kontroll över det. Valvägaren kan även konfigurera granskning för att logga vem som använder hemligheter och nycklar. Administratörer kan styra nyckelns livscykel. De kan distribuera en ny version av nyckeln, säkerhetskopiera den och utföra andra relaterade uppgifter.

- **Valvkonsument**: valvkonsumenten kan utföra åtgärder på tillgångarna i nyckelvalvet när valvägaren ger konsumenten åtkomst. Vilka åtgärder som är tillgängliga beror på vilka behörigheter som beviljats.

- **Hanterade HSM-administratörer:** Användare som har tilldelats rollen Administratör har fullständig kontroll över en hanterad HSM-pool. De kan skapa fler rolltilldelningar för att delegera kontrollerad åtkomst till andra användare.

- **Managed HSM Crypto Officer/User:** Inbyggda roller som vanligtvis tilldelas till användare eller tjänstens huvudnamn som utför kryptografiska åtgärder med hjälp av nycklar i Managed HSM. Crypto-användare kan skapa nya nycklar, men kan inte ta bort nycklar.

- **Kryptering för hanterad HSM-krypteringstjänst:** Inbyggd roll som vanligtvis tilldelas till en tjänstkontos hanterade tjänstidentitet (t.ex. lagringskonto) för kryptering av vilodata med kund hanterad nyckel.

- **Resurs**: en resurs är ett hanterbart objekt som är tillgängligt via Azure. Vanliga exempel är virtuell dator, lagringskonto, webbapp, databas och virtuellt nätverk. Det finns många fler.

- **Resursgrupp**: resursgruppen är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som passar din organisation bäst.

- **Säkerhetsobjekt:** Ett azure-säkerhetsobjekt är en säkerhetsidentitet som användarskapade appar, tjänster och automatiseringsverktyg använder för att få åtkomst till specifika Azure-resurser. Se det som en "användaridentitet" (användarnamn och lösenord eller certifikat) med en viss roll och välkontrollerade behörigheter. Ett säkerhetsobjekt behöver bara göra vissa saker, till skillnad från en allmän användaridentitet. Det förbättrar säkerheten om du endast ger den lägsta behörighetsnivå som krävs för att utföra dess hanteringsuppgifter. Ett säkerhetsobjekt som används med ett program eller en tjänst kallas specifikt för **tjänstens huvudnamn.**

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Azure AD är Active Directory-tjänsten för en klientorganisation. Varje katalog har en eller flera domäner. En katalog kan ha många prenumerationer som är associerade med den, men endast en klientorganisation.

- **ID för Azure-klientorganisation**: detta är ett unikt sätt att identifiera en Azure Active Directory-instans inom en Azure-prenumeration.

- **Hanterade identiteter:** Azure Key Vault ett sätt att lagra autentiseringsuppgifter och andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras för att Key Vault hämta dem. Med en hanterad identitet blir det enklare att lösa det här problemet genom att ge Azure-tjänsterna en automatiskt hanterad identitet i Azure AD. Du kan använda den här identiteten för att autentisera till Key Vault eller alla tjänster som stöder Azure AD-autentisering utan att behöva ha några autentiseringsuppgifter i koden. Mer information finns i följande bild och översikten [över hanterade identiteter för Azure-resurser.](../../active-directory/managed-identities-azure-resources/overview.md)

## <a name="authentication"></a>Autentisering
Om du vill göra Key Vault med en tjänst måste du först autentisera till den. Det finns tre sätt att autentisera för Key Vault:

- [Hanterade identiteter för](../../active-directory/managed-identities-azure-resources/overview.md)Azure-resurser: När du distribuerar en app på en virtuell dator i Azure kan du tilldela en identitet till den virtuella datorn som har åtkomst till Key Vault. Du kan också tilldela identiteter till andra [Azure-resurser.](../../active-directory/managed-identities-azure-resources/overview.md) Fördelen med den här metoden är att appen eller tjänsten inte hanterar rotationen av den första hemligheten. Azure roterar automatiskt identiteten. Vi rekommenderar den här metoden som bästa praxis. 
- **Tjänstens huvudnamn och** certifikat: Du kan använda ett huvudnamn för tjänsten och ett associerat certifikat som har åtkomst till Key Vault. Vi rekommenderar inte den här metoden eftersom programägaren eller utvecklaren måste rotera certifikatet.
- **Tjänstens huvudnamn och** hemlighet: Även om du kan använda ett huvudnamn för tjänsten och en hemlighet för att autentisera Key Vault, rekommenderar vi det inte. Det är svårt att automatiskt rotera bootstrap-hemligheten som används för att autentisera för att Key Vault.


## <a name="key-vault-roles"></a>Nyckelvalvroller

Använd följande tabell för att bättre förstå hur Key Vault kan hjälpa utvecklare och säkerhetsadministratörer.

| Roll | Problembeskrivning | Åtgärdas av Azure Key Vault |
| --- | --- | --- |
| Azure-programutvecklare |"Jag vill skriva ett program för Azure som använder nycklar för signering och kryptering. Men jag vill att de här nycklarna ska vara externa från mitt program så att lösningen passar för ett program som är geografiskt distribuerat. <br/><br/>Jag vill att de här nycklarna och hemligheterna ska skyddas utan att behöva skriva koden själv. Jag vill också att de här nycklarna och hemligheterna ska vara enkla för mig att använda från mina program, med optimala prestanda." |√ Nycklar lagras i ett valv och anropas av en URI vid behov.<br/><br/> √ Nycklar skyddas av Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler.<br/><br/> √ Nycklar bearbetas i HSM-modulerna som finns i samma Azure-datacenter som programmen. Denna metod ger bättre tillförlitlighet och kortare svarstider än om nycklarna finns på en annan plats, till exempel lokalt. |
| Utvecklare av programvara som en tjänst (SaaS) |"Jag vill inte ha ansvaret eller det potentiella ansvaret för mina kunders klientnycklar och hemligheter. <br/><br/>Jag vill att kunderna ska äga och hantera sina nycklar så att jag kan koncentrera mig på att göra det jag gör bäst, vilket är att tillhandahålla grundläggande programvarufunktioner." |√ Kunder kan importera sina egna nycklar till Azure och hantera dem. När ett SaaS-program behöver utföra kryptografiska åtgärder med hjälp av kundernas nycklar Key Vault dessa åtgärder för programmets räkning. Programmet ser inte kundernas nycklar. |
| Säkerhetschef |"Jag vill veta att våra program uppfyller FIPS 140-2 Level 2 eller FIPS 140-2 Level 3 HSM för säker nyckelhantering. <br/><br/>Jag vill vara säker på att min organisation har kontrollen över nycklarnas livscykel och kan övervaka nyckelanvändningen. <br/><br/>Och även om vi använder flera Azure-tjänster och -resurser vill jag hantera nycklarna från en enda plats i Azure." |√ Välj **valv för** FIPS 140-2 Level 2-verifierade HSM:er.<br/>√ Välj **hanterade HSM-pooler** för FIPS 140-2 Level 3-verifierade HSM:er.<br/><br/>√ Key Vault är utformat så att Microsoft inte kan se eller extrahera dina nycklar.<br/>√ Nyckelanvändningen loggas i nära realtid.<br/><br/>√ Valvet tillhandahåller ett enda gränssnitt, oavsett hur många valv du har i Azure, vilka regioner de stöder och vilka program använder dem. |

Vem som helst med en Azure-prenumeration kan skapa och använda nyckelvalv. Även Key Vault utvecklare och säkerhetsadministratörer kan den implementeras och hanteras av en organisations administratör som hanterar andra Azure-tjänster. Den här administratören kan till exempel logga in med en Azure-prenumeration, skapa ett valv för organisationen där nycklar ska lagras och sedan ansvara för driftsuppgifter som dessa:

- Skapa eller importera en nyckel eller hemlighet.
- Återkalla eller ta bort en nyckel eller hemlighet.
- Ge användare eller program åtkomst till nyckelvalvet, så att de kan hantera eller använda sina nycklar och hemligheter.
- Konfigurera nyckelanvändningen (till exempel signera eller kryptera).
- Övervaka nyckelanvändningen.

Administratören ger sedan utvecklarens URI:er att anropa från sina program. Den här administratören ger även säkerhetsadministratören viktig information om användningsloggning. 

![Översikt över hur Azure Key Vault fungerar][1]

Utvecklare kan också hantera nycklar direkt, med hjälp av API:er. Mer information finns i [guiden för Key Vault-utvecklare](developers-guide.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer [om Azure Key Vault säkerhetsfunktioner](security-features.md).
- Lär dig hur du [skyddar dina hanterade HSM-pooler](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).
