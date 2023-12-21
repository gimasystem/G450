# Avaya G450 monitor template for Zabbix 6.0
## Overview
Zabbix6.0でAvaya製 VoIP-GW G450を監視するための監視テンプレートです
## Zabbixへのインポート方法
*see also https://www.zabbix.com/documentation/current/jp/manual/xml_export_import/templates

## G450 初期状態からの設定方法 ( G450 configuration )
* PCのIPアドレスを変更する
* G450とPCをLANケーブルで接続する
* PCからSSH接続する(推奨：teraterm)
* ID/Passを入力する
* G450にSSH接続したことを確認する
* SNMP設定する
* 設定コピー

## Template links
SSH Service

## Items collected
|Name|Description|type|OID|Key and additional info|
|----|-----------|----|----|----|
|型番(詳細)|Description of this component: G700 Media Gateway|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.3.0|cmgDescription<p>Update:1 day</p>|
|ハードウェアモジュールの健康状態|ハードウェアモジュールに不具合が発生したときに、不具合発生フラグ1が立ちます。|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.12.0|cmgHardwareFaultMask<p>Update:10 min</p>|
|ハードウェアステータス|POSTのステータス|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.10.13.0|cmgHardwareStatusMask<p>Update:5 min</p>|
|ハードウェアタイプ|type of component|`SNMP agent`|.1.3.6.1.4.1.6889.2.9.1.1.1.0|cmgHWType<p>Update:1 day</p>|


## Triggers
未実装 (unimplemented)

## アイテム追加予定
* 挿されているモジュールの型番とシリアル番号
* モジュールのステータス
* ACMへのレジスト情報
* 冷却FANの回転数
* トリガー
* SNMPトラップ
* 筐体内温度
* CPU使用率のしきい値
* メモリ使用率のしきい値
* 筐体内温度のしきい値
