# Zabbix 8.0 Template for Avaya G450 Media Gateway

Not tested<BR>
Zabbix8 LTS版用に作成しています。Zabbix8正式リリース前のため動作確認されていません。ご注意ください

---

Avaya G450 Media Gateway（およびG700/G350/G250シリーズ互換機器）をSNMP経由でエンタープライズグレード監視するための Zabbix 8.0 LTS 完全対応テンプレートです。

CPU/DSP温度、電源・ファン等のハードウェア状態に加え、H.248制御リンク、CMレジストレーション状態、同期クロック切替、メディアモジュール障害、および **VoIP DSPチャネル使用率・リソース枯渇** をリアルタイムに監視します。

---

## 📋 概要 (Overview)

* **対応機器:** Avaya G450 Media Gateway
* **対応Zabbixバージョン:** Zabbix 7.0 LTS以降
* **監視方式:** SNMPv2c / SNMPv3
* **使用MIB:** `G700-MG-MIB` (`1.3.6.1.4.1.6889.2.9`)

---

## 🔧 動作要件 (Prerequisites)

1. **Avaya G450側の設定**
   * SNMPエージェント（SNMPv2c または SNMPv3）が有効化され、適切なコミュニティ名 / 認証情報が設定されていること。
2. **ネットワーク要件**
   * Zabbix Server / Proxy から対象の G450 管理IPアドレスに対して UDP/161 ポートで通信が可能であること。

---

## ⚙️ ユーザーマクロ (Template Macros)

| マクロ名 | 初期値 (デフォルト) | 説明 |
| :--- | :--- | :--- |
| `{$SNMP_COMMUNITY}` | `public` | SNMPv2cコミュニティ名 |
| `{$CPU_TEMP_WARN}` | `60` | CPU温度警告閾値 (℃) |
| `{$DSP_TEMP_WARN}` | `60` | DSP温度警告閾値 (℃) |

---

## 📊 監視項目一覧 (Monitored Items)

### 1. ハードウェア & 環境ステータス (Environment & Hardware)
| アイテム名 | OID | 説明 |
| :--- | :--- | :--- |
| **CPU Temperature** | `.1.3.6.1.4.1.6889.2.9.1.1.10.1.0` | G450 メインCPU温度 (℃) |
| **DSP Temperature** | `.1.3.6.1.4.1.6889.2.9.1.1.10.4.0` | オンボードDSP基板温度 (℃) |
| **Power Processor Status** | `.1.3.6.1.4.1.6889.2.9.1.1.10.7.0` | メインプロセッサ電源ステータス |
| **Power Media Modules Status** | `.1.3.6.1.4.1.6889.2.9.1.1.10.8.0` | メディアモジュールバス給電ステータス |
| **Power VoIP Complex Status** | `.1.3.6.1.4.1.6889.2.9.1.1.10.9.0` | VoIP Complex / DSP電源ステータス |
| **Fan Speed Level Alert** | `.1.3.6.1.4.1.6889.2.9.1.1.10.14.0` | ファン低速回転・障害警報ステータス |

### 2. H.248・CM制御 & クロック (Control & Synchronization)
| アイテム名 | OID | 説明 |
| :--- | :--- | :--- |
| **CM Registration State** | `.1.3.6.1.4.1.6889.2.9.1.3.1.0` | CMへの登録状態 (1: registered, 2: unregistered, 3: registering) |
| **H.248 Link Status** | `.1.3.6.1.4.1.6889.2.9.1.3.3.0` | H.248制御リンク状態 (1: up, 2: down) |
| **Active Clock Source** | `.1.3.6.1.4.1.6889.2.9.1.2.3.3.0` | アクティブ同期クロックソースID |

### 3. システムインフォメーション & ネットワーク (Inventory & Network)
| アイテム名 | OID | 説明 |
| :--- | :--- | :--- |
| **Current IP Address** | `.1.3.6.1.4.1.6889.2.9.1.2.1.4.0` | 現在運用中のゲートウェイIPアドレス |
| **Model Number** | `.1.3.6.1.4.1.6889.2.9.1.1.2.0` | 機器モデル名 (G450等) |
| **Serial Number** | `.1.3.6.1.4.1.6889.2.9.1.1.4.0` | 筐体シリアル番号 |
| **Firmware Version** | `.1.3.6.1.4.1.6889.2.9.1.2.1.3.0` | ゲートウェイファームウェアバージョン |

### 4. LLD: メディアモジュール自動発見 (`cmgModuleTable`)
| アイテム名 | OID | 説明 |
| :--- | :--- | :--- |
| **Module Slot {#MODULE_SLOT} Description** | `.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.3.{#MODULE_SLOT}` | 装着モジュール名 (MM710, MM711, MM716等) |
| **Module Slot {#MODULE_SLOT} Fault Mask** | `.1.3.6.1.4.1.6889.2.9.1.1.11.1.1.10.{#MODULE_SLOT}` | モジュールごとの障害マスクビット |

### 5. LLD: VoIP Engine & DSPリソース自動発見 (`cmgVoipEngineTable`)
| アイテム名 | OID / キー | 説明 |
| :--- | :--- | :--- |
| **VoIP Fault Mask Slot {#VOIPSLOT}** | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.14.{#SNMPINDEX}` | DSPエンジン障害マスク (正常時: 00 00 00 00) |
| **VoIP Active Channels (Slot {#VOIPSLOT})** | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.7.{#SNMPINDEX}` | 現在使用中のVoIP通話チャネル数 (ch) |
| **VoIP Max Channels (Slot {#VOIPSLOT})** | `.1.3.6.1.4.1.6889.2.9.1.4.5.1.6.{#SNMPINDEX}` | スロットの最大収容可能チャネル数 (ch) |
| **VoIP DSP Usage (Slot {#VOIPSLOT})** | `voip.dsp.usage[{#VOIPSLOT}]` | **[計算アイテム]** DSPチャネル使用率 (%) |

---

## 🚨 トリガー条件一覧 (Triggers)

| トリガー名 | 深刻度 (Severity) | 発生条件・説明 |
| :--- | :--- | :--- |
| **G450: Registration State Failed (Unregistered from CM)** | **High** | Communication ManagerへのH.248登録が解除された場合（音声全滅/LSP切替直前） |
| **G450: H.248 Link Status DOWN** | **High** | H.248制御リンクが切断された場合 |
| **G450: Power Processor Failure** | **High** | メインプロセッサ電源異常 |
| **G450: Power Media Modules Failure** | **High** | メディアモジュール用電源異常 |
| **G450: Power VoIP Complex Failure** | **High** | VoIP DSP基板用電源異常 |
| **G450: Fault detected on Module Slot {#MODULE_SLOT}** | **High** | メディアモジュールカード（MM710/MM711等）の物理障害検出 |
| **G450: VoIP Engine Fault Detected (Slot {#VOIPSLOT})** | **High** | VoIP DSPエンジンの内部障害・ファームウェアクラッシュを検出 |
| **G450: VoIP DSP Resources Exhausted on Slot {#VOIPSLOT} (100% Full)** | **High** | DSPチャネルが満杯（100%）に達し、新規発着信が拒否されている状態 |
| **G450: High CPU Temperature (>={$CPU_TEMP_WARN}°C)** | **Warning** | CPU温度が閾値（初期値: 60℃）を超過 |
| **G450: High DSP Temperature (>={$DSP_TEMP_WARN}°C)** | **Warning** | DSP温度が閾値（初期値: 60℃）を超過 |
| **G450: Fan Speed Level Warning** | **Warning** | ファン回転速度低下・低下警報 |
| **G450: Active Clock Source Changed** | **Warning** | 同期クロックソースが予期せず切り替わった場合（音質劣化・FAXエラーの原因） |
| **G450: High VoIP DSP Usage on Slot {#VOIPSLOT} (>85% for 5m)** | **Warning** | DSPチャネル使用率が5分間継続して85%を超過した場合 |
| **G450: System IP Address Changed to {ITEM.VALUE}** | **Info** | ゲートウェイの管理IPアドレスが変更された場合 |

---

## 🚀 設定手順 (Installation)

1. **テンプレートのインポート**
   * Zabbix 7.0 Web UI ＞ **[管理] (Data collection)** ＞ **[テンプレート] (Templates)** を開きます。
   * 右上の **[インポート] (Import)** から `G450template.xml` を読み込みます。

2. **ホストへのバインドとマクロ設定**
   * 対象ホストに `Avaya G450 Media Gateway` テンプレートを割り当てます。
   * ホストの **SNMP interface** （UDP: 161）を設定します。
   * 必要に応じてマクロ `{$SNMP_COMMUNITY}` にコミュニティ名を入力します。

---

## 📝 障害時の一次切り分け CLI コマンド (Troubleshooting)

障害検知時に G450 CLI で実行する主要な確認コマンドです。

* **H.248 / CM接続確認:** `show mgc`
* **VoIP / DSP状態確認:** `show voip-parameters`, `show voip`
* **同期クロック状態確認:** `show sync-clock`
* **ファン・環境ステータス:** `show environment`, `show fault-monitoring`
