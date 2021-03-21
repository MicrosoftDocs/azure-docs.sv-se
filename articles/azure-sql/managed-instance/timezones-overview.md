---
title: Tids zoner för hanterad Azure SQL-instans
description: Läs mer om tids zonens information om den hanterade Azure SQL-instansen
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 10/12/2020
ms.openlocfilehash: b4ba5f50f5c66b404ec76128a828a1060328f81c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101690999"
---
# <a name="time-zones-in-azure-sql-managed-instance"></a>Tids zoner i Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

UTC (Coordinated Universal Time) är den rekommenderade tids zonen för data nivån för moln lösningar. Azure SQL Managed instance erbjuder också ett urval av tids zoner som uppfyller behoven hos befintliga program som lagrar datum-och tids värden och anropar datum-och tids funktioner med en implicit kontext för en speciell tidszon.

T-SQL-funktioner som [getDate ()](/sql/t-sql/functions/getdate-transact-sql) eller CLR-kod studerar tids zonen på instans nivå. SQL Server Agents jobb följer också scheman enligt tids zonen för instansen.

  > [!NOTE]
  > Azure SQL Database stöder inte tids zons inställningar. den följer alltid UTC. Använd [vid tidszon](/sql/t-sql/queries/at-time-zone-transact-sql) i SQL Database om du behöver tolka datum-och tidsinformation i en tidszon som inte är UTC-tid.

## <a name="supported-time-zones"></a>Tids zoner som stöds

En uppsättning tids zoner som stöds ärvs från det underliggande operativ systemet för den hanterade instansen. Den uppdateras regelbundet för att hämta nya definitioner för tids zoner och återspegla ändringar av befintliga.

[Princip för sommar tid/tids zons ändringar](/troubleshoot/windows-client/system-management-components/daylight-saving-time-help-support) garanterar historisk noggrannhet från 2010 forward.

En lista med namnen på de tids zoner som stöds visas i [sys.time_zone_info](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) system-vyn.

## <a name="set-a-time-zone"></a>Ange en tidszon

En tids zon för en hanterad instans kan bara anges när en instans skapas. Standard tids zonen är UTC.

  >[!NOTE]
  > Det går inte att ändra tids zonen för en befintlig hanterad instans.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Ange tids zonen via Azure Portal

När du anger parametrar för en ny instans väljer du en tidszon i listan över tids zoner som stöds.
  
![Ange en tidszon när en instans skapas](./media/timezones-overview/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Ange timezoneId-egenskapen i [Resource Manager-mallen](./create-template-quickstart.md) för att ställa in tids zonen under skapandet av instansen.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

En lista med värden som stöds för egenskapen timezoneId finns i slutet av den här artikeln.

Om detta inte anges anges tids zonen till UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Kontrol lera tids zonen för en instans

Funktionen [CURRENT_TIMEZONE](/sql/t-sql/functions/current-timezone-transact-sql) returnerar visnings namnet för instansens tidszon.

## <a name="cross-feature-considerations"></a>Överväganden för olika funktioner

### <a name="restore-and-import"></a>Återställa och importera

Du kan återställa en säkerhets kopia eller importera data till en hanterad instans från en instans eller en server med olika tids zons inställningar. Se till att göra det med försiktighet. Analysera program beteendet och resultaten av frågorna och rapporterna, precis som när du överför data mellan två SQL Server instanser med olika tids zons inställningar.

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

När du utför en tidpunkts återställning, tolkas tiden för att återställa till UTC-tid. På så sätt kan eventuella tvetydigheter på grund av sommar tid och eventuella ändringar undvikas.

### <a name="auto-failover-groups"></a>Automatiska redundansgrupper

Att använda samma tidszon i en primär och sekundär instans i en grupp för växling vid fel är inte tvingande, men vi rekommenderar starkt.

  >[!WARNING]
  > Vi rekommenderar starkt att du använder samma tidszon för den primära och sekundära instansen i en grupp för växling vid fel. På grund av vissa ovanliga användnings fall är det inte tvingande att behålla samma tidszon mellan primära och sekundära instanser. Det är viktigt att förstå att om du använder manuell eller automatisk redundans behåller den sekundära instansen den ursprungliga tids zonen.

## <a name="limitations"></a>Begränsningar

- Det går inte att ändra tids zonen för den befintliga hanterade instansen. Som en lösning kan du skapa en ny hanterad instans med rätt tidszon och sedan antingen utföra en manuell säkerhets kopiering och återställning, eller vad vi rekommenderar, utföra en [återställning mellan olika instanser](./point-in-time-restore.md?tabs=azure-portal#restore-an-existing-database).
- Externa processer som startas från SQL Server Agent jobben observerar inte tids zonen för instansen.

## <a name="list-of-supported-time-zones"></a>Lista över tids zoner som stöds

| **Tidszons-ID** | **Visnings namn för tidszon** |
| --- | --- |
| Datum gränsen, normal tid | (UTC-12:00) Internationella datum linjen, väst |
| UTC-11 | (UTC-11:00) Koordinerad universell tid – 11 |
| Aleuterna normal tid | (UTC-10:00) Aleuterna öarna |
| Hawaii, normal tid | (UTC-10:00) Hawaii |
| Marquesas, normal tid | (UTC-09:30) Marquesas, öarna |
| Alaska, normal tid | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Koordinerad universell tid-09 |
| Pacific, normal tid (Mexiko) | (UTC-08:00) Baja Kalifornien |
| UTC-08 | (UTC-08:00) Koordinerad universell tid – 08 |
| Pacific, normal tid | (UTC-08:00) Pacific Time (USA & Kanada) |
| US Mountain, normal tid | (UTC-07:00) Arizona |
| Mountain, normal tid (Mexiko) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Mountain, normal tid | (UTC-07:00) Mountain-tid (USA & Kanada) |
| Central Amerika, normal tid | (UTC-06:00) Central Amerika |
| Central, normal tid | (UTC-06:00) Central Time (USA & Kanada) |
| Påskön, normal tid | (UTC-06:00) Påskön, Julön |
| Central normal tid (Mexiko) | (UTC-06:00) Guadalajara, Mexiko City, Monterrey |
| Kanada, Central, normal tid | (UTC-06:00) Saskatchewan |
| SA Pacific, normal tid | (UTC-05:00) Bogotá, Lima, Quito, Rio Branco |
| Eastern, normal tid (Mexiko) | (UTC-05:00) Chetumal |
| Eastern, normal tid | (UTC-05:00) Eastern Time (USA & Kanada) |
| Haiti, normal tid | (UTC-05:00) Haiti |
| Kuba, normal tid | (UTC-05:00) Havanna |
| Östra USA, normal tid | (UTC-05:00) Indiana (öst) |
| Turks-och Caicosöarna, normal tid | (UTC-05:00) Turks-och Caicosöarna |
| Paraguay, normal tid | (UTC-04:00) Asunción |
| Atlantic, normal tid | (UTC-04:00) Atlant tid (Kanada) |
| Venezuela, normal tid | (UTC-04:00) Caracas |
| Centrala Brasilien, normal tid | (UTC-04:00) Cuiaba |
| Västra Sydamerika, normal tid | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pacific SA, normal tid | (UTC-04:00) Santiago |
| Newfoundland, normal tid | (UTC-03:30) Newfoundland |
| Tocantins, normal tid | (UTC-03:00) Araguaina |
| E. Södra Amerika, normal tid | (UTC-03:00) Brasilien |
| SA Eastern, normal tid | (UTC-03:00) Cayenne, Fortaleza |
| Argentina, normal tid | (UTC-03:00) Staden Buenos Aires |
| Grönland, normal tid | (UTC-03:00) Grön |
| Montevideo, normal tid | (UTC-03:00) Montevideo, |
| Magallanes normal tid | (UTC-03:00) Punta Arenas |
| Saint Pierre, normal tid | (UTC-03:00) Saint Pierre och Miquelon |
| Bahia, normal tid | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Koordinerad universell tid-02 |
| Mid-Atlantic normal tid | (UTC-02:00) Mid-Atlantic – gammal |
| Azorerna, normal tid | (UTC-01:00) Azorerna |
| Kap Verde, normal tid | (UTC-01:00) Kap Verde |
| UTC | (UTC) Koordinerad universell tid |
| GMT, normal tid | (UTC + 00:00) Dublin, Edinburgh, Lissabon, London |
| Greenwich, normal tid | (UTC + 00:00) Monrovia, Reykjavik |
| A. Europa, normal tid | (UTC + 01:00) Amsterdam, Berlin, Bern, Rom, Stockholm, Wien |
| Central Europa, normal tid | (UTC + 01:00) Belgrad, Bratislava, Budapest, Ljubljana, Prag |
| Romantik, normal tid | (UTC + 01:00) Bryssel, köpen hamn, Madrid, Paris |
| Marocko, normal tid | (UTC + 01:00) Casablanca |
| Sao Tome, normal tid | (UTC + 01:00) Sao Tome |
| Central europeisk normal tid | (UTC + 01:00) Sarajevo, Skopje, Warszawa, Zagreb |
| A. Central Afrika, normal tid | (UTC + 01:00) Västra Centralafrika |
| Jordanien, normal tid | (UTC + 02:00) Amman |
| GTB, normal tid | (UTC + 02:00) Aten, Bukarest |
| Mellanöstern, normal tid | (UTC + 02:00) Beirut |
| Egypten, normal tid | (UTC + 02:00) Cairo |
| E. Europa, normal tid | (UTC + 02:00) Chisinau |
| Syrien, normal tid | (UTC + 02:00) Damaskus |
| Västra bankens normal tid | (UTC + 02:00) Västbanken, Hebron |
| Södra Afrika, normal tid | (UTC + 02:00) Harare, Pretoria |
| FLE, normal tid | (UTC + 02:00) Helsingfors, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Israel, normal tid | (UTC + 02:00) Jord |
| Kaliningrad normal tid | (UTC + 02:00) Kaliningrad |
| Sudan, normal tid | (UTC + 02:00) Khartoum |
| Libyen, normal tid | (UTC + 02:00) Tripoli |
| Namibia, normal tid | (UTC + 02:00) Windhoek |
| Irak, normal tid | (UTC + 03:00) Bagdad |
| Turkiet, normal tid | (UTC + 03:00) Istanbul |
| Arabemiraten, normal tid | (UTC + 03:00) Kuwait, Riyad |
| Vitryssland, normal tid | (UTC + 03:00) Minsk |
| Ryssland, normal tid | (UTC + 03:00) Moskva, St. Petersburg |
| E. Afrika, normal tid | (UTC + 03:00) Nairobi |
| Iran, normal tid | (UTC + 03:30) Teheran |
| Arabisk normal tid | (UTC + 04:00) Abu Dhabi, Muskat |
| Astrachan, normal tid | (UTC + 04:00) Astrachan,, Ulyanovsk |
| Azerbajdzjan, normal tid | (UTC + 04:00) Baku |
| Ryssland-tid Zon 3 | (UTC + 04:00) Izjevsk, Samara |
| Mauritius, normal tid | (UTC + 04:00) Port Louis |
| Saratov normal tid | (UTC + 04:00) Saratov |
| Georgien, normal tid | (UTC + 04:00) Tbilisi |
| Volgograd, normal tid | (UTC + 04:00) Volgograd |
| Kaukasus, normal tid | (UTC + 04:00) Jerevan |
| Afghanistan, normal tid | (UTC + 04:30) Kabul |
| Västra Asien, normal tid | (UTC + 05:00) Asjchabad, Tasjkent |
| Jekaterinburg normal tid | (UTC + 05:00) Jekaterinburg |
| Pakistan, normal tid | (UTC + 05:00) Islamabad, Karachi |
| Indien, normal tid | (UTC + 05:30) Chennai, Calcutta, Mumbai, New Delhi |
| Sri Lanka, normal tid | (UTC + 05:30) Sri Jayawardenapura |
| Nepal, normal tid | (UTC + 05:45) Katmandu |
| Central Asien, normal tid | (UTC + 06:00) Astana |
| Bangladesh, normal tid | (UTC + 06:00) Dacca |
| Omsk normal tid | (UTC + 06:00) Omsk |
| Myanmar, normal tid | (UTC + 06:30) Yangon (Yangon) |
| Sydostasien, normal tid | (UTC + 07:00) Bangkok, Hanoi, Jakarta |
| Altai normal tid | (UTC + 07:00) Barnaul, Gorno-Altaysk |
| A. Mongoliet, normal tid | (UTC + 07:00) Hovd |
| Nord Asien, normal tid | (UTC + 07:00) Krasnojarsk |
| M. Central Asien, normal tid | (UTC + 07:00) Novosibirsk |
| Tomsk, normal tid | (UTC + 07:00) Tomsk, |
| Kina, normal tid | (UTC + 08:00) Beijing, Chongqing, Hongkong, Urumqi |
| Östra Nord Asien, normal tid | (UTC + 08:00) Irkutsk |
| Singapore, normal tid | (UTC + 08:00) Kuala Lumpur, Singapore |
| A. Australien, normal tid | (UTC + 08:00) Perth |
| Taipei, normal tid | (UTC + 08:00) Taipei, |
| Ulan Bator normal tid | (UTC + 08:00) Ulan Bator |
| Centrala Australien, normal tid | (UTC + 08:45) Eucla |
| Transbajkal, normal tid | (UTC + 09:00) Tjita |
| Tokyo, normal tid | (UTC + 09:00) Osaka, Sapporo, Tokyo |
| Nord Korea, normal tid | (UTC + 09:00) Pyongyang |
| Korea, normal tid | (UTC + 09:00) Söul |
| Jakutsk normal tid | (UTC + 09:00) Jakutsk |
| Cent. Australien, normal tid | (UTC + 09:30) Adelaide |
| Centrala Australien, normal tid | (UTC + 09:30) Darwin |
| E. Australien, normal tid | (UTC + 10:00) Brisbane |
| Östra Australien, normal tid | (UTC + 10:00) Canberra, Melbourne, Sydney |
| Västra Stilla havs området, normal tid | (UTC + 10:00) Guam, Port Moresby |
| Tasmanien, normal tid | (UTC + 10:00) Hobart |
| Vladivostok, normal tid | (UTC + 10:00) Vladivostok, |
| Herren Howe normal tid | (UTC + 10:30) Herren Howe ön |
| Bougainville normal tid | (UTC + 11:00) Bougainville, Julön |
| Ryssland-tidszon 10 | (UTC + 11:00) Chokurdakh |
| Magadan, normal tid | (UTC + 11:00) Magadan |
| Norfolk, normal tid | (UTC + 11:00) Norfolkön |
| Sachalin, normal tid | (UTC + 11:00) Sachalin, |
| Central Pacific, normal tid | (UTC + 11:00) Salomonöarna, Nya Kaledonien |
| Ryssland-tidszon 11 | (UTC + 12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Nya Zeeland, normal tid | (UTC + 12:00) Auckland, Wellington |
| UTC + 12 | (UTC + 12:00) Koordinerad universell tid + 12 |
| Fidji, normal tid | (UTC + 12:00) Fiji |
| Kamtjatka, normal tid | (UTC + 12:00) Petropavlovsk-Kamchatsky – gammal |
| Chathamöarna Islands, normal tid | (UTC + 12:45) Chathamöarna öarna |
| UTC + 13 | (UTC + 13:00) Koordinerad universell tid + 13 |
| Tonga, normal tid | (UTC + 13:00) NUKU ' Alofa |
| Samoa, normal tid | (UTC + 13:00) Samoa |
| Linje öarna, normal tid | (UTC + 14:00) Kiritimati, Julön |

## <a name="see-also"></a>Se även 

- [CURRENT_TIMEZONE (Transact-SQL)](/sql/t-sql/functions/current-timezone-transact-sql)
- [CURRENT_TIMEZONE_ID (Transact-SQL)](/sql/t-sql/functions/current-timezone-id-transact-sql)
- [I tidszon (Transact-SQL)](/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)