---
title: Vad är nytt i Azure cache för Redis
description: Senaste uppdateringar för Azure cache för Redis
author: yegu-ms
ms.service: cache
ms.topic: reference
ms.date: 09/28/2020
ms.author: yegu
ms.openlocfilehash: b30e83b89b25e6400b8c7e0419406631fa1edcd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91492541"
---
# <a name="whats-new-in-azure-cache-for-redis"></a>Vad är nytt i Azure cache för Redis

## <a name="azure-tls-certificate-change"></a>Ändring av Azure TLS-certifikat

Microsoft uppdaterar Azure-tjänster för att använda TLS-servercertifikat från en annan uppsättning certifikat utfärdare (ca). Den här ändringen har distribuerats i faser från och med den 13 augusti 2020 till den 26 oktober 2020 (uppskattat). Azure gör den här ändringen eftersom [de aktuella CA-certifikaten inte är kompatibla med ett av forumen för ca/webbläsares bas linje](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951). Problemet rapporterades den 1 juli 2020 och gäller för flera populära PKI-leverantörer (Public Key Infrastructure) över hela världen. De flesta TLS-certifikat som används av Azure-tjänster kommer idag från *Baltimore CyberTrust rot* -PKI. Azure cache för Redis-tjänsten kommer fortsätta att länkas till Baltimore CyberTrust-roten. Dess TLS-servercertifikat kommer dock att utfärdas av nya mellanliggande certifikat utfärdare (ICAs) från den 12 oktober 2020.

> [!NOTE]
> Den här ändringen är begränsad till tjänster i offentliga [Azure-regioner](https://azure.microsoft.com/global-infrastructure/geographies/). Den utesluter suverän (t. ex. Kina) eller offentliga moln.
>
>

### <a name="does-this-change-affect-me"></a>Påverkar den här ändringen mig?

Vi räknar med att Azure cache för Redis-kunder inte påverkas av ändringen. Ditt program kan påverkas om det explicit anger en lista över godkända certifikat, en metod som kallas "certifikat fästning". Om den är fäst på ett mellanliggande eller löv certifikat i stället för Baltimore CyberTrust-roten bör du **vidta omedelbara åtgärder** för att ändra certifikat konfigurationen.

Följande tabell innehåller information om de certifikat som har registrerats. Beroende på vilket certifikat som programmet använder kan du behöva uppdatera det för att förhindra att anslutningen till Azure-cachen för Redis-instansen bryts.

| CA-typ | Aktuellt | Post valsning (12 oktober 2020) | Action |
| ----- | ----- | ----- | ----- |
| Rot | Tumavtryck: d4de20d05e66fc53fe1a50882c78db2852cae474<br><br> Utgångs datum: måndag, 12 maj 2025, 4:59:00 PM<br><br> Ämnes namn:<br> CN = Baltimore CyberTrust-rot<br> OU = CyberTrust<br> O = Baltimore<br> C = IE | Ändra inte | Inget |
| Mellanprodukter | Tumavtrycken<br> CN = Microsoft IT TLS CA 1<br> Tumavtryck: 417e225037fbfaa4f95761d5ae729e1aea7e3a42<br><br> CN = Microsoft IT TLS CA 2<br> Tumavtryck: 54d9d20239080c32316ed9ff980a48988f4adf2d<br><br> CN = Microsoft IT TLS CA 4<br> Tumavtryck: 8a38755d0996823fe8fa3116a277ce446eac4e99<br><br> CN = Microsoft IT TLS CA 5<br> Tumavtryck: Ad898ac73df333eb60ac1f5fc6c4b2219ddb79b7<br><br> Förfallo datum: fredag 20 maj 2024 5:52:38 AM<br><br> Ämnes namn:<br> OU = Microsoft IT<br> O = Microsoft Corporation<br> L = Redmond<br> S = Washington<br> C = US<br> | Tumavtrycken<br> CN = Microsoft RSA TLS CA 01<br> Tumavtryck: 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a<br><br> CN = Microsoft RSA TLS CA 02<br> Tumavtryck: b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75<br><br> Förfallo datum: tisdag 8 oktober 2024 12:00:00 AM;<br><br> Ämnes namn:<br> O = Microsoft Corporation<br> C = US<br> | Obligatorisk |

### <a name="what-actions-should-i-take"></a>Vilka åtgärder ska jag göra?

Om programmet använder operativ systemets certifikat arkiv eller fäster Baltimore-roten bland andra krävs ingen åtgärd. Å andra sidan rekommenderar vi att du fäster följande rötter om ditt program tappar ett mellanliggande eller löv-TLS-certifikat:

| Certifikat | Stämpel |
| ----- | ----- |
| [Baltimore rot certifikat utfärdare](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 |
| [DigiCert global root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |

> [!TIP]
> Både mellanliggande och löv certifikat förväntas ändras ofta. Vi rekommenderar att du inte tar ett beroende på dem. Fästa i stället ditt program till ett rot certifikat eftersom det rullar mindre ofta.
>
>

Om du vill fortsätta att fästa mellanliggande certifikat lägger du till följande i listan över fasta mellanliggande certifikat, som innehåller några ytterligare för att minimera framtida ändringar:

| Eget namn på CA: n | Stämpel |
| ----- | ----- |
| [Microsoft RSA TLS CA 01](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2001.crt) | 703d7a8f0ebf55aaa59f98eaf4a206004eb2516a |
| [Microsoft RSA TLS CA 02](https://www.microsoft.com/pki/mscorp/Microsoft%20RSA%20TLS%20CA%2002.crt) | b0c2d2d13cdd56cdaa6ab6e2c04440be4a429c75 |
| [Microsoft Azure TLS-utfärdande CA 01](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2001.cer) | 2f2877c5d778c31e0f29c7e371df5471bd673173 |
| [Microsoft Azure TLS-utfärdande CA 02](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2002.cer) | e7eea674ca718e3befd90858e09f8372ad0ae2aa |
| [Microsoft Azure TLS-utfärdande CA 05](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2005.cer) | 6c3af02e7f269aa73afd0eff2a88a4a1f04ed1e5 |
| [Microsoft Azure TLS-utfärdande CA 06](https://www.microsoft.com/pkiops/certs/Microsoft%20Azure%20TLS%20Issuing%20CA%2006.cer) | 30e01761ab97e59a06b41ef20af6f2de7ef4f7b0 |

Om ditt program validerar certifikat i kod måste du ändra det för att känna igen egenskaperna (t. ex. utfärdare, tumavtryck) för de nyligen fästa certifikaten. Den här extra kontrollen bör avse alla fästa certifikat för att vara mer framtida.

## <a name="next-steps"></a>Nästa steg

Om du har fler frågor kan du kontakta oss via [supporten](https://azure.microsoft.com/support/options/).  
