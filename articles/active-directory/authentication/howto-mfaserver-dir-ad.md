---
title: Azure MFA Server och Active Directory-Azure Active Directory
description: Hur du integrerar Azure Multi-Factor Authentication-server med Active Directory så att du kan synkronisera katalogerna.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b3778ea68edf1fbbb41efb899749e6f35b39bae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742297"
---
# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Katalogintegrering mellan Azure MFA Server och Active Directory

Använd avsnittet Katalogintegrering i Azure MFA Server om du vill integrera med Active Directory eller en annan LDAP-katalog. Du kan konfigurera attribut för att matcha katalogschemat och ställa in automatisk användarsynkronisering.

> [!IMPORTANT]
> Från den 1 juli 2019 erbjuder Microsoft inte längre MFA-Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication (MFA) vid inloggnings händelser bör använda molnbaserad Azure AD-Multi-Factor Authentication.
>
> Information om hur du kommer igång med molnbaserad MFA finns i [Självstudier: skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Befintliga kunder som aktiverade MFA server före den 1 juli 2019 kan ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

## <a name="settings"></a>Inställningar

Som standard konfigureras Azure Multi-Factor Authentication (MFA) Server att importera eller synkronisera användare från Active Directory.  På fliken Katalogintegrering kan du åsidosätta standardbeteendet och binda till en annan LDAP-katalog, en ADAM-katalog eller en särskild Active Directory-domänkontrollant.  Fliken innehåller också inställningar för användning av LDAP-autentisering via proxy eller för LDAP-bindning som ett RADIUS-mål, förautentisering för IIS-autentisering eller primär autentisering för användarportalen.  De enskilda inställningarna beskrivs i följande tabell.

![Redigera LDAP-konfiguration i MFA Server](./media/howto-mfaserver-dir-ad/dirint.png)

> [!NOTE]
> Katalog integrering garanterar inte att fungera med andra kataloger än Active Directory Domain Services.

| Funktion | Beskrivning |
| --- | --- |
| Använd Active Directory |Välj alternativet Använd Active Directory om du vill använda Active Directory för import och synkronisering.  Det här är standardinställningen. <br>Obs! För att Active Directory-integreringen ska fungera korrekt ansluter du datorn till en domän och loggar in med ett domänkonto. |
| Inkludera betrodda domäner |Markera kryssrutan **Inkludera betrodda domäner** om du vill att agenten ska försöka ansluta till domäner som är betrodda av den aktuella domänen, en annan domän i skogen eller domäner som ingår i ett skogsförtroende.  Om du inte ska importera eller synkronisera användare från någon av de betrodda domänerna avmarkerar du kryssrutan för att förbättra prestanda.  Kryssrutan är markerad som standard. |
| Använd specifik LDAP-konfiguration |Välj alternativet Använd LDAP om du vill använda LDAP-inställningarna som angetts för import och synkronisering. Obs! Om du väljer Använd LDAP ändras referenserna i användargränssnittet från Active Directory till LDAP. |
| Knappen Redigera |Du kan använda knappen Redigera om du vill redigera de aktuella LDAP-inställningarna. |
| Använd attributbegränsade frågor |Anger om attributbegränsade frågor ska användas.  Med attributbegränsade frågor kan du göra effektiva katalogsökningar efter kvalificerade poster baserat på posterna i en annan posts attribut.  Azure Multi-Factor Authentication Server använder attributbegränsade frågor för att effektivt fråga användare som är medlemmar i en säkerhetsgrupp.   <br>Obs! Det finns vissa fall där attributbegränsade frågor stöds, men inte bör användas.  Active Directory kan exempelvis ha problem med attributbegränsade frågor när en säkerhetsgrupp innehåller medlemmar från mer än en domän. I detta fall avmarkerar du kryssrutan. |

LDAP-inställningarna beskrivs i följande tabell.

| Funktion | Beskrivning |
| --- | --- |
| Server |Ange värdnamnet eller IP-adressen för servern som kör LDAP-katalogen.  En sekundär server kan också anges, avgränsad med semikolon. <br>Obs: när bindnings typen är SSL (TLS) krävs ett fullständigt kvalificerat värdnamn. |
| Grundläggande unikt namn |Ange det unika namnet på baskatalogobjektet som alla katalogfrågor ska starta från.  Till exempel dc=abc,dc=com. |
| Bindningstyp – frågor |Välj lämplig bindningstyp som ska användas vid bindning för att söka i LDAP-katalogen.  Detta används för importer, synkronisering och matchning av användarnamn. <br><br>  Anonym – En anonym bindning utförs.  Unikt namn för bindning och Bindningslösenord används inte.  Detta fungerar bara om LDAP-katalogen tillåter anonym bindning och om behörigheterna tillåter frågor efter relevanta poster och attribut.  <br><br> Enkelt – Unikt namn för bindning och Bindningslösenord skickas som klartext för att binda till LDAP-katalogen.  Detta används för testningsändamål för att kontrollera att servern kan nås och att bindningskontot har rätt åtkomst. När lämpligt certifikat har installerats använder du SSL i stället.  <br><br> SSL – Unikt namn för bindning och Bindningslösenord krypteras med SSL för att binda till LDAP-katalogen.  Installera ett certifikat lokalt som LDAP-katalogen litar på.  <br><br> Windows – Bind användarnamn och Bindningslösenord används för att på ett säkert sätt ansluta till en Active Directory-domänkontrollant eller en ADAM-katalog.  Om Bind användarnamn lämnas tomt används det inloggade användarkontot för att binda. |
| Bindningstyp – autentiseringar |Välj lämplig bindningstyp som ska användas när LDAP-bindningsautentisering utförs.  Se beskrivningarna av bindningstyperna under Bindningstyp – frågor.  Detta gör till exempel att en anonym bindning kan användas för frågor medan SSL-bindning används för att skydda LDAP-bindningsautentiseringar. |
| Unikt namn för bindning eller Bind användarnamn |Ange det unika namnet för användarposten för kontot som ska användas vid bindningar till LDAP-katalogen.<br><br>Det unika namnet för bindning används endast när bindningstypen är Enkel eller SSL.  <br><br>Ange användarnamnet för Windows-kontot som ska användas vid bindningar till LDAP-katalogen när bindningstypen är Windows.  Om inget anges används den inloggade användarens konto för att binda. |
| Bindningslösenord |Ange bindningslösenordet för det unika namnet för bindning eller användarnamnet som används för att binda till LDAP-katalogen.  När du konfigurerar lösenordet för tjänsten Multi-Factor Auth Server AdSync aktiverar du synkronisering och kontrollerar att tjänsten körs på den lokala datorn.  Lösenordet sparas i Lagrade användarnamn och lösenord i Windows under det konto som tjänsten Multi-Factor Auth Server AdSync körs som.  Lösenordet sparas även under kontot som användargränssnittet för Multi-Factor Auth Server körs som och under kontot som tjänsten Multi-Factor Auth Server körs som.  <br><br>Eftersom lösenordet bara lagras i Lagrade användarnamn och lösenord i Windows på den lokala servern upprepar du detta steg på alla Multi-Factor Auth-servrar som behöver åtkomst till lösenordet. |
| Storleksgränsen för fråga |Ange storleksgränsen för det högsta antalet användare som en katalogsökning ska returnera.  Den här gränsen måste matcha LDAP-katalogens konfiguration.  För stora sökningar som inte stöder sidindelning försöker importerna och synkroniseringarna att hämta användare batchvis.  Om storleksgränsen som anges här är större än den gräns som konfigurerats för LDAP-katalogen kanske vissa användare missas. |
| Knappen Testa |Klicka på **Testa** för att testa bindningen till LDAP-servern.  <br><br>Du behöver inte välja alternativet **Använd LDAP** för att testa bindningen. Det betyder att du kan testa bindningen innan du använder LDAP-konfigurationen. |

## <a name="filters"></a>Filter

Med filter kan du ange villkor för att kvalificera poster när du utför en katalogsökning.  Genom att ange filtret kan du definiera de objekt som du vill synkronisera.  

![Konfigurera katalog filtrering i MFA Server](./media/howto-mfaserver-dir-ad/dirint2.png)

Azure Multi-Factor Authentication har följande tre filteralternativ:

* **Containerfilter** – Ange filtervillkoren som används för att kvalificera behållarposter när en katalogsökning utförs.  För Active Directory och ADAM används oftast (|(objectClass=organizationalUnit)(objectClass=container)).  För andra LDAP-kataloger använder du filtervillkoren som kvalificerar varje typ av containerobjekt, beroende på katalogschemat.  <br>Obs! Om det lämnas tomt används ((objectClass=organizationalUnit)(objectClass=container)) som standard.
* **Säkerhetsgruppfilter** – Ange filtervillkoren som används för att kvalificera säkerhetsposter när en katalogsökning utförs.  För Active Directory och ADAM används oftast (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)).  För andra LDAP-kataloger använder du filtervillkoren som kvalificerar varje typ av säkerhetsobjekt, beroende på katalogschemat.  <br>Obs! Om det lämnas tomt används (&(objectCategory=group)(groupType:1.2.840.113556.1.4.804:=-2147483648)) som standard.
* **Användarfilter** – Ange filtervillkoren som används för att kvalificera användarposter när en katalogsökning utförs.  För Active Directory och ADAM används oftast (&(objectClass=user)(objectCategory=person)).  För andra LDAP-kataloger använder du (objectClass=inetOrgPerson) eller liknande, beroende på katalogschemat. <br>Om det lämnas tomt används (&(objectCategory=person)(objectClass=user)) som standard.

## <a name="attributes"></a>Attribut

Du kan anpassa attributen efter behov för en viss katalog.  På så sätt kan du lägga till anpassade attribut och finjustera synkroniseringen till endast de attribut som du behöver. Använd namnet på attributet som det definieras i katalog schemat för värdet för varje attribut-fält. Följande tabell innehåller ytterligare information om varje funktion.

Attribut kan anges manuellt och behöver inte matcha ett attribut i attributlistan.

![Anpassa attribut för katalog integrering i MFA Server](./media/howto-mfaserver-dir-ad/dirint3.png)

| Funktion | Beskrivning |
| --- | --- |
| Unik identifierare |Ange attributnamnet för attributet som fungerar som den unika identifieraren för container-, säkerhetsgrupps- och användarposter.  I Active Directory är detta vanligtvis objectGUID. Andra LDAP-implementeringar kan använda entryUUID eller liknande.  Standardvärdet är objectGUID. |
| Typ av unik identifierare |Välj typen för attributet för unik identifierare.  I Active Directory har objectGUID-attributet typen GUID. Andra LDAP-implementeringar kan använda typen ASCII, bytematris eller Sträng.  Standardvärdet är GUID. <br><br>Det är viktigt att du anger den här typen korrekt eftersom de unika identifierarna används för att referera till synkroniseringsobjekt. Typen av unik identifierare används för att hitta objektet i katalogen.  Om du anger typen till Sträng trots att katalogen lagrar värdet som en bytematris (ByteArray) med ASCII-tecken så fungerar inte synkroniseringen korrekt. |
| Unikt namn |Ange attributnamnet för attributet som innehåller det unika namnet för varje post.  I Active Directory är detta normalt distinguishedName. Andra LDAP-implementeringar kan använda entryDN eller liknande.  Standardvärdet är distinguishedName. <br><br>Om det inte finns något attribut som bara innehåller det unika namnet kan attributet sökväg för annonser användas.  "LDAP:// \<server\> /"-delen av sökvägen tas bort automatiskt och lämnar bara det unika namnet på objektet. |
| Containerns namn |Ange attributnamnet för attributet som innehåller namnet i en containerpost.  Värdet för det här attributet visas i containerhierarkin när du importerar från Active Directory eller lägger till synkroniseringsobjekt.  Standardvärdet är name. <br><br>Om olika containrar använder olika attribut för sina namn använder du semikolon för att avgränsa flera attribut med containernamn.  Det första attributet för containernamn som hittas i ett containerobjekt används för att visa dess namn. |
| Namn på säkerhetsgrupp |Ange attributnamnet för attributet som innehåller namnet i en säkerhetsgruppspost.  Värdet för det här attributet visas i säkerhetsgruppslistan när du importerar från Active Directory eller lägger till synkroniseringsobjekt.  Standardvärdet är name. |
| Användarnamn |Ange attributnamnet för attributet som innehåller användarnamnet i en användarpost.  Värdet för det här attributet används som Multi-Factor Auth Server-användarnamnet.  Ett andra attribut kan anges som en reserv till det första.  Det andra attributet används endast om det första attributet inte innehåller ett värde för användaren.  Standardvärdena är userPrincipalName och sAMAccountName. |
| Förnamn |Ange attributnamnet för attributet som innehåller förnamnet i en användarpost.  Standardvärdet är givenName. |
| Efternamn |Ange attributnamnet för attributet som innehåller efternamnet i en användarpost.  Standardvärdet är sn. |
| E-postadress |Ange attributnamnet för attributet som innehåller e-postadressen i en användarpost.  E-postadressen används för att skicka välkomst- och uppdateringsmeddelanden till användaren via e-post.  Standardvärdet är mail. |
| Användargrupp |Ange attributnamnet för attributet som innehåller användargruppen i en användarpost.  Användargrupp kan användas för att filtrera användare i agenten och rapporter på Multi-Factor Auth Server-hanteringsportalen. |
| Beskrivning |Ange attributnamnet för attributet som innehåller beskrivningen i en användarpost.  Beskrivningen används endast för sökning.  Standardvärdet är description. |
| Telefonsamtalsspråk |Ange attributnamnet för attributet som innehåller kortnamnet för språket som ska användas för röstsamtal för användaren. |
| Textmeddelandespråk |Ange attributnamnet för attributet som innehåller kortnamnet för språket som ska användas för SMS-meddelanden för användaren. |
| Mobilappspråk |Ange attributnamnet för attributet som innehåller kortnamnet för språket som ska användas för textmeddelanden i telefonappar för användaren. |
| OATH-tokenspråk |Ange attributnamnet för attributet som innehåller kortnamnet för språket som ska användas för OATH-tokenmeddelanden för användaren. |
| Telefon, arbete |Ange attributnamnet för attributet som innehåller företagstelefonnumret i en användarpost.  Standardvärdet är telephoneNumber. |
| Telefon, hem |Ange attributnamnet för attributet som innehåller hemtelefonnumret i en användarpost.  Standardvärdet är homePhone. |
| Personsökare |Ange attributnamnet för attributet som innehåller personsökarnumret i en användarpost.  Standardvärdet är pager. |
| Mobiltelefon |Ange attributnamnet för attributet som innehåller mobiltelefonnumret i en användarpost.  Standardvärdet är mobile. |
| Fax |Ange attributnamnet för attributet som innehåller faxnumret i en användarpost.  Standardvärdet är facsimileTelephoneNumber. |
| IP-telefon |Ange attributnamnet för attributet som innehåller IP-telefonnumret i en användarpost.  Standardvärdet är ipPhone. |
| Anpassad |Ange attributnamnet för attributet som innehåller ett anpassat telefonnummer i en användarpost.  Standardvärdet är tomt. |
| Anknytning |Ange attributnamnet för attributet som innehåller anknytningsnumret i en användarpost.  Värdet för anknytningsfältet används endast som anknytningen till det primära telefonnumret.  Standardvärdet är tomt. <br><br>Om attributet Anknytning inte anges kan anknytningar tas med som en del av telefonattributet. I så fall lägger du till ”x” före anknytningen så att den tolkas korrekt.  I exempelnumret 555-123-4567 x890 tolkas 555-123-4567 som telefonnumret och 890 som anknytningen. |
| Knappen Återställ standardvärden |Klicka på **Återställ standardvärden** om du vill återställa standardvärdet för alla attribut.  Standardvärdena bör fungera korrekt med det vanliga Active Directory- eller ADAM-schemat. |

Om du vill redigera attribut klickar du på **Redigera** på fliken attribut.  Då visas ett fönster där du kan redigera attributen. Välj **...** bredvid valfritt attribut så öppnas ett fönster där du kan välja vilka attribut som ska visas.

![Redigera mappning av katalog-attribut i MFA Server](./media/howto-mfaserver-dir-ad/dirint4.png)

## <a name="synchronization"></a>Synkronisering

Synkronisering ser till att Azure MFA-databasen synkroniseras med användare i Active Directory eller en annan LDAP-katalog (Lightweight Directory Access Protocol). Processen påminner om när du importerar användare manuellt från Active Directory, men söker regelbundet efter ändringar i Active Directory-användare och säkerhetsgrupper som behöver bearbetas.  Synkronisering tar också bort eller inaktiverar användare som har tagits bort från en container, säkerhetsgrupp eller Active Directory.

Tjänsten Multi-Factor Auth ADSync är en Windows-tjänst som utför den periodiska avsökningen av Active Directory.  Detta ska inte förväxlas med Azure AD Sync eller Azure AD Connect.  Multi-Factor Auth ADSync bygger visserligen på en liknande kodbas, men är specifik för Azure Multi-Factor Authentication-servern.  Den installeras i stoppat läge och startas av Multi-Factor Auth  Server-tjänsten när den konfigurerats att köra.  Om du har en Multi-Factor Auth Server-konfiguration med flera servrar kan Multi-Factor Auth ADSync endast köras på en enskild server.

Tjänsten Multi-Factor Auth ADSync använder DirSync LDAP-servertillägget från Microsoft för att effektivt söka efter ändringar.  Den här DirSync-kontrollanroparen måste ha rättigheten ”Katalogtjänständringar” och den utökade åtkomsträttigheten DS-Replication-Get-Changes.  Som standard tilldelas dessa rättigheter till kontona Administratör och LocalSystem på domänkontrollanter.  Tjänsten Multi-Factor Auth AdSync är konfigurerad att köras som LocalSystem som standard.  Därför är det enklast att köra tjänsten på en domänkontrollant.  Om du konfigurerar tjänsten så att den alltid utför en fullständig synkronisering kan den köras som ett konto med lägre behörighet.  Detta är mindre effektivt, men kräver färre kontoprivilegier.

Om LDAP-katalogen har stöd för och är konfigurerad för DirSync fungerar avsökningen av ändringar i användar- och säkerhetsgrupper på samma sätt som med Active Directory.  Om LDAP-katalogen inte stöder DirSync-kontrollen utförs en fullständig synkronisering under varje cykel.

![Synkronisering av katalog objekt till MFA-Server](./media/howto-mfaserver-dir-ad/dirint5.png)

Följande tabell innehåller ytterligare information om inställningarna på fliken Synkronisering.

| Funktion | Beskrivning |
| --- | --- |
| Aktivera synkronisering med Active Directory |Om det här alternativet har valts avsöker tjänsten Multi-Factor Auth Server regelbundet Active Directory för att identifiera ändringar. <br><br>Obs! Minst ett synkroniseringsobjekt måste läggas till och Synkronisera nu måste köras innan Multi-Factor Auth Server- tjänsten börjar bearbeta ändringar. |
| Synkronisera var |Ange tidsintervallet som Multi-Factor Auth Server-tjänsten ska vänta mellan att göra avsökningar och bearbeta ändringar. <br><br> Obs! Det angivna intervallet är tiden mellan början av varje cykel.  Om tiden för att bearbeta ändringar överskrider intervallet startar tjänsten en ny avsökning direkt. |
| Ta bort användare som inte längre finns i Active Directory |Om du väljer det här alternativet bearbetar tjänsten Multi-Factor Auth Server tombstone-objekten för borttagna användare i Active Directory och tar bort relaterade Multi-Factor Auth Server-användare. |
| Utför alltid en fullständig synkronisering |Om du väljer det här alternativet utför tjänsten Multi-Factor Auth Server alltid en fullständig synkronisering.  Om alternativet är avmarkerat utför tjänsten Multi-Factor Auth Server en inkrementell synkronisering genom att endast fråga efter användare som har ändrats.  Alternativet är avmarkerat som standard. <br><br>Om alternativet är avmarkerat utför Azure MFA Server endast inkrementell synkronisering om katalogen stöder DirSync-kontrollen och om kontobindningen till katalogen har behörighet att köra inkrementella DirSync-frågor.  Om kontot inte har rätt behörighet eller om flera domäner omfattas av synkroniseringen utför Azure MFA Server en fullständig synkronisering. |
| Kräv administratörsgodkännande om fler än X användare ska inaktiveras eller tas bort |Synkroniseringsobjekt kan konfigureras att inaktivera eller ta bort användare som inte längre är medlemmar i objektets container eller säkerhetsgrupp.  Som ett skydd kan godkännande från en administratör krävas om antalet användare som ska inaktiveras eller tas bort överskrider ett tröskelvärde.  Om det här alternativet har valts krävs godkännande för det angivna tröskelvärdet.  Standardvärdet är 5 och intervallet är 1 till 999. <br><br> Godkännandet börjar med att ett e-postmeddelande skickas till administratören. E-postmeddelandet innehåller anvisningar för att granska och godkänna inaktiveringen och borttagningen av användare.  När användargränssnittet för Multi-Factor Auth Server startas uppmanas administratören att ge sitt godkännande. |

Med knappen **Synkronisera nu** kan du köra en fullständig synkronisering för de angivna synkroniseringsobjekten.  En fullständig synkronisering krävs om du lägger till, ändrar, tar bort eller ändrar ordning på synkroniseringsobjekt.  Det krävs också för att starta tjänsten Multi-Factor Auth AdSync eftersom det anger startpunkten då tjänsten ska söka efter inkrementella ändringar.  Om ändringar har gjorts i synkroniseringsobjekten men ingen fullständig synkronisering har utförts, uppmanas du att synkronisera nu.

Med knappen **Ta bort** kan administratören ta bort ett eller flera synkroniseringsobjekt från listan med synkroniseringsobjekt i Multi-Factor Auth Server.

> [!WARNING]
> När posten för ett synkroniseringsobjekt har tagits bort kan det inte återställas. Du måste lägga till posten för synkroniseringsobjektet igen om du tar bort den av misstag.

Synkroniseringsobjektet eller synkroniseringsobjekten har tagits bort från Multi-Factor Auth Server.  Tjänsten Multi-Factor Auth Server kommer inte längre att bearbeta synkroniseringsobjekten.

Med knapparna Flytta upp och Flytta ned kan administratören ändra ordning på synkroniseringsobjekten.  Ordningen är viktig eftersom samma användare kan vara medlem i mer än ett synkroniseringsobjekt (t.ex. en container och en säkerhetsgrupp).  Inställningarna som tillämpas på användaren under synkroniseringen kommer från det första synkroniseringsobjektet i listan som användaren är associerad med.  Därför bör synkroniseringsobjekten placeras i prioritetsordning.

> [!TIP]
> En fullständig synkronisering bör utföras när du har tagit bort synkroniseringsobjekt.  En fullständig synkronisering bör utföras när du har ändrat ordning på synkroniseringsobjekt.  Klicka på **Synkronisera nu** om du vill utföra en fullständig synkronisering.

## <a name="multi-factor-authentication-servers"></a>Multi-Factor Authentication servrar

Ytterligare Multi-Factor Authentication-servrar kan konfigureras för att fungera som en säkerhets kopierings-RADIUS-proxy, LDAP-proxy eller för IIS-autentisering. Synkroniseringskonfigurationen delas av alla agenter. Men endast en av dessa agenter kan ha Multi-Factor Authentication Server-tjänsten igång. På den här fliken kan du välja den Multi-Factor Authentication Server som ska aktive ras för synkronisering.

![Relaterade Multi-Factor Authentication-servrar](./media/howto-mfaserver-dir-ad/dirint6.png)
