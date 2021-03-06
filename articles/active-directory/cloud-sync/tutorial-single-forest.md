---
title: Självstudie – integrera en enda skog med en enda Azure AD-klient
description: I det här avsnittet beskrivs förutsättningarna och maskin varu kraven för moln synkronisering.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3bc9378539e6a7f98e34d0a149848d0e892c224
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306100"
---
# <a name="tutorial-integrate-a-single-forest-with-a-single-azure-ad-tenant"></a>Självstudie: integrera en enda skog med en enda Azure AD-klient

Den här självstudien vägleder dig genom att skapa en hybrid identitets miljö med Azure Active Directory (Azure AD) Connect Cloud Sync.

![Skapa](media/tutorial-single-forest/diagram-2.png)

Du kan använda den miljö som du skapar i den här självstudien för testning eller för att få bättre kunskaper om moln synkronisering.

## <a name="prerequisites"></a>Förutsättningar
### <a name="in-the-azure-active-directory-admin-center"></a>I Azure Active Directory administrations Center

1. Skapa ett globalt administratörs konto för molnet på Azure AD-klienten. På så sätt kan du hantera konfigurationen av din klient organisation om dina lokala tjänster kraschar eller blir otillgängliga. Lär dig mer om [att lägga till ett globalt administratörs konto för molnet](../fundamentals/add-users-azure-active-directory.md). Att slutföra det här steget är viktigt för att säkerställa att du inte blir utelåst från din klient.
2. Lägg till ett eller flera [anpassade domän namn](../fundamentals/add-custom-domain.md) i Azure AD-klienten. Användarna kan logga in med ett av dessa domän namn.

### <a name="in-your-on-premises-environment"></a>I din lokala miljö

1. Identifiera en domänansluten värd server som kör Windows Server 2012 R2 eller senare med minst 4 GB RAM-minne och .NET 4.7.1 + runtime 

2. Om det finns en brand vägg mellan dina servrar och Azure AD konfigurerar du följande objekt:
   - Se till att agenter kan göra *utgående* begär anden till Azure AD över följande portar:

     | Portnummer | Hur den används |
     | --- | --- |
     | **80** | Hämtar listor över återkallade certifikat (CRL) När TLS/SSL-certifikatet verifieras |
     | **443** | Hanterar all utgående kommunikation med tjänsten |
     | **8080** (valfritt) | Agenter rapporterar sin status var 10: e minut via port 8080, om port 443 inte är tillgänglig. Den här statusen visas i Azure AD-portalen. |
     
     Om brandväggen framtvingar regler baserat på de användare som genererar den öppnar du dessa portar för trafik från Windows-tjänster som körs som en nätverkstjänst.
   - Om din brand vägg eller proxy låter dig ange säkra suffix, lägger du till anslutningar t till **\* . msappproxy.net** och **\* . ServiceBus.Windows.net**. Om inte, Tillåt åtkomst till [Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653)som uppdateras varje vecka.
   - Dina agenter behöver åtkomst till **login.Windows.net** och **login.microsoftonline.com** för inledande registrering. Öppna brand väggen för dessa URL: er även.
   - För certifikat validering, avblockera följande URL: er: **mscrl.Microsoft.com:80**, **CRL.Microsoft.com:80**, **OCSP.msocsp.com:80** och **www \. Microsoft.com:80**. Eftersom dessa URL: er används för certifikat validering med andra Microsoft-produkter kan dessa URL: er vara avblockerade.

## <a name="install-the-azure-ad-connect-provisioning-agent"></a>Installera Azure AD Connect etablerings agenten
1. Logga in på den domänanslutna servern.  Om du använder självstudierna  [Basic A D och Azure-miljön](tutorial-basic-ad-azure.md) , är det DC1.
2. Logga in på Azure Portal med endast molnbaserad autentiseringsuppgifter för globala administratörer.
3. Till vänster väljer du **Azure Active Directory**, klickar på **Azure AD Connect** och i mitten väljer du **Hantera moln synkronisering**.

   ![Azure Portal](media/how-to-install/install-6.png)

4. Klicka på **Hämta agent**.
5. Kör Azure AD Connect etablerings agenten.
6. På Välkomst skärmen **godkänner** du licens villkoren och klickar på **Installera**.

   ![Skärm bild som visar välkomst skärmen "Microsoft Azure A D Connect upetablerings agent paket".](media/how-to-install/install-1.png)

7. När den här åtgärden har slutförts startas konfigurations guiden.  Logga in med ditt globala administratörs konto för Azure AD.  Observera att om du har aktiverat förbättrad säkerhet i Internet Explorer blockeras inloggningen.  Om så är fallet, Stäng installationen, inaktivera Förbättrad säkerhet i Internet Explorer i Serverhanteraren och klicka på **guiden AAD Connect etablerings agent** för att starta om installationen.
8. På skärmen **anslut Active Directory** klickar du på **Lägg till katalog** och loggar sedan in med ditt Active Directory domän administratörs konto.  Obs! domän administratörs kontot ska inte ha krav på lösen ords ändring. Om lösen ordet går ut eller ändras måste du konfigurera om agenten med de nya autentiseringsuppgifterna. Den här åtgärden lägger till din lokala katalog.  Klicka på **Nästa**.

   ![Skärm bild av skärmen "Anslut Active Directory".](media/how-to-install/install-3a.png)

9. Klicka på **Bekräfta** på sidan **konfiguration slutförd** .  Den här åtgärden registrerar och startar om agenten.

   ![Skärm bild som visar skärmen "konfigurationen har slutförts".](media/how-to-install/install-4a.png)

10. När den här åtgärden har slutförts bör du se ett meddelande: **din agent konfiguration har verifierats.**  Du kan klicka på **Avsluta**.</br>
![Välkomstskärmen](media/how-to-install/install-5.png)</br>
11. Om du fortfarande ser den inledande välkomst skärmen klickar du på **Stäng**.


## <a name="verify-agent-installation"></a>Verifiera agent installation
Agent verifiering sker i Azure Portal och på den lokala server som kör-agenten.

### <a name="azure-portal-agent-verification"></a>Azure Portal agent verifiering
Följ dessa steg för att kontrol lera att agenten visas i Azure:

1. Logga in på Azure-portalen.
2. Till vänster väljer du **Azure Active Directory**, klickar på **Azure AD Connect** och i mitten väljer du **hantera synkronisering av moln**.</br>
![Azure-portalen](media/how-to-install/install-6.png)</br>

3.  Klicka på **Granska alla agenter** på skärmen **Azure AD Connect Cloud Sync** .
![Azure A D-etablering](media/how-to-install/install-7.png)</br>
 
4. På **skärmen lokala etablerings agenter** visas de agenter som du har installerat.  Kontrol lera att agenten i fråga finns där och har marker ATS som **aktiv**.
![Etablerings agenter](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>På den lokala servern
Verifiera att agenten körs genom att följa dessa steg:

1.  Logga in på servern med ett administratörs konto
2.  Öppna **tjänster** genom att antingen navigera till den eller genom att gå till starta/köra/Services. msc.
3.  Under **tjänster** kontrollerar du att **Microsoft Azure AD ansluter agent uppdaterings** **agenten och Microsoft Azure AD ansluta etablerings agenten** finns och att statusen **körs**.
![Tjänster](media/how-to-install/troubleshoot-1.png)

## <a name="configure-azure-ad-connect-cloud-sync"></a>Konfigurera Azure AD Connect Cloud Sync
 Använd följande steg för att konfigurera etableringen

1.  Logga in på Azure AD-portalen.
2.  Klicka på **Azure Active Directory**
3.  Klicka på **Azure AD Connect**
4.  Välj **Hantera Cloud Sync**- 
 ![ skärm bild som visar länken hantera molnbaserad synkronisering.](media/how-to-configure/manage-1.png)
5.  Klicka på **ny konfiguration** 
 ![ skärm bild av sidan Azure A D Connect Cloud Sync med länken "ny konfiguration" markerad.](media/tutorial-single-forest/configure-1.png)
7.  På sidan konfiguration anger du ett **e-postmeddelande för aviseringar**, flyttar väljaren för att **Aktivera** och klickar på **Spara**.
![Skärm bild av konfigurera skärmen med e-postmeddelandet ifyllt och aktivera valt.](media/how-to-configure/configure-2.png)
1.  Konfigurations statusen bör nu vara **felfri**.
![Skärm bild av Azure A D Connect Cloud Sync skärm bild som visar felfri status.](media/how-to-configure/manage-4.png)

## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Verifiera att användare skapas och att synkronisering sker
Nu ska du kontrol lera att de användare som du hade i din lokala katalog har synkroniserats och nu finns i din Azure AD-klient.  Observera att det kan ta några timmar att slutföra.  För att verifiera att användarna synkroniseras gör du följande.


1. Bläddra till [Azure-portalen](https://portal.azure.com) och logga in med ett konto som har en Azure-prenumeration.
2. Välj **Azure Active Directory** till vänster
3. Under **Hantera** väljer du **Användare**.
4. Kontrol lera att du ser de nya användarna i din klient organisation</br>

## <a name="test-signing-in-with-one-of-your-users"></a>Testa att logga in med en av dina användare

1. Gå till [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Logga in med ett användar konto som har skapats i din klient organisation.  Du måste logga in med följande format: (user@domain.onmicrosoft.com). Använd samma lösenord som användaren använder för att logga in lokalt.</br>
   ![Verifiera](media/tutorial-single-forest/verify-1.png)</br>

Nu har du konfigurerat en hybrid identitets miljö med Azure AD Connect Cloud Sync.


## <a name="next-steps"></a>Nästa steg 

- [Vad är etablering?](what-is-provisioning.md)
- [Vad är Azure AD Connect-molnetablering?](what-is-cloud-sync.md)
