---
title: Använda utökade grupper i Azure HPC-cache
description: Så här konfigurerar du katalog tjänster för klient åtkomst till lagrings mål i Azure HPC cache
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 03/15/2021
ms.author: v-erkel
ms.openlocfilehash: fd5dce0760953bf19c72e1a1062a9c03ffe861e7
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563391"
---
# <a name="configure-directory-services"></a>Konfigurera katalog tjänster

Med inställningarna för **katalog tjänster** kan Azure HPC-cachen använda en extern källa för att autentisera användare för åtkomst till backend-lagring.

Du kan behöva aktivera **utökade grupper** om arbets flödet inkluderar NFS-lagrings mål och klienter som är medlemmar i fler än 16 grupper.

När du har klickat på knappen för att aktivera utökade grupper måste du välja den källa som Azure HPC cache ska använda för att hämta autentiseringsuppgifter för användare och grupper.

* [Active Directory](#configure-active-directory) – Hämta autentiseringsuppgifter från en extern Active Directory-Server. Du kan inte använda Azure Active Directory för den här uppgiften.
* [Flat fil](#configure-file-download) – hämtning `/etc/group` och `/etc/passwd` filer från en nätverks plats.
* [LDAP](#configure-ldap) – Hämta autentiseringsuppgifter från en LDAP-kompatibel källa (Lightweight Directory Access Protocol).

> [!NOTE]
> Kontrol lera att din cache har åtkomst till dess grupp informations källa inifrån dess säkra under nätverk.<!-- + details/examples -->

Fältet för **hämtade användar namn** visar status för den senaste hämtningen av grupp information.

![skärm bild av sidan Inställningar för katalog tjänster på portalen med alternativet Ja valt för utökade grupper och den nedrullningsbara menyn med etiketten hämtnings källa öppen](media/directory-services-select-group-source.png)

## <a name="configure-active-directory"></a>Konfigurera Active Directory

I det här avsnittet beskrivs hur du konfigurerar cacheminnet för att hämta autentiseringsuppgifter för användare och grupper från en extern Active Directory-Server (AD).

Ange följande värden under **Active Directory-information**:

* **Primär DNS** – ange IP-adressen för en domän namn server som cachen kan använda för att matcha Ad-domännamnet.

* **Sekundär DNS** (valfritt) – ange adressen till den namnserver som ska användas om den primära servern inte är tillgänglig.

* **AD DNS-domännamn** – ange det fullständigt kvalificerade domän namnet för den AD-server som cachen ska anslutas till för att hämta autentiseringsuppgifterna.

* **Namn på cache-server (dator konto)** – ange det namn som ska tilldelas denna HPC-cache när den ansluter till AD-domänen. Ange ett namn som är lätt att identifiera som denna cache. Namnet kan innehålla upp till 15 tecken och kan innehålla versaler eller gemener, siffror och bindestreck (-).

I avsnittet **autentiseringsuppgifter** anger du ett AD-administratörs användar namn och lösen ord som Azure HPC-cachen kan använda för att få åtkomst till AD-servern. Den här informationen krypteras när den lagras och kan inte frågas.

Spara inställningarna genom att klicka på knappen överst på sidan.

![skärm bild av avsnittet hämtnings information med Active Directory värden ifyllda](media/group-download-details-ad.png)

## <a name="configure-file-download"></a>Konfigurera fil hämtning

De här värdena krävs om du vill hämta filer med din användar-och grupp information. Filerna måste vara i standard Linux/UNIX `/etc/group` och `/etc/passwrd` format.

* **URI för användar filen** – ange filens fullständiga URI `/etc/passwrd` .
* **Grupp fil-URI** – ange den fullständiga URI: n för `/etc/group` filen.

![skärm bild av avsnittet hämtnings information för en platt fil hämtning](media/group-download-details-file.png)

## <a name="configure-ldap"></a>Konfigurera LDAP

Fyll i dessa värden om du vill använda en icke-AD LDAP-källa för att hämta autentiseringsuppgifter för användare och grupper. Kontrol lera med LDAP-administratören om du behöver hjälp med dessa värden.

* **LDAP-server** – ange det fullständigt kvalificerade domän namnet eller IP-adressen för den LDAP-server som ska användas. <!-- only one, not up to 3 -->

* **LDAP-bas-DN** – ange det grundläggande unika namnet för LDAP-domänen i DN-format. Fråga LDAP-administratören om du inte känner till bas-DN.

Servern och Bas-DN är de enda inställningar som krävs för att göra LDAP-arbete, men ytterligare alternativ gör anslutningen säkrare.

![skärm bild av LDAP-konfigurationsfilen på sidan Inställningar för katalog tjänst Sidan](media/group-download-details-ldap.png)

I avsnittet **säker åtkomst** kan du aktivera kryptering och certifikat validering för LDAP-anslutningen. När du har klickat på **Ja** för att aktivera kryptering har du följande alternativ:

* **Verifiera certifikat** – när det här anges VERIFIERAs LDAP-serverns certifikat mot certifikat utfärdaren i URI-fältet nedan.

* **Ca-certifikat-URI** – ange sökvägen till det auktoritativa certifikatet. Detta kan vara en länk till ett CA-verifierat certifikat eller ett självsignerat certifikat. Det här fältet krävs för att använda inställningen för externt validerade certifikat.

* **Hämta certifikat automatiskt** – Välj **Ja** om du vill försöka hämta ett certifikat så snart du skickar de här inställningarna.

Fyll i avsnittet **autentiseringsuppgifter** om du vill använda statiska autentiseringsuppgifter för LDAP-säkerhet. Den här informationen krypteras när den lagras och kan inte frågas.

* **BIND DN** – ange det unika bindnings namnet som ska användas för att autentisera till LDAP-servern. (Använd DN-format.)
* **Bindnings lösen ord** – ange lösen ordet för BINDNINGENS unika namn.

## <a name="next-steps"></a>Nästa steg

* Läs mer om klient åtkomst i [montera Azure HPC-cachen](hpc-cache-mount.md)
* Om autentiseringsuppgifterna inte laddas ned korrekt kan du kontakta administratören för din källa med autentiseringsuppgifter. Öppna ett [support ärende](hpc-cache-support-ticket.md) om det behövs.
