# Avaya G450 monitor template for Zabbix 6.0
## Overview
ZABBIX monitoring template for Avaya G450 Media Gateway.

<P>(Japanese)<BR>
Avaya製VoIP-GW G450用のZabbix監視テンプレートです。
</P>

## G450 Configuration
1. connect SSH to Avaya G450 (recommend:teraterm)
1. put "snmp-server community read-only public read-write private"
1. put "copy running-config startup-config”

#### Avaya G450 SNMP configuration manual
[SNMP access configuration](https://documentation.avaya.com/ja-JP/bundle/AvayaG450BranchGatewayCLIReference_10.2.x/page/SNMPAccessConfiguration.html)

## Template links
no links

## Discovery rules

|Name|Description|Type|Key and additional info|
|----|-----------|----|----|
|Cooling FAN discovery|(English)<BR>add cooling fan of the G450 as an item.<BR><BR>(Japanese)<BR>G450内部の冷却ファンを自動登録します。|`SNMP agent`|{#PHYDESCR}_Shutdown threshold<BR>{#PHYDESCR} HiWarning threshold<BR>{#PHYDESCR} LoWarning threshold<BR>{#PHYDESCR}_Status<BR>{#PHYDESCR}_{#SENSORDISP}|
|Ambient Temperature Sensor discovery|(Englisth)<BR>temperature sensors and thresholds inside G450 chassis as items.<BR><BR>(Japanese)<BR>G450筐体内部の温度センサーを自動登録します。|`SNMP agent`|{#PHYDESCR} HiShutdown Threshold<BR>{#PHYDESCR} HiWarning Thresholdd<BR>{#PHYDESCR} LoWarning Thresholdd<BR>AmbientTemperature[{#SNMPINDEX}]|
|Module discovery|(Englisth)<BR>discovery used SLOT,name,Serial Number,Firmware version,status.<BR><BR>(Japanese)<BR>使用中のモジュール、モジュールの型番、シリアル番号、ファームウェアバージョン、モジュール使用状況やエラーステータスを取得します|`SNMP agent`|ModuleSlot V{#SLOT} FaultMask<BR>ModuleSlot V{#SLOT} Firmware version<BR>ModuleSlot V{#SLOT} SerialNumber<BR>ModuleSlot V{#SLOT} Module Name<BR>ModuleSlot V{#SLOT} ModuleStatusMask<BR>|

## Items collected

### System infomateion & hardware status

<P>
(Japanese)<BR>
システムのシリアル番号や、ハードウェア全体の健康状態がアイテムとして登録されます。
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|Component Description|Description of this component: G700 Media Gateway<BR><BR>(Japanese)<BR>筐体の型番名|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.3.0|cmgDescription<p>Update:1 day</p>|
|Hardware Sensor Health|<BR><BR>(Japanese)<BR>ハードウェアセンサーの健康状態<BR>電源モジュール異常を検知したときや冷却ファン、筐体内温度に異常が発生したときに値が変化します。|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.12.0|cmgHardwareFaultMask<p>Update:10 min</p>|
|Hardware Status|<P>(Japanese)<BR>POST（Power ON Self Test）のステータス<BR>メディアゲートウェイのコントローラやDSP、I/Oポート、LANモジュールなどに異常が発生したときに値が変化します。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.13.0|cmgHardwareStatusMask<p>Update:5 min</p>|
|ハードウェアタイプ|<P>type of component</P><P>(Japanese)<BR></P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.1.0|cmgHWType<p>Update:1 day</p>|

### CPU status

<P>
(Japanese)<BR>
CPU負荷率およびCPUモニタ設定がアイテムとして登録されます。<BR>
CPUモニタ設定が無効の時はCPU負荷率が0%として出力されます。CPU負荷率を監視しない場合、CPU statusに関する監視アイテムを無効にするか、CLIからCPUモニタ設定を有効にすることをお勧めします。
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|CPU Utilization (1min Average)|<P>curent average CPU utilization in percentage</P><P>(Japanese)<BR>CPU使用率をパーセンテージで示します。<BR>1分間のCPU負荷率を5秒間隔でサンプルした平均値です。<BR>CPU使用率のモニタリング設定が有効になっている必要があります。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.5.10|genCpuAverageUtilization<p>Update:5s</p>|
|CPU Utilization (current)|<P>The current CPU utilization in percentage if the monitoring is enabled.The sample interval is 5 seconds.</P><P>(Japanese)<BR>現在のCPU使用率をパーセンテージで示します。<BR>CPU使用率のモニタリング設定が有効になっている必要があります。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.6.10|genCpuCurrentUtilization|
|CPU Utilization Enable Monitoring|<P>(Japanese)<BR>CPU使用率のモニタリング設定が有効になっていると、この値が1になります。</P>|`SNMP agent`|  |  |

### Memory status

<P>
(Japanese)<BR>
Memory容量や使用率が登録されます。
</P>

### Module status

<P>(Japanese)<BR>
接続しているモジュールの型番やシリアル番号、ステータスが登録されます。
</P>

### Cooling FAN status

<P>(Japanese)<BR>
冷却ファンがディスカバリで自動登録されます。
冷却ファンの回転数、ステータス、発報のしきい値が監視アイテムとして登録されます
</P>

### Temperature Sensor status

<P>(Japanese)<BR>
温度センサーがディスカバリで自動登録されます。
筐体温度、ステータス、、発報のしきい値が監視アイテムとして登録されます
</P>

### DSP Module Status, DSP status

<P>(Japanese)<BR>
DSPボードのステータスや負荷状況が自動登録されます。<BR>
</P>

### VoIP connect Status

<P>(Japanese)<BR>
G450が接続しているCall Controler (主にACM) への接続状況が監視アイテムとして登録されます
</P>

### ISDN Clock status

<P>(Japanese)<BR>
ISDN同期クロックの状況が監視アイテムとして登録されます。
BRI回線またはPRI回線接続の対向先とクロックの同期ができていないと、音声品質が悪化したり通話が切れることがあります。
</P>

## Triggers
未実装 (unimplemented)

## Item implemente plan
- [x] 挿されているモジュールの型番とシリアル番号
- [x] モジュールのステータス
- [x] DSPおよびエコーキャンセラーのステータス
- [x] ACMへのレジスト情報
- [x] 冷却FANの回転数
- [ ] トリガー各種
- [x] 筐体内温度のしきい値
- [ ] CPU使用率のしきい値
- [ ] メモリ使用率のしきい値
