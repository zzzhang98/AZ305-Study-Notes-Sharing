# AZ-305 知識レビュー（日本語）

このレビューは、提供された AZ305 原文エクスポートだけをもとに整理しています。原文全体と元の画像39枚もリポジトリ内に保存しています。

- [原文ノート全文](AZ305-Original-Notes.md)
- [元画像リファレンス（39枚）](AZ305-Original-Image-References.md)

## 試験向けサービス整理

### ID、ガバナンス、監視
- **Azure Monitor** はメトリック、ログ、分散トレース、リソース変更を扱います。メトリックは数値の準リアルタイム監視とアラート、ログはイベント調査、トレースはアプリ内コンポーネント間の要求追跡に使います。
- **Log Analytics Workspace** は Azure Monitor Logs の保存先です。**Azure Monitor Agent（AMA）** と **Data Collection Rule（DCR）** で収集内容を設定します。XPath はイベントの絞り込み、KQL はデータ加工・分析に使います。DCR と DCE はリージョン単位です。
- **Microsoft Entra ID** はクラウド ID とアクセス管理を提供します。アプリ登録はアプリの ID とユーザー認証連携、マネージド ID は Azure 上のワークロードが他の Azure リソースへ認証する用途です。
- **Entra ID Governance** は権限付与のライフサイクルとアクセスレビュー、**PIM** は Just-in-Time の特権昇格、**条件付きアクセス** はサインイン条件の評価を担います。
- **Azure Lighthouse** はテナントをまたぐ Azure リソース管理の委任、**Entra Application Proxy** は VPN を使わずに社内 Web アプリを安全に公開する用途です。

### ストレージとデータ移行
- データ形式とアクセス方法で選択します。Blob はオブジェクト、Files はファイル共有、Queue はメッセージ、Table はキー属性型 NoSQL データ向けです。
- 対応サービスでは Microsoft Entra 認証と最小権限 RBAC を優先します。**SAS** は範囲と期限を限定したアクセスを付与します。アカウントキーは広い権限を持つため慎重に管理します。
- 移行ツールは評価と移行を区別します。**DMA** は互換性評価や SQL 移行シナリオ、**SSMA** は対応する非 SQL データベースから SQL Server への変換・移行、**Azure Migrate** は広範なインフラ移行の評価・調整に使います。

### ネットワークとセキュリティ
- **Load Balancer** は L4、**Application Gateway** はリージョン内 L7 と WAF、**Front Door** はグローバル HTTP(S) エントリと高速化を担当します。
- **NAT Gateway** はプライベートなワークロードのアウトバウンド接続、**VPN Gateway** はインターネット経由の暗号化トンネル、**ExpressRoute** は専用のプライベート接続、**Virtual WAN** は拠点とハブ接続の集中管理に使います。
- **Private Link** は対応サービスへのプライベート IP 接続を提供します。**NSG** は送信元・宛先・ポート・プロトコルで通信を制御し、**Azure Firewall** は集中型のネットワークフィルタリング、**DDoS Protection** は公開エンドポイント保護を担います。
- **CDN / Front Door** は利用者に近い場所でコンテンツをキャッシュし、**Azure Cache for Redis** はアプリが頻繁に使うデータをキャッシュします。

### 要件からサービスを選ぶ

| 要件 | 主な選択肢 |
| --- | --- |
| ログを一元保存して KQL で検索 | Log Analytics Workspace |
| VM のゲスト OS ログをルールに基づき収集 | AMA + DCR |
| 管理者権限を必要な時だけ有効化 | Entra PIM |
| アクセス権を定期的に再確認 | Entra Access Reviews |
| Azure ワークロードが資格情報を保存せず別の Azure リソースへ接続 | マネージド ID |
| 対応 PaaS サービスへプライベート接続 | Private Link |
| プライベート VM に制御された外向きインターネット接続 | NAT Gateway |
| 公衆インターネット上の暗号化サイト間接続 | VPN Gateway |
| オンプレミスと Azure の専用プライベート接続 | ExpressRoute |
| テナントをまたぐ Azure 運用の委任 | Azure Lighthouse |

原文ノートには、元の表・補足・画像39枚をすべて保持しています。
