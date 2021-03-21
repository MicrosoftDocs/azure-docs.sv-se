---
title: Använd dina egna certifikat med Azure Data Box/Azure Data Box Heavy-enheter
description: Hur du använder dina egna certifikat för att komma åt det lokala webb gränssnittet och blogg lagringen på Data Box-enhet eller Data Box Heavy enheten.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/11/2020
ms.author: alkohli
ms.openlocfilehash: 1836ed57305fd7e168961eb81670b56d4ce296cd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545181"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Använd dina egna certifikat med Data Box-enhet och Data Box Heavy enheter

Under order bearbetningen skapas självsignerade certifikat för åtkomst till det lokala webb gränssnittet och Blob Storage för en Data Box-enhet-eller Data Box Heavy-enhet. Om du hellre vill kommunicera med din enhet via en betrodd kanal kan du använda dina egna certifikat.

I artikeln beskrivs hur du installerar dina egna certifikat och hur du återställer till standard certifikaten innan du återställer enheten till data centret. Det ger också en översikt över certifikat krav.

## <a name="about-certificates"></a>Om certifikat

Ett certifikat tillhandahåller en länk mellan en **offentlig nyckel** och en entitet (till exempel domän namn) som har **signerats** (verifierats) av en betrodd tredje part, till exempel en **certifikat utfärdare**.  Ett certifikat är ett bekvämt sätt att distribuera betrodda offentliga krypterings nycklar. På det här sättet ser certifikat till att kommunikationen är betrodd och du skickar krypterad information till rätt server.

När din Data Box-enhet enhet konfigureras från början skapas automatiskt självsignerade certifikat. Du kan också ta med dina egna certifikat. Det finns rikt linjer som du måste följa om du planerar att ta med dina egna certifikat.

På en Data Box-enhet-eller Data Box Heavys enhet används två typer av slut punkts certifikat:

- Blob Storage-certifikat
- Lokalt UI-certifikat

### <a name="certificate-requirements"></a>Certifikatkrav

Certifikaten måste uppfylla följande krav:

- Slut punkts certifikatet måste ha `.pfx` ett format med en privat nyckel som kan exporteras.
- Du kan använda ett enskilt certifikat för varje slut punkt, ett certifikat för flera domäner för flera slut punkter eller ett slut punkts certifikat med jokertecken.
- Egenskaperna för ett slut punkts certifikat liknar egenskaperna för ett typiskt SSL-certifikat.
- Ett motsvarande certifikat i DER-format ( `.cer` fil namns tillägg) krävs på klient datorn.
- När du har laddat upp det lokala UI-certifikatet måste du starta om webbläsaren och rensa cacheminnet. Läs om vilka anvisningar som gäller för din webbläsare.
- Certifikaten måste ändras om enhets namnet eller DNS-domännamnet ändras.
- Använd följande tabell när du skapar slut punkts certifikat:

  |Typ |Ämnes namn (SN)  |Alternativt namn för certifikat mottagare (SAN)  |Exempel på ämnes namn |
  |---------|---------|---------|---------|
  |Lokalt användar gränssnitt| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob Storage|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Enskilt SAN-certifikat med flera SAN|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

Mer information finns i [certifikat krav](../../articles/databox-online/azure-stack-edge-gpu-certificate-requirements.md).

## <a name="add-certificates-to-device"></a>Lägg till certifikat i enheten

Du kan använda dina egna certifikat för att komma åt det lokala webb gränssnittet och för att komma åt Blob Storage.

> [!IMPORTANT]
> Om enhets namnet eller DNS-domänen ändras måste nya certifikat skapas. Klient certifikaten och enhets certifikaten bör sedan uppdateras med det nya enhets namnet och DNS-domänen.

Följ dessa steg om du vill lägga till ditt eget certifikat på enheten:

1. Gå till **Hantera**  >  **certifikat**.

   **Namn** visar enhetens namn. **DNS-domän** visar DNS-serverns domän namn.

   Längst ned på skärmen visas de certifikat som används för närvarande. För en ny enhet ser du de självsignerade certifikat som genererades under order bearbetning.

   ![Sidan certifikat för en Data Box-enhet enhet](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. Om du behöver ändra **namnet** (enhets namn) eller **DNS-domän** (domänen för DNS-servern för enheten) gör du det nu innan du lägger till certifikatet. Välj sedan **Använd**.

   Certifikatet måste ändras om enhets namnet eller DNS-domännamnet ändras.

   ![Använd ett nytt enhets namn och en DNS-domän för en Data Box-enhet](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Om du vill lägga till ett certifikat väljer du **Lägg till certifikat** för att öppna panelen **Lägg till certifikat** . Välj sedan **certifikat typ** – antingen **Blob Storage** eller **lokalt webb gränssnitt**.

   ![Panelen Lägg till certifikat på sidan certifikat för en Data Box-enhet enhet](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Välj certifikat filen (i `.pfx` formatet) och ange lösen ordet som angavs när certifikatet exporterades. Välj sedan **verifiera & Lägg till**.

   ![Inställningar för att lägga till ett BLOB-slutpunkts certifikat till en Data Box-enhet](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   När certifikatet har lagts till visar sidan **certifikat** tumavtrycket för det nya certifikatet. Certifikatets status är **giltig**.

   ![Ett giltigt nytt certifikat som har lagts till](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. Om du vill se certifikat information markerar du och klickar på certifikatets namn. Certifikatet upphör att gälla efter ett år.

   ![Visa certifikat information för en Data Box-enhet enhet](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Om du har ändrat certifikatet för det lokala webb gränssnittet måste du starta om webbläsaren och sedan det lokala webb gränssnittet. Det här steget krävs för att undvika SSL-cache-problem.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

7. Installera det nya certifikatet på klient datorn som du använder för att få åtkomst till det lokala webb gränssnittet. Instruktioner finns i [Importera certifikat till klienten](#import-certificates-to-client)nedan.


## <a name="import-certificates-to-client"></a>Importera certifikat till klienten

När du har lagt till ett certifikat till din Data Box-enhet-enhet måste du importera certifikatet till den klient dator som du använder för att få åtkomst till enheten. Du importerar certifikatet till arkivet för betrodda rot certifikat utfärdare för den lokala datorn.

Följ dessa steg om du vill importera ett certifikat på en Windows-klient:

1. I Utforskaren högerklickar du på certifikat filen (med `.cer` format) och väljer **Installera certifikat**. Den här åtgärden startar guiden Importera certifikat.

    ![Importera certifikat 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. För **lagrings plats** väljer du **lokal dator** och väljer sedan **Nästa**.

    ![Välj lokal dator som lagrings plats i guiden Importera certifikat](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. Välj **Placera alla certifikat i följande Arkiv**, Välj **betrodd rot certifikat utfärdare** och välj sedan **Nästa**.

   ![Välj arkivet Betrodda rot certifikat utfärdare i guiden Importera certifikat](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Granska inställningarna och välj **Slutför**. Ett meddelande anger att importen har slutförts.

   ![Granska dina certifikat inställningar och Slutför guiden Importera certifikat](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Återgå till standard certifikat

Innan du returnerar enheten till Azure-datacentret bör du återgå till de ursprungliga certifikaten som genererades under order bearbetningen.

Följ dessa steg om du vill återgå till de certifikat som genereras under order bearbetning:

1. Gå till **Hantera**  >  **certifikat** och välj **Återställ certifikat**.

   Återställning av certifikat återgår till att använda de självsignerade certifikat som har genererats under order bearbetning. Dina egna certifikat tas bort från enheten.

   ![Alternativet Återställ certifikat i hantera certifikat för en Data Box-enhet enhet](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. När certifikat versionen har slutförts går du till **Stäng av eller starta om** och väljer **starta om**. Det här steget krävs för att undvika SSL-cache-problem.

   ![Stänga av eller starta om det lokala webb gränssnittet när du har återställt certifikat på en Data Box-enhet enhet](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Vänta några minuter och logga sedan in på det lokala webb gränssnittet igen.
