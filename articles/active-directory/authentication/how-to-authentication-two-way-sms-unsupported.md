---
title: Dubbelriktat SMS stöds inte längre-Azure Active Directory
description: Förklarar hur du aktiverar en annan metod för användare som fortfarande använder dubbelriktat SMS.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689036"
---
# <a name="two-way-sms-unsupported"></a>Dubbelriktat SMS stöds inte

Dubbelriktad SMS för Azure AD Multi-Factor Authentication-Server (MFA) var ursprungligen inaktuell i 2018 och stöds inte längre efter den 24 februari 2021. Administratörer bör aktivera en annan metod för användare som fortfarande använder dubbelriktat SMS.

E-postmeddelanden och Azure Portal Service Health meddelanden (portal-popups) har skickats till berörda administratörer den 8 december 2020 och 28 januari 2021. Aviseringarna gick till rollerna ägare, medägare, administratör och tjänst administratör RBAC knutna till prenumerationerna. Om du redan har slutfört följande steg krävs ingen åtgärd.

## <a name="required-actions"></a>Nödvändiga åtgärder

1. Aktivera mobilappen för dina användare om du inte redan har gjort det. Mer information finns i [Aktivera Mobile App-autentisering med MFA Server](howto-mfaserver-deploy-mobileapp.md).
1. Meddela slutanvändarna att gå till din [användar Portal](howto-mfaserver-deploy-userportal.md) för MFA-servern för att aktivera mobilappen. Den [Microsoft Authenticator appen](https://www.microsoft.com/en-us/account/authenticator) är det rekommenderade verifierings alternativet eftersom det är säkrare än dubbelriktat SMS. Mer information finns i se [hur det är dags att lägga på telefon transporter för autentisering](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. Ändra användar inställningarna från dubbelriktat textmeddelande till mobilapp som standard metod.

## <a name="faq"></a>Vanliga frågor

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>Vad händer om jag inte ändrar standard metoden från dubbelriktat SMS till mobilappen?
Dubbelriktat SMS Miss lyckas efter den 24 februari 2021. Användarna får ett fel meddelande när de försöker logga in och skicka MFA.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Hur gör jag för att ändra användar inställningarna från dubbelriktat textmeddelande till mobilappen?

Du bör ändra användar inställningarna genom att följa dessa steg:

1. I MFA Server filtrerar du användar listan för dubbelriktat textmeddelande.
1. Välj alla användare.
1. Öppna dialog rutan Redigera användare.
1. Ändra användare från SMS till mobilapp.

   ![Skärm bild av slutanvändare](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Behöver mina användare utföra någon åtgärd? Om ja, hur?
Ja. Dina slutanvändare behöver besöka din särskilda MFA Server-användargrupp för att aktivera mobilappen, om de inte redan har gjort det. När du har genomfört steg 3 börjar alla användare som inte besöker användar portalen för att konfigurera mobilappen att logga in igen tills de besöker användar portalen för att registrera om.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>Vad händer om mina användare inte kan installera mobilappen? Vilka andra alternativ har de?
Det alternativa sättet att använda dubbelriktat SMS eller mobilappen är ett telefonsamtal. Men Microsoft Authenticator-appen är den rekommenderade verifierings metoden.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Kommer envägs-SMS att vara inaktuellt också?
Nej, det är bara dubbelriktat SMS som är föråldrat. För MFA Server fungerar ett enkelriktat SMS för en delmängd scenarier:

- AD FS adapter
- IIS-autentisering (kräver användar Portal och konfiguration)
- RADIUS (kräver att RADIUS-klienter har stöd för åtkomst utmaning och att PAP-protokollet används)

Det finns begränsningar i när ett enkelriktat SMS kan användas som gör mobilappen till ett bättre alternativ eftersom den inte kräver verifierings kod frågan.
Om du fortfarande vill använda ett enkelriktat SMS i vissa fall kan du lämna dessa markerade, men ändra avsnittet **företags inställningar** , fliken **Allmänt** **användar standard i SMS** till **envägs** i stället för **tvåvägs**. Om du använder Directory-synkronisering som standard för SMS måste du ändra den till One-Way i stället för dubbelriktad.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Hur kan jag kontrol lera vilka användare som fortfarande använder dubbelriktat SMS?
Om du vill visa de här användarna, starta **MFA Server**, Välj avsnittet **användare** , klicka på **filtrera användar lista** och filtrera efter **textmeddelande tvåvägs**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Hur döljer vi dubbelriktat SMS som ett alternativ i MFA-portalen för att hindra användare från att välja den i framtiden?
I användar portalen för MFA-servern klickar du på **Inställningar**. du kan ta bort **textmeddelandet** så att det inte är tillgängligt. Samma sak gäller för **AD FS** avsnittet om du använder AD FS för användar registrering.

