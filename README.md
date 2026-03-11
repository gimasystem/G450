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
|Cooling FAN discovery|add cooling fan of the G450 as an item.<BR><BR>(Japanese)<BR>G450内部の冷却ファンを自動登録します。|`SNMP agent`|{#PHYDESCR}_Shutdown threshold<BR>{#PHYDESCR} HiWarning threshold<BR>{#PHYDESCR} LoWarning threshold<BR>{#PHYDESCR}_Status<BR>{#PHYDESCR}_{#SENSORDISP}|
|Ambient Temperature Sensor discovery|temperature sensors and thresholds inside G450 chassis as items.<BR><BR>(Japanese)<BR>G450筐体内部の温度センサーを自動登録します。|`SNMP agent`|{#PHYDESCR} HiShutdown Threshold<BR>{#PHYDESCR} HiWarning Thresholdd<BR>{#PHYDESCR} LoWarning Thresholdd<BR>AmbientTemperature[{#SNMPINDEX}]|
|Module discovery|discovery used SLOT,name,Serial Number,Firmware version,status.<BR><BR>(Japanese)<BR>使用中のモジュール、モジュールの型番、シリアル番号、ファームウェアバージョン、モジュール使用状況やエラーステータスを取得します|`SNMP agent`|ModuleSlot V{#SLOT} FaultMask<BR>ModuleSlot V{#SLOT} Firmware version<BR>ModuleSlot V{#SLOT} SerialNumber<BR>ModuleSlot V{#SLOT} Module Name<BR>ModuleSlot V{#SLOT} ModuleStatusMask<BR>|

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
|Component Description|Description of this component: Media Gateway<BR>(Japanese)<BR>製品フルネームとソフトウェアバージョンです。ホストインベントリフィールドに自動設定されます。|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.3.0|ModelDescription<p>Update:1 day</p>|
|Hardware Sensor Health|The health status of hardware sensors changes when abnormalities are detected, such as power module issues, cooling fan problems, or abnormal chassis temperature.<BR>(Japanese)<BR>ハードウェアセンサーの動作状況<BR>電源モジュール異常を検知したときや冷却ファン、筐体内温度に異常が発生したときに値が変化します。|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.12.0|HardwareFaultMask<p>Update:10 min</p>|
|Hardware Status|<P>changes when abnormalities occur in the media gateway controller, POST, DSP, I/O ports, or LAN modules.</P><P>(Japanese)<BR>POST（Power ON Self Test）のステータス<BR>メディアゲートウェイのコントローラやDSP、I/Oポート、LANモジュールなどに異常が発生したときに値が変化します。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.13.0|HardwareStatusMask<p>Update:5 min</p>|

### CPU status
<P>
The CPU load percentage and CPU monitor settings are registered as items. When the CPU monitor settings are disabled, the CPU load percentage will be output as 0%. If you do not wish to monitor the CPU load, it is recommended to disable the CPU status monitoring items or enable the CPU monitor settings via CLI.
</p>
<P>
(Japanese)<BR>
CPU負荷率、およびCPUモニタ設定の状態を監視します。
  <BR> 
注意： G450の仕様により、CPUモニタ設定はデフォルトで「無効」となっています。無効時はCPU負荷率が常に0%と出力されるため、監視を行う場合は事前にG450のCLIから設定を「有効」に変更してください。監視が不要な場合は、本アイテムを無効化することを推奨します。
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|CPU Utilization (1min Average)|<P>curent average CPU utilization in percentage</P><P>(Japanese)<BR>CPU使用率をパーセンテージで示します。<BR>1分間のCPU負荷率を5秒間隔でサンプルした平均値です。<BR>CPU使用率のモニタリング設定が有効になっている必要があります。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.5.10|genCpuAverageUtilization<p>Update:5s</p>|
|CPU Utilization (current)|<P>The current CPU utilization in percentage if the monitoring is enabled.The sample interval is 5 seconds.</P><P>(Japanese)<BR>現在のCPU使用率をパーセンテージで示します。<BR>CPU使用率のモニタリング設定が有効になっている必要があります。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.6.10|genCpuCurrentUtilization|
|CPU Utilization Enable Monitoring|When the CPU usage monitoring setting is enabled, this value will be set to 1.<P>(Japanese)<BR>CPU使用率のモニタリング設定が有効になっていると、この値が1になります。</P>|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.1.1.1.2.10|genCpuUtilizationEnableMonitoring|

### Memory status
<P>
Memory容量や使用率が登録されます。
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|total count of memory failures|メモリの割り当てに失敗した回数|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.2.4.0|MemoryUtilizationAllocationFailures<p>Update:5m</p>|
|available dynamically allocated memory|利用可能な動的割り当てメモリ（Byte単位）| `SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.2.3.3.0|MemoryUtilizationDynAllocMemAvailable|
|allocated memory|動的に割り当てられたメモリ(バイト単位)|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.2.3.1.0|MemoryUtilizationDynAllocMemUsed|

### Module status
接続しているモジュールの型番やシリアル番号、ステータスが登録されます。
|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|Component Model Description| | `SNMP agent`| | |
|Component Model Number| | `SNMP agent`| | |
|Component Serial Number| | `SNMP agent`| | |


### Cooling FAN status

<P>
冷却ファンがディスカバリで自動登録されます。
冷却ファンの回転数、ステータス、発報のしきい値が監視アイテムとして登録されます
</P>

### Temperature Sensor status

<P>
温度センサーがディスカバリで自動登録されます。
筐体温度、ステータス、発報のしきい値が監視アイテムとして登録されます
{#PHYDESCR}の部分はディスカバリで自動登録された名称が登録されます
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|{#PHYDESCR}| | `SNMP agent`| | |
|{#PHYDESCR} HiShutdown Threshold| | `SNMP agent`| | |
|{#PHYDESCR} HiWarning Threshold| | `SNMP agent`| | |
|{#PHYDESCR} LoWarning Threshold| | `SNMP agent`| | |
|{#PHYDESCR} Status| | `SNMP agent`| | |

### DSP Module Status, DSP status

<P>
DSPボードのステータスや負荷状況が自動登録されます。<BR>
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|DSP Core Admin State #{#COREID}| | `SNMP agent`| | |
|DSP Core Channels InUse #{#COREID}| | `SNMP agent`| | |
|DSP Core Demand Test #{#COREID}| | `SNMP agent`| | |
|DSP Core Demand Test Result #{#COREID}| | `SNMP agent`| | |
|DSP Core Status #{#COREID}| | `SNMP agent`| | |
|DSP Core Total Channels #{#COREID}| | `SNMP agent`| | |

### VoIP connect Status
G450が接続しているCall Controler (主にACM) への接続状況が監視アイテムとして登録されます
|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|VoIP Average Occupancy No{#VOIPSLOT}| | `SNMP agent`| | |
|VoIP Channels In Use No{#VOIPSLOT}| | `SNMP agent`| | |
|VoIP Fault Mask No{#VOIPSLOT}| | `SNMP agent`| | |
|VoIP Hyper Activity No{#VOIPSLOT}| | `SNMP agent`| | |
|VoIP Total Channels| | `SNMP agent`| | |

### ISDN Clock status

<P>
The status of the ISDN synchronization clock is registered as a monitoring item.
If clock synchronization with the peer connected via a BRI or PRI line fails, voice quality may degrade, or calls may be disconnected.<BR>
(Japanese)<BR>
ISDN同期クロックの稼働状況を監視します。BRIやPRI回線において、対向機器とのクロック同期に不整合が生じると、音声通信へのノイズ混入や品質劣化、あるいは予期せぬ通話切断（通信断）が発生するリスクがあります。
音声基盤の安定稼働を評価するための重要な指標となります。
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|ISDN Active Clock Source| | `SNMP agent`| | |
|ISDN Primary Clock Source| | `SNMP agent`| | |
|ISDN Secondary Clock Source| | `SNMP agent`| | |
|Media gateway fault| | `SNMP agent`| | |

## Triggers
testing now
