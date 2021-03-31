---
title: Azure AD Connect – uppdatera TLS/SSL-certifikatet för en AD FS-servergrupp | Microsoft Docs
description: Det här dokumentet innehåller information om hur du uppdaterar TLS/SSL-certifikatet för en AD FS server grupp med hjälp av Azure AD Connect.
services: active-directory
manager: daveba
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/09/2018
ms.subservice: hybrid
author: billmath
ms.custom: seohack1
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 451b50e70b98849dfc4654566d09a5a961abe451
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279915"
---
# <a name="update-the-tlsssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Uppdatera TLS/SSL-certifikatet för en Active Directory Federation Services (AD FS)-grupp (AD FS)

## <a name="overview"></a>Översikt
Den här artikeln beskriver hur du kan använda Azure AD Connect för att uppdatera TLS/SSL-certifikatet för en Active Directory Federation Services (AD FS) (AD FS) grupp. Du kan använda verktyget Azure AD Connect för att enkelt uppdatera TLS/SSL-certifikatet för AD FS-gruppen även om den valda användar inloggnings metoden inte är AD FS.

Du kan utföra hela driften av att uppdatera TLS/SSL-certifikat för AD FS server gruppen över alla Federations-och Webbprogramproxy-servrar (WAP) i tre enkla steg:

![Tre steg](./media/how-to-connect-fed-ssl-update/threesteps.png)


>[!NOTE]
>Om du vill veta mer om certifikat som används av AD FS, se [förstå certifikat som används av AD FS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc730660(v=ws.11)).

## <a name="prerequisites"></a>Förutsättningar

* **AD FS server grupp**: kontrol lera att AD FS server gruppen är Windows Server 2012 R2-baserad eller senare.
* **Azure AD Connect**: kontrol lera att versionen av Azure AD Connect är 1.1.553.0 eller högre. Du använder aktiviteten **uppdatera AD FS SSL-certifikat**.

![Uppdatera TLS-aktivitet](./media/how-to-connect-fed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Steg 1: Ange AD FS server grupps information

Azure AD Connect försöker hämta information om AD FS-gruppen automatiskt av:
1. Fråga Server grupps informationen från AD FS (Windows Server 2016 eller senare).
2. Att referera till informationen från tidigare körningar som lagras lokalt med Azure AD Connect.

Du kan ändra listan över servrar som visas genom att lägga till eller ta bort servrarna för att återspegla den aktuella konfigurationen av AD FS-servergruppen. Så snart som server informationen har angetts visar Azure AD Connect anslutningen och aktuell status för TLS/SSL-certifikatet.

![Information om AD FS server](./media/how-to-connect-fed-ssl-update/adfsserverinfo.png)

Om listan innehåller en server som inte längre är en del av AD FS server gruppen klickar du på **ta bort** för att ta bort servern från listan över servrar i AD FSS gruppen.

![Offline-server i listan](./media/how-to-connect-fed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Att ta bort en server från listan över servrar för en AD FS-servergrupp i Azure AD Connect är en lokal åtgärd och uppdaterar informationen för AD FS-gruppen som Azure AD Connect upprätthåller lokalt. Azure AD Connect ändrar inte konfigurationen på AD FS för att avspegla ändringen.    

## <a name="step-2-provide-a-new-tlsssl-certificate"></a>Steg 2: Ange ett nytt TLS/SSL-certifikat

När du har bekräftat informationen om AD FS server grupps servrar Azure AD Connect fråga efter det nya TLS/SSL-certifikatet. Ange ett lösenordsskyddat PFX-certifikat för att fortsätta installationen.

![TLS/SSL-certifikat](./media/how-to-connect-fed-ssl-update/certificate.png)

När du har angett certifikatet går Azure AD Connect igenom en serie med krav. Verifiera certifikatet för att kontrol lera att certifikatet är korrekt för AD FS server gruppen:

-   Ämnes namnet/det alternativa ämnes namnet för certifikatet är antingen detsamma som Federations tjänstens namn, eller så är det ett jokertecken.
-   Certifikatet är giltigt i mer än 30 dagar.
-   Certifikat förtroende kedjan är giltig.
-   Certifikatet är lösenordsskyddat.

## <a name="step-3-select-servers-for-the-update"></a>Steg 3: Välj servrar för uppdateringen

I nästa steg väljer du de servrar som ska ha TLS/SSL-certifikatet uppdaterat. Det går inte att välja frånkopplade servrar för uppdateringen.

![Välj servrar att uppdatera](./media/how-to-connect-fed-ssl-update/selectservers.png)

När du har slutfört konfigurationen visar Azure AD Connect meddelandet som anger status för uppdateringen och ger ett alternativ för att verifiera AD FS inloggning.

![Konfigurationen är klar](./media/how-to-connect-fed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Vanliga frågor och svar

* **Vad bör vara certifikatets ämnes namn för det nya AD FS TLS/SSL-certifikatet?**

    Azure AD Connect kontrollerar om certifikatets ämnes namn/alternativa ämnes namn innehåller Federations tjänstens namn. Om Federations tjänstens namn till exempel är fs.contoso.com måste ämnes namnet/det alternativa ämnes namnet vara fs.contoso.com.  Jokertecken godkänns också.

* **Varför uppmanas jag att ange autentiseringsuppgifter igen på WAP server-Sidan?**

    Om de autentiseringsuppgifter som du anger för att ansluta till AD FS-servrar inte också har behörighet att hantera WAP-servrarna, ber Azure AD Connect efter autentiseringsuppgifter som har administratörs behörighet på WAP-servrarna.

* **Servern visas som offline. Vad ska jag göra?**

    Azure AD Connect kan inte utföra någon åtgärd om servern är offline. Om servern är en del av AD FS server gruppen kontrollerar du anslutningen till servern. När du har löst problemet, trycker du på uppdaterings ikonen för att uppdatera statusen i guiden. Om servern var en del av Server gruppen tidigare men inte längre finns, klickar du på **ta bort** för att ta bort den från listan över servrar som Azure AD Connect underhåll. Om du tar bort servern från listan i Azure AD Connect ändras inte själva AD FS själva konfigurationen. Om du använder AD FS i Windows Server 2016 eller senare finns servern kvar i konfigurations inställningarna och visas igen nästa gång uppgiften körs.

* **Kan jag uppdatera en delmängd av mina Server grupp servrar med det nya TLS/SSL-certifikatet?**

    Ja. Du kan alltid köra aktiviteten **Uppdatera SSL-certifikatet** igen för att uppdatera de återstående servrarna. På sidan **Välj servrar för uppdatering av SSL-certifikat** kan du sortera listan över servrar vid **förfallo datum för SSL** för att enkelt komma åt de servrar som inte har uppdaterats än.

* **Jag har tagit bort servern i föregående körning, men den visas fortfarande som offline och visas på sidan AD FS servrar. Varför finns det kvar offline-servern trots att jag har tagit bort den?**

    Om du tar bort servern från listan i Azure AD Connect tas den inte bort i AD FS-konfigurationen. Azure AD Connect referenser AD FS (Windows Server 2016 eller senare) för all information om Server gruppen. Om servern fortfarande finns i AD FS-konfigurationen visas den i listan igen.  

## <a name="next-steps"></a>Nästa steg

- [Azure AD Connect och federation](how-to-connect-fed-whatis.md)
- [Active Directory Federation Services (AD FS) hantering och anpassning med Azure AD Connect](how-to-connect-fed-management.md)