---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: d934d40cad5f4eec929cfd273b6e30ea291e48d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010972"
---
Azure Key Vault tjänst stöder två resurs typer: valv och hanterade HSM: er. I följande två avsnitt beskrivs tjänst gränserna för var och en av dem.

### <a name="resource-type-vault"></a>Resurs typ: valv

I det här avsnittet beskrivs tjänst begränsningar för resurs typ `vaults` .

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Nyckel transaktioner (maximalt antal transaktioner som tillåts om 10 sekunder, per valv per region<sup>1</sup>):

|Nyckeltyp|HSM-nyckel<br>SKAPA nyckel|HSM-nyckel<br>Alla andra transaktioner|Program varu nyckel<br>SKAPA nyckel|Program varu nyckel<br>Alla andra transaktioner|
|:---|---:|---:|---:|---:|
|RSA 2 048-bitars|5|1 000|10|2 000|
|RSA 3 072-bitars|5|250|10|500|
|RSA 4 096-bitars|5|125|10|250|
|ECC P-256|5|1 000|10|2 000|
|ECC P-384|5|1 000|10|2 000|
|ECC P-521|5|1 000|10|2 000|
|ECC-SECP256K1|5|1 000|10|2 000|
||||||

> [!NOTE]
> I den föregående tabellen ser vi att för RSA 2 048-bitars program varu nycklar tillåts 2 000 Hämta transaktioner per 10 sekunder. För RSA 2 048-bitars HSM-Keys tillåts 1 000 Hämta transaktioner per 10 sekunder.
>
> Tröskelvärdena för begränsningen viktas och tvången är på deras summa. Som du ser i föregående tabell när du utför åtgärder på RSA HSM-nycklar, är det till exempel åtta gånger dyrare att använda 4 096-bitars nycklar jämfört med 2 048-bitars nycklar. Det beror på att 1000/125 = 8.
>
> I ett angivet intervall på 10 sekunder kan en Azure Key Vault-klient *bara utföra en* av följande åtgärder innan den påträffar en begränsning för `429` http-status kod:
> - 2 000 RSA 2 048-bitars program varu nyckel Hämta transaktioner
> - 1 000 RSA 2 048-bitars HSM-nyckel Hämta transaktioner
> - 125 RSA 4 096-bitars HSM-nyckel Hämta transaktioner
> - 124 RSA 4 096-bitars HSM-nyckel Hämta transaktioner och 8 RSA 2 048-bit HSM-nyckel Hämta transaktioner

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Hemligheter, hanterade lagrings konto nycklar och valv transaktioner:

| Transaktions typ | Maximalt antal transaktioner som tillåts inom 10 sekunder, per valv per region<sup>1</sup> |
| --- | --- |
| Alla transaktioner |2 000 |

Information om hur du hanterar begränsning när gränserna överskrids finns i [Azure Key Vault begränsnings vägledning](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> en begränsning för hela prenumerationen för alla transaktions typer är fem gånger per begränsning för nyckel valvet. Till exempel är HSM-andra transaktioner per prenumeration begränsad till 5 000 transaktioner på 10 sekunder per prenumeration.

#### <a name="azure-private-link-integration"></a>Azure Private Link-integrering

> [!NOTE]
> Antalet nyckel valv med privata slut punkter som har Aktiver ATS per prenumeration är en justerbar gräns. Den gräns som visas nedan är standard gränsen. Skicka ett e-postmeddelande till om du vill begära en större gräns för tjänsten akv-privatelink@microsoft.com . Vi kommer att godkänna dessa förfrågningar från fall till fall.

| Resurs | Gräns |
| -------- | -----:|
| Privata slut punkter per nyckel valv | 64 |
| Nyckel valv med privata slut punkter per prenumeration | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Resurs typ: hanterad HSM (förhands granskning)

I det här avsnittet beskrivs tjänst begränsningar för resurs typ `managed HSM` .

#### <a name="object-limits"></a>Objekt gränser

|Objekt|Gränser|
|----|------:|
Antal HSM-instanser per prenumeration per region|1 (under för hands versionen)
Antal nycklar per HSM-pool|5000
Antal versioner per nyckel|100
Antal anpassade roll definitioner per HSM|50
Antal roll tilldelningar i HSM-omfång|50
Antal roll tilldelningar i varje enskilt nyckel omfång|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Transaktions gränser för administrativa åtgärder (antal åtgärder per sekund per HSM-instans)
|Åtgärd |Antal åtgärder per sekund|
|----|------:|
Alla RBAC-åtgärder<br/>(innehåller alla CRUD-åtgärder för roll definitioner och roll tilldelningar)|5
Fullständig HSM-säkerhetskopiering/återställning<br/>(endast en samtidig säkerhets kopierings-eller återställnings åtgärd per HSM-instans stöds)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Transaktions gränser för kryptografiska åtgärder (antal åtgärder per sekund per HSM-instans)

- Varje hanterad HSM-instans utgör 3 belastningsutjämnade HSM-partitioner. Data flödes gränserna är en funktion för underliggande maskin varu kapacitet som allokeras för varje partition. Tabellerna nedan visar maximalt data flöde med minst en partition tillgänglig. Det faktiska data flödet kan vara upp till 3x högre om alla tre partitionerna är tillgängliga.
- Data flödes gränser anges förutsätter att en enda nyckel används för att uppnå maximalt data flöde. Om t. ex. en enskild RSA-2048-nyckel används så kommer det maximala data flödet att bli 1100 signerings åtgärder. Om du använder 1100 olika nycklar med 1 transaktion per sekund kommer de inte att kunna uppnå samma data flöde.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>RSA-nyckel åtgärder (antal åtgärder per sekund per HSM-instans)

|Åtgärd|2048-bitars|3072-bitars|4096-bitars|
|--|--:|--:|--:|
Skapa nyckel|1| 1| 1
Ta bort nyckel (mjuk borttagning)|10|10|10 
Rensa nyckel|10|10|10 
Säkerhets kopierings nyckel|10|10|10 
Återställ nyckel|10|10|10 
Hämta viktig information|1100|1100|1100
Kryptera|10000|10000|6000
Avkryptera|1100|360|160
Flytta|10000|10000|6000
Packa upp|1100|360|160
Tecken|1100|360|160
Verifiera|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>EG-nyckel åtgärder (antal åtgärder per sekund per HSM-instans)

I den här tabellen beskrivs antalet åtgärder per sekund för varje kurv typ.

|Åtgärd|P-256|P-256 K|P-384|P-521|
|---|---:|---:|---:|---:|
Skapa nyckel| 1| 1| 1| 1
Ta bort nyckel (mjuk borttagning)|10|10|10|10
Rensa nyckel|10|10|10|10
Säkerhets kopierings nyckel|10|10|10|10
Återställ nyckel|10|10|10|10
Hämta viktig information|1100|1100|1100|1100
Tecken|260|260|165|56
Verifiera|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>AES-nyckel åtgärder (antal åtgärder per sekund per HSM-instans)
- Att kryptera och dekryptera åtgärder förutsätter en 4KB paket storlek.
- Data flödes gränser för kryptering/dekryptering gäller för algoritmer med AES-CBC och AES-GCM.
- Data flödes gränser för wrap/unwrap gäller AES-KW-algoritmen.

|Åtgärd|128-bitars|192-bitars|256-bitars|
|--|--:|--:|--:|
Skapa nyckel|1| 1| 1
Ta bort nyckel (mjuk borttagning)|10|10|10
Rensa nyckel|10|10|10
Säkerhets kopierings nyckel|10|10|10
Återställ nyckel|10|10|10
Hämta viktig information|1100|1100|1100
Kryptera|8000|8000 |8000 
Avkryptera|8000|8000|8000
Flytta|9000|9000|9000
Packa upp|9000|9000|9000

