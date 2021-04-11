---
title: Nöd rotation av AD FS certifikaten | Microsoft Docs
description: Den här artikeln förklarar hur du återkallar och uppdaterar AD FS certifikat omedelbart.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 9035c0a91bbbd7493437c692540fcbb3136a094e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105613129"
---
# <a name="emergency-rotation-of-the-ad-fs-certificates"></a>Nöd rotation av AD FS certifikat
I händelse av att du behöver rotera AD FS certifikat omedelbart kan du följa stegen som beskrivs nedan i det här avsnittet.

> [!IMPORTANT]
> Genom att utföra stegen nedan i AD FSs miljön återkallar du de gamla certifikaten direkt.  Eftersom detta görs omedelbart, är den normala tiden som vanligt vis tillåts för Federations partner att använda det nya certifikatet som-skickas. Det kan resultera i ett tjänst avbrott när förtroendet har uppdaterats för att använda de nya certifikaten.  Detta bör lösa när alla Federations partner har de nya certifikaten.

> [!NOTE]
> Microsoft rekommenderar starkt att du använder en modul för maskin varu säkerhet (HSM) för att skydda och skydda certifikat.
> Mer information finns i [modulen för maskin varu säkerhet](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#hardware-security-module-hsm) under metod tips för att skydda AD FS.

## <a name="determine-your-token-signing-certificate-thumbprint"></a>Bestäm tumavtrycket för ditt token signerings certifikat
För att återkalla det gamla token signerings certifikat som AD FS för närvarande använder måste du bestämma tumavtrycket för sigining certifikat.  Det gör du genom att följa stegen nedan:

 1. Anslut till Microsoft Online-tjänsten `PS C:\>Connect-MsolService`
 2. Dokumentera både tumavtryck för ditt lokala och moln-token för signering och förfallo datum.
`PS C:\>Get-MsolFederationProperty -DomainName <domain>` 
 3.  Kopiera ned tumavtrycket.  Den kommer att användas senare för att ta bort befintliga certifikat.

Du kan också hämta tumavtrycket genom att använda AD FS hantering, navigera till tjänst/certifikat, högerklicka på certifikatet, välja Visa certifikat och sedan välja information. 

## <a name="determine-whether-ad-fs-renews-the-certificates-automatically"></a>Ta reda på om AD FS förnyar certifikaten automatiskt
Som standard är AD FS konfigurerad för att generera certifikat för Token-signering och token-dekryptering automatiskt, både vid den inledande konfigurations tiden och när certifikaten närmar sig sitt förfallo datum.

Du kan köra följande Windows PowerShell-kommando: `PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*` .

Egenskapen AutoCertificateRollover beskriver om AD FS har kon figurer ATS för att förnya Token-signering och token-dekryptering av certifikat automatiskt.  Om AutoCertificateRollover är inställt på Sant följer du instruktionerna som beskrivs nedan i [skapa ett nytt självsignerat certifikat om AutoCertificateRollover har angetts till TRUE].  Om AutoCertificateRollover har angetts till FALSe följer du instruktionerna som beskrivs nedan i [generera nya certifikat manuellt om AutoCertificateRollover har angetts till FALSe]


## <a name="generating-new-self-signed-certificate-if-autocertificaterollover-is-set-to-true"></a>Skapa ett nytt självsignerat certifikat om AutoCertificateRollover har angetts till TRUE
I det här avsnittet ska du skapa **två** certifikat för tokensignering.  Den första kommer att använda flaggan **-brådskande** , som kommer att ersätta det aktuella primära certifikatet omedelbart.  Den andra kommer att användas för det sekundära certifikatet.  

>[!IMPORTANT]
>Anledningen till att vi skapar två certifikat är att Azure innehåller information om det tidigare certifikatet.  Genom att skapa en andra, tvingar vi Azure att lansera information om det gamla certifikatet och ersätta det med information om det andra certifikatet.
>
>Om du inte skapar det andra certifikatet och uppdaterar Azure med det kan det vara möjligt för det gamla certifikatet för tokensignering att autentisera användare.

Du kan använda följande steg för att generera nya certifikat för tokensignering.

 1. Se till att du är inloggad på den primära AD FS servern.
 2. Öppna Windows PowerShell som administratör. 
 3. Kontrol lera att AutoCertificateRollover har angetts till true.
`PS C:\>Get-AdfsProperties | FL AutoCert*, Certificate*`
 4. Så här genererar du ett nytt certifikat för tokensignering: `Update-ADFSCertificate –CertificateType token-signing -Urgent` .
 5. Verifiera uppdateringen genom att köra följande kommando: `Get-ADFSCertificate –CertificateType token-signing`
 6. Generera nu det andra token signerings certifikat: `Update-ADFSCertificate –CertificateType token-signing` .
 7. Du kan kontrol lera uppdateringen genom att köra följande kommando igen: `Get-ADFSCertificate –CertificateType token-signing`


## <a name="generating-new-certificates-manually-if-autocertificaterollover-is-set-to-false"></a>Generera nya certifikat manuellt om AutoCertificateRollover har angetts till FALSe
Om du inte använder standardvärdet som skapas automatiskt, självsignerade Token signering och token-dekryptering, måste du förnya och konfigurera dessa certifikat manuellt.  Detta innebär att du skapar två nya certifikat för tokensignering och importerar dem.  Sedan höjer du en till primär, återkallar det gamla certifikatet och konfigurerar det andra certifikatet som sekundärt certifikat.

Först måste du skaffa två nya certifikat från certifikat utfärdaren och importera dem till den lokala datorns personliga certifikat Arkiv på varje Federations Server. Mer information finns i artikeln [Importera ett certifikat](https://technet.microsoft.com/library/cc754489.aspx) .

>[!IMPORTANT]
>Anledningen till att vi skapar två certifikat är att Azure innehåller information om det tidigare certifikatet.  Genom att skapa en andra, tvingar vi Azure att lansera information om det gamla certifikatet och ersätta det med information om det andra certifikatet.
>
>Om du inte skapar det andra certifikatet och uppdaterar Azure med det kan det vara möjligt för det gamla certifikatet för tokensignering att autentisera användare.

### <a name="to-configure-a-new-certificate-as-a-secondary-certificate"></a>Så här konfigurerar du ett nytt certifikat som ett sekundärt certifikat
Sedan måste du konfigurera ett certifikat som det sekundära AD FS token signering eller dekryptering av certifikatet och sedan befordra det till den primära

1. När du har importerat certifikatet. Öppna konsolen för **AD FS hantering** .
2. Expandera **tjänst** och välj sedan **certifikat**.
3. I rutan åtgärder klickar du på **Lägg till Token-Signing certifikat**.
4. Välj det nya certifikatet i listan med certifikat som visas och klicka sedan på OK.

### <a name="to-promote-the-new-certificate-from-secondary-to-primary"></a>För att befordra det nya certifikatet från sekundär till primär
Nu när det nya certifikatet har importer ATS och kon figurer ATS i AD FS måste du ange som primärt certifikat.
1. Öppna konsolen för **AD FS hantering** .
2. Expandera **tjänst** och välj sedan **certifikat**.
3. Klicka på certifikatet för sekundär Token-signering.
4. I rutan **åtgärder** klickar du på **Ange som primär**. Klicka på Ja när du uppmanas att bekräfta.
5. När du har uppgraderat det nya certifikatet som det primära certifikatet bör du ta bort det gamla certifikatet eftersom det fortfarande kan användas. Se avsnittet [ta bort dina gamla certifikat](#remove-your-old-certificates) nedan.  

### <a name="to-configure-the-second-certificate-as-a-secondary-certificate"></a>Så här konfigurerar du det andra certifikatet som ett sekundärt certifikat
Nu när du har lagt till det första certifikatet och gjort det till ett primärt och tagit bort det gamla, importerar du det andra certifikatet.  Sedan måste du konfigurera certifikatet som sekundärt AD FS token signerings certifikat

1. När du har importerat certifikatet. Öppna konsolen för **AD FS hantering** .
2. Expandera **tjänst** och välj sedan **certifikat**.
3. I rutan åtgärder klickar du på **Lägg till Token-Signing certifikat**.
4. Välj det nya certifikatet i listan med certifikat som visas och klicka sedan på OK.

## <a name="update-azure-ad-with-the-new-token-signing-certificate"></a>Uppdatera Azure AD med det nya certifikat för tokensignering
Öppna Microsoft Azure Active Directory-modul för Windows PowerShell. Du kan också öppna Windows PowerShell och köra kommandot `Import-Module msonline`

Anslut till Azure AD genom att köra följande kommando: `Connect-MsolService` och ange sedan dina autentiseringsuppgifter som global administratör.

>[!Note]
> Om du kör de här kommandona på en dator som inte är den primära Federations servern, anger du följande kommando först: `Set-MsolADFSContext –Computer <servername>` . Ersätt <servername> med namnet på AD FS servern. Ange sedan administratörsautentiseringsuppgifter för den AD FS servern när du uppmanas till det.

Du kan också kontrol lera om en uppdatering krävs genom att kontrol lera den aktuella certifikat informationen i Azure AD. Det gör du genom att köra följande kommando: `Get-MsolFederationProperty` . Ange namnet på den federerade domänen när du uppmanas till det.

Kör följande kommando för att uppdatera certifikat informationen i Azure AD: `Update-MsolFederatedDomain` och ange sedan domän namnet när du uppmanas till det.

>[!Note]
> Om du ser ett fel när du kör det här kommandot kör du följande kommando: `Update-MsolFederatedDomain –SupportMultipleDomain` och anger sedan domän namnet när du uppmanas till det.

## <a name="replace-ssl-certificates"></a>Ersätt SSL-certifikat
I händelse av att du behöver ersätta ditt certifikat för tokensignering på grund av en kompromiss bör du också återkalla och ersätta SSL-certifikaten för AD FS och dina WAP-servrar.  

Återkallande av SSL-certifikat måste göras hos den certifikat utfärdare (CA) som utfärdade certifikatet.  Dessa certifikat utfärdas ofta av leverantörer från tredje part, till exempel GoDaddy.  Ett exempel finns i (återkalla ett certifikat | SSL-certifikat – GoDaddy hjälper oss).  Mer information finns i [så här fungerar återkallade certifikat](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee619754(v=ws.10)?redirectedfrom=MSDN).

När det gamla SSL-certifikatet har återkallats och en ny som utfärdats, kan du ersätta SSL-certifikaten. Mer information finns i [ersätta SSL-certifikatet för AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs).


## <a name="remove-your-old-certificates"></a>Ta bort dina gamla certifikat
När du har ersatt dina gamla certifikat bör du ta bort det gamla certifikatet eftersom det fortfarande kan användas. . Det gör du genom att följa stegen nedan:.  Det gör du genom att följa stegen nedan:

1. Se till att du är inloggad på den primära AD FS servern.
2. Öppna Windows PowerShell som administratör. 
4. Ta bort det gamla token signerings certifikat: `Remove-ADFSCertificate –CertificateType token-signing -thumbprint <thumbprint>` .

## <a name="updating-federation-partners-who-can-consume-federation-metadata"></a>Uppdatera Federations partner som kan använda federationsmetadata
Om du har förnyat och konfigurerat ett nytt certifikat för tokensignering eller token-dekryptering, måste du se till att alla dina Federations partner (resurs organisation eller konto organisations partner som representeras i dina AD FS av förtroenden för förlitande part och anspråks leverantörs förtroenden) har påvisat de nya certifikaten.

## <a name="updating-federation-partners-who-can-not-consume-federation-metadata"></a>Uppdatera Federations partner som inte kan använda federationsmetadata
Om Federations partnern inte kan använda dina federationsmetadata måste du manuellt skicka dem den offentliga nyckeln för ditt nya certifikat för tokensignering/token-dekryptering. Skicka din nya offentliga certifikat nyckel (. cer-fil eller. p7b om du vill inkludera hela kedjan) till alla resurs organisationer eller konto organisations partner (som representeras i AD FS av förlitande part förtroenden och anspråks leverantörs förtroenden). Låta partners implementera ändringar på deras sida för att lita på de nya certifikaten.



## <a name="revoke-refresh-tokens-via-powershell"></a>Återkalla uppdateringstoken via PowerShell
Nu vill vi återkalla uppdateringstoken för användare som kan ha dem och tvinga dem att logga in igen och hämta nya token.  Detta kommer att logga användare från deras telefon, aktuella webbaserad e-postsessioner, tillsammans med andra objekt som använder tokens och uppdaterade tokens.  Information finns [här](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0&preserve-view=true) och du kan också referera till hur du kan [återkalla användar åtkomst i Azure Active Directory](../../active-directory/enterprise-users/users-revoke-access.md).

## <a name="next-steps"></a>Nästa steg

- [Hantera SSL-certifikat i AD FS och WAP i Windows Server 2016](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap#replacing-the-ssl-certificate-for-ad-fs)
- [Hämta och konfigurera Token signering och token dekryptering av certifikat för AD FS](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn781426(v=ws.11)#updating-federation-partners)
- [Förnya Federations certifikat för Microsoft 365 och Azure Active Directory](how-to-connect-fed-o365-certs.md)



















