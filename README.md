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
- [ ] 挿されているモジュールの型番とシリアル番号
- [ ] モジュールのステータス
- [ ] DSPおよびエコーキャンセラーのステータス
- [ ] ACMへのレジスト情報
- [ ] 冷却FANの回転数
- [ ] トリガー各種
- [ ] 筐体内温度のしきい値
- [ ] CPU使用率のしきい値
- [ ] メモリ使用率のしきい値
