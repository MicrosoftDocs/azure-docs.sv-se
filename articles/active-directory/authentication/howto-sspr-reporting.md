---
title: Rapporter om lösen ords återställning via självbetjäning – Azure Active Directory
description: Rapportering om Azure AD självbetjäning för lösen ords återställning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/01/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad1b8318e261c7dfef7fc125716736087a84bdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579186"
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Rapporteringsalternativ för Azure AD-lösenordshantering

Efter distributionen vill många organisationer veta hur eller om självbetjäning för återställning av lösen ord (SSPR) faktiskt används. Med rapporterings funktionen som Azure Active Directory (Azure AD) kan du besvara frågor genom att använda färdiga rapporter. Om du är korrekt licensierad kan du också skapa anpassade frågor.

![Rapportera om SSPR med gransknings loggarna i Azure AD][Reporting]

Följande frågor kan besvaras av de rapporter som finns i [Azure Portal](https://portal.azure.com/):

> [!NOTE]
> Du måste vara [Global administratör](../roles/permissions-reference.md)och du måste säga att dessa data ska samlas in på uppdrag av din organisation. Om du vill välja måste du gå till fliken **rapportering** eller gransknings loggarna minst en gång. Fram till dess har data inte samlats in för din organisation.
>

* Hur många personer har registrerat sig för lösen ords återställning?
* Vem har registrerat sig för lösen ords återställning?
* Vilka data registreras av personer?
* Hur många användare återställer sina lösen ord under de senaste sju dagarna?
* Vilka är de vanligaste metoderna som användare eller administratörer använder för att återställa sina lösen ord?
* Vad är vanliga problem med användare eller administratörer vid försök att använda lösen ords återställning?
* Vilka administratörer återställer sina egna lösen ord ofta?
* Finns det någon misstänkt aktivitet med lösen ords återställning?

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Så här visar du lösenordshanteringsrapporter i Azure Portal

I Azure Portals upplevelsen har vi förbättrat hur du kan visa registrerings aktiviteten för lösen ords återställning och återställning av lösen ord. Använd följande steg för att hitta registrerings händelser för lösen ords återställning och lösen ords återställning:

1. Bläddra till [Azure Portal](https://portal.azure.com).
2. Välj **Alla tjänster** i den vänstra rutan.
3. Sök efter **Azure Active Directory** i listan över tjänster och markera den.
4. Välj **användare** i avsnittet hantera.
5. Välj **gransknings loggar** från bladet **användare** . Detta visar alla gransknings händelser som inträffat mot alla användare i din katalog. Du kan filtrera den här vyn om du vill se alla lösen ords-relaterade händelser.
6. På **filter** -menyn längst upp i fönstret väljer du List rutan **tjänst** och ändrar den till tjänst typen självbetjäning för **lösen ords hantering** .
7. Du kan också filtrera listan ytterligare genom att välja den angivna **aktivitet** som du är intresse rad av.

### <a name="combined-registration"></a>Kombinerad registrering

Om du har aktiverat [kombinerad registrering](./concept-registration-mfa-sspr-combined.md)visas information om användar aktivitet i gransknings loggarna **under**  >  **säkerhetsautentiseringsmetoder**.

## <a name="description-of-the-report-columns-in-the-azure-portal"></a>Beskrivning av rapport kolumnerna i Azure Portal

I följande lista beskrivs var och en av rapport kolumnerna i Azure Portal i detalj:

* **Användare**: användaren som försökte registrera en registrerings åtgärd för lösen ords återställning.
* **Roll**: användarens roll i katalogen.
* **Datum och tid**: datum och tid för försöket.
* **Registrerade data**: autentiseringsdata som användaren angav vid registreringen av lösen ords återställning.

## <a name="description-of-the-report-values-in-the-azure-portal"></a>Beskrivning av rapport värden i Azure Portal

I följande tabell beskrivs de olika värden som du kan ställa in för varje kolumn i Azure Portal:

| Kolumn | Tillåtna värden och deras betydelser |
| --- | --- |
| Registrerade data |**Alternativt e-postmeddelande**: användaren använde en alternativ e-postadress eller ett e-postmeddelande för att autentisera.<p><p>**Arbets telefon**: användaren använde en arbets telefon för att autentisera.<p>**Mobil telefon**: användaren använde en mobil telefon eller en autentiserings-telefon för att autentisera.<p>**Säkerhets frågor**: användare som använder säkerhets frågor för att autentisera.<p>**En kombination av föregående metoder, t. ex. alternativ e-post + mobil telefon**: inträffar när en princip för två grindar anges och visar vilka två metoder användaren använde för att autentisera sin begäran om återställning av lösen ord. |

## <a name="self-service-password-management-activity-types"></a>Self-Service aktivitets typer för lösen ords hantering

Följande aktivitets typer visas i händelse kategorin granskning av **lösen ords hantering i självbetjäning** :

* [Blockerad från lösen ords återställning via](#activity-type-blocked-from-self-service-password-reset)självbetjäning: visar att en användare har försökt återställa ett lösen ord, använder en speciell grind eller validera ett telefonnummer mer än fem totala gånger på 24 timmar.
* [Ändra lösen ord (självbetjäning)](#activity-type-change-password-self-service): anger att en användare utförde en frivillig eller Tvingad (på grund av förfallo datum) lösen ords ändring.
* [Återställ lösen ord (av administratör)](#activity-type-reset-password-by-admin): anger att en administratör utförde ett lösen ords återställning för en användares räkning från Azure Portal.
* [Återställ lösen ord (självbetjäning)](#activity-type-reset-password-self-service): anger att en användare har återställt sitt lösen ord från [Azure AD-portalen för återställning av lösen ord](https://passwordreset.microsoftonline.com).
* [Förlopp för återställning av lösen ord för självbetjäning](#activity-type-self-serve-password-reset-flow-activity-progress): anger varje enskilt steg som en användare går igenom, till exempel att skicka en speciell port för autentisering av lösen ords återställning, som en del av processen för lösen ords återställning.
* [Lås upp användar konto (självbetjäning)](#activity-type-unlock-a-user-account-self-service)): visar att en användare har låst upp Active Directory-kontot utan att återställa sitt lösen ord från [Azure AD-portalen för återställning av lösen ord](https://passwordreset.microsoftonline.com) med hjälp av funktionen Active Directory för att låsa upp kontot utan att återställa.
* [Användare som registrerats för självbetjäning för återställning av lösen ord](#activity-type-user-registered-for-self-service-password-reset): visar att en användare har registrerat all nödvändig information för att kunna återställa sina lösen ord i enlighet med den aktuella angivna principen för lösen ords återställning för innehavare.

### <a name="activity-type-blocked-from-self-service-password-reset"></a>Aktivitets typ: blockerad från lösen ords återställning via självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitets Beskrivning**: anger att en användare har försökt återställa ett lösen ord, använder en enskild grind eller validera ett telefonnummer mer än fem totala gånger på 24 timmar.
* **Aktivitets skådespelare**: användaren som har begränsats från att utföra ytterligare återställnings åtgärder. Användaren kan vara en användare eller en administratör.
* **Aktivitets mål**: användaren som har begränsats från att utföra ytterligare återställnings åtgärder. Användaren kan vara en användare eller en administratör.
* **Aktivitets status**:
  * _Lyckades_: indikerar att en användare har begränsats från att utföra ytterligare återställningar, försöka med ytterligare autentiseringsmetoder eller verifiera ytterligare telefonnummer under de närmaste 24 timmarna.
* **Orsak till aktivitets status**: ej tillämpligt.

### <a name="activity-type-change-password-self-service"></a>Aktivitets typ: ändra lösen ord (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitets Beskrivning**: anger att en användare utförde en frivillig eller Tvingad (på grund av förfallo datum) lösen ords ändring.
* **Aktivitets skådespelare**: den användare som har ändrat sitt lösen ord. Användaren kan vara en användare eller en administratör.
* **Aktivitets mål**: den användare som har ändrat sitt lösen ord. Användaren kan vara en användare eller en administratör.
* **Aktivitets status**:
  * _Lyckades_: indikerar att en användare har ändrat sitt lösen ord.
  * _Fel_: indikerar att en användare inte har ändrat sitt lösen ord. Du kan välja raden för att se kategorin **aktivitets status orsak** för att lära dig mer om varför felet inträffade.
* **Orsak till status för aktivitets status**:
  * _FuzzyPolicyViolationInvalidPassword_: användaren har valt ett lösen ord som har blockerats automatiskt eftersom Microsoft-funktionen för att identifiera lösen ord identifierade att det var för vanligt eller särskilt svagt.

### <a name="activity-type-reset-password-by-admin"></a>Aktivitets typ: Återställ lösen ord (av administratör)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitets Beskrivning**: anger att en administratör utförde ett lösen ords återställning för en användares räkning från Azure Portal.
* **Aktivitets skådespelare**: administratören som utförde lösen ords återställning för en annan slutanvändare eller administratör. Måste vara administratör för lösen ord, användar administratör eller supportavdelningen.
* **Aktivitets mål**: användaren vars lösen ord har återställts. Användaren kan vara en användare eller en annan administratör.
* **Aktivitets status**:
  * _Lyckades_: visar att en administratör har återställt en användares lösen ord.
  * _Fel_: anger att en administratör inte kunde ändra en användares lösen ord. Du kan välja raden för att se kategorin **aktivitets status orsak** för att lära dig mer om varför felet inträffade.

### <a name="activity-type-reset-password-self-service"></a>Aktivitets typ: Återställ lösen ord (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitets Beskrivning**: anger att en användare har återställt sitt lösen ord från [Azure AD-portalen för återställning av lösen ord](https://passwordreset.microsoftonline.com).
* **Aktivitets skådespelare**: användaren som återställer sitt lösen ord. Användaren kan vara en användare eller en administratör.
* **Aktivitets mål**: användaren som återställer sitt lösen ord. Användaren kan vara en användare eller en administratör.
* **Aktivitets status**:
  * _Lyckades_: indikerar att en användare har återställt sina egna lösen ord.
  * _Fel_: indikerar att en användare inte kunde återställa sina egna lösen ord. Du kan välja raden för att se kategorin **aktivitets status orsak** för att lära dig mer om varför felet inträffade.
* **Orsak till status för aktivitets status**:
  * _FuzzyPolicyViolationInvalidPassword_: administratören har valt ett lösen ord som har blockerats automatiskt eftersom Microsoft-funktionen för att identifiera lösen ord identifierade att den var för vanlig eller särskilt svag.

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>Aktivitets typ: självbetjäning förlopp för lösen ords återställning av flödes aktivitet

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitets Beskrivning**: anger varje enskilt steg som en användare fortsätter genom (till exempel att skicka en speciell port för autentisering av lösen ords återställning) som en del av processen för lösen ords återställning.
* **Aktivitets skådespelare**: den användare som utförde en del av flödet för lösen ords återställning. Användaren kan vara en användare eller en administratör.
* **Aktivitets mål**: användaren som utförde en del av flödet för lösen ords återställning. Användaren kan vara en användare eller en administratör.
* **Aktivitets status**:
  * _Lyckades_: indikerar att en användare har slutfört ett speciellt steg i flödet för lösen ords återställning.
  * _Fel_: indikerar att ett enskilt steg i flödet för lösen ords återställning misslyckades. Du kan välja raden för att se kategorin **aktivitets status orsak** för att lära dig mer om varför felet inträffade.
* **Orsaker till aktivitets status**: se följande tabell för [alla status orsaker för den tillåtna återställnings aktiviteten](#description-of-the-report-columns-in-the-azure-portal).

### <a name="activity-type-unlock-a-user-account-self-service"></a>Aktivitets typ: Lås upp ett användar konto (självbetjäning)

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitets Beskrivning**: anger att en användare har låst upp Active Directory-kontot utan att återställa sitt lösen ord från [Azure AD-portalen för återställning av lösen ord](https://passwordreset.microsoftonline.com) med hjälp av funktionen Active Directory för att låsa upp kontot utan att återställa.
* **Aktivitets skådespelare**: användaren som låste sitt konto utan att återställa sitt lösen ord. Användaren kan vara en användare eller en administratör.
* **Aktivitets mål**: användaren som låste sitt konto utan att återställa sitt lösen ord. Användaren kan vara en användare eller en administratör.
* **Status för tillåtna aktiviteter**:
  * _Lyckades_: visar att en användare har låst upp sitt eget konto.
  * _Fel_: indikerar att en användare inte kunde låsa upp sitt konto. Du kan välja raden för att se kategorin **aktivitets status orsak** för att lära dig mer om varför felet inträffade.

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>Aktivitets typ: användare registrerad för lösen ords återställning via självbetjäning

I följande lista beskrivs den här aktiviteten i detalj:

* **Aktivitets Beskrivning**: anger att en användare har registrerat all nödvändig information för att kunna återställa sina lösen ord i enlighet med den aktuella principen för klient lösen ords återställning. 
* **Aktivitets skådespelare**: den användare som registrerade sig för lösen ords återställning. Användaren kan vara en användare eller en administratör.
* **Aktivitets mål**: den användare som registrerade sig för lösen ords återställning. Användaren kan vara en användare eller en administratör.
* **Status för tillåtna aktiviteter**:
  * _Lyckades_: visar att en användare har registrerat sig för lösen ords återställning i enlighet med den aktuella principen. 
  * _Fel_: indikerar att en användare inte kunde registreras för lösen ords återställning. Du kan välja raden för att se kategorin **aktivitets status orsak** för att lära dig mer om varför felet inträffade.

     >[!NOTE]
     >Ett problem innebär inte att en användare inte kan återställa sina egna lösen ord. Det innebär att de inte slutförde registrerings processen. Om det finns overifierade data på det konto som är korrekt, till exempel ett telefonnummer som inte har verifierats, även om de inte har verifierat det här telefonnumret, kan de fortfarande använda det för att återställa sina lösen ord.

## <a name="next-steps"></a>Nästa steg

* [SSPR och MFA-användning och insikts rapportering](./howto-authentication-methods-activity.md)
* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registrera dig för lösen ords återställning via självbetjäning](../user-help/active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](./tutorial-enable-sspr-writeback.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är brutet. Hur gör jag för att felsöka SSPR?](./troubleshoot-sspr.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Reporting]: ./media/howto-sspr-reporting/sspr-reporting.png "Exempel på gransknings loggar för SSPR-aktivitet i Azure AD"