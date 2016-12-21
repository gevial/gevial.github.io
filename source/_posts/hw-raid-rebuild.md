---
title: Ребилд аппаратного RAID-массива
description: "Добавляем новый диск в массив с помощью MegaCli"
date: 2015-06-17
tags: [hardware, storage]
---
У физических дисков в аппаратных RAID-массивах LSI и Intel есть атрибут Firmware State – “состояние прошивки”.

При штатной работе состояние всех дисков должно быть “Online”. В случае неисправности состояние может перейти в “Failed” или “Unconfigured(bad)”.
Иногда (может, это зависит от модели контроллера и версий ПО) после замены диска на новый состояние не меняется – остаётся “Unconfigured (bad)”. В таком случае требуется добавить заменённый диск в массив вручную.

Выводим информацию о физических дисках и определяем сбойный:

```
# /root/MegaRAID/MegaCli/MegaCli64 -PDList -aAll
Adapter #0
Enclosure Device ID: 252
Slot Number: 8
Device Id: 8
Sequence Number: 2
Media Error Count: 0
Other Error Count: 0
Predictive Failure Count: 0
Last Predictive Failure Event Seq Number: 0
PD Type: SAS
Raw Size: 140014MB [0x11177330 Sectors]
Non Coerced Size: 139502MB [0x11077330 Sectors]
Coerced Size: 139236MB [0x10ff2000 Sectors]
Firmware state: Online
SAS Address(0): 0x5000c500241370a5
SAS Address(1): 0x0
Connected Port Number: 1(path0)
Inquiry Data: SEAGATE ST3146356SS 00063QN4DX50
Foreign State: None

Enclosure Device ID: 252
Slot Number: 9
Device Id: 9
Sequence Number: 2
Media Error Count: 0
Other Error Count: 0
Predictive Failure Count: 0
Last Predictive Failure Event Seq Number: 0
PD Type: SAS
Raw Size: 140014MB [0x11177330 Sectors]
Non Coerced Size: 139502MB [0x11077330 Sectors]
Coerced Size: 139236MB [0x10ff2000 Sectors]
Firmware state: Unconfigured(bad)
SAS Address(0): 0x5000c50023e46d19
SAS Address(1): 0x0
Connected Port Number: 2(path0)
Inquiry Data: SEAGATE ST3146356SS 00063QN4CW33
Foreign State: None
...
```

В данном случае сбойный диск имеет Enclosure ID 252 и Slot 9. Его идентификатор в MegaCli будет выглядеть как [252:9]. В случае, когда значение Enclosure ID равно N/A, идентификатор принимает вид [:9]. Принудительно помечаем диск как “good”:

```
# /root/MegaRAID/MegaCli/MegaCli64 -PDMakeGood -PhysDrv[252:9] -a0
Adapter: 0: EnclId-252 SlotId-9 state changed to Unconfigured-Good.
```

После этого контроллер должен определить диск как “чужой” – “foreign”. Это значит, что контроллер нашёл на диске метаинформацию другого RAID-массива.

```
# /root/MegaRAID/MegaCli/MegaCli64 -CfgForeign -Scan -a0
There are 1 foreign configuration(s) on controller 0. “Чужую” конфигурацию нужно очистить.
# /root/MegaRAID/MegaCli/MegaCli64 -CfgForeign -Clear -a0
Foreign configuration 0 is cleared on controller 0.
```

Определим положение диска в массиве. Если после заголовка Physical Disk – пустая строка, значит этот диск не является частью массива.

```
#/root/MegaRAID/MegaCli/MegaCli64 -CfgDsply -a0
...
DISK GROUPS: 1
Number of Spans: 1
SPAN: 0           
Span Reference: 0x01
Number of PDs: 4    
Number of VDs: 1    
Number of dedicated Hotspares: 0
Virtual Disk Information:       
Virtual Disk: 0 (Target Id: 1)
...

Physical Disk: 2

Physical Disk: 3
Enclosure Device ID: 252
Slot Number: 5          
Device Id: 4            
...
```

Расположение диска в массиве идентифицируется номером массива, который соответствует Span Reference (в нашем случае 1, 0×0 следует отбросить) и номером ряда, который соответствует номеру диска (в данном случае 2). Подключим диск [252:9] в массив 1, ряд 2.

```
# /root/MegaRAID/MegaCli/MegaCli64 -PdReplaceMissing -PhysDrv[252:9] -array1 -row2 -a0
Adapter: 0: Missing PD at Array 1, Row 2 is replaced
```

Теперь следует вручную запустить ребилд массива.

```
# /root/MegaRAID/MegaCli/MegaCli64 -PDRbld -Start -PhysDrv[252:4] -a0
Started rebuild progress on device(Encl-252 Slot-4)
```

Cсылки: [http://hwraid.le-vert.net/wiki/LSIMegaRAIDSAS](http://hwraid.le-vert.net/wiki/LSIMegaRAIDSAS)
