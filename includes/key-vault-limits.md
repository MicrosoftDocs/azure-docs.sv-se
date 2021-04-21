---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: 9ecfcff00e6f44f5c739513c063baaa3fa02a3db
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753314"
---
Azure Key Vault har stöd för två resurstyper: Valv och hanterade HSM:er. I följande två avsnitt beskrivs tjänstbegränsningarna för var och en av dem.

### <a name="resource-type-vault"></a>Resurstyp: valv

I det här avsnittet beskrivs tjänstbegränsningar för resurstypen `vaults` .

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Nyckeltransaktioner (maximalt antal transaktioner som tillåts inom 10 sekunder, per valv per region<sup>1</sup>):

|Nyckeltyp|HSM-nyckel<br>CREATE-nyckel|HSM-nyckel<br>Alla andra transaktioner|Programvarunyckel<br>CREATE-nyckel|Programvarunyckel<br>Alla andra transaktioner|
|:---|---:|---:|---:|---:|
|RSA 2 048-bitars|5|1 000|10|2 000|
|RSA 3 072-bitars|5|250|10|500|
|RSA 4 096-bitars|5|125|10|250|
|ECC P-256|5|1 000|10|2 000|
|ECC P-384|5|1 000|10|2 000|
|ECC P-521|5|1 000|10|2 000|
|ECC SECP256K1|5|1 000|10|2 000|
||||||

> [!NOTE]
> I föregående tabell ser vi att för RSA 2 048-bitars programvarunycklar tillåts 2 000 GET-transaktioner per 10 sekunder. För RSA 2 048-bitars HSM-nycklar tillåts 1 000 GET-transaktioner per 10 sekunder.
>
> Tröskelvärdena för begränsning viktas och tvingande är på summan. Som du ser i föregående tabell är det till exempel åtta gånger dyrare att använda 4 096-bitars nycklar när du utför GET-åtgärder på RSA HSM-nycklar jämfört med 2 048-bitars nycklar. Det beror på 1 000/125 = 8.
>
> Under ett visst 10-sekundersintervall kan en  Azure Key Vault-klient bara göra en av följande åtgärder innan den påträffar en begränsning av `429` HTTP-statuskod:
> - GET-transaktioner med 2 000 RSA 2 048-bitars programvarunyckel
> - GET-transaktioner med 1 000 RSA 2 048-bitars HSM-nyckel
> - 125 RSA 4 096-bitars GET-transaktioner med HSM-nyckel
> - GET-transaktioner med 124 RSA 4 096-bitars HSM-nyckel och 8 RSA 2 048-bitars HSM-key GET-transaktioner

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Hemligheter, hanterade lagringskontonycklar och valvtransaktioner:

| Transaktionstyp | Maximalt antal transaktioner som tillåts på 10 sekunder, per valv per region<sup>1</sup> |
| --- | --- |
| Alla transaktioner |2 000 |

Information om hur du hanterar begränsning när dessa gränser överskrids finns i Azure Key Vault [vägledningen om begränsning.](../articles/key-vault/general/overview-throttling.md)

<sup>1</sup> En prenumerationsomfattande gräns för alla transaktionstyper är fem gånger per nyckelvalvsgräns. Till exempel är HSM-andra transaktioner per prenumeration begränsade till 5 000 transaktioner på 10 sekunder per prenumeration.

#### <a name="backup-keys-secrets-certificates"></a>Säkerhetskopieringsnycklar, hemligheter, certifikat

När du säkerhetskopierar ett nyckelvalvsobjekt, till exempel en hemlighet, nyckel eller ett certifikat, laddar säkerhetskopieringsåtgärden ned objektet som en krypterad blob. Den här bloben kan inte dekrypteras utanför Azure. Om du vill hämta användbara data från den här bloben måste du återställa bloben till ett nyckelvalv inom samma Azure-prenumeration och Azure-geografi

| Transaktionstyp | Maximalt antal tillåtna key vault-objektversioner |
| --- | --- |
| Säkerhetskopiera enskild nyckel, hemlighet, certifikat |500 |

> [!NOTE]
> Om du försöker säkerhetskopiera en nyckel, en hemlighet eller ett certifikatobjekt med fler versioner än gränsen ovan resulterar det i ett fel. Det går inte att ta bort tidigare versioner av en nyckel, hemlighet eller ett certifikat. 

#### <a name="azure-private-link-integration"></a>Azure Private Link integrering

> [!NOTE]
> Antalet nyckelvalv med privata slutpunkter aktiverat per prenumeration är en justerbar gräns. Den gräns som visas nedan är standardgränsen. Om du vill begära en gränsökning för din tjänst skapar du en supportbegäran så utvärderas den från fall till fall.

| Resurs | Gräns |
| -------- | -----:|
| Privata slutpunkter per nyckelvalv | 64 |
| Nyckelvalv med privata slutpunkter per prenumeration | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Resurstyp: Hanterad HSM (förhandsversion)

I det här avsnittet beskrivs tjänstbegränsningar för resurstypen `managed HSM` .

#### <a name="object-limits"></a>Objektbegränsningar

|Objekt|Gränser|
|----|------:|
Antal HSM-instanser per prenumeration och region|1 (under förhandsversion)
Antal nycklar per HSM-pool|5000
Antal versioner per nyckel|100
Antal anpassade rolldefinitioner per HSM|50
Antal rolltilldelningar i HSM-omfång|50
Antal rolltilldelningar för varje enskilt nyckelomfång|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Transaktionsgränser för administrativa åtgärder (antal åtgärder per sekund per HSM-instans)
|Åtgärd |Antal åtgärder per sekund|
|----|------:|
Alla RBAC-åtgärder<br/>(innehåller alla CRUD-åtgärder för rolldefinitioner och rolltilldelningar)|5
Fullständig säkerhetskopiering/återställning av HSM<br/>(endast en samtidig säkerhetskopierings- eller återställningsåtgärd per HSM-instans stöds)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Transaktionsgränser för kryptografiska åtgärder (antal åtgärder per sekund per HSM-instans)

- Varje Hanterad HSM-instans utgör 3 belastningsutjämnade HSM-partitioner. Dataflödesbegränsningarna är en funktion av underliggande maskinvarukapacitet som allokeras för varje partition. Tabellerna nedan visar maximalt dataflöde med minst en tillgänglig partition. Det faktiska dataflödet kan vara upp till 3 x högre om alla tre partitionerna är tillgängliga.
- De dataflödesgränser som anges förutsätter att en enda nyckel används för att uppnå maximalt dataflöde. Om till exempel en enda RSA-2048-nyckel används är det maximala dataflödet 1 100 tecken. Om du använder 1 100 olika nycklar med 1 transaktion per sekund var kan de inte uppnå samma dataflöde.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA-nyckelåtgärder (antal åtgärder per sekund per HSM-instans)

|Åtgärd|2048-bitars|3072-bitars|4096-bitars|
|--|--:|--:|--:|
Skapa nyckel|1| 1| 1
Ta bort nyckel (mjuk borttagning)|10|10|10 
Rensa nyckel|10|10|10 
Säkerhetskopieringsnyckel|10|10|10 
Återställ nyckel|10|10|10 
Hämta viktig information|1100|1100|1100
Kryptera|10000|10000|6000
Avkryptera|1100|360|160
Wrap|10000|10000|6000
Packa upp|1100|360|160
Tecken|1100|360|160
Verifiera|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EC-nyckelåtgärder (antal åtgärder per sekund per HSM-instans)

I den här tabellen beskrivs antalet åtgärder per sekund för varje kurvatyp.

|Åtgärd|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Skapa nyckel| 1| 1| 1| 1
Ta bort nyckel (mjuk borttagning)|10|10|10|10
Rensa nyckel|10|10|10|10
Säkerhetskopieringsnyckel|10|10|10|10
Återställ nyckel|10|10|10|10
Hämta viktig information|1100|1100|1100|1100
Tecken|260|260|165|56
Verifiera|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES-nyckelåtgärder (antal åtgärder per sekund per HSM-instans)
- Åtgärder för kryptering och dekryptering förutsätter en paketstorlek på 4 kB.
- Dataflödesbegränsningar för kryptera/dekryptera gäller för AES-CBC- och AES-GCM-algoritmer.
- Dataflödesbegränsningar för Wrap/Unwrap gäller för AES-HASH-algoritmen.

|Åtgärd|128-bitars|192-bitars|256-bitars|
|--|--:|--:|--:|
Skapa nyckel|1| 1| 1
Ta bort nyckel (mjuk borttagning)|10|10|10
Rensa nyckel|10|10|10
Säkerhetskopieringsnyckel|10|10|10
Återställ nyckel|10|10|10
Hämta viktig information|1100|1100|1100
Kryptera|8000|8000 |8000 
Avkryptera|8000|8000|8000
Wrap|9000|9000|9000
Packa upp|9000|9000|9000

