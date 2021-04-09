---
title: Felsöka lösen ords återställning via självbetjäning – Azure Active Directory
description: Lär dig hur du felsöker vanliga problem och lösnings steg för lösen ords återställning via självbetjäning i Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1dee21182349108c44f9d498417d3b760ac4913
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103600870"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Felsöka lösen ords återställning via självbetjäning i Azure Active Directory

Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR) gör det möjligt för användare att återställa sina lösen ord i molnet.

Om du har problem med SSPR kan du använda följande fel söknings steg och vanliga fel. Du kan också titta på den här korta videon om [hur man löser de sex vanligaste SSPR slut användar fel meddelandena](https://www.youtube.com/watch?v=9RPrNVLzT8I&list=PL3ZTgFEc7LyuS8615yo39LtXR7j1GCerW&index=1).

Om du inte hittar svaret på ditt problem [är våra support team alltid tillgängliga](#contact-microsoft-support) för att hjälpa dig.

## <a name="sspr-configuration-in-the-azure-portal"></a>SSPR-konfiguration i Azure Portal

Om du har problem med att se eller konfigurera SSPR alternativ i Azure Portal kan du läsa följande fel söknings steg:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Jag ser inte avsnittet **lösen ords återställning** under Azure AD i Azure Portal.

Du ser inte meny alternativet om **lösen ords återställning** om du inte har en Azure AD-licens tilldelad till administratören som utför åtgärden.

Om du vill tilldela en licens till administratörs kontot i fråga, följer du stegen för att [tilldela, verifiera och lösa problem med licenser](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="i-dont-see-a-particular-configuration-option"></a>Jag kan inte se ett visst konfigurations alternativ.

Många delar av användar gränssnittet är dolda tills de behövs. Kontrol lera att alternativet är aktiverat innan du söker efter de olika konfigurations alternativen.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>Jag ser inte fliken **lokal integrering** .

Tillbakaskrivning av lösen ord visas bara om du har hämtat Azure AD Connect och konfigurerat funktionen.

Mer information finns i [komma igång med Azure AD Connect](../hybrid/how-to-connect-install-express.md).

## <a name="sspr-reporting"></a>SSPR-rapportering

Om du har problem med SSPR-rapportering i Azure Portal kan du läsa följande fel söknings steg:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>Jag ser inte några aktivitets typer för lösen ords hantering i händelse kategorin för granskning av **lösen ords hantering i självbetjäning** .

Detta kan inträffa om du inte har en Azure AD-licens som tilldelats administratören att utföra åtgärden.

Om du vill tilldela en licens till administratörs kontot i fråga, följer du stegen för att [tilldela, verifiera och lösa problem med licenser](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="user-registrations-show-multiple-times"></a>Användar registreringar visas flera gånger.

När en användare registreras loggar vi för närvarande varje enskild data mängd som är registrerad som en separat händelse.

Om du vill aggregera dessa data och få större flexibilitet i hur du kan visa den, kan du hämta rapporten och öppna data som en pivot-tabell i Excel.

## <a name="sspr-registration-portal"></a>SSPR-registrerings Portal

Om dina användare har problem med att registrera för SSPR kan du läsa följande fel söknings steg:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Katalogen är inte aktive rad för lösen ords återställning. Användaren kan se ett fel som rapporterar "administratören har inte aktiverat dig att använda den här funktionen".

Du kan aktivera SSPR för alla användare, inga användare eller för valda användar grupper. Endast en Azure AD-grupp kan för närvarande aktive ras för SSPR med hjälp av Azure Portal. Som en del av en större distribution av SSPR stöds kapslade grupper. Se till att de användare i gruppen som du väljer har rätt tilldelade licenser.

I Azure Portal ändrar du konfigurationen för **lösen ords återställning via självbetjäning** till *markerad* eller *alla* och väljer sedan **Spara**.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Användaren har ingen tilldelad Azure AD-licens. Användaren kan se ett fel som rapporterar "administratören har inte aktiverat dig att använda den här funktionen".

Endast en Azure AD-grupp kan för närvarande aktive ras för SSPR med hjälp av Azure Portal. Som en del av en större distribution av SSPR stöds kapslade grupper. Se till att de användare i gruppen som du väljer har rätt tilldelade licenser. Granska föregående fel söknings steg för att aktivera SSPR efter behov.

Granska också fel söknings stegen för att se till att administratören som utför konfigurations alternativen har en tilldelad licens. Om du vill tilldela en licens till administratörs kontot i fråga, följer du stegen för att [tilldela, verifiera och lösa problem med licenser](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="theres-an-error-processing-the-request"></a>Ett fel uppstod när begäran bearbetades.

Allmänna SSPR kan orsakas av många problem, men vanligt vis orsakas felet av ett tjänst avbrott eller ett konfigurations problem. Om du fortsätter att se det generiska felet när du försöker registrera SSPR igen kontaktar du [Microsoft Support](#contact-microsoft-support) för ytterligare hjälp.

## <a name="sspr-usage"></a>SSPR-användning

Om du eller dina användare har problem med att använda SSPR kan du läsa följande fel söknings scenarier och lösnings steg:

| Fel | Lösning |
| --- | --- |
| Katalogen är inte aktive rad för lösen ords återställning. | I Azure Portal ändrar du konfigurationen för **lösen ords återställning via självbetjäning** till *markerad* eller *alla* och väljer sedan **Spara**. |
| Användaren har ingen tilldelad Azure AD-licens. | Detta kan inträffa om du inte har en Azure AD-licens tilldelad till önskad användare. Om du vill tilldela en licens till administratörs kontot i fråga, följer du stegen för att [tilldela, verifiera och lösa problem med licenser](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| Katalogen är aktive rad för lösen ords återställning, men användaren saknar eller har en felaktig autentiseringsinformation. | Se till att användaren har korrekt formulerad kontakt information på filen i katalogen. Mer information finns i [data som används av lösen ords återställning via](howto-sspr-authenticationdata.md)självbetjäning i Azure AD. |
| Katalogen är aktive rad för lösen ords återställning, men användaren har bara en kontakt persons information på filen när principen är inställd på att kräva två verifierings metoder. | Se till att användaren har minst två korrekt konfigurerade kontakt metoder. Ett exempel är att ha både ett mobiltelefon nummer *och* ett telefonnummer till ett kontor. |
| Katalogen är aktive rad för lösen ords återställning och användaren är korrekt konfigurerad, men användaren kan inte kontaktas. | Detta kan bero på ett tillfälligt tjänst fel eller om det finns felaktiga kontakt data som inte kan identifieras korrekt. <br> <br> Om användaren väntar 10 sekunder visas en länk för att "försök igen" och "kontakta administratören". Om användaren väljer "försök igen" försöker den igen. Om användaren väljer "kontakta administratören" skickar den ett formulär-e-postmeddelande till de administratörer som begär att en lösen ords återställning ska utföras för det användar kontot. |
| Användaren får aldrig lösen ords återställning av SMS eller telefonsamtal. | Detta kan vara resultatet av ett felformaterat telefonnummer i katalogen. Kontrol lera att telefonnumret är i formatet "+ 1 4251234567". <br> <br>Lösen ords återställning stöder inte tillägg, även om du anger en i katalogen. Tilläggen tas bort innan anropet görs. Använd ett nummer utan tillägg eller integrera tillägget i telefonnumret i ditt PBX (Private Branch Exchange). |
| Användaren får aldrig e-postmeddelandet för lösen ords återställning. | Den vanligaste orsaken till det här problemet är att meddelandet avvisas av ett skräp post filter. Kontrol lera mappen skräp post, skräp post eller borttagna objekt för e-postmeddelandet. <br> <br> Kontrol lera också att användaren kontrollerar rätt e-postkonto som registrerats med SSPR. |
| Jag har angett en princip för lösen ords återställning, men när ett administratörs konto använder lösen ords återställning tillämpas inte principen. | Microsoft hanterar och styr principen för återställning av lösen ord för att säkerställa den högsta säkerhets nivån. |
| Användaren nekas ett försök att återställa lösen ordet för många gånger under en dag. | En funktion för automatisk begränsning används för att hindra användare från att försöka återställa sina lösen ord för många gånger under en kort tids period. Begränsningen sker i följande scenarier: <br><ul><li>Användaren försöker verifiera ett telefonnummer fem gånger om en timme.</li><li>Användaren försöker använda säkerhets frågornas grind fem gånger om en timme.</li><li>Användaren försöker återställa ett lösen ord för samma användar konto fem gånger om en timme.</li></ul>Om en användare stöter på det här problemet måste de vänta 24 timmar efter det senaste försöket. Användaren kan sedan återställa sitt lösen ord. |
| Användaren ser ett fel när de verifierar sitt telefonnummer. | Felet uppstår när det angivna telefonnumret inte matchar telefonnumret i filen. Se till att användaren anger hela telefonnumret, inklusive rikt nummer och landskod, när de försöker använda en telefonbaserad metod för lösen ords återställning. |
| Ett fel uppstod när begäran bearbetades. | Allmänna SSPR kan orsakas av många problem, men vanligt vis orsakas felet av ett tjänst avbrott eller ett konfigurations problem. Om du fortsätter att se det generiska felet när du försöker registrera SSPR igen [kontaktar du Microsoft Support](#contact-microsoft-support) för ytterligare hjälp. |
| Överträdelse av lokal princip | Lösen ordet uppfyller inte den lokala Active Directory lösen ords principen. Användaren måste definiera ett lösen ord som uppfyller komplexitets-eller hållfasthets kraven. |
| Lösen ordet är inte kompatibelt med en Fuzzy-princip | Det lösen ord som användes visas i [listan över blockerade lösen ord](./concept-password-ban-bad.md#how-are-passwords-evaluated) och kan inte användas. Användaren måste definiera ett lösen ord som uppfyller eller överskrider den blockerade lösen ords principen. |

## <a name="sspr-errors-that-a-user-might-see"></a>SSPR fel som en användare kan se

Följande fel och teknisk information kan visas för en användare som en del av SSPR-processen. Ofta är felet inte något som de kan lösa ut, eftersom funktionen SSPR måste aktive ras, konfigureras eller registreras för sitt konto.

Använd följande information för att förstå problemet och vad som behöver åtgärdas på Azure AD-klienten eller ett enskilt användar konto.

| Fel | Information | Teknisk information |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom din administratör har inaktiverat lösen ords återställning för din organisation. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta administratören och be dem att aktivera den här funktionen.<br /><br />Mer information finns i [hjälp, jag har glömt mitt Azure AD-lösenord](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions). | SSPR_0009: vi har upptäckt att lösen ords återställning inte har Aktiver ATS av administratören. Kontakta din administratör och be dem att aktivera återställning av lösen ord för din organisation. |
| WritebackNotEnabled = 10 |Du kan tyvärr inte återställa ditt lösen ord just nu eftersom administratören inte har aktiverat en nödvändig tjänst för din organisation. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta din administratör och be dem att kontrol lera din organisations konfiguration.<br /><br />Mer information om den här nödvändiga tjänsten finns i [Konfigurera tillbakaskrivning av lösen ord](./tutorial-enable-sspr-writeback.md). | SSPR_0010: vi har upptäckt att tillbakaskrivning av lösen ord inte har Aktiver ATS. Kontakta din administratör och be dem att aktivera tillbakaskrivning av lösen ord. |
| SsprNotEnabledInUserPolicy = 11 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom administratören inte har konfigurerat lösen ords återställning för din organisation. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta administratören och be dem att konfigurera lösen ords återställning.<br /><br />Mer information om konfiguration av lösen ords återställning finns i [snabb start: återställning av lösen ord för självbetjäning i Azure AD](./tutorial-enable-sspr.md). | SSPR_0011: din organisation har inte definierat någon princip för lösen ords återställning. Kontakta administratören och be dem att definiera en princip för lösen ords återställning. |
| UserNotLicensed = 12 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom de licenser som krävs saknas i din organisation. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta din administratör och be dem att kontrol lera licens tilldelningen.<br /><br />Läs mer om licensiering i [licens krav för återställning av lösen ord för Azure AD Self-Service](./concept-sspr-licensing.md). | SSPR_0012: din organisation har inte de nödvändiga licenser som krävs för att återställa lösen ordet. Kontakta din administratör och be dem att granska licens tilldelningarna. |
| UserNotMemberOfScopedAccessGroup = 13 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom din administratör inte har konfigurerat ditt konto för att använda lösen ords återställning. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta administratören och be dem att konfigurera ditt konto för lösen ords återställning.<br /><br />Mer information om konto konfigurationen för lösen ords återställning finns i [distribuera lösen ords återställning för användare](./howto-sspr-deployment.md). | SSPR_0013: du är inte medlem i en grupp som är aktive rad för lösen ords återställning. Kontakta din administratör och din begäran som ska läggas till i gruppen. |
| UserNotProperlyConfigured = 14 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom nödvändig information saknas i ditt konto. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta administratören och be dem att återställa ditt lösen ord åt dig. När du har åtkomst till ditt konto igen måste du registrera den information som krävs.<br /><br />Om du vill registrera information följer du stegen i artikeln [Registrera för självbetjäning för återställning av lösen ord](../user-help/active-directory-passwords-reset-register.md) . | SSPR_0014: ytterligare säkerhets information krävs för att återställa ditt lösen ord. Kontakta administratören och be dem att återställa ditt lösen ord om du vill fortsätta. När du har åtkomst till ditt konto kan du registrera ytterligare säkerhets information på https://aka.ms/ssprsetup . Din administratör kan lägga till ytterligare säkerhets information i ditt konto genom att följa stegen i [Ange och läsa autentiseringsdata för lösen ords återställning](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Vi kan tyvärr inte återställa ditt lösen ord just nu på grund av ett problem med konfigurationen av lösen ords återställning i organisationen. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta din administratör och be dem att undersöka. <br /><br />Eller<br /><br />Det går inte att återställa lösen ordet just nu på grund av ett problem med konfigurationen av lösen ords återställning i organisationen. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa problemet. Kontakta din administratör och be dem att undersöka.<br /><br />Mer information om det potentiella problemet finns i [Felsöka tillbakaskrivning av lösen ord](troubleshoot-sspr-writeback.md). | SSPR_0029: vi kan inte återställa lösen ordet på grund av ett fel i din lokala konfiguration. Kontakta din administratör och be dem att undersöka. |
| OnPremisesConnectivityError = 30 | Vi kan tyvärr inte återställa ditt lösen ord just nu på grund av anslutnings problem till din organisation. Det finns ingen åtgärd att ta just nu, men problemet kan lösas om du försöker igen senare. Kontakta administratören och be dem att undersöka om problemet kvarstår.<br /><br />Mer information om anslutnings problem finns i [Felsöka anslutningar för tillbakaskrivning av lösen ord](troubleshoot-sspr-writeback.md). | SSPR_0030: det går inte att återställa lösen ordet på grund av en dålig anslutning till din lokala miljö. Kontakta administratören och be dem att undersöka.|

## <a name="azure-ad-forums"></a>Azure AD-forum

Om du har allmänna frågor om Azure AD och lösen ords återställning via självbetjäning kan du be communityn om hjälp på [sidan Microsoft Q&en fråga för Azure Active Directory](/answers/topics/azure-active-directory.html). Medlemmar i communityn är tekniker, produkt chefer, MVP: er och andra IT-proffs.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

Om du inte hittar svaret på ett problem är våra support team alltid tillgängliga för att hjälpa dig.

För att du ska kunna hjälpa dig, ber vi dig att ange så mycket information som möjligt när du öppnar ett ärende. Den här informationen omfattar följande:

* **Allmän beskrivning av felet**: Vad är felet? Vad var det beteende som har märkts? Hur kan vi återskapa felet? Ange så mycket information som möjligt.
* **Sida**: vilken sida var du på när du noterade felet? Ta med URL: en om du kan och en skärm bild av sidan.
* **Support kod**: Vad var den support kod som genererades när användaren såg felet?
   * Du hittar den här koden genom att återskapa felet och sedan välja länken **support kod** längst ned på skärmen och skicka support teknikern till det GUID som det resulterar i.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="Support koden finns längst ned till höger i webbläsarfönstret.":::

  * Om du är på en sida utan support kod längst ned väljer du F12 och söker efter SID och CID och skickar dessa två resultat till support teknikern.
* **Datum, tid och** tidszon: inkludera det exakta datumet och tiden *med tids zonen* då felet inträffade.
* **Användar-ID**: Vem var den användare som såg felet? Ett exempel är *user \@ contoso.com*.
   * Är detta en federerad användare?
   * Är detta en direkt autentiserings användare?
   * Är detta en lösen ords-hash-synkroniserad användare?
   * Är det här en endast molnbaserad användare?
* **Licensiering**: har användaren en Azure AD-licens tilldelad?
* **Program händelse logg**: om du använder tillbakaskrivning av lösen ord och felet finns i den lokala infrastrukturen inkluderar du en zippad kopia av program händelse loggen från Azure AD Connect-servern.

## <a name="next-steps"></a>Nästa steg

Mer information om SSPR finns i [så här fungerar det: återställning av lösen](concept-sspr-howitworks.md) ord för självbetjäning i Azure AD eller [Hur fungerar tillbakaskrivning av lösen ord för självbetjänings återställning i Azure AD?](concept-sspr-writeback.md).
