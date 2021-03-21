---
title: Arbeta med Defender for IoT-CLI-kommandon
description: Den här artikeln beskriver Defender för IoT CLI-kommandon för sensorer och lokala hanterings konsoler.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 93efc89722d3152d92b6f8c8038deaa566741f7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636568"
---
# <a name="work-with-defender-for-iot-cli-commands"></a>Arbeta med Defender for IoT-CLI-kommandon

I den här artikeln beskrivs CLI-kommandon för sensorer och lokala hanterings konsoler. Kommandona är tillgängliga för följande användare:

- Administratör
- CyberX 
- Support

Om du vill börja arbeta i CLI ansluter du med en Terminal. Till exempel Terminal name `Putty` och `Support` User. 

## <a name="create-local-alert-exclusion-rules"></a>Skapa undantags regler för lokal varning

Du kan skapa en regel för lokal varnings undantag genom att ange följande kommando i CLI:

```azurecli-interactive
alerts exclusion-rule-create [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Följande attribut kan användas med undantags reglerna för varningar:

| Attribut | Beskrivning |
|--|--|
| [-h] | Skriver ut hjälp information för kommandot. |
| -n namn | Namnet på den regel som skapas. |
| [-TS gånger] | Tidsintervallet som regeln är aktiv för. Detta ska anges som:<br />`xx:yy-xx:yy`<br />Du kan definiera mer än en tids period genom att använda ett kommatecken mellan dem. Exempel: `xx:yy-xx:yy, xx:yy-xx:yy`. |
| [-dir-riktning] | Riktningen som regeln tillämpas på. Detta ska anges som:<br />`both | src | dst` |
| [-dev-enheter] | IP-adressen och adress typen för de enheter som ska uteslutas av regeln, anges som:<br />`ip-x.x.x.x`<br />`mac-xx:xx:xx:xx:xx:xx`<br />`subnet: x.x.x.x/x` |
| [-a AVISERINGar] | Namnet på den avisering som regeln kommer att exkludera:<br />`0x00000`<br />`0x000001` |

## <a name="append-local-alert-exclusion-rules"></a>Lägg till undantags regler för lokal varning

Du kan lägga till undantags regler för lokala varningar genom att ange följande kommando i CLI:

```azurecli-interactive
alerts exclusion-rule-append [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Attributen som används här är desamma som de som beskrivs i avsnittet Skapa undantags regler för lokala varningar. Skillnaden i användningen är att attributen tillämpas på befintliga regler.

## <a name="show-local-alert-exclusion-rules"></a>Visa undantags regler för lokala varningar

Ange följande kommando för att presentera den befintliga listan över undantags regler:

```azurecli-interactive
alerts exclusion-rule-list [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

## <a name="delete-local-alert-exclusion-rules"></a>Ta bort undantags regler för lokal varning

Du kan ta bort en befintlig varnings exkluderings regel genom att ange följande kommando:

```azurecli-interactive
alerts exclusion-rule-remove [-h] -n NAME [-ts TIMES] [-dir DIRECTION]  
[-dev DEVICES] [-a ALERTS]
```

Följande attribut kan användas med undantags reglerna för varningar:

| Attribut | Beskrivning|
| --------- | ---------------------------------- |
| -n namn | Namnet på regeln som ska tas bort. |

## <a name="sync-time-from-the-ntp-server"></a>Synkroniseringstid från NTP-servern

Du kan aktivera eller inaktivera en tidssynkronisering från en angiven NTP-server.

### <a name="enable-ntp-sync"></a>Aktivera NTP-synkronisering

Ange följande kommando för att regelbundet hämta tiden från den angivna NTP-servern:

```azurecli-interactive
ntp enable IP
```

Det attribut som du kan definiera i kommandot är NTP-serverns IP-adress.

### <a name="disable-ntp-sync"></a>Inaktivera NTP-synkronisering

Ange följande kommando för att inaktivera tidssynkroniseringen med den angivna NTP-servern:

```azurecli-interactive
ntp disable IP
```

Det attribut som du kan definiera i kommandot är NTP-serverns IP-adress.

## <a name="network-configuration"></a>Konfiguration av nätverk

I följande tabell beskrivs de kommandon som är tillgängliga för att konfigurera nätverks alternativ för Azure Defender för IoT:

|Name|Kommando|Beskrivning|
|-----------|-------|-----------|
|Pinga|`ping IP`| Pinga en adress utanför Defender för IoT-plattformen.|
|Blink|`network blink`| Hitta en anslutning genom att orsaka att gränssnitts lamporna blinkar. |
|Konfigurera om nätverket |`network edit-settings`| Aktivera en ändring i parametrarna för nätverks konfigurationen. |
|Visa nätverks inställningar |`network list`|Visar parametrarna för nätverkskortet. |
|Verifiera nätverks konfigurationen |`network validate` |Visar nätverks inställningarna för utdata. <br /> <br />Exempel: <br /> <br />Aktuella nätverks inställningar: <br /> gränssnitt: ETH0 <br /> IP: 10.100.100.1 <br />undernät: 255.255.255.0 <br />standardgateway: 10.100.100.254 <br />DNS: 10.100.100.254 <br />övervaka gränssnitt: eth1|
|Importera ett certifikat |`certificate import FILE` |Importerar HTTPS-certifikatet. Du måste ange den fullständiga sökvägen, som leder till en \* . CRT-fil. |
|Visa datum |`date` |Returnerar aktuellt datum på värden i GMT-format. |

## <a name="filter-network-configurations"></a>Filtrera nätverkskonfigurationer

Med `network capture-filter` kommandot kan administratörer eliminera nätverks trafik som inte behöver analyseras. Du kan filtrera trafik genom att använda en include-lista eller en exkluderings lista.

```azurecli-interactive
network capture-filter
```

När du har angett kommandot uppmanas du att ange följande fråga:

>`Would you like to supply devices and subnet masks you wish to include in the capture filter? [Y/N]:`

Välj `Y` det här alternativet om du vill öppna en nano-fil där du kan lägga till en enhet, kanal, port och delmängd enligt följande syntax:

| Attribut | Beskrivning |
|--|--|
| 1.1.1.1 | Inkluderar all trafik för den här enheten. |
| 1.1.1.1, 2.2.2.2 | Inkluderar all trafik för den här kanalen. |
| 1.1.1, 2.2.2 | Inkluderar all trafik för det här under nätet. |

Separera argument genom att släppa en rad.

När du inkluderar en enhet, kanal eller undernät, bearbetar sensorn all giltig trafik för det argumentet, inklusive portar och trafik som vanligt vis inte bearbetas.

Sedan uppmanas du att göra följande fråga:

>`Would you like to supply devices and subnet masks you wish to exclude from the capture filter? [Y/N]:`

Välj `Y` det här alternativet om du vill öppna en nano-fil där du kan lägga till en enhet, kanal, port och del mängder enligt följande syntax:

| Attribut | Beskrivning |
|--|--|
| 1.1.1.1 | Undantar all trafik för den här enheten. |
| 1.1.1.1, 2.2.2.2 | Undantar all trafik för den här kanalen, vilket innebär all trafik mellan två enheter. |
| 1.1.1.1, 2.2.2.2, 443 | Undantar all trafik för den här kanalen via port. |
| 1.1.1 | Undantar all trafik för det här under nätet. |
| 1.1.1, 2.2.2 | Undantar all trafik för mellan undernät. |

Separera argument genom att släppa en rad.

När du exkluderar en enhet, kanal eller undernät, kommer sensorn att undanta all giltig trafik för det argumentet.

### <a name="ports"></a>Portar

Ta med eller Uteslut UDP-och TCP-portar för all trafik.

>`502`: enskild port

>`502,443`: båda portarna

>`Enter tcp ports to include (delimited by comma or Enter to skip):`

>`Enter udp ports to include (delimited by comma or Enter to skip):`

>`Enter tcp ports to exclude (delimited by comma or Enter to skip):`

>`Enter udp ports to exclude (delimited by comma or Enter to skip):`

### <a name="components"></a>Komponenter

Du uppmanas följande fråga:

>`In which component do you wish to apply this capture filter?`

Alternativen är:,,,,  `all` `dissector` `collector` `statistics-collector` `rpc-parser` eller `smb-parser` .

I de flesta användnings fall väljer du `all` .

### <a name="custom-base-capture-filter"></a>Anpassat bas insamlings filter

Det grundläggande infångnings filtret är bas linjen för komponenterna. Filtret bestämmer till exempel vilka portar som är tillgängliga för komponenten.

Välj `Y` för alla följande alternativ. Alla filter läggs till i bas linjen när ändringarna har angetts. Om du gör en ändring kommer den befintliga bas linjen att skrivas över.

>`Would you like to supply a custom base capture filter for the dissector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the statistics-collector component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the rpc-parser component? [Y/N]:`

>`Would you like to supply a custom base capture filter for the smb-parser component? [Y/N]:`

>`Type Y for "internal" otherwise N for "all-connected" (custom operation mode enabled) [Y/N]:`

Om du väljer att undanta ett undernät, till exempel 1.1.1:

- `internal` kommer bara att undanta det under nätet.

- `all-connected` kommer att undanta det under nätet och all trafik till och från det under nätet.

Vi rekommenderar att du väljer `internal` .

> [!NOTE]
> Dina val används för alla filter i verktyget och är inte beroende av sessioner. Med andra ord kan du välja `internal`   vissa filter och  `all-connected`   andra.

### <a name="comments"></a>Kommentarer

Du kan visa filter i  ```/var/cyberx/properties/cybershark.properties``` :

- **statistik-insamlare**:  `bpf_filter property` i ```/var/cyberx/properties/net.stats.collector.properties```

- **desektor**: `override.capture_filter`   egenskap i ```/var/cyberx/properties/cybershark.properties```

- **RPC-parser**: `override.capture_filter` egenskap i ```/var/cyberx/properties/rpc-parser.properties```

- **SMB-parser**:  `override.capture_filter`   egenskap i ```/var/cyberx/properties/smb-parser.properties```

- **insamlare**: `general.bpf_filter`   egenskap i ```/var/cyberx/properties/collector.properties```

Du kan återställa standard konfigurationen genom att ange följande kod för Cyberx-användaren:

```azurecli-interactive
sudo cyberx-xsense-capture-filter -p all -m all-connected
```

## <a name="define-client-and-server-hosts"></a>Definiera klient-och Server värdar

Om Defender för IoT inte identifierade klienten automatiskt, och Server värdar, anger du följande kommando för att ställa in klient-och Server värdarna:

```azurecli-interactive
directions [-h] [--identifier IDENTIFIER] [--port PORT] [--remove] [--add]  
[--tcp] [--udp]
```

Du kan använda följande attribut med `directions` kommandot:

| Attribut | Beskrivning |
|--|--|
| [-h] | Skriver ut hjälp information för kommandot. |
| [--identifierare för identifierare] | Server-ID. |
| [--port PORT] | Server porten. |
| [--Remove] | Tar bort en klient eller server värd från listan. |
| [--add] | Lägger till en klient-eller server värd i listan. |
| [--TCP] | Använd TCP vid kommunikation med denna värd. |
| [--UDP] | Använd UDP vid kommunikation med denna värd. |

## <a name="system-actions"></a>System åtgärder
I följande tabell beskrivs de kommandon som är tillgängliga för att utföra olika system åtgärder i Defender för IoT:

|Name|Kod|Beskrivning|
|----|----|-----------|
|Visa datum|`date`|Returnerar aktuellt datum på värden i GMT-format.|
|Starta om värden|`system reboot`|Startar om värd enheten.|
|Stäng av värden|`system shutdown`|Stänger av värden.|
|Säkerhetskopiera systemet|`system backup`|Initierar en omedelbar säkerhets kopiering (en säkerhets kopia som inte har schemalagts).|
|Återställa systemet från en säkerhets kopia|`system restore`|Återställer från den senaste säkerhets kopian.|
|Lista de säkerhetskopierade filerna|`system backup-list`|Visar en lista över tillgängliga säkerhetskopierade filer.|
|Visa status för alla Defender för IoT Platform-tjänster|`system sanity`|Kontrollerar systemets prestanda genom att lista den aktuella statusen för alla Defender för IoT Platform-tjänster.|
|Visa program varu versionen|`system version`|Visar den version av program varan som för närvarande körs på systemet.|

## <a name="deploy-ssl-and-tls-certificates-to-appliances"></a>Distribuera SSL-och TLS-certifikat till enheter

Ange följande kommando för att importera SSL-och TLS-företags certifikat till CLI:

```azurecli-interactive
cyberx-xsense-certificate-import
```
Om du vill använda verktyget måste du ladda upp certifikatfiler till enheten. Du kan göra detta med hjälp av verktyg som WinSCP eller wget. 

Kommandot stöder följande ingångs flaggor:

| Flagga | Beskrivning |
|--|--|
| -h | Visar kommando rads hjälpens syntax. |
| --CRT | Sökvägen till certifikat filen (. CRT-tillägget). |
| --nyckel | \*Nyckel filen. Nyckel längden måste vara minst 2 048 bitar. |
| --kedja | Sökväg till filen med certifikat kedjan (valfritt). |
| --pass | Lösen fras som används för att kryptera certifikatet (valfritt). |
| --lösen fras-ange | Standardvärdet är **false**, **används** inte. <br />Ange till **Sant** om du vill använda den tidigare lösen frasen som angavs med föregående certifikat (valfritt). |  |

När du använder verktyget:

- Kontrol lera att certifikatmallarna är läsbara på enheten. 

- Bekräfta med IT-domänen (som den visas i certifikatet) med DNS-servern och motsvarande IP-adress. 
    
## <a name="see-also"></a>Se även

[API: er för IoT API-sensor och hanterings konsol](references-work-with-defender-for-iot-apis.md)
