---
title: Hög tillgänglighet för Azure MFA Server – Azure Active Directory
description: Distribuera flera instanser av Azure Multi-Factor Authentication-server i konfigurationer som ger hög tillgänglighet.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1853a4784e3098ecbcefa94d5512b4877ac4dc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584484"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Konfigurera Azure Multi-Factor Authentication-server för hög tillgänglighet

För att uppnå hög tillgänglighet med din distribution av Azure Server MFA måste du distribuera flera MFA-servrar. Det här avsnittet innehåller information om en belastningsutjämnad design för att uppnå dina hög tillgänglighets mål i Azure MFS Server-distributionen.

> [!IMPORTANT]
> Från den 1 juli 2019 erbjuder Microsoft inte längre MFA-Server för nya distributioner. Nya kunder som vill kräva Multi-Factor Authentication (MFA) vid inloggnings händelser bör använda molnbaserad Azure AD-Multi-Factor Authentication.
>
> Information om hur du kommer igång med molnbaserad MFA finns i [Självstudier: skydda användar inloggnings händelser med Azure AD Multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Befintliga kunder som aktiverade MFA server före den 1 juli 2019 kan ladda ned den senaste versionen, framtida uppdateringar och generera autentiseringsuppgifter för aktivering som vanligt.

## <a name="mfa-server-overview"></a>Översikt över MFA-Server

Azure MFA Server-tjänstens arkitektur består av flera komponenter som visas i följande diagram:

 ![Arkitektur komponenter för MFA-Server](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

En MFA-Server är en Windows-Server som har Azure Multi-Factor Authentication-programmet installerat. MFA Server-instansen måste aktive ras av MFA-tjänsten i Azure för att fungera. Mer än en MFA-Server kan installeras lokalt.

Den första MFA-servern som installeras är den primära MFA-servern vid aktivering av Azure MFA-tjänsten som standard. Den primära MFA-servern har en skrivbar kopia av PhoneFactor. pfdata-databasen. Efterföljande installationer av instanser av MFA Server kallas underordnade. De underordnade MFA-instanserna har en replikerad skrivskyddad kopia av databasen PhoneFactor. pfdata. MFA-servrar replikerar information med hjälp av RPC (Remote Procedure Call). Alla MFA-servrar måste gemensamt antingen vara domänanslutna eller fristående för att replikera information.

Både MFA-primära och underordnade MFA-servrar kommunicerar med MFA-tjänsten när tvåfaktorautentisering krävs. Till exempel när en användare försöker få åtkomst till ett program som kräver tvåfaktorautentisering, autentiseras användaren först av en identitets leverantör, till exempel Active Directory (AD).

Efter en lyckad autentisering med AD kommer MFA-servern att kommunicera med MFA-tjänsten. MFA-servern väntar på meddelanden från MFA-tjänsten för att tillåta eller neka användar åtkomst till programmet.

Om den MFA-primära servern kopplas från, kan autentiseringarna fortfarande bearbetas, men åtgärder som kräver att det inte går att bearbeta MFA-databasen. (Exempel: tillägg av användare, självbetjänings-PIN-ändringar, ändring av användar information eller åtkomst till användar portalen)

## <a name="deployment"></a>Distribution

Tänk på följande viktiga punkter för belastnings utjämning av Azure MFA Server och dess relaterade komponenter.

* **Använder RADIUS-standarden för att uppnå hög tillgänglighet**. Om du använder Azure MFA-servrar som RADIUS-servrar kan du konfigurera en MFA-server som ett primärt RADIUS-autentiseringsschema och andra Azure MFA-servrar som sekundära autentiserings mål. Den här metoden för att uppnå hög tillgänglighet är dock inte praktisk eftersom du måste vänta tills en timeout-period inträffar när autentiseringen Miss lyckas på det primära målet innan du kan autentisera mot det sekundära autentiserings målet. Det är mer effektivt att belastningsutjämna trafiken mellan RADIUS-klienten och RADIUS-servrarna (i det här fallet Azure MFA-servrar som fungerar som RADIUS-servrar) så att du kan konfigurera RADIUS-klienterna med en enda URL som de kan peka på.
* **Du måste befordra MFA-underordnade manuellt**. Om den primära Azure MFA-servern är offline fortsätter de sekundära Azure MFA-servrarna att bearbeta MFA-begäranden. Men tills en primär MFA-Server är tillgänglig kan administratörer inte lägga till användare eller ändra MFA-inställningar och användarna kan inte göra ändringar med hjälp av användar portalen. Att befordra en MFA-underordnad till den primära rollen är alltid en manuell process.
* **Separability av komponenter**. Azure MFA-servern består av flera komponenter som kan installeras på samma Windows Server-instans eller på olika instanser. Dessa komponenter omfattar användar portalen, webb tjänsten Mobile app och ADFS adapter (agent). Den här separability gör det möjligt att använda webbprogramproxy för att publicera användar portalen och webb servern för mobilapp från perimeternätverket. En sådan konfiguration ökar den övergripande säkerheten för din design, som du ser i följande diagram. MFA-användargruppen och Mobile App-webbservern kan också distribueras i HA belastnings Utjämnings konfiguration.

   ![MFA server med ett perimeternätverk](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **Eng ång slö sen ord (eng ång slö sen ord) över SMS (även kallat enkelriktat SMS) kräver att tröga sessioner används om trafiken är** belastningsutjämnad. Enkelriktat SMS är ett autentiseringsalternativ som gör MFA-servern att skicka ett textmeddelande som innehåller ett eng ång slö sen ord. Användaren skriver in eng ång slö sen ord i ett prompt-fönster för att slutföra MFA-utmaningen. Om du belastningsutjämna Azure MFA-servrar måste samma server som hanterade den inledande autentiseringsbegäran vara den server som tar emot meddelandet för eng ång slö sen ord från användaren. om en annan MFA-server tar emot eng ång slö sen ord, Miss lyckas autentiseringen. Mer information finns i [eng ång slö sen ord via SMS tillagt i Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server).
* **Belastningsutjämnade distributioner av användar portalen och webb tjänsten Mobile App kräver tröga sessioner**. Om du läser in balansering av MFA-användargruppen och webb tjänsten Mobile app måste varje session vara kvar på samma server.

## <a name="high-availability-deployment"></a>Distribution med hög tillgänglighet

Följande diagram visar en fullständig belastnings bal anse rad implementering av Azure MFA och dess komponenter, tillsammans med ADFS för referens.

 ![Implementering av Azure MFA Server HA](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Observera följande objekt för motsvarande numrerade områden i föregående diagram.

1. De två Azure MFA-servrarna (MFA1 och MFA2) är belastningsutjämnade (mfaapp.contoso.com) och är konfigurerade för att använda en statisk port (4443) för att replikera PhoneFactor. pfdata-databasen. Webbtjänst-SDK: n installeras på varje MFA-Server för att aktivera kommunikation via TCP-port 443 med ADFS-servrarna. MFA-servrarna distribueras i en tillstånds lös belastningsutjämnad konfiguration. Om du vill använda eng ång slö sen ord via SMS måste du dock använda tillstånds känslig belastnings utjämning.
   ![Azure MFA Server – App Server HA](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Eftersom RPC använder dynamiska portar rekommenderar vi inte att du öppnar brand väggar upp till det intervall med dynamiska portar som RPC kan använda. Om du har en brand vägg **mellan** dina MFA-programservrar bör du Konfigurera MFA-servern så att den kommunicerar med en statisk port för replikeringstrafiken mellan underordnade och primära servrar och öppnar porten i brand väggen. Du kan tvinga den statiska porten genom att skapa ett DWORD-registervärde med ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` namnet ```Pfsvc_ncan_ip_tcp_port``` och ange värdet till en tillgänglig statisk port. Anslutningar initieras alltid av de underordnade MFA-servrarna till den primära, den statiska porten krävs bara på den primära porten, men eftersom du kan befordra en underordnad som primär när som helst ska du ange den statiska porten på alla MFA-servrar.

2. De två användar portalen/MFA Mobile App-servrarna (MFA-UP-MAS1 och MFA-UP-MAS2) är belastningsutjämnade i en **tillstånds känslig** konfiguration (MFA.contoso.com). Kom ihåg att tröga sessioner är ett krav för belastnings utjämning av MFA-användargränssnittet och mobila App Service.
   ![Azure MFA Server – användar Portal och Mobile App Service HA](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. AD FS-servergruppen är belastningsutjämnad och publiceras på Internet via belastningsutjämnade ADFS-proxyservrar i perimeternätverket. Varje ADFS-Server använder ADFS-agenten för att kommunicera med Azure MFA-servrarna med en enda belastningsutjämnad URL (mfaapp.contoso.com) via TCP-port 443.

## <a name="next-steps"></a>Nästa steg

* [Installera och konfigurera Azure MFA Server](howto-mfaserver-deploy.md)
