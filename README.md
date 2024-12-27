# Avaya G450 monitor template for Zabbix 6.0
## Overview
Zabbix6.0でAvaya製 VoIP-GW G450を監視するための監視テンプレートです
## Zabbixへのインポート方法
[Zabbixマニュアル](https://www.zabbix.com/documentation/current/jp/manual/xml_export_import/templates) 参照

## G450のSNMP設定手順順
1. PCからG450にSSH接続する(推奨：teraterm)
1. "snmp-server community read-only public read-write private" と入力してSNMPの設定を追加する (読み込み用のコミュニテイ名を"public" 書き込み可のコミュニテイ名を"private")
1. "copy running-config startup-config” と入力して設定を保存する。
2. 
#### Avaya G450 SNMP configuration manual
[SNMP access configuration](https://documentation.avaya.com/ja-JP/bundle/AvayaG450BranchGatewayCLIReference_10.2.x/page/SNMPAccessConfiguration.html)

## Template links
no links

## Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Cooling FAN discovery|(Englisth)<BR>add cooling fan of the G450 as an item.<BR><BR>(Japanese)<BR>G450内部の冷却ファンを登録します。|`SNMP agent`|{#PHYDESCR}_Shutdown threshold<BR>{#PHYDESCR} HiWarning threshold<BR>{#PHYDESCR} LoWarning threshold<BR>{#PHYDESCR}_Status<BR>{#PHYDESCR}_{#SENSORDISP}|
|Ambient Temperature Sensor discovery|(Englisth)<BR>temperature sensors and thresholds inside G450 chassis as items.<BR><BR>(Japanese)<BR>G450筐体内部の温度センサーを登録します。|`SNMP agent`|{#PHYDESCR} HiShutdown Threshold<BR>{#PHYDESCR} HiWarning Thresholdd<BR>{#PHYDESCR} LoWarning Thresholdd<BR>AmbientTemperature[{#SNMPINDEX}]|
|Module discovery|(Englisth)<BR>discovery used SLOT,name,Serial Number,Firmware version,status.<BR><BR>(Japanese)<BR>使用中のモジュール、モジュールの型番、シリアル番号、ファームウェアバージョン、モジュール使用状況やエラーステータスを取得します|`SNMP agent`|ModuleSlot V{#SLOT} FaultMask<BR>ModuleSlot V{#SLOT} Firmware version<BR>ModuleSlot V{#SLOT} SerialNumber<BR>ModuleSlot V{#SLOT} Module Name<BR>ModuleSlot V{#SLOT} ModuleStatusMask<BR>|


## Items collected

### System Inventory & hardware status

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|Component Description|Description of this component: G700 Media Gateway<BR><BR>(Japanese)<BR>筐体の型番名|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.3.0|cmgDescription<p>Update:1 day</p>|
|ハードウェアモジュールの健康状態|ハードウェアモジュールに不具合が発生したときに、不具合発生フラグ1が立ちます。|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.12.0|cmgHardwareFaultMask<p>Update:10 min</p>|
|ハードウェアステータス|POSTのステータス|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.13.0|cmgHardwareStatusMask<p>Update:5 min</p>|
|ハードウェアタイプ|type of component|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.1.0|cmgHWType<p>Update:1 day</p>|

### CPU status

(Japanese)
CPUの負荷率の他に、CPUモニタ設定がアイテムとして登録されています。

### Memory status

(Japanese)
Memory容量や使用率がアイテムとして登録されています。

### Module status

(Japanese)

ディスカバリで自動検出されたモジュール番号やモジュールの型番、シリアル番号、ステータスが登録されます。<BR>Avaya G450に接続されているモジュールの型番やシリアル番号、ステータスが登録されます。

### Cooling FAN status

(Japanese)
冷却ファン毎にディスカバリで自動検出されたアイテムが登録されます。

### Temperature Sensor status

(Japanese)
ディスカバリで自動検出された温度センサーが登録されます。
システムが停止する筐体温度の閾値や、発報する閾値が自動登録されます。

## Triggers
未実装 (unimplemented)

## アイテム追加予定
- [x] 挿されているモジュールの型番とシリアル番号
- [x] モジュールのステータス
- [ ] DSPおよびエコーキャンセラーのステータス
- [ ] ACMへのレジスト情報
- [x] 冷却FANの回転数
- [ ] トリガー各種
- [x] 筐体内温度のしきい値
- [ ] CPU使用率のしきい値
- [ ] メモリ使用率のしきい値
