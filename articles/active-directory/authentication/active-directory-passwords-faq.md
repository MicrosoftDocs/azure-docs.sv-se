---
title: Vanliga frågor och svar om lösen ords återställning via självbetjäning – Azure Active Directory
description: Vanliga frågor om återställning av lösen ord för självbetjäning i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0874f6aeb74b028e92e10d972757cc5971735e39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744320"
---
# <a name="self-service-password-reset-frequently-asked-questions"></a>Vanliga frågor och svar om lösen ords återställning via självbetjäning

Här följer några vanliga frågor och svar (FAQ) för alla saker som rör självbetjäning för återställning av lösen ord.

Om du har en allmän fråga om Azure Active Directory (Azure AD) och lösen ords återställning via självbetjäning (SSPR) som inte besvaras här kan du be communityn om hjälp på [sidan Microsoft Q&en fråga för Azure Active Directory](/answers/topics/azure-active-directory.html). Medlemmar i communityn är tekniker, produkt chefer, MVP: er och andra IT-proffs.

Vanliga frågor och svar är indelade i följande avsnitt:

* [Frågor om registrering av lösen ords återställning](#password-reset-registration)
* [Frågor om lösen ords återställning](#password-reset)
* [Frågor om lösen ords ändring](#password-change)
* [Frågor om lösen ords hanterings rapporter](#password-management-reports)
* [Frågor om tillbakaskrivning av lösen ord](#password-writeback)

## <a name="password-reset-registration"></a>Registrering av lösenordsåterställning

* **F: kan mina användare registrera sina egna lösen ords återställnings data?**

  > **S:** Ja. Så länge lösen ords återställning är aktiverat och de är licensierade kan användare gå till registrerings portalen för lösen ords återställning ( https://aka.ms/ssprsetup) för att registrera deras autentiseringsinformation). Användare kan även registrera sig via åtkomst panelen ( https://myapps.microsoft.com) . Om du vill registrera dig via åtkomst panelen måste du välja profil bilden, välja **profil** och sedan välja alternativet **Registrera för lösen ords återställning** .
  >
  > Om du aktiverar [kombinerad registrering](concept-registration-mfa-sspr-combined.md)kan användarna registrera sig för både SSPR och Azure AD Multi-Factor Authentication samtidigt.
* **F: om jag aktiverar lösen ords återställning för en grupp och sedan bestämmer dig för att aktivera den för alla, måste mina användare omregistrera sig?**

  > **A:** Nej. Användare som har fyllt i diagnostikdata behöver inte registreras igen.
  >
  >
* **F: kan jag definiera lösen ords återställnings data för mina användares räkning?**

  > **A:** Ja, du kan göra det med Azure AD Connect, PowerShell, [Azure Portal](https://portal.azure.com)eller [Microsoft 365 administrations centret](https://admin.microsoft.com). Mer information finns i [data som används av lösen ords återställning via](howto-sspr-authenticationdata.md)självbetjäning i Azure AD.
  >
  >
* **F: kan jag synkronisera data för säkerhets frågor från lokala platser?**

  > **A:** Nej, det är inte möjligt idag.
  >
  >
* **F: kan mina användare registrera data på ett sådant sätt att andra användare inte kan se dessa data?**

  > **S:** Ja. När användarna registrerar data med hjälp av registrerings portalen för lösen ords återställning, sparas data i fält för privata autentisering som endast är synliga för globala administratörer och användare.
  >
  >
* **F: måste mina användare registreras innan de kan använda lösen ords återställning?**

  > **A:** Nej. Om du definierar tillräckligt autentiseringsinformation för deras räkning behöver användarna inte registrera sig. Lösen ords återställning fungerar så länge du har formaterat data som lagras i lämpliga fält i katalogen.
  >
  >
* **F: kan jag synkronisera eller ställa in telefon för autentisering, e-postautentisering eller alternativa inloggnings telefon fält för mina användares räkning?**

  > **A:** De fält som kan ställas in av en global administratör definieras i artikeln [SSPR data krav](howto-sspr-authenticationdata.md).
  >
  >
* **F: Hur avgör registrerings portalen vilka alternativ som ska visa mina användare?**

  > **A:** Registrerings portalen för lösen ords återställning visar bara de alternativ som du har aktiverat för dina användare. De här alternativen finns i avsnittet **princip för lösen ords återställning för användare** på fliken **Konfigurera** . Om du till exempel inte aktiverar säkerhets frågor kan användarna inte registrera sig för det alternativet.
  >
  >
* **F: När är en användare anses registrerad?**

  > **A:** En användare anses vara registrerad för SSPR när de har registrerat minst det **antal metoder som krävs för att återställa** ett lösen ord som du har angett i [Azure Portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Lösenordsåterställning

* **F: hindrar du användare från att återställa ett lösen ord under en kort tids period?**

  > **A:** Ja, det finns säkerhetsfunktioner som är inbyggda i lösen ords återställning för att skydda dem från missbruk. 
  >
  > Användare kan bara prova fem försök att återställa lösen ord inom en 24-timmarsperiod innan de är låsta i 24 timmar. 
  >
  > Användare kan försöka verifiera ett telefonnummer, skicka ett SMS eller verifiera säkerhets frågor och svara bara fem gånger inom en timme innan de är låsta i 24 timmar. 
  >
  > Användare kan skicka ett e-postmeddelande högst 10 gånger inom en 10-minuters period innan de är låsta i 24 timmar.
  >
  > Räknarna återställs när en användare återställer sitt lösen ord.
  >
  >
* **F: hur lång tid ska jag vänta på att få ett e-postmeddelande, SMS eller telefonsamtal från återställning av lösen ord?**

  > **A:** E-postmeddelanden, SMS-meddelanden och telefonsamtal ska komma in under en minut. Det normala fallet är 5 till 20 sekunder.
  > Om du inte får meddelandet inom denna tidsram:
  > * Kontrol lera mappen skräp post.
  > * Kontrol lera att det nummer eller den e-postadress som du kontaktar är det du förväntar dig.
  > * Kontrol lera att autentiseringsdata i katalogen är korrekt formaterat, till exempel + 1 4255551234 eller *user \@ contoso.com*. 
* **F: vilka språk stöds av lösen ords återställning?**

  > **A:** Användar gränssnittet för lösen ords återställning, SMS-meddelanden och röst samtal lokaliseras på samma språk som stöds i Microsoft 365.
  >
  >
* **F: vilka delar av lösen ords återställningen som visas när jag anger objekt för organisations anpassning på fliken Konfigurera i min katalog?**

  > **A:** Portalen för lösen ords återställning visar din organisations logo typ och gör att du kan konfigurera länken "kontakta administratören" så att den pekar på en anpassad e-postadress eller URL. Alla e-postmeddelanden som skickas med återställning av lösen ord innehåller din organisations logo typ, färger och namn i e-postmeddelandets brödtext och är anpassad från inställningarna för det aktuella namnet.
  >
  >
* **F: Hur kan jag utbilda mina användare om var man ska återställa sina lösen ord?**

  > **A:** Prova några av förslagen i vår [SSPR-distributions](howto-sspr-deployment.md#plan-communications) artikel.
  >
  >
* **F: kan jag använda den här sidan från en mobil enhet?**

  > **A:** Ja, den här sidan fungerar på mobila enheter.
  >
  >
* **F: har du stöd för att låsa upp lokala Active Directory-konton när användarna återställer sina lösen ord?**

  > **S:** Ja. När en användare återställer sitt lösen ord, om tillbakaskrivning av lösen ord har distribuerats via Azure AD Connect låses användarens konto automatiskt när lösen ordet återställs.
  >
  >
* **F: Hur kan jag integrera lösen ords återställning direkt i min användares inloggnings upplevelse?**

  > **A:** Om du är Azure AD Premium kund kan du installera Microsoft Identity Manager utan extra kostnad och distribuera den lokala lösningen för återställning av lösen ord.
  >
  >
* **F: kan jag ange olika säkerhets frågor för olika språk?**

  > **A:** Nej, det är inte möjligt idag.
  >
  >
* **F: hur många frågor kan jag konfigurera för autentisering med säkerhets frågor?**

  > **A:** Du kan konfigurera upp till 20 anpassade säkerhets frågor i [Azure Portal](https://portal.azure.com).
  >
  >
* **F: hur länge kan säkerhets frågor vara?**

  > **A:** Säkerhets frågor kan vara mellan 3 och 200 tecken långa.
  >
  >
* **F: hur länge får svar på säkerhets frågor?**

  > **A:** Svar kan vara mellan 3 och 40 tecken långt.
  >
  >
* **F: är dubbla svar på säkerhets frågor som avvisats?**

  > **A:** Ja, vi avvisar dubbla svar på säkerhets frågor.
  >
  >
* **F: kan en användare registrera samma säkerhets fråga mer än en gång?**

  > **A:** Nej. När en användare registrerar en viss fråga kan de inte registrera sig för den frågan en gång i sekunden.
  >
  >
* **F: är det möjligt att ange en minsta gräns för säkerhets frågor för registrering och återställning?**

  > **A:** Ja, en gräns kan ställas in för registrering och en annan för återställning. Tre till fem säkerhets frågor kan krävas för registrering och tre till fem frågor kan krävas för återställning.
  >
  >
* **F: jag konfigurerade min princip för att kräva att användare använder säkerhets frågor för återställning, men Azure-administratörer verkar vara konfigurerade på olika sätt.**

  > **A:** Detta är det förväntade beteendet. Microsoft tillämpar en stark standardprincip med två gates för lösenordsåterställning för alla Azure-administratörsroller. Detta förhindrar administratörer från att använda säkerhets frågor. Du hittar mer information om den här principen i [lösen ords principer och begränsningar i Azure Active Directory](concept-sspr-policy.md) artikel.
  >
  >
* **F: om en användare har registrerat fler än det högsta antalet frågor som krävs för att återställa, hur ska säkerhets frågorna väljas vid återställning?**

  > **A:** *n* antal säkerhets frågor väljs slumpmässigt ut från det totala antalet frågor som en användare har registrerat sig för, där *N* är den mängd som anges för det **antal frågor som krävs för att återställa** alternativet. Om en användare exempelvis har registrerat fem säkerhets frågor, men bara tre krävs för att återställa ett lösen ord, väljs tre av de fem frågorna slumpmässigt och visas vid återställning. Om användaren får svar på frågor som är inaktive rad börjar urvals processen för att förhindra frågans hammare.
  >
  >
* **F: hur länge är e-post och lösen ord för en eng ång slö sen ord giltiga?**

  > **A:** Sessionens livs längd för lösen ords återställning är 15 minuter. Från början av åtgärden för lösen ords återställning har användaren 15 minuter på sig att återställa sitt lösen ord. Lösen ordet för e-post och SMS-eng ång slö sen ord är giltiga i 5 minuter under sessionen för lösen ords återställning.
  >
  >
* **F: kan jag blockera användare från att återställa sitt lösen ord?**

  > **A:** Ja, om du använder en grupp för att aktivera SSPR kan du ta bort en enskild användare från gruppen som gör det möjligt för användare att återställa sina lösen ord. Om användaren är global administratör behåller de möjligheten att återställa sitt lösen ord och kan inte inaktive ras.
  >
  >

## <a name="password-change"></a>Lösenordsändring

* **F: var ska mina användare ändra sina lösen ord?**

  > **A:** Användare kan ändra sina lösen ord var de än befinner sig i sin profil bild eller ikon, som i det övre högra hörnet av [Office 365](https://portal.office.com) -portalen eller [åtkomst panels](https://myapps.microsoft.com) upplevelser. Användare kan ändra sina lösen ord från [åtkomst panelens profil sida](https://account.activedirectory.windowsazure.com/r#/profile). Användare kan också uppmanas att ändra sina lösen ord automatiskt på inloggnings sidan för Azure AD om deras lösen ord har upphört att gälla. Slutligen kan användarna Bläddra till [Azure AD-portalen för ändring av lösen ord](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) direkt om de vill ändra sina lösen ord.
  >
  >
* **F: kan mina användare meddelas i Office-portalen när deras lokala lösen ord upphör att gälla?**

  > **A:** Ja, det här är möjligt idag om du använder Active Directory Federation Services (AD FS) (AD FS). Om du använder AD FS följer du anvisningarna i artikeln [skicka anspråk för lösen ords princip med AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) . Om du använder hash-synkronisering av lösen ord är detta inte möjligt idag. Vi synkroniserar inte lösen ords principer från lokala kataloger, så det är inte möjligt för oss att skicka meddelanden om förfallo datum till moln upplevelser. I båda fallen är det också möjligt att [meddela användare vars lösen ord håller på att gå ut från PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **F: kan jag blockera användare från att ändra sina lösen ord?**

  > **A:** Det går inte att blockera lösen ords ändringar för enbart moln användare. För lokala användare kan du ange att alternativet **användare inte kan ändra lösen ordet** till är markerat. De valda användarna kan inte ändra sina lösen ord.
  >
  >

## <a name="password-management-reports"></a>Rapporter för lösen ords hantering

* **F: hur lång tid tar det innan data visas i rapporterna för lösen ords hantering?**

  > **A:** Data ska visas i rapporterna för lösen ords hantering i 5 till 10 minuter. I vissa fall kan det ta upp till en timme innan det visas.
  >
  >
* **F: Hur kan jag filtrera lösen ords hanterings rapporter?**

  > **A:** Filtrera rapporterna för lösen ords hantering genom att välja det små förstorings glaset i den yttersta högerkanten av kolumn etiketterna, längst upp i rapporten. Om du vill göra bättre filtrering kan du ladda ned rapporten till Excel och skapa en pivot-tabell.
  >
  >
* **F: Vad är det maximala antalet händelser som lagras i rapporterna för lösen ords hantering?**

  > **A:** Upp till 75 000 lösen ords återställning eller registrerings händelser för lösen ords återställning lagras i rapporterna för lösen ords hantering, som sträcker sig tillbaka så långt som 30 dagar. Vi arbetar för att expandera det här talet för att inkludera fler händelser.
  >
  >
* **F: hur långt tillbaka kommer rapporterna för lösen ords hantering att användas?**

  > **A:** Rapporter för lösen ords hantering visar åtgärder som inträffat under de senaste 30 dagarna. För tillfället kan du hämta rapporterna regelbundet och spara dem på en annan plats om du behöver arkivera dessa data.
  >
  >
* **F: finns det ett maximalt antal rader som kan visas i rapporterna för lösen ords hantering?**

  > **S:** Ja. Högst 75 000 rader kan visas i någon av rapporterna för lösen ords hantering, oavsett om de visas i användar gränssnittet eller hämtas.
  >
  >
* **F: finns det ett API för att få åtkomst till återställnings data för lösen ords återställning eller registrering?**

  > **S:** Ja. Information om hur du får åtkomst till rapporterings data för lösen ords återställning finns i [REST API referens för Azure Log Analytics](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>Tillbakaskrivning av lösenord

* **F: Hur fungerar tillbakaskrivning av lösen ord bakom bakgrunden?**

  > **A:** Se artikeln [så här fungerar tillbakaskrivning av lösen ord](./tutorial-enable-sspr-writeback.md) för att få en förklaring av vad som händer när du aktiverar tillbakaskrivning av lösen ord och hur data flödar genom systemet tillbaka till din lokala miljö.
  >
  >
* **F: hur länge fungerar tillbakaskrivning av lösen ord? Finns det en fördröjning av lösen ordets hash-synkronisering?**

  > **A:** Tillbakaskrivning av lösen ord är direkt. Det är en synkron pipeline som fungerar på ett annat sätt än hash-synkronisering av lösen ord. Med tillbakaskrivning av lösen ord kan användarna få feedback i real tid om resultatet av återställningen av lösen ordet eller ändrings åtgärden. Genomsnittlig tid för en lyckad tillbakaskrivning av lösen ord är under 500 ms.
  >
  >
* **F: om mitt lokala konto är inaktiverat, hur fungerar mitt moln konto och åtkomst?**

  > **A:** Om ditt lokala ID är inaktiverat inaktive ras även ditt moln-ID och åtkomst vid nästa synkroniseringstillstånd genom att Azure AD Connect. Som standard är den här synkroniseringen var 30: e minut.
  >
  >
* **F: om mitt lokala konto begränsas av en lokal Active Directory lösen ords princip, följer SSPR den här principen när jag ändrar mitt lösen ord?**

  > **A:** Ja, SSPR förlitar sig på och följer den lokala Active Directory lösen ords principen. Den här principen omfattar den typiska Active Directory domän lösen ords principen, samt eventuella definierade, detaljerade lösen ords principer som är riktade till en användare.
  >
  >
* **F: vilka typer av konton fungerar tillbakaskrivning av lösen ord?**

  > **A:** Tillbakaskrivning av lösen ord fungerar för användar konton som synkroniseras från lokala Active Directory till Azure AD, inklusive federerad, hash-synkronisering av lösen ord och Pass-Through autentisering av användare.
  >
  >
* **F: Använd tillbakaskrivning av lösen ord min domäns lösen ords principer?**

  > **S:** Ja. Tillbakaskrivning av lösen ord tvingar lösen ordets ålder, historik, komplexitet, filter och annan begränsning som du kan lägga på på lösen ord i din lokala domän.
  >
  >
* **F: är tillbakaskrivning av lösen ord?  Hur kan jag vara säker på att jag inte blir hackad?**

  > **A:** Ja, tillbakaskrivning av lösen ord är säker. Om du vill läsa mer om flera säkerhets nivåer som implementeras av tjänsten för tillbakaskrivning av lösen ord, kan du titta på avsnittet om [tillbakaskrivning](concept-sspr-writeback.md#password-writeback-security) av lösen ord i artikeln [Översikt över tillbakaskrivning av lösen](./tutorial-enable-sspr-writeback.md) ord.
  >
  >

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösen ord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord med självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](./tutorial-enable-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något är brutet. Hur gör jag för att felsöka SSPR?](./troubleshoot-sspr.md)