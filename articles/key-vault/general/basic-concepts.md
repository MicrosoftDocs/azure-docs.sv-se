---
title: Vad är Azure Key Vault? | Microsoft Docs
description: Lär dig hur Azure Key Vault skyddar de kryptografiska nycklar och hemligheter som används av moln program och tjänster.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: cc00a4f1c1551932b4a30a8ef9b27cb1d4082667
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071604"
---
# <a name="azure-key-vault-basic-concepts"></a>Azure Key Vault grundläggande begrepp

Azure Key Vault är en moln tjänst för säker lagring och åtkomst till hemligheter. En hemlighet är något som du vill noggrant kontrol lera åtkomsten till, till exempel API-nycklar, lösen ord, certifikat eller krypterings nycklar. Key Vault-tjänsten stöder två typer av behållare: valv och hanterade HSM-pooler. Valv stöder lagring av program vara och HSM-säkerhetskopierade nycklar, hemligheter och certifikat. Hanterade HSM-pooler stöder bara HSM-backade nycklar. Fullständig information finns i [Azure Key Vault REST API översikt](about-keys-secrets-certificates.md) .

Här följer andra viktiga villkor:

- **Klientorganisation**: en klientorganisation är den organisation som äger och hanterar en specifik instans av Microsoft-molntjänster. Den används oftast för att referera till Azure-och Microsoft 365-tjänster för en organisation.

- **Valvägare**: valvägaren kan skapa ett nyckelvalv och få fullständig åtkomst till och kontroll över det. Valvägaren kan även konfigurera granskning för att logga vem som använder hemligheter och nycklar. Administratörer kan styra nyckelns livscykel. De kan distribuera en ny version av nyckeln, säkerhetskopiera den och utföra andra relaterade uppgifter.

- **Valvkonsument**: valvkonsumenten kan utföra åtgärder på tillgångarna i nyckelvalvet när valvägaren ger konsumenten åtkomst. Vilka åtgärder som är tillgängliga beror på vilka behörigheter som beviljats.

- **Hanterade HSM-administratörer**: användare som har tilldelats rollen administratör har fullständig kontroll över en HANTERAd HSM-pool. De kan skapa fler roll tilldelningar för att delegera kontrollerad åtkomst till andra användare.

- **Hanterad HSM-kryptografisk utanordnare/användare**: inbyggda roller som vanligt vis tilldelas till användare eller tjänst huvud namn som ska utföra kryptografiska åtgärder med hjälp av nycklar i hanterad HSM. Krypterings användare kan skapa nya nycklar, men kan inte ta bort nycklar.

- **Kryptering av hanterad HSM-** kryptering: inbyggd roll som vanligt vis tilldelas en tjänst identitet för tjänst konton (t. ex. lagrings konto) för kryptering av data i vila med kundhanterad nyckel.

- **Resurs**: en resurs är ett hanterbart objekt som är tillgängligt via Azure. Vanliga exempel är virtuella datorer, lagrings konton, webbapp, databaser och virtuella nätverk. Det finns många fler.

- **Resursgrupp**: resursgruppen är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som passar din organisation bäst.

- **Säkerhets objekt**: ett Azure-säkerhetsobjekt är en säkerhets identitet som användare skapade appar, tjänster och automatiserings verktyg använder för att få åtkomst till särskilda Azure-resurser. Tänk på det som "användar identitet" (användar namn och lösen ord eller certifikat) med en speciell roll och tätt kontrollerade behörigheter. Ett säkerhets objekt behöver bara göra vissa saker, till skillnad från en allmän användar identitet. Det förbättrar säkerheten om du endast beviljar den lägsta behörighets nivå som krävs för att utföra dess hanterings uppgifter. Ett säkerhets objekt som används med ett program eller en tjänst kallas särskilt för **tjänstens huvud namn**.

- [Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md): Azure AD är Active Directory-tjänsten för en klientorganisation. Varje katalog har en eller flera domäner. En katalog kan ha många prenumerationer som är associerade med den, men endast en klientorganisation.

- **ID för Azure-klientorganisation**: detta är ett unikt sätt att identifiera en Azure Active Directory-instans inom en Azure-prenumeration.

- **Hanterade identiteter**: Azure Key Vault är ett sätt att lagra autentiseringsuppgifter på ett säkert sätt och andra nycklar och hemligheter, men koden måste autentiseras för att Key Vault ska kunna hämta dem. Genom att använda en hanterad identitet kan du lösa det här problemet enklare genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure AD. Du kan använda den här identiteten för att autentisera till Key Vault eller alla tjänster som stöder Azure AD-autentisering utan att behöva ha några autentiseringsuppgifter i koden. Mer information finns i följande bild och [Översikt över hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md).

## <a name="authentication"></a>Autentisering
Om du vill utföra några åtgärder med Key Vault måste du först autentisera till den. Det finns tre sätt att autentisera till Key Vault:

- [Hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md): när du distribuerar en app på en virtuell dator i Azure kan du tilldela en identitet till den virtuella datorn som har åtkomst till Key Vault. Du kan också tilldela identiteter till [andra Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). Fördelen med den här metoden är att appen eller tjänsten inte hanterar rotationen av den första hemligheten. Azure roterar automatiskt identiteten. Vi rekommenderar den här metoden som bästa praxis. 
- **Tjänstens huvud namn och certifikat**: du kan använda ett huvud namn för tjänsten och ett associerat certifikat som har åtkomst till Key Vault. Vi rekommenderar inte den här metoden eftersom program ägaren eller utvecklaren måste rotera certifikatet.
- **Tjänstens huvud namn och hemlighet**: även om du kan använda ett huvud namn för tjänsten och en hemlighet för att autentisera till Key Vault, rekommenderar vi inte det. Det är svårt att automatiskt rotera start hemligheten som används för att autentisera till Key Vault.


## <a name="key-vault-roles"></a>Nyckelvalvroller

Använd följande tabell för att bättre förstå hur Key Vault kan hjälpa utvecklare och säkerhetsadministratörer.

| Roll | Problembeskrivning | Åtgärdas av Azure Key Vault |
| --- | --- | --- |
| Azure-programutvecklare |"Jag vill skriva ett program för Azure som använder nycklar för signering och kryptering. Men jag vill att dessa nycklar ska vara externa från mitt program så att lösningen är lämplig för ett program som är geografiskt distribuerat. <br/><br/>Jag vill att de här nycklarna och hemligheterna ska skyddas utan att behöva skriva koden själv. Jag vill också att dessa nycklar och hemligheter ska vara lätta att använda från mina program, med optimala prestanda. " |√ Nycklar lagras i ett valv och anropas av en URI vid behov.<br/><br/> √ Nycklar skyddas av Azure med branschstandardalgoritmer, nyckellängder och maskinvarusäkerhetsmoduler.<br/><br/> √ Nycklar bearbetas i HSM-modulerna som finns i samma Azure-datacenter som programmen. Denna metod ger bättre tillförlitlighet och kortare svarstider än om nycklarna finns på en annan plats, till exempel lokalt. |
| Utvecklare av programvara som en tjänst (SaaS) |"Jag vill inte ha ansvaret eller potentiella skyldigheter för mina kunders klient nycklar och hemligheter. <br/><br/>Jag vill att kunderna ska äga och hantera sina nycklar så att jag kan koncentrera dig på vad jag gör bäst, vilket ger de viktigaste program varu funktionerna. " |√ Kunder kan importera sina egna nycklar till Azure och hantera dem. När ett SaaS-program behöver utföra kryptografiska åtgärder med hjälp av kundernas nycklar, Key Vault utföra dessa åtgärder för programmets räkning. Programmet ser inte kundens nycklar. |
| Säkerhetschef |"Jag vill veta att våra program följer FIPS 140-2-nivå 2-eller FIPS 140-2 nivå 3-HSM: er för säker nyckel hantering. <br/><br/>Jag vill vara säker på att min organisation har kontrollen över nycklarnas livscykel och kan övervaka nyckelanvändningen. <br/><br/>Även om vi använder flera Azure-tjänster och-resurser, vill jag hantera nycklarna från en enda plats i Azure. " |√ Väljer **valv** för FIPS 140-2 nivå 2-verifierade HSM: er.<br/>√ Väljer **hanterade HSM-pooler** för FIPS 140-2 nivå 3-verifierade HSM: er.<br/><br/>√ Key Vault är utformat så att Microsoft inte kan se eller extrahera dina nycklar.<br/>√ Nyckelanvändningen loggas i nära realtid.<br/><br/>√ Valvet tillhandahåller ett enda gränssnitt, oavsett hur många valv du har i Azure, vilka regioner de stöder och vilka program använder dem. |

Vem som helst med en Azure-prenumeration kan skapa och använda nyckelvalv. Även om Key Vault fördelar utvecklare och säkerhets administratörer kan de implementeras och hanteras av en organisations administratör som hanterar andra Azure-tjänster. Administratören kan till exempel Logga in med en Azure-prenumeration, skapa ett valv för den organisation där nycklarna ska lagras och sedan ansvara för drift uppgifter som dessa:

- Skapa eller importera en nyckel eller hemlighet.
- Återkalla eller ta bort en nyckel eller hemlighet.
- Ge användare eller program åtkomst till nyckelvalvet, så att de kan hantera eller använda sina nycklar och hemligheter.
- Konfigurera nyckelanvändningen (till exempel signera eller kryptera).
- Övervaka nyckelanvändningen.

Den här administratören ger sedan utvecklare URI: er möjlighet att anropa från sina program. Den här administratören ger även loggnings information om nyckel användning till säkerhets administratören. 

![Översikt över hur Azure Key Vault fungerar][1]

Utvecklare kan också hantera nycklar direkt, med hjälp av API:er. Mer information finns i [guiden för Key Vault-utvecklare](developers-guide.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skyddar ditt valv](secure-your-key-vault.md).
- Lär dig hur du [skyddar dina hanterade HSM-pooler](../managed-hsm/access-control.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault är tillgängligt i de flesta regioner. Mer information finns på sidan med [Key Vault-priser](https://azure.microsoft.com/pricing/details/key-vault/).