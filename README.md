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
The system's serial number and the overall operational status of the hardware are registered as items.
</P>
<P>
(Japanese)<BR>
システムのシリアル番号やハードウェア全体の稼働状況がアイテムとして登録されます。
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|Component Description|Description of this component: Media Gateway<BR><BR>(Japanese)<BR>製品フルネームとソフトウェアバージョンです。ホストインベントリフィールドに自動設定されます。|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.3.0|ModelDescription<p>Update:1 day</p>|
|Hardware Sensor Health|The health status of hardware sensors changes when abnormalities are detected, such as power module issues, cooling fan problems, or abnormal chassis temperature.<BR><BR>(Japanese)<BR>ハードウェアセンサーの動作状況<BR>電源モジュール異常を検知したときや冷却ファン、筐体内温度に異常が発生したときに値が変化します。|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.12.0|HardwareFaultMask<p>Update:10 min</p>|
|Hardware Status|<P>changes when abnormalities occur in the media gateway controller, POST, DSP, I/O ports, or LAN modules.</P><P>(Japanese)<BR>POST（Power ON Self Test）のステータス<BR>メディアゲートウェイのコントローラやDSP、I/Oポート、LANモジュールなどに異常が発生したときに値が変化します。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.13.0|HardwareStatusMask<p>Update:5 min</p>|

### CPU status
<P>
The CPU load percentage and CPU monitor settings are registered as items. When the CPU monitor settings are disabled, the CPU load percentage will be output as 0%. If you do not wish to monitor the CPU load, it is recommended to disable the CPU status monitoring items or enable the CPU monitor settings via CLI.
</p>
<P>
(Japanese)<BR>
CPU負荷率およびCPUモニタ設定がアイテムとして登録されます。<BR>
CPUモニタ設定が無効の時はCPU負荷率が0%として出力されます。CPU負荷率を監視しない場合、CPU statusに関する監視アイテムを無効にするか、CLIからCPUモニタ設定を有効にすることをお勧めします。
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|CPU Utilization (1min Average)|<P>curent average CPU utilization in percentage</P><P>(Japanese)<BR>CPU使用率をパーセンテージで示します。<BR>1分間のCPU負荷率を5秒間隔でサンプルした平均値です。<BR>CPU使用率のモニタリング設定が有効になっている必要があります。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.5.10|genCpuAverageUtilization<p>Update:5s</p>|
|CPU Utilization (current)|<P>The current CPU utilization in percentage if the monitoring is enabled.The sample interval is 5 seconds.</P><P>(Japanese)<BR>現在のCPU使用率をパーセンテージで示します。<BR>CPU使用率のモニタリング設定が有効になっている必要があります。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.6.10|genCpuCurrentUtilization|
|CPU Utilization Enable Monitoring|When the CPU usage monitoring setting is enabled, this value will be set to 1.<P>(Japanese)<BR>CPU使用率のモニタリング設定が有効になっていると、この値が1になります。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.2.10|genCpuUtilizationEnableMonitoring|

### Memory status
<P>
memory usage 
</P>

<P>
(Japanese)<BR>
Memory容量や使用率が登録されます。
</P>

### Module status

<P>The model numbers, serial numbers, and statuses of the connected modules are registered.</P>

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
筐体温度、ステータス、発報のしきい値が監視アイテムとして登録されます
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

<P>
The status of the ISDN synchronization clock is registered as a monitoring item.
If clock synchronization with the peer connected via a BRI or PRI line fails, voice quality may degrade, or calls may be disconnected.<BR>
(Japanese)<BR>
ISDN同期クロックの状況が監視アイテムとして登録されます。
BRI回線またはPRI回線接続の対向先とクロックの同期ができていないと、音声品質が悪化したり通話が切れることがあります。
</P>

## Triggers
testing now

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
