---
title: Självstudie – konfigurera LDAPs för Azure Active Directory Domain Services | Microsoft Docs
description: I den här självstudien får du lära dig hur du konfigurerar säkra LDAP (Lightweight Directory Access Protocol) för en Azure Active Directory Domain Services hanterad domän.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 6da1d285440daa5d1d5a230905a77057728d4ae6
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256549"
---
# <a name="tutorial-configure-secure-ldap-for-an-azure-active-directory-domain-services-managed-domain"></a>Självstudie: Konfigurera säker LDAP för en Azure Active Directory Domain Services hanterad domän

För att kommunicera med din Azure Active Directory Domain Services (Azure AD DS)-hanterade domän används LDAP (Lightweight Directory Access Protocol). Som standard är LDAP-trafiken inte krypterad, vilket är ett säkerhets problem i många miljöer.

Med Azure AD DS kan du konfigurera den hanterade domänen för att använda säkra LDAP-protokoll (Lightweight Directory Access Protocol). När du använder säker LDAP krypteras trafiken. Säkert LDAP kallas även LDAP över Secure Sockets Layer (SSL)/Transport Layer Security (TLS).

Den här självstudien visar hur du konfigurerar LDAPs för en Azure AD DS-hanterad domän.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett digitalt certifikat för användning med Azure AD DS
> * Aktivera säker LDAP för Azure AD DS
> * Konfigurera säker LDAP för användning över det offentliga Internet
> * Bind och testa säker LDAP för en hanterad domän

Om du inte har någon Azure-prenumeration [skapar du ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande resurser och behörigheter:

* En aktiv Azure-prenumeration.
    * [Skapa ett konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)om du inte har någon Azure-prenumeration.
* En Azure Active Directory klient som är associerad med din prenumeration, antingen synkroniserad med en lokal katalog eller en katalog som endast är moln.
    * Om det behövs kan du [skapa en Azure Active Directory klient][create-azure-ad-tenant] eller [associera en Azure-prenumeration med ditt konto][associate-azure-ad-tenant].
* En Azure Active Directory Domain Services hanterad domän aktive rad och konfigurerad i Azure AD-klienten.
    * Om det behövs kan du [skapa och konfigurera en Azure Active Directory Domain Services hanterad domän][create-azure-ad-ds-instance].
* Verktyget *LDP.exe* installerat på datorn.
    * Om det behövs [installerar du verktyg för fjärrserveradministration (RSAT)][rsat] för *Active Directory Domain Services och LDAP*.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

I den här självstudien konfigurerar du säker LDAP för den hanterade domänen med hjälp av Azure Portal. Börja med att logga in på [Azure Portal](https://portal.azure.com)för att komma igång.

## <a name="create-a-certificate-for-secure-ldap"></a>Skapa ett certifikat för säker LDAP

Om du vill använda säker LDAP används ett digitalt certifikat för att kryptera kommunikationen. Det här digitala certifikatet tillämpas på din hanterade domän, och du kan använda verktyg som *LDP.exe* använda säker krypterad kommunikation vid frågor mot data. Det finns två sätt att skapa ett certifikat för säker LDAP-åtkomst till den hanterade domänen:

* Ett certifikat från en offentlig certifikat utfärdare (CA) eller en företags certifikat utfärdare.
    * Om din organisation hämtar certifikat från en offentlig certifikat utfärdare hämtar du det säkra LDAP-certifikatet från den offentliga certifikat utfärdaren. Om du använder en företags certifikat utfärdare i din organisation hämtar du det säkra LDAP-certifikatet från företags certifikat utfärdaren.
    * En offentlig certifikat utfärdare fungerar bara när du använder ett anpassat DNS-namn med din hanterade domän. Om DNS-domännamnet för den hanterade domänen slutar på *. onmicrosoft.com* kan du inte skapa ett digitalt certifikat för att skydda anslutningen till den här standard domänen. Microsoft äger *onmicrosoft.com* -domänen, så en offentlig certifikat utfärdare utfärdar inget certifikat. I det här scenariot skapar du ett självsignerat certifikat och använder det för att konfigurera säker LDAP.
* Ett självsignerat certifikat som du själv skapar.
    * Den här metoden är bra för test ändamål och är vad den här självstudien visar.

Det certifikat som du begär eller skapar måste uppfylla följande krav. Din hanterade domän stöter på problem om du aktiverar säker LDAP med ett ogiltigt certifikat:

* **Betrodd utfärdare** – certifikatet måste utfärdas av en utfärdare som är betrodd av datorer som ansluter till den hanterade domänen med hjälp av säker LDAP. Denna myndighet kan vara en offentlig certifikat utfärdare eller en företags certifikat utfärdare som är betrodd av dessa datorer.
* **Livs längd** -certifikatet måste vara giltigt under minst de kommande 3-6 månaderna. Säkert LDAP åtkomst till din hanterade domän avbryts när certifikatet upphör att gälla.
* **Ämnes namn** – ämnes namnet för certifikatet måste vara din hanterade domän. Om din domän till exempel heter *aaddscontoso.com*, måste certifikatets ämnes namn vara **. aaddscontoso.com*.
    * Det alternativa DNS-namnet eller det alternativa ämnes namnet för certifikatet måste vara ett certifikat med jokertecken för att säkerställa att det säkra LDAP fungerar korrekt med Azure AD Domain Services. Domänkontrollanter använder slumpmässiga namn och kan tas bort eller läggas till för att säkerställa att tjänsten är tillgänglig.
* **Nyckel användning** – certifikatet måste konfigureras för *digitala signaturer* och *nyckelchiffrering*.
* **Certifikat syfte** – certifikatet måste vara giltigt för TLS-serverautentisering.

Det finns flera tillgängliga verktyg för att skapa ett självsignerat certifikat som OpenSSL, knapp verktyg, MakeCert, [New-SelfSignedCertificate-][New-SelfSignedCertificate] cmdlet osv.

I den här självstudien ska vi skapa ett självsignerat certifikat för säker LDAP med cmdleten [New-SelfSignedCertificate][New-SelfSignedCertificate] .

Öppna ett PowerShell-fönster som **administratör** och kör följande kommandon. Ersätt *$dnsName* variabeln med DNS-namnet som används av din egen hanterade domän, till exempel *aaddscontoso.com*:

```powershell
# Define your own DNS name used by your managed domain
$dnsName="aaddscontoso.com"

# Get the current date to set a one-year expiration
$lifetime=Get-Date

# Create a self-signed certificate for use with Azure AD DS
New-SelfSignedCertificate -Subject *.$dnsName `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName
```

Följande exempel på utdata visar att certifikatet har skapats och är lagrat i det lokala certifikat arkivet (*LocalMachine\MY*):

```output
PS C:\WINDOWS\system32> New-SelfSignedCertificate -Subject *.$dnsName `
>>   -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
>>   -Type SSLServerAuthentication -DnsName *.$dnsName, $dnsName.com

   PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\MY

Thumbprint                                Subject
----------                                -------
959BD1531A1E674EB09E13BD8534B2C76A45B3E6  CN=aaddscontoso.com
```

## <a name="understand-and-export-required-certificates"></a>Förstå och exportera nödvändiga certifikat

Om du vill använda säker LDAP krypteras nätverks trafiken med PKI (Public Key Infrastructure).

* En **privat** nyckel tillämpas på den hanterade domänen.
    * Den privata nyckeln används för att *dekryptera* den säkra LDAP-trafiken. Den privata nyckeln bör endast tillämpas på den hanterade domänen och inte distribueras till klient datorer.
    * Ett certifikat som innehåller den privata nyckeln använder *. PFX* -filformat.
    * Krypteringsalgoritmen för certifikatet måste vara *TripleDES-SHA1*.
* En **offentlig** nyckel tillämpas på klient datorerna.
    * Den här offentliga nyckeln används för att *kryptera* den säkra LDAP-trafiken. Den offentliga nyckeln kan distribueras till klient datorer.
    * Certifikat utan privat nyckel använder *. CER* -filformat.

Dessa två nycklar, *privata* och *offentliga* nycklar, ser till att endast lämpliga datorer kan kommunicera med varandra. Om du använder en offentlig certifikat utfärdare eller en företags certifikat utfärdare, utfärdas ett certifikat som innehåller den privata nyckeln och kan tillämpas på en hanterad domän. Den offentliga nyckeln bör redan vara känd och betrodd av klient datorerna.

I den här självstudien har du skapat ett självsignerat certifikat med den privata nyckeln, så du måste exportera lämpliga privata och offentliga komponenter.

### <a name="export-a-certificate-for-azure-ad-ds"></a>Exportera ett certifikat för Azure AD DS

Innan du kan använda det digitala certifikatet som skapades i föregående steg med din hanterade domän, exportera certifikatet till en *. PFX* -certifikatfil som innehåller den privata nyckeln.

1. Öppna dialog rutan *Kör* genom att välja **Windows**  +  **R** -nycklar.
1. Öppna Microsoft Management Console (MMC) genom att ange **MMC** i dialog rutan *Kör* och välj sedan **OK**.
1. I rutan **User Account Control** väljer du **Ja** för att starta MMC som administratör.
1. I menyn **Arkiv** väljer du **Lägg till/ta bort snapin-modul...**
1. I **snapin-modulen certifikat** väljer du **dator konto** och väljer sedan **Nästa**.
1. På sidan **Välj dator** väljer du **lokal dator: (den dator den här konsolen körs på)** och väljer sedan **Slutför**.
1. I dialog rutan **Lägg till eller ta bort snapin-moduler** väljer du **OK** för att lägga till snapin-modulen certifikat i MMC.
1. Expandera **konsol rot** i MMC-fönstret. Välj **certifikat (lokal dator)** och expandera sedan den **personliga** noden, följt av noden **certifikat** .

    ![Öppna arkivet personliga certifikat i Microsoft Management Console](./media/tutorial-configure-ldaps/open-personal-store.png)

1. Det självsignerade certifikatet som skapades i föregående steg visas, till exempel *aaddscontoso.com*. Högerklicka på det här certifikatet och välj sedan **alla aktiviteter > exportera...**

    ![Exportera certifikat i Microsoft Management Console](./media/tutorial-configure-ldaps/export-cert.png)

1. I **guiden Exportera certifikat** väljer du **Nästa**.
1. Den privata nyckeln för certifikatet måste exporteras. Om den privata nyckeln inte ingår i det exporterade certifikatet, så Miss lyckas åtgärden att aktivera säker LDAP för din hanterade domän.

    På sidan **Exportera privat nyckel** väljer du **Ja, exportera den privata nyckeln** och väljer sedan **Nästa**.
1. Hanterade domäner stöder bara *.* Fil format för PFX-certifikat som innehåller den privata nyckeln. Exportera inte certifikatet som *. CER* -certifikatets fil format utan privat nyckel.

    På sidan **fil format för export** väljer du **personal information Exchange – PKCS #12 (. PFX)** som fil format för det exporterade certifikatet. Markera kryss rutan för att *Inkludera alla certifikat i certifierings Sök vägen om möjligt*:

    ![Välj alternativet för att exportera certifikatet i PKCS 12 (. PFX) fil format](./media/tutorial-configure-ldaps/export-cert-to-pfx.png)

1. Eftersom det här certifikatet används för att dekryptera data bör du kontrol lera åtkomsten noggrant. Ett lösen ord kan användas för att skydda användningen av certifikatet. Utan rätt lösen ord kan certifikatet inte tillämpas på en tjänst.

    På sidan **säkerhet** väljer du alternativet för **lösen ord** för att skydda *. PFX* -certifikatfil. Krypteringsalgoritmen måste vara *TripleDES-SHA1*. Ange och bekräfta ett lösen ord och välj sedan **Nästa**. Det här lösen ordet används i nästa avsnitt för att aktivera säker LDAP för din hanterade domän.
1. På sidan **fil som ska exporteras** anger du det fil namn och den plats där du vill exportera certifikatet, till exempel *C:\Users\accountname\azure-AD-DS.pfx*. Anteckna lösen ordet och platsen för *. PFX* -fil som denna information krävs i nästa steg.
1. På sidan Granska väljer du **Slutför** för att exportera certifikatet till en *. PFX* -certifikatfil. En bekräftelse dialog ruta visas när certifikatet har exporter ATS.
1. Lämna MMC öppet för användning i följande avsnitt.

### <a name="export-a-certificate-for-client-computers"></a>Exportera ett certifikat för klient datorer

Klient datorerna måste ha förtroende för utfärdaren av det säkra LDAP-certifikatet för att kunna ansluta till den hanterade domänen med hjälp av LDAP. Klient datorerna behöver ett certifikat för att kunna kryptera data som dekrypteras av Azure AD DS. Om du använder en offentlig certifikat utfärdare bör datorn automatiskt lita på dessa certifikat utfärdare och ha motsvarande certifikat.

I den här självstudien använder du ett självsignerat certifikat och genererade ett certifikat som innehåller den privata nyckeln i föregående steg. Nu ska vi exportera och sedan installera det självsignerade certifikatet i det betrodda certifikat arkivet på klient datorn:

1. Gå tillbaka till MMC för *certifikat (lokal dator) > personliga > certifikat* arkiv. Det självsignerade certifikatet som skapades i ett föregående steg visas, till exempel *aaddscontoso.com*. Högerklicka på det här certifikatet och välj sedan **alla aktiviteter > exportera...**
1. I **guiden Exportera certifikat** väljer du **Nästa**.
1. Eftersom du inte behöver den privata nyckeln för-klienter väljer du **Nej, exportera inte den privata nyckeln** på sidan **Exportera privat nyckel** och väljer sedan **Nästa**.
1. På sidan **fil format för export** väljer du **Base-64-kodad X. 509 (. CER)** som fil format för det exporterade certifikatet:

    ![Välj alternativet för att exportera certifikatet i Base-64-kodad X. 509 (. CER) fil format](./media/tutorial-configure-ldaps/export-cert-to-cer-file.png)

1. På sidan **fil som ska exporteras** anger du det fil namn och den plats där du vill exportera certifikatet, till exempel *C:\Users\accountname\azure-AD-DS-client.cer*.
1. På sidan Granska väljer du **Slutför** för att exportera certifikatet till en *. CER* -certifikatfil. En bekräftelse dialog ruta visas när certifikatet har exporter ATS.

*. CER* -certifikatfil kan nu distribueras till klient datorer som behöver lita på säker LDAP-anslutning till den hanterade domänen. Nu ska vi installera certifikatet på den lokala datorn.

1. Öppna Utforskaren och bläddra till den plats där du sparade filen *. CER* -certifikatfil, till exempel *C:\Users\accountname\azure-AD-DS-client.cer*.
1. Högerklicka på *. CER* -certifikatfil och välj sedan **Installera certifikat**.
1. I **guiden Importera certifikat** väljer du att lagra certifikatet på den *lokala datorn* och väljer sedan **Nästa**:

    ![Välj alternativet för att importera certifikatet till den lokala datorns Arkiv](./media/tutorial-configure-ldaps/import-cer-file.png)

1. När du uppmanas väljer du **Ja** så att datorn kan göra ändringar.
1. Välj att **välja certifikat Arkiv automatiskt baserat på certifikat typ** och välj sedan **Nästa**.
1. På sidan Granska väljer du **Slutför** för att importera *. CER* -certifikat. filen en bekräftelse dialog ruta visas när certifikatet har importer ATS.

## <a name="enable-secure-ldap-for-azure-ad-ds"></a>Aktivera säker LDAP för Azure AD DS

Med ett digitalt certifikat som har skapats och exporter ATS som innehåller den privata nyckeln, och klient datorn är inställd på att lita på anslutningen, aktiverar du nu säker LDAP på din hanterade domän. Utför följande konfigurations steg för att aktivera säker LDAP på en hanterad domän:

1. I [Azure Portal](https://portal.azure.com)anger du *domän tjänster* i rutan **Sök resurser** . Välj **Azure AD Domain Services** från Sök resultatet.
1. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. På vänster sida av Azure AD DS-fönstret väljer du **säkert LDAP**.
1. Som standard är säker LDAP-åtkomst till din hanterade domän inaktive rad. **Aktivera** genom att växla **säkert LDAP** .
1. Säkert LDAP åtkomst till din hanterade domän via Internet är inaktive rad som standard. När du aktiverar offentlig säker LDAP-åtkomst är din domän sårbar för angrepp av lösen ord i brutet skydd via Internet. I nästa steg konfigureras en nätverks säkerhets grupp för att låsa åtkomsten till de käll-IP-adressintervall som krävs.

    Växla **Tillåt säker LDAP-åtkomst över Internet** för att **Aktivera**.

1. Välj mappikonen bredvid **. PFX-fil med säkert LDAP-certifikat**. Bläddra till sökvägen till *. PFX* -fil och välj sedan det certifikat som skapades i ett föregående steg som innehåller den privata nyckeln.

    > [!IMPORTANT]
    > Som anges i föregående avsnitt om certifikat krav kan du inte använda ett certifikat från en offentlig certifikat utfärdare med default *. onmicrosoft.com* -domänen. Microsoft äger *onmicrosoft.com* -domänen, så en offentlig certifikat utfärdare utfärdar inget certifikat.
    >
    > Kontrol lera att certifikatet har rätt format. Om det inte är det genererar Azure-plattformen certifikat verifierings fel när du aktiverar säker LDAP.

1. Ange **lösen ordet för att dekryptera. PFX-fil** anges i föregående steg när certifikatet exporterades till en *. PFX* -fil.
1. Välj **Spara** för att aktivera säker LDAP.

    ![Aktivera säker LDAP för en hanterad domän i Azure Portal](./media/tutorial-configure-ldaps/enable-ldaps.png)

Ett meddelande visas om att säker LDAP konfigureras för den hanterade domänen. Du kan inte ändra andra inställningar för den hanterade domänen förrän åtgärden har slutförts.

Det tar några minuter att aktivera säker LDAP för din hanterade domän. Om det säkra LDAP-certifikatet som du anger inte uppfyller de krav som krävs kan åtgärden för att aktivera säker LDAP för den hanterade domänen Miss lyckas.

Några vanliga orsaker till fel är om domän namnet är felaktigt, krypteringsalgoritmen för certifikatet inte är *TripleDES-SHA1* eller om certifikatet upphör snart att gälla eller redan har gått ut. Du kan återskapa certifikatet med giltiga parametrar och sedan Aktivera säker LDAP med det uppdaterade certifikatet.

## <a name="change-an-expiring-certificate"></a>Ändra ett certifikat som upphör att gälla

1. Skapa ett nytt, säkert LDAP-certifikat genom att följa stegen för att [skapa ett certifikat för säker LDAP](#create-a-certificate-for-secure-ldap).
1. Om du vill tillämpa ersättnings certifikatet på Azure AD DS går du till den vänstra menyn för Azure AD DS i Azure Portal, väljer **säkert LDAP** och väljer sedan **ändra certifikat**.
1. Distribuera certifikatet till alla klienter som ansluter med hjälp av säker LDAP. 

## <a name="lock-down-secure-ldap-access-over-the-internet"></a>Lås säker LDAP-åtkomst via Internet

När du aktiverar säker LDAP-åtkomst över Internet till din hanterade domän, skapas ett säkerhetshot. Den hanterade domänen kan kommas åt från Internet på TCP-port 636. Vi rekommenderar att du begränsar åtkomsten till den hanterade domänen till vissa kända IP-adresser för din miljö. En regel för nätverks säkerhets grupper i Azure kan användas för att begränsa åtkomsten till säker LDAP.

Nu ska vi skapa en regel för att tillåta inkommande säker LDAP-åtkomst via TCP-port 636 från en angiven uppsättning IP-adresser. En standard regel för *denyall* med lägre prioritet gäller för all annan inkommande trafik från Internet, så bara de angivna adresserna kan nå din hanterade domän med hjälp av säker LDAP.

1. I Azure Portal väljer du *resurs grupper* i navigeringen till vänster.
1. Välj din resurs grupp, till exempel *myResourceGroup*, och välj sedan din nätverks säkerhets grupp, till exempel *aaads-NSG*.
1. Listan över befintliga inkommande och utgående säkerhets regler visas. Välj **inställningar > inkommande säkerhets regler** till vänster i fönstret nätverks säkerhets grupp.
1. Välj **Lägg till** och skapa sedan en regel för att tillåta *TCP* -port *636*. För förbättrad säkerhet väljer du källan som *IP-adresser* och anger sedan din egen giltiga IP-adress eller intervall för din organisation.

    | Inställning                           | Värde        |
    |-----------------------------------|--------------|
    | Källa                            | IP-adresser |
    | Käll-IP-adresser/CIDR-intervall | En giltig IP-adress eller ett giltigt intervall för din miljö |
    | Källportintervall                | *            |
    | Mål                       | Valfri          |
    | Målportintervall           | 636          |
    | Protokoll                          | TCP          |
    | Åtgärd                            | Tillåt        |
    | Prioritet                          | 401          |
    | Name                              | AllowLDAPS   |

1. När du är klar väljer du **Lägg till** för att spara och tillämpa regeln.

    ![Skapa en regel för nätverks säkerhets grupp för säker åtkomst via LDAP via Internet](./media/tutorial-configure-ldaps/create-inbound-nsg-rule-for-ldaps.png)

## <a name="configure-dns-zone-for-external-access"></a>Konfigurera DNS-zon för extern åtkomst

Med säker LDAP-åtkomst aktive rad via Internet uppdaterar du DNS-zonen så att klient datorerna kan hitta den här hanterade domänen. Den *säkert LDAP externa IP-adressen* visas på fliken **Egenskaper** för din hanterade domän:

![Visa den skyddade LDAP-externa IP-adressen för din hanterade domän i Azure Portal](./media/tutorial-configure-ldaps/ldaps-external-ip-address.png)

Konfigurera den externa DNS-providern för att skapa en värd post, till exempel *LDAPS*, för att matcha den externa IP-adressen. Du kan skapa en post i Windows hosts-filen för att testa lokalt på datorn först. För att kunna redigera hosts-filen på den lokala datorn öppnar du *anteckningar* som administratör och öppnar sedan filen *C:\Windows\System32\drivers\etc\hosts*

I följande exempel DNS-post, antingen med den externa DNS-providern eller i den lokala värd filen, löser trafik för *LDAPS.aaddscontoso.com* till den externa IP-adressen för *168.62.205.103*:

```
168.62.205.103    ldaps.aaddscontoso.com
```

## <a name="test-queries-to-the-managed-domain"></a>Testa frågor till den hanterade domänen

Om du vill ansluta och binda till din hanterade domän och söka via LDAP använder du *LDP.exe* -verktyget. Det här verktyget ingår i verktyg för fjärrserveradministration-paketet (RSAT). Mer information finns i [installera verktyg för fjärrserveradministration][rsat].

1. Öppna *LDP.exe* och Anslut till den hanterade domänen. Välj **anslutning**, välj sedan **Anslut...**.
1. Ange det säkra LDAP DNS-domännamnet för din hanterade domän som skapats i föregående steg, till exempel *LDAPS.aaddscontoso.com*. Om du vill använda säker LDAP ställer du in **port** på *636* och markerar sedan kryss rutan för **SSL**.
1. Välj **OK** för att ansluta till den hanterade domänen.

Bind sedan till din hanterade domän. Användare (och tjänst konton) kan inte utföra enkla LDAP-bindningar om du har inaktiverat NTLM-lösenord för hash-synkronisering på din hanterade domän. Mer information om hur du inaktiverar hash-synkronisering av NTLM-lösenord finns i [skydda din hanterade domän][secure-domain].

1. Välj meny alternativet **anslutning** och välj sedan **BIND...**.
1. Ange autentiseringsuppgifterna för ett användar konto som tillhör den hanterade domänen. Ange användar kontots lösen ord och ange sedan din domän, till exempel *aaddscontoso.com*.
1. För **bindnings typ** väljer du alternativet för *BIND med autentiseringsuppgifter*.
1. Välj **OK** för att binda till din hanterade domän.

För att se de objekt som lagras i din hanterade domän:

1. Välj meny alternativet **Visa** och välj sedan **träd**.
1. Lämna fältet *baserat* tomt och välj sedan **OK**.
1. Välj en behållare, till exempel *AADDC-användare*, högerklicka på behållaren och välj **Sök**.
1. Lämna de förifyllda fälten inställda och välj sedan **Kör**. Resultatet av frågan visas i den högra rutan, som du ser i följande exempel på utdata:

    ![Sök efter objekt i din hanterade domän med hjälp av LDP.exe](./media/tutorial-configure-ldaps/ldp-query.png)

Om du vill fråga efter en speciell behållare direkt från **trädvyn > träd** -menyn kan du ange en **baserad** , till exempel *OU = AADDC-användare, DC = AADDSCONTOSO, DC = com* eller *OU = AADDC Computers, DC = AADDSCONTOSO, DC = com*. Mer information om hur du formaterar och skapar frågor finns i [grunderna för LDAP-frågor][ldap-query-basics].

## <a name="clean-up-resources"></a>Rensa resurser

Om du har lagt till en DNS-post i den lokala värd filen på datorn för att testa anslutningen för den här självstudien tar du bort posten och lägger till en formell post i din DNS-zon. Utför följande steg för att ta bort posten från den lokala värd filen:

1. Öppna *anteckningar* som administratör på den lokala datorn
1. Bläddra till och öppna filen *C:\Windows\System32\drivers\etc\hosts*
1. Ta bort raden för den post som du har lagt till, till exempel `168.62.205.103    ldaps.aaddscontoso.com`

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett digitalt certifikat för användning med Azure AD DS
> * Aktivera säker LDAP för Azure AD DS
> * Konfigurera säker LDAP för användning över det offentliga Internet
> * Bind och testa säker LDAP för en hanterad domän

> [!div class="nextstepaction"]
> [Konfigurera hash-synkronisering av lösen ord för en hybrid Azure AD-miljö](tutorial-configure-password-hash-sync.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[secure-domain]: secure-your-domain.md

<!-- EXTERNAL LINKS -->
[rsat]: /windows-server/remote/remote-server-administration-tools
[ldap-query-basics]: /windows/desktop/ad/creating-a-query-filter
[New-SelfSignedCertificate]: /powershell/module/pkiclient/new-selfsignedcertificate
