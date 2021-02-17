---
title: Certifikat krav och fel sökning med Azure Stack Edge Pro | Microsoft Docs
description: Beskriver certifikat krav och fel sökning av certifikat fel med Azure Stack Edge Pro-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/17/2020
ms.author: alkohli
ms.openlocfilehash: bbe4be0b6cc1d3ea9e0391e82ce21e307e9de640
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549811"
---
# <a name="certificate-requirements"></a>Certifikatkrav

I den här artikeln beskrivs de certifikat krav som måste uppfyllas innan certifikaten kan installeras på din Azure Stack Edge Pro-enhet. Kraven är relaterade till PFX-certifikat, utfärdande utfärdare, certifikat mottagar namn och alternativt namn för certifikat mottagare och algoritmer som stöds.

## <a name="certificate-issuing-authority"></a>Certifikat utfärdare

Krav för certifikat utfärdare är följande:

* Certifikat måste utfärdas antingen från en intern certifikat utfärdare eller en offentlig certifikat utfärdare.

* Det finns inte stöd för användning av självsignerade certifikat.

* Certifikatets *utfärdade till:* -fält får inte vara samma som det som *utfärdats av:* fältet, förutom rot certifikat utfärdarens certifikat.


## <a name="certificate-algorithms"></a>Certifikat algoritmer

Certifikatets algoritmer måste ha följande krav:

* Certifikat måste använda RSA-nyckelalgoritm.

* Endast RSA-certifikat med Microsoft RSA/SChannel Cryptographic Provider stöds.

* Algoritmen för certifikatautentisering får inte vara SHA1.

* Den minsta nyckel storleken är 4096.

## <a name="certificate-subject-name-and-subject-alternative-name"></a>Certifikatets ämnes namn och alternativt namn för certifikat mottagare

Certifikat måste ha följande ämnes namn och alternativa namn för certifikat mottagare:

* Du kan antingen använda ett enda certifikat som täcker alla namn utrymmen i certifikatets fält alternativa namn (SAN). Du kan också använda enskilda certifikat för var och en av namn områdena. Båda metoderna kräver användning av jokertecken för slut punkter där det krävs, till exempel binary large object (BLOB).

* Se till att ämnes namnen (eget namn i ämnes namnet) är en del av alternativa namn för certifikat mottagare i tillägget alternativt namn.

* Du kan använda ett enda certifikat för jokertecken som täcker alla namn utrymmen i certifikatets SAN-fält.

* Använd följande tabell när du skapar ett slut punkts certifikat:

    |Typ |Ämnes namn (SN)  |Alternativt namn för certifikat mottagare (SAN)  |Exempel på ämnes namn |
    |---------|---------|---------|---------|
    |Azure Resource Manager|`management.<Device name>.<Dns Domain>`|`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`|`management.mydevice1.microsoftdatabox.com` |
    |Blob Storage|`*.blob.<Device name>.<Dns Domain>`|`*.blob.< Device name>.<Dns Domain>`|`*.blob.mydevice1.microsoftdatabox.com` |
    |Lokalt användar gränssnitt| `<Device name>.<DnsDomain>`|`<Device name>.<DnsDomain>`| `mydevice1.microsoftdatabox.com` |
    |Flera SAN-certifikat för båda slut punkterna|`<Device name>.<dnsdomain>`|`<Device name>.<dnsdomain>`<br>`login.<Device name>.<Dns Domain>`<br>`management.<Device name>.<Dns Domain>`<br>`*.blob.<Device name>.<Dns Domain>`|`mydevice1.microsoftdatabox.com` |
    |Nod|`<NodeSerialNo>.<DnsDomain>`|`*.<DnsDomain>`<br><br>`<NodeSerialNo>.<DnsDomain>`|`mydevice1.microsoftdatabox.com` |
    |VPN|`AzureStackEdgeVPNCertificate.<DnsDomain>`<br><br> * AzureStackEdgeVPNCertificate är hårdkodad.  | `*.<DnsDomain>`<br><br>`<AzureStackVPN>.<DnsDomain>` | `edgevpncertificate.microsoftdatabox.com`|
    
## <a name="pfx-certificate"></a>PFX-certifikat

De PFX-certifikat som är installerade på din Azure Stack Edge Pro-enhet uppfyller följande krav:

* När du hämtar dina certifikat från SSL-utfärdaren, ser du till att du får den fullständiga signerings kedjan för certifikaten.

* När du exporterar ett PFX-certifikat måste du kontrol lera att du har valt alternativet **Inkludera alla certifikat i kedjan om möjligt** .

* Använd ett PFX-certifikat för slut punkt, lokalt användar gränssnitt, nod, VPN och Wi-Fi som både offentliga och privata nycklar krävs för Azure Stack Edge Pro. Den privata nyckeln måste ha attributet lokal dator nyckel angiven.

* Certifikatets PFX-kryptering bör vara 3DES. Detta är standard krypteringen som används vid export från en Windows 10-klient eller Windows Server 2016-certifikat arkiv. Mer information om 3DES finns i [TREDUBBEL des](https://en.wikipedia.org/wiki/Triple_DES).

* Certifikatets PFX-filer måste ha giltiga *digitala signaturer* och *KeyEncipherment* -värden i fältet *nyckel användning* .

* Certifikatets PFX-filer måste ha *1.3.6.1.5.5.7.3.1 (Values Server Authentication)* och *klientautentisering (1.3.6.1.5.5.7.3.2)* i fältet *förbättrad nyckel användning* .

* Lösen orden till alla PFX-filer för certifikat måste vara samma vid tidpunkten för distributionen om du använder verktyget Azure Stack readiness Checker. Mer information finns i [Skapa certifikat för din Azure Stack Edge Pro med hjälp av Azure Stack Hub readiness Checker-verktyget](azure-stack-edge-gpu-create-certificates-tool.md).

* Lösen ordet till certifikatets PFX måste vara ett komplext lösen ord. Anteckna det här lösen ordet eftersom det används som en distributions parameter.

* Använd endast RSA-certifikat med Microsoft RSA/SChannel Cryptographic Provider.

Mer information finns i [Exportera PFX-certifikat med privat nyckel](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key).

## <a name="next-steps"></a>Nästa steg

[Använda certifikat med Azure Stack Edge Pro](azure-stack-edge-gpu-manage-certificates.md)

[Skapa certifikat för din Azure Stack Edge Pro med Azure Stack Hub readiness Checker-verktyget](azure-stack-edge-gpu-create-certificates-tool.md)

[Exportera PFX-certifikat med privat nyckel](azure-stack-edge-gpu-manage-certificates.md#export-certificates-as-pfx-format-with-private-key)

[Felsöka certifikat fel](azure-stack-edge-gpu-certificate-troubleshooting.md)