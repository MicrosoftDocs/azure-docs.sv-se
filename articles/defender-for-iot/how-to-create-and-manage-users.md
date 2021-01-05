---
title: Skapa och hantera användare
description: Skapa och hantera användare av sensorer och den lokala hanterings konsolen. Användare kan tilldelas rollen administratör, säkerhet analytiker eller skrivskyddad användare.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/21/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: c3a9e1c7e96d0392e1f94b71549f612738622dea
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843340"
---
# <a name="about-defender-for-iot-console-users"></a>Om Defender för IoT Console-användare

Den här artikeln beskriver hur du skapar och hanterar användare av sensorer och den lokala hanterings konsolen. Användar roller är administratör, säkerhetsanalytiker eller skrivskyddad användare. Varje roll är associerad med en uppsättning behörigheter till verktyg för sensorn eller den lokala hanterings konsolen. Roller är utformade för att under lätta detaljerad och säker åtkomst till Azure Defender för IoT.

Funktioner är också tillgängliga för att spåra användar aktivitet och aktivera Active Directory inloggning.

Som standard installeras varje sensor och lokal hanterings konsol med en *Cyberx och en support* användare. Dessa användare har till gång till avancerade verktyg för fel sökning och installation. Administratörs användare ska logga in med dessa användarautentiseringsuppgifter, skapa en administratörs användare och sedan skapa ytterligare användare för säkerhetsanalytiker och skrivskyddade användare.

## <a name="role-based-permissions"></a>Rollbaserade behörigheter
Följande användar roller är tillgängliga:

- **Skrivskyddad**: skrivskyddade användare utför uppgifter som att visa aviseringar och enheter på enhets kartan. Dessa användare har åtkomst till alternativ som visas under **navigering**.

- **Säkerhetsanalytiker**: säkerhetsanalytiker har skrivskyddade användar behörigheter. De kan också utföra åtgärder på enheter, bekräfta aviseringar och använda verktyg för undersökning. Dessa användare har åtkomst till alternativ som visas under **navigering** och **analys**.

- **Administratör**: administratörer har åtkomst till alla verktyg, inklusive definiera systemkonfigurationer, skapa och hantera användare med mera. Dessa användare har åtkomst till alternativ som visas under **navigering**, **analys** och **Administration**.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Rollbaserade behörigheter till verktyg för lokal hanterings konsol

I det här avsnittet beskrivs behörigheter som är tillgängliga för administratörer, säkerhets analyser och skrivskyddade användare för den lokala hanterings konsolen.  

| Behörighet | Skrivskyddad | Säkerhetsanalytiker | Administratör |
|--|--|--|--|
| Visa och filtrera företags kartan | ✓ | ✓ | ✓ |
| Bygg en plats |  |  | ✓ |
| Hantera en plats (Lägg till och redigera zoner) |  |  | ✓ |
| Visa och filtrera enhets inventering | ✓ | ✓ | ✓ |
| Visa och hantera aviseringar: bekräfta, lära och fäst | ✓ | ✓ | ✓ |
| Skapa rapporter |  | ✓ | ✓ |
| Visa risk utvärderings rapporter |  | ✓ | ✓ |
| Ange aviserings undantag |  | ✓ | ✓ |
| Visa eller definiera åtkomst grupper |  |  | ✓ |
| Hantera systeminställningar |  |  | ✓ |
| Hantera användare |  |  | ✓ |
| Skicka aviserings data till partner |  |  | ✓ |
| Hantera certifikat |  |  | ✓ |
| Tids gräns för session när användare inte är aktiva | 30 minuter | 30 minuter  | 30 minuter  |

#### <a name="assign-users-to-access-groups"></a>Tilldela användare åtkomst till grupper

Administratörer kan förbättra användar åtkomst kontrollen i Defender för IoT genom att tilldela användare till vissa *åtkomst grupper*. Åtkomst grupper tilldelas zoner, platser, regioner och affär senheter där en sensor befinner sig. Genom att tilldela användare åtkomst grupper får administratörer en speciell kontroll över var användarna hanterar och analyserar enhets identifieringar. 

Om du arbetar på det här sättet kan du hantera stora organisationer där användar behörigheter kan vara komplexa eller avfattas av en global organisations säkerhets princip. Mer information finns i [definiera global åtkomst kontroll](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Rollbaserade behörigheter till sensor verktyg

I det här avsnittet beskrivs behörigheter som är tillgängliga för sensor administratörer, säkerhetsanalytiker och skrivskyddade användare.  

| Behörighet | Skrivskyddad | Säkerhetsanalytiker | Administratör |
|--|--|--|--|
| Visa instrumentpanelen | ✓ | ✓ | ✓ |
| Vyer för att kontrol lera kart zoomning |  |  | ✓ |
| Visa aviseringar | ✓ | ✓ | ✓ |
| Hantera aviseringar: bekräfta, lär och fäst |  | ✓ | ✓ |
| Visa händelser på en tids linje |  | ✓ | ✓ |
| Auktorisera enheter, kända skannings enheter, programmerings enheter |  | ✓ | ✓ |
| Visa gransknings data | ✓ | ✓ | ✓ |
| Hantera systeminställningar |  |  | ✓ |
| Hantera användare |  |  | ✓ |
| DNS-servrar för omvänd sökning |  |  | ✓ |
| Skicka aviserings data till partner |  | ✓ | ✓ |
| Skapa aviserings kommentarer |  | ✓ | ✓ |
| Visa program ändrings historik | ✓ | ✓ | ✓ |
| Skapa anpassade aviserings regler |  | ✓ | ✓ |
| Hantera flera meddelanden samtidigt |  | ✓ | ✓ |
| Hantera certifikat |  |  | ✓ |
| Tids gräns för session när användare inte är aktiva | 30 minuter | 30 minuter | 30 minuter |

## <a name="define-users"></a>Definiera användare

I det här avsnittet beskrivs hur du definierar användare. Cyberx-, support-och administratörs användare kan lägga till, ta bort och uppdatera andra användar definitioner.

Så här definierar du en användare:

1. I det vänstra fönstret för sensorn eller den lokala hanterings konsolen väljer **du användare**.
2. I fönstret **användare** väljer du **skapa användare**.
3. I fönstret **skapa användare** definierar du följande parametrar:

   - **Användar namn**: Ange ett användar namn.
   - **E-post**: Ange användarens e-postadress.
   - **Förnamn**: Ange användarens förnamn.
   - **Efter namn**: Ange användarens efter namn.
   - **Roll**: definiera användarens roll. Se [rollbaserade behörigheter](#role-based-permissions).
   - **Åtkomst grupp**: om du skapar en användare för den lokala hanterings konsolen definierar du användarens åtkomst grupp. Se [definiera global åtkomst kontroll](how-to-define-global-user-access-control.md).
   - **Lösen ord**: Välj användar typ enligt följande:
     - **Lokal användare**: definiera ett lösen ord för användaren av en sensor eller en lokal hanterings konsol. Lösen ordet måste innehålla minst sex tecken och måste innehålla bokstäver och siffror.
     - **Active Directory användare**: du kan tillåta att användare loggar in på sensorn eller hanterings konsolen genom att använda Active Directory autentiseringsuppgifter. Definierade Active Directory grupper kan associeras med vissa behörighets nivåer. Du kan till exempel konfigurera en speciell Active Directory grupp och tilldela alla användare i gruppen till den skrivskyddade användar typen.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Hantera dina användare.":::

## <a name="user-session-timeout"></a>Tids gräns för användarsession

Om användarna inte är aktiva på tangent bordet eller musen under en angiven tid loggas de ut från sessionen och måste logga in igen.

När användarna inte har arbetat med sin konsol mus eller tangent bord under en period på 30 minuter tvingas en utloggning av sessionen.

Den här funktionen är aktive rad som standard och vid uppgradering, men kan inaktive ras. Dessutom kan inventerings tider för sessioner uppdateras. Tiderna för sessionen definieras i sekunder. Definitioner tillämpas per sensor och lokal hanterings konsol.

Ett tids gräns meddelande för session visas i konsolen när tids gränsen för inaktivitet har passerats.

### <a name="control-inactivity-sign-out"></a>Kontrol lera utloggning av inaktivitet

Administratörs användare kan aktivera och Inaktivera utloggning av inaktivitet och justera inaktivitet.

För att få åtkomst till kommandot:

1. Logga in på CLI för sensorn eller den lokala hanterings konsolen genom att använda Defender för IoT-administratörsautentiseringsuppgifter.

2. Ange `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Ändra till om du vill inaktivera `infinity_session_expiration = true` funktionen `infinity_session_expiration = false` .

Om du vill uppdatera antalet utloggade perioder justerar du `= <number>` värdet till den begärda tiden.


## <a name="track-user-activity"></a>Spåra användar aktivitet 

Du kan spåra användar aktivitet i händelsens tids linje på varje sensor. Tids linjen visar händelsen eller den berörda enheten och den tid och det datum då användaren utförde aktiviteten.

Så här visar du användar aktivitet:

1. Logga in på sensorn.
1. I händelse tids linjen aktiverar du alternativet **användar åtgärder** . 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Visa en användares aktivitet.":::

## <a name="integrate-with-active-directory-servers"></a>Integrera med Active Directory-servrar 

Konfigurera sensorn eller den lokala hanterings konsolen så att den fungerar med Active Directory. Detta gör att Active Directory användare kan komma åt Defender för IoT-konsoler genom att använda sina Active Directory-autentiseringsuppgifter.

Två typer av LDAP-baserad autentisering stöds:

- **Fullständig autentisering**: användar information hämtas från LDAP-servern. Exempel är förnamn, efter namn, e-post och användar behörigheter.

- **Betrodd användare**: endast användar lösen ordet hämtas. Andra användar uppgifter som hämtas baseras på användare som definierats i sensorn.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Active Directory och Defender för IoT-behörigheter

Du kan associera Active Directory grupper som definierats här med vissa behörighets nivåer. Du kan till exempel konfigurera en speciell Active Directory grupp och tilldela RO-behörigheter till alla användare i gruppen. Mer information finns i [skapa och hantera användare](how-to-create-and-manage-users.md) .

Så här konfigurerar du Active Directory:

1. I den vänstra rutan väljer du **Systeminställningar**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Visa Active Directory Systeminställningar.":::

2. I fönstret **system inställningar** väljer du **Active Directory**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Redigera dina Active Directory-konfigurationer.":::

3. Välj **Active Directory integration Enabled** Save i dialog rutan **Redigera Active Directory konfiguration**  >  . Dialog rutan **redigera Active Directory konfiguration** expanderas och du kan nu ange de parametrar som ska konfigureras Active Directory.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Ange de parametrar som ska konfigureras Active Directory.":::

    > [!NOTE]
    > - Du måste definiera LDAP-parametrarna här exakt som de visas i Active Directory.
    > - Använd endast gemener för alla Active Directory parametrar. Använd gemener även när konfigurationerna i Active Directory använda versaler.
    > - Du kan inte konfigurera både LDAP och LDAP för samma domän. Du kan dock använda båda för olika domäner samtidigt.

4. Ange parametrarna för Active Directory servern enligt följande:

   | Server parameter | Beskrivning |
   |--|--|
   | Domänkontrollantens FQDN | Ange det fullständigt kvalificerade domän namnet (FQDN) precis som det visas på LDAP-servern. Ange till exempel `host1.subdomain.domain.com`. |
   | Port för domänkontrollant | Definiera porten som ditt LDAP har kon figurer ATS på. |
   | Primär domän | Ange domän namnet (till exempel `subdomain.domain.com` ) och anslutnings typen enligt din LDAP-konfiguration. |
   | Active Directory grupper | Ange de grupp namn som definieras i Active Directory-konfigurationen på LDAP-servern. |
   | Betrodda domäner | Lägg till en betrodd domän genom att lägga till domän namnet och anslutnings typen för en betrodd domän. <br />Du kan bara konfigurera betrodda domäner för användare som har definierats under användare. |

5. Välj **Spara**.

6. Om du vill lägga till en betrodd server väljer du **Lägg till Server** och konfigurerar en annan server.

## <a name="see-also"></a>Se även

[Aktivera och konfigurera din sensor](how-to-activate-and-set-up-your-sensor.md) 
 [Aktivera och konfigurera den lokala hanterings konsolen](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 [Spåra sensor aktivitet](how-to-track-sensor-activity.md)
