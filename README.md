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
メモリの使用量・空き容量およびメモリ割り当て失敗回数を監視します。
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|total count of memory failures|メモリの割り当てに失敗した累積回数|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.2.4.0|MemoryUtilizationAllocationFailures<p>Update:5m</p>|
|available dynamically allocated memory|利用可能な動的割り当てメモリ（Byte単位）| `SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.2.3.3.0|MemoryUtilizationDynAllocMemAvailable|
|allocated memory|動的に割り当てられたメモリ(バイト単位)|`SNMP agent`|.1.3.6.1.4.1.6889.2.1.11.1.2.3.1.0|MemoryUtilizationDynAllocMemUsed|

### System information
接続しているモジュールの型番やシリアル番号が登録されます。
|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
| Component Model Description | コンポーネントの製品名・ソフトウェアバージョン | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.1.3.0` | `ModelDescription`<p>Update: 1d</p> |
| Component Model Number      | コンポーネントのハードウェア型番        | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.1.2.0` | `ModelNumber`                       |
| Component Serial Number     | ゲートウェイ本体のシリアル番号         | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.1.4.0` | `SerialNumber`<p>Update: 1d</p>     |


### Cooling FAN status

<P>
冷却ファンがディスカバリで自動登録されます。<BR>
冷却ファンの回転数、ステータス、発報のしきい値が監視アイテムとして登録されます
</P>

<P>
冷却ファン交換後は1分以上経ってから `show platform fans` コマンドで、ステータスを確認することをお勧めします。
</P>

| Name                       | Description              | type         | OID                                           | Key and additional info                              |
| -------------------------- | ------------------------ | ------------ | --------------------------------------------- | ---------------------------------------------------- |
| Fan rotation speed         | 冷却ファンの回転数（RPM）           | `SNMP agent` | `.1.3.6.1.2.1.99.1.1.1.4.{#SNMPINDEX}`        | `FanSensorRPM[{#SNMPINDEX}]`<p>Update: 5m</p>        |
| Fan sensor status          | 冷却ファンの回転数センサーの状態         | `SNMP agent` | `.1.3.6.1.2.1.99.1.1.1.5.{#SNMPINDEX}`        | `FanSensorStatus[{#SNMPINDEX}]`<p>Update: 5m</p>     |
| Fan shutdown threshold     | 冷却ファンの回転数異常と判断する停止しきい値   | `SNMP agent` | `.1.3.6.1.4.1.6889.2.1.99.1.1.1.{#SNMPINDEX}` | `FanSensorHiShutdown[{#SNMPINDEX}]`<p>Update: 2h</p> |
| Fan high warning threshold | 冷却ファンの回転数が高くなった場合の警告しきい値 | `SNMP agent` | `.1.3.6.1.4.1.6889.2.1.99.1.1.2.{#SNMPINDEX}` | `FanSensorHiWarning[{#SNMPINDEX}]`<p>Update: 2h</p>  |
| Fan low warning threshold  | 冷却ファンの回転数が低下した場合の警告しきい値  | `SNMP agent` | `.1.3.6.1.4.1.6889.2.1.99.1.1.5.{#SNMPINDEX}` | `FanSensorLoWarning[{#SNMPINDEX}]`<p>Update: 2h</p>  |

### Temperature Sensor status

<P>
温度センサーがディスカバリで自動登録されます。<BR>
「温度、センサーのステータス、および温度異常を判定するためのしきい値が監視アイテムとして登録されます。<BR>
{#PHYDESCR}には、ディスカバリで自動登録された名称が登録されます
</P>

| Name                               | Description                                                          | type         | OID                                           | Key and additional info                                                                        |
| ---------------------------------- | -------------------------------------------------------------------- | ------------ | --------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `{#PHYDESCR}`                      | 温度センサーで測定した温度                                                        | `SNMP agent` | `.1.3.6.1.2.1.99.1.1.1.4.{#SNMPINDEX}`        | `TemperatureSensor[{#SNMPINDEX}]`<p>Update: 5m</p><p>Units: `{#SENSORDISP}`</p>                |
| `{#PHYDESCR} HiShutdown Threshold` | 温度センサーのシャットダウンしきい値。温度がこの値に達した場合、過熱による異常としてシステムのシャットダウンが発生する可能性があります。 | `SNMP agent` | `.1.3.6.1.4.1.6889.2.1.99.1.1.1.{#SNMPINDEX}` | `TemperatureSensorHiShutdown[{#SNMPINDEX}]`<p>Update: 2h</p><p>Units: `{#SENSORDISP}`</p>      |
| `{#PHYDESCR} HiWarning Threshold`  | 温度センサーの高温警告しきい値。温度がこの値を超えた場合に高温警告の対象となります。                           | `SNMP agent` | `.1.3.6.1.4.1.6889.2.1.99.1.1.2.{#SNMPINDEX}` | `TemperatureSensorHiWarning[{#SNMPINDEX}]`<p>Update: 2h</p><p>Units: `{#SENSORDISP}`</p>       |
| `{#PHYDESCR} LoWarning Threshold`  | 温度センサーの低温警告しきい値。温度がこの値を下回った場合に低温警告の対象となります。                          | `SNMP agent` | `.1.3.6.1.4.1.6889.2.1.99.1.1.5.{#SNMPINDEX}` | `TemperatureSensorLoWarning[{#SNMPINDEX}]`<p>Update: 2h</p><p>Units: `{#SENSORDISP}`</p>       |
| `{#PHYDESCR} Status`               | 温度センサーの動作状態                                                          | `SNMP agent` | `.1.3.6.1.2.1.99.1.1.1.5.{#SNMPINDEX}`        | `TemperatureSensorStatus[{#SNMPINDEX}]`<p>Update: 5m</p><p>Value map: `EntitySensorStatus`</p> |


### DSP Module Status, DSP status

<P>
DSPボードに搭載されたDSP Coreの状態、使用チャネル数、およびセルフテストの状態・結果がディスカバリによって自動登録されます。<BR>
{#COREID}には、ディスカバリによって取得したDSP CoreのIDが登録されます。
</P>

| Name                                     | Description                                             | type         | OID                                                | Key and additional info                                                                              |
| ---------------------------------------- | ------------------------------------------------------- | ------------ | -------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| DSP Core Admin State `#{#COREID}`        | DSP Coreの管理状態。メンテナンス操作によるBusy-outなど、DSP Coreの管理状態を示します。 | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.4.101.{#SNMPINDEX}` | `DSPCoreAdminState.[{#SNMPINDEX}]`<p>Update: 3m</p><p>Value map: `DSPCoreAdminState`</p>             |
| DSP Core Channels InUse `#{#COREID}`     | DSP Coreで現在使用中の通話チャネル数                                  | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.3.101.{#SNMPINDEX}` | `DSPCoreChannelsInUse.[{#SNMPINDEX}]`<p>Update: 3m</p>                                               |
| DSP Core Demand Test `#{#COREID}`        | DSP Coreのセルフテスト実行状態                                     | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.6.101.{#SNMPINDEX}` | `DSPCoreDemandTest.[{#SNMPINDEX}]`<p>Update: 3m</p><p>Value map: `DSPCoreDemandTest`</p>             |
| DSP Core Demand Test Result `#{#COREID}` | DSP Coreに対して実行されたセルフテストの結果                              | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.7.101.{#SNMPINDEX}` | `DSPCoreDemandTestResult.[{#SNMPINDEX}]`<p>Update: 3m</p><p>Value map: `DSPCoreDemandTestResult`</p> |
| DSP Core Status `#{#COREID}`             | DSP Coreの動作状態                                           | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.5.101.{#SNMPINDEX}` | `DSPCoreStatus.[{#SNMPINDEX}]`<p>Update: 3m</p><p>Value map: `DSPCoreStatus`</p>                     |
| DSP Core Total Channels `#{#COREID}`     | DSP Coreで利用可能な総チャネル数                                    | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.6.1.2.101.{#SNMPINDEX}` | `DSPCoreTotalChannels.[{#SNMPINDEX}]`<p>Update: 3m</p>                                               |

### VoIP engine status and capacity
<P>
G450に搭載されたVoIPエンジンのスロットがディスカバリによって自動登録されます。<BR>
各VoIPエンジンについて、稼働率、使用中チャネル数、障害状態、過負荷状態、およびCommunication Manager（ACM）によって管理される総チャネル数が監視アイテムとして登録されます。<BR>
{#VOIPSLOT}には、ディスカバリによって取得したVoIPエンジンのスロット番号が登録されます。
</P>

| Name | Description | type | OID  | Key and additional |
| ------------------------------------ | ------------------------------------------------ | ------------ | ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------ |
| VoIP Average Occupancy No{#VOIPSLOT} | VoIPエンジンの稼働率（5分間平均）                              | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.8.{#SNMPINDEX}`  | `VoipAverageOccupancy_[{#VOIPSLOT}]`<p>Update: 5m</p>                                                        |
| VoIP Channels In Use No{#VOIPSLOT}   | VoIPエンジンで現在使用中のチャネル数                             | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.7.{#SNMPINDEX}`  | `VoipChannelsInUse_[{#VOIPSLOT}]`                                                                            |
| VoIP Fault Mask No{#VOIPSLOT}        | VoIPエンジンで発生している障害をビット単位で示すフラグ                    | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.14.{#SNMPINDEX}` | `VoipFaultMask_[{#VOIPSLOT}]`<p>Update: 5m</p>                                                               |
| VoIP Hyper Activity No{#VOIPSLOT}    | VoIPエンジンの過負荷状態。過負荷を検出すると `hyperactive(2)` になります。 | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.9.{#SNMPINDEX}`  | `VoipHyperactivity_[{#VOIPSLOT}]`<p>Update: 5m</p><p>Value map: `1=normal / 2=hyperactive / 255=unknown`</p> |
| VoIP Total Channels                  | Communication Manager（ACM）によって管理される総チャネル数        | `SNMP agent` | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.6.{#SNMPINDEX}`  | `VoipTotalChannels_[{#VOIPSLOT}]`<p>Update: 30m</p>                                                          |

### ISDN Clock status / Media Gateway status

<P>
ISDN同期クロックの設定および現在使用しているクロックソースを監視します。<BR>
G450では、Primary、Secondary、Localのクロックソースを設定でき、現在使用しているクロックソースをActive Clock Sourceとして管理します。<BR>
BRIやPRIなどのISDN回線では、対向機器とのクロック同期が適切に行われていることが重要です。クロック同期に問題が発生した場合、音声品質低下や通信障害につながる可能性があります。
</P>

|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|ISDN Active Clock Source|現在使用しているISDNクロックソース。`local` はG450自身のクロック、`remote` は対向機器から供給されるクロックを示します。| `SNMP agent`|`.1.3.6.1.4.1.6889.2.9.1.2.3.3.0`|`ISDNActiveClockSource`<p>Update: 1h</p><p>Value map: `Active Clock Source`</p>|
|ISDN Primary Clock Source|設定されているPrimaryクロックソース。T1/BRIモジュールのポート、VoIPモジュール、または未設定を示します。| `SNMP agent`|`.1.3.6.1.4.1.6889.2.9.1.2.3.1.0`|`ISDNPrimaryClockSource`<p>Update: 1h</p>|
|ISDN Secondary Clock Source|設定されているSecondaryクロックソース。T1/BRIモジュールのポート、VoIPモジュール、または未設定を示します。| `SNMP agent`|`.1.3.6.1.4.1.6889.2.9.1.2.3.2.0`|`ISDNSecondaryClockSource`<p>Update: 1h</p>|
|Media gateway fault|メディアゲートウェイプロセッサの障害発生フラグ。`00 00` 以外の場合、何らかの障害が発生している可能性があります。| `SNMP agent`|`.1.3.6.1.4.1.6889.2.9.1.2.1.15.0`|`MediagatewayFaultMask`<p>Update: 3m</p>|

## Triggers

<p>
監視アイテムの値が設定した条件を満たした場合に、障害として検知します。<BR>
</P>

<p>💡トリガーは実機での発報テストを実施していません。
特に温度センサーについては、個人環境でG450を意図的に高温状態にすることが困難なため、しきい値超過時の発報動作は未確認です。
</P>

|Name|Description|Condition|Severity|
|---|---|---|---|
|High memory utilization|メモリ使用率が80%を超えた場合に発報します。|Memory Usage > 80%|未指定|
|DSP Core Admin State has changed|DSP CoreのAdmin Stateが変更された場合に発報します。|直前値が2（release）ではない場合|INFO|
|Temperature has reached the critical threshold.|温度センサーの測定値がHiShutdown Thresholdを超えた場合に発報します。|Temperature > HiShutdown Threshold|DISASTER|
|Temperature has reached the warning threshold.|温度センサーの測定値がHiWarning Thresholdを超えた場合に発報します。|Temperature > HiWarning Threshold|HIGH|