---
title: Uppgradera Azure MFA Server – Azure Active Directory
description: Steg och vägledning för att uppgradera Azure-Multi-Factor Authentication-server till en nyare version.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/12/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbddd2eb52414827561d8896dfc8bc9ff705f41b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584399"
---
# <a name="upgrade-to-the-latest-azure-multi-factor-authentication-server"></a>Uppgradera till den senaste versionen av Azure Multi-Factor Authentication Server

Den här artikeln vägleder dig genom processen för att uppgradera Azure Multi-Factor Authentication (MFA) server v 6.0 eller senare. Om du behöver uppgradera en gammal version av PhoneFactor-agenten, se [Uppgradera phonefactor-agenten till Azure Multi-Factor Authentication-Server](howto-mfaserver-deploy-upgrade-pf.md).

Om du uppgraderar från v6. x eller äldre till v7. x eller senare ändras alla komponenter från .NET 2,0 till .NET 4,5. Alla komponenter kräver också Microsoft Visual C++ 2015 Redistributable Update 1 eller senare. MFA Server Installer installerar både x86-och x64-versionerna av dessa komponenter om de inte redan är installerade. Om användar portalen och webb tjänsten Mobile App körs på separata servrar måste du installera dessa paket innan du uppgraderar dessa komponenter. Du kan söka efter den senaste uppdateringen för Microsoft Visual C++ 2015 Redistributable på [Microsoft Download Center](https://www.microsoft.com/download/). 

> [!IMPORTANT]
> Från den 1 juli 2019 erbjuder Microsoft inte längre MFA-Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication (MFA) vid inloggnings händelser bör använda molnbaserad Azure AD-Multi-Factor Authentication.
>
> Information om hur du kommer igång med molnbaserad MFA finns i [Självstudier: skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Befintliga kunder som aktiverade MFA server före den 1 juli 2019 kan ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

Att snabbt uppgradera steg:

* Uppgradera Azure MFA-servrar (underordnade sedan primär)
* Uppgradera användar Portal instanserna
* Uppgradera AD FS korts instanser

## <a name="upgrade-azure-mfa-server"></a>Uppgradera Azure MFA Server

1. Använd anvisningarna i [Hämta Azure-Multi-Factor Authentication-Server](howto-mfaserver-deploy.md#download-the-mfa-server) för att hämta den senaste versionen av Azure MFA Server-installationsprogrammet.
2. Gör en säkerhets kopia av MFA Server-datafilen som finns i C:\Program Files\Multi-Factor Authentication Server\Data\PhoneFactor.pfdata (som förutsätter standard installations platsen) på den primära MFA-servern.
3. Om du kör flera servrar för hög tillgänglighet ändrar du klient systemen som autentiseras mot MFA-servern så att de slutar skicka trafik till de servrar som uppgraderas. Om du använder en belastningsutjämnare tar du bort en underordnad MFA-Server från belastningsutjämnaren, uppgraderar och lägger sedan till servern i Server gruppen igen.
4. Kör det nya installations programmet på varje MFA-Server. Uppgradera underordnade servrar först eftersom de kan läsa den gamla data filen som replikeras av den primära.

   > [!NOTE]
   > När du uppgraderar en server bör den tas bort från alla belastnings Utjämnings-eller trafik delning med andra MFA-servrar.
   >
   > Du behöver inte avinstallera din aktuella MFA-server innan du kör installations programmet. Installations programmet utför en uppgradering på plats. Installations Sök vägen hämtas från registret från den tidigare installationen, så den installeras på samma plats (t. ex. C:\Program Files\Multi-Factor Authentication Server).
  
5. Om du uppmanas att installera ett uppdaterings paket för Microsoft Visual C++ 2015 Redistributable accepterar du prompten. Både x86-och x64-versionerna av paketet är installerade.
6. Om du använder webbtjänst-SDK uppmanas du att installera den nya webbtjänst-SDK: n. När du installerar det nya webb tjänst-SDK: n måste du kontrol lera att namnet på den virtuella katalogen matchar den tidigare installerade virtuella katalogen (till exempel MultiFactorAuthWebServiceSdk).
7. Upprepa stegen på alla underordnade servrar. Flytta upp ett av de underordnade till den nya primära servern och uppgradera sedan den gamla primära servern.

## <a name="upgrade-the-user-portal"></a>Uppgradera användar portalen

Slutför uppgraderingen av MFA-servrarna innan du går vidare till det här avsnittet.

1. Gör en säkerhets kopia av den web.config-fil som finns i den virtuella katalogen på installations platsen för användar portalen (till exempel C:\inetpub\wwwroot\MultiFactorAuth). Om du har gjort ändringar i standard temat, gör du även en säkerhets kopia av mappen App_Themes \Default. Det är bättre att skapa en kopia av standardmappen och skapa ett nytt tema än att ändra standard temat.
2. Om användar portalen körs på samma server som andra MFA Server-komponenter, kommer MFA Server-installationen att begära att du uppdaterar användar portalen. Godkänn meddelandet och installera användar Portal uppdateringen. Kontrol lera att namnet på den virtuella katalogen matchar den tidigare installerade virtuella katalogen (till exempel MultiFactorAuth).
3. Om användar portalen finns på en egen server kopierar du MultiFactorAuthenticationUserPortalSetup64.msi-filen från installations platsen för en av MFA-servrarna och infogar den på webb servern för användar portalen. Kör installationsprogrammet.

   Om det uppstår ett fel som säger "Microsoft Visual C++ 2015 Redistributable Update 1 eller senare krävs" Ladda ned och installera det senaste uppdaterings paketet från [Microsoft Download Center](https://www.microsoft.com/download/). Installera både x86-och x64-versionerna.

4. När den uppdaterade användar Portal program varan har installerats, jämför web.config säkerhets kopia som du gjorde i steg 1 med den nya web.configs filen. Om det inte finns några nya attribut i den nya web.config kopierar du säkerhets kopian web.config till den virtuella katalogen för att skriva över den nya. Ett annat alternativ är att kopiera/klistra in appSettings-värdena och webbtjänst-SDK-URL: en från säkerhets kopian till den nya web.config.

Om du har användar portalen på flera servrar upprepar du installationen på alla.

## <a name="upgrade-the-mobile-app-web-service"></a>Uppgradera webb tjänsten Mobile App

> [!NOTE]
> När du uppgraderar från en version av Azure MFA server som är äldre än 8,0 till 8.0 +, kan webb tjänsten Mobile App avinstalleras efter uppgraderingen

## <a name="upgrade-the-ad-fs-adapters"></a>Uppgradera AD FS nätverkskort

Slutför uppgraderingen av MFA-servrarna och användar portalen innan du går vidare till det här avsnittet.

### <a name="if-mfa-runs-on-different-servers-than-ad-fs"></a>Om MFA körs på olika servrar än AD FS

Dessa anvisningar gäller endast om du kör Multi-Factor Authentication-server separat från dina AD FS-servrar. Om båda tjänsterna körs på samma servrar hoppar du över det här avsnittet och går till installations stegen. 

1. Spara en kopia av den MultiFactorAuthenticationAdfsAdapter.config-fil som har registrerats i AD FS eller exportera konfigurationen med följande PowerShell-kommando: `Export-AdfsAuthenticationProviderConfigurationData -Name [adapter name] -FilePath [path to config file]` . Kort namnet är antingen "WindowsAzureMultiFactorAuthentication" eller "AzureMfaServerAuthentication" beroende på vilken version som har installerats.
2. Kopiera följande filer från installations platsen för MFA-servern till AD FS-servrarna:

   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config

3. Redigera Register-MultiFactorAuthenticationAdfsAdapter.ps1-skriptet genom att lägga till `-ConfigurationFilePath [path]` i slutet av `Register-AdfsAuthenticationProvider` kommandot. Ersätt *[sökväg]* med den fullständiga sökvägen till MultiFactorAuthenticationAdfsAdapter.config-filen eller konfigurations filen som exporterades i föregående steg.

   Kontrol lera attributen i den nya MultiFactorAuthenticationAdfsAdapter.config för att se om de matchar den gamla konfigurations filen. Om några attribut har lagts till eller tagits bort i den nya versionen kopierar du attributvärdena från den gamla konfigurations filen till den nya eller ändrar den gamla konfigurations filen så att den matchar.

### <a name="install-new-ad-fs-adapters"></a>Installera nya AD FS kort

> [!IMPORTANT]
> Användarna kommer inte att behöva utföra tvåstegsverifiering under steg 3-8 i det här avsnittet. Om du AD FS konfigurerat i flera kluster kan du ta bort, uppgradera och återställa varje kluster i Server gruppen oberoende av de andra klustren för att undvika drift stopp.

1. Ta bort några AD FS-servrar från Server gruppen. Uppdatera servrarna medan de andra fortfarande körs.
2. Installera det nya AD FS kortet på varje server som har tagits bort från AD FS-servergruppen. Om MFA-servern är installerad på varje AD FS server kan du uppdatera via MFA server admin UX. Annars kan du uppdatera genom att köra MultiFactorAuthenticationAdfsAdapterSetup64.msi.

   Om det uppstår ett fel som säger "Microsoft Visual C++ 2015 Redistributable Update 1 eller senare krävs" Ladda ned och installera det senaste uppdaterings paketet från [Microsoft Download Center](https://www.microsoft.com/download/). Installera både x86-och x64-versionerna.

3. Gå till **AD FS**  >  **autentiseringsprinciper**  >  **Redigera globala** principer för multifaktorautentisering. Avmarkera **WindowsAzureMultiFactorAuthentication** eller **AzureMFAServerAuthentication** (beroende på vilken version som är installerad).

   När det här steget är slutfört är tvåstegsverifiering via MFA Server inte tillgängligt i det här AD FS klustret förrän du har slutfört steg 8.

4. Avregistrera den äldre versionen av AD FS adaptern genom att köra Unregister-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-skriptet. Se till att parametern *-Name* (antingen "WindowsAzureMultiFactorAuthentication" eller "AzureMFAServerAuthentication") matchar det namn som visades i steg 3. Detta gäller för alla servrar i samma AD FS kluster eftersom det finns en central konfiguration.
5. Registrera den nya AD FS adaptern genom att köra Register-MultiFactorAuthenticationAdfsAdapter.ps1 PowerShell-skriptet. Detta gäller för alla servrar i samma AD FS kluster eftersom det finns en central konfiguration.
6. Starta om AD FS tjänsten på varje server som har tagits bort från AD FS server gruppen.
7. Lägg tillbaka de uppdaterade servrarna i AD FS server gruppen och ta bort de andra servrarna från Server gruppen.
8. Gå till **AD FS**  >  **autentiseringsprinciper**  >  **Redigera globala** principer för multifaktorautentisering. Kontrol lera **AzureMfaServerAuthentication**.
9. Upprepa steg 2 för att uppdatera servrarna som nu har tagits bort från AD FS server gruppen och starta om AD FS tjänsten på dessa servrar.
10. Lägg tillbaka servrarna i AD FS-servergruppen.

## <a name="next-steps"></a>Nästa steg

* Få exempel på [avancerade scenarier med Azure Multi-Factor Authentication och VPN-nätverk från tredje part](howto-mfaserver-nps-vpn.md)

* [Synkronisera MFA server med Windows Server Active Directory](howto-mfaserver-dir-ad.md)

* [Konfigurera Windows-autentisering](howto-mfaserver-windows.md) för dina program
