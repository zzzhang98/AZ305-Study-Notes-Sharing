# AZ305 Original Notes（日本語訳版）

Source: 指定された AZ305 Notion ページのユーザー提供エクスポート

# AZ305

以下は、元のメモの中で残っていた中国語の説明を日本語に翻訳し、理解しやすいように整理した版です。

### ID、ガバナンス、及び監視ソリューションを設計する（25〜30%）

### Azure Monitor

- データ種別
  - メトリック：数値データ、軽量、収集頻度はリアルタイムに近く、アラートに利用される
  - ログ：テキストまたは数値データ。イベント発生時などに散発的に収集され、原因分析に利用される
  - 分散トレース：監視対象アプリ内の各コンポーネント間のやり取りを追跡する
  - 変更点：監視対象リソースのさまざまな変更を記録する

- Azure Monitor による監視データの活用
  - 通知と自動処理：異常なメトリックやログを検出し、自動的に警告を発する
  - 可視化：Azure Workbooks を用いて、監視データを簡単に可視化する
  - より詳細な分析：Azure Monitor Insights

| Insight | 説明 |
| --- | --- |
| Application Insights | Azure Monitor が提供する拡張可能なアプリケーション パフォーマンス管理（APM）サービスを利用し、あらゆるプラットフォーム上のリアルタイム Web アプリを監視する |
| Container Insights | Azure Container Instances または AKS にデプロイされた管理対象 Kubernetes クラスターのコンテナ ワークロードのパフォーマンスを確認する |
| Network Insights | すべてのネットワーク リソースの正常性とメトリックに関する包括的な情報を取得する。高度な検索機能を用いて依存関係を確認する |
| Resource Group Insights | 各リソースで発生している問題の診断と、全体のリソース グループの正常性やパフォーマンスの背景を確認する |
| VM Insights | Azure VM、VM Scale Set、その他の仮想マシンを監視し、Windows/Linux VM の性能と正常性を分析する |
| Azure Cache for Redis Insights | データベース クエリのキャッシュ、セッション保存、リアルタイムランキングなどをキャッシュし、ユーザーがより高速に処理を完了できるようにする |
| Azure Cosmos DB Insights | Azure Cosmos DB リソース全体のパフォーマンス、障害、容量、運用状態を統合的に確認する |
| Azure Key Vault Insights | キー コンテナーの要求、パフォーマンス、障害、遅延を統合的に監視する |
| Azure Storage Insights | ストレージ アカウントのパフォーマンス、容量、可用性を統合レポートで監視する |

- パートナー ツールでの分析：Azure Monitor の監視データを外部の監視サービスでも分析できる（例：Azure Event Hubs）

- Azure Monitor ログによる監視データの分析手順
  1. Log Analytics Workspace を作成する
     - Log Analytics Workspace は Azure Monitor ログ専用のデータ ストア
     - 価格レベル

| 価格レベル | 説明 |
| --- | --- |
| 従量課金制モデル | 既定の価格レベル |
| コミットメント レベル | 事前にデータ量を予約することで、従量課金制モデルより 30% 割引になる（1 日あたり 100GB〜50TB） |

  2. コンピューターに Azure Monitor Agent（AMA）をインストールする
  3. データ収集ルール（DCR）を作成する
     - 収集可能なデータソース
       - Heartbeat：エージェントの正常性を示すログ
       - Perf：パフォーマンス カウンター
       - Event：Windows のイベント ログ
       - Syslog：Linux のイベント ログ
       - W3CIISLog：IIS のテキスト ログを収集する
       - テーブル名_CL（Custom Log）：Apache アクセスログなどのテキスト ログを収集する
     - DCR は JSON 形式で定義され、フィルタリングや加工を行うことができる
       - データをフィルタリングして必要なデータのみ収集するには、DCR 内に XPath クエリを記述する
       - データを加工するには KQL（Kusto Query Language）を記述する
       - DCR では、データ ソースが Custom Log または IIS の場合、Data Collection Endpoint（DCE）を指定する
       - DCR と DCE はリージョンごとに作成する必要がある
  4. データを分析する
     - ログの保持期間は 30 日〜730 日（2 年）の間で設定可能
     - 最大 12 年間保持できるアーカイブ機能もある
  5. Azure の監視データを収集する

#### Azure の認証ソリューションと Microsoft Entra

1. Microsoft Entra
   - Microsoft Entra は ID 管理とアクセス管理製品ファミリーのこと

| サービス | 説明 |
| --- | --- |
| Microsoft Entra ID = Azure Active Directory（Azure AD） | Microsoft Entra の中核となるサービス。クラウドベースの ID 管理を実行する |
| Microsoft Entra Domain Services | Windows の Active Directory ドメイン コントローラーを Azure で簡単に展開できる |
| Microsoft Entra Private Access | オンプレミス アプリにインターネット経由で安全にアクセスできる |
| Microsoft Entra Internet Access | Web コンテンツ フィルタリングにより SaaS アプリへの安全なアクセスを実現する |
| Microsoft Entra ID Governance | ID、アクセス権、特権のライフサイクルを管理する |
| Microsoft Entra ID Protection | 機械学習を使って ID を保護する |
| Microsoft Entra Verified ID | デジタル資格情報の発行と検証を行う |
| Microsoft Entra External ID | 組織間でアクセスやアプリを共有する際、ゲスト ユーザーではなく Entra テナント間で信頼関係を構築する |
| Microsoft Entra Permissions Management | マルチクラウド環境におけるアクセス権の管理、可視化、過剰権限の検出を行う（CIEM と呼ばれる） |
| Microsoft Entra Workload ID | ワークロード向け ID に対して、条件付きアクセス、ID 保護、アクセスレビューを提供する |

2. 外部ユーザー管理
   - 直接 Entra テナントにそのメンバー ユーザーを作成することは推奨されない
   - ゲスト ユーザー：Entra テナントに作成できる特別なユーザー
   - Azure Lighthouse：外部の Entra テナントのユーザーやグループに、自社の Azure サブスクリプションへのアクセス権を簡単に割り当てられる

3. シングル サインオン
   - Federation SSO：Entra ID は SAML や OpenID Connect などの SSO 標準をサポートする
   - Password Based SSO：Entra ID では、ユーザーが作成したシンプルなアプリでも SSO を実装できる

4. Microsoft Entra Connect
   - 多くの企業では、オンプレミス（社内ネットワーク）のユーザー管理サービスとして Windows Server 標準機能の Active Directory を利用している
   - Password Writeback：クラウド側で変更されたパスワードをローカル AD に書き戻す機能
   - Self-Service Password Reset：ユーザー自身がパスワードをリセットできる機能

5. Microsoft Entra Connect Health
   - Microsoft Entra Connect の複数コンポーネントを一元的に監視するサービス

6. Microsoft Entra Application Proxy
   - 外部ユーザーが社内の Web アプリケーションに VPN を使わずに安全にアクセスできるようにする仕組み

7. Microsoft Entra ID Governance
   - Entitlement Management：従業員の入社、昇格、配置転換、退職などのライフサイクルに応じて、必要な権限を自動的に割り当てる
   - Access Review：アクセス権を定期的に評価する

8. Microsoft Entra Managed Identity
   - Azure のアプリまたはリソースが別の Azure リソースへアクセスする際の認証方法

9. Microsoft App Registration
   - App1 を Entra ID に登録し、Entra ID がそのアプリを認識できるようにする
   - ユーザーがアプリにアクセスしたときに Entra ID による認証 / SSO を利用する

#### Azure の認可ソリューション（Role Based Access Control）

1. Azure ロール：Owner、Co-Contributor、Reader
2. Microsoft Entra ロール

| 組み込みロール | 説明 |
| --- | --- |
| グローバル管理者 | Entra ID 全体を管理できる |
| ユーザー管理者 | ユーザーとグループを管理できる |
| ヘルプデスク管理者 | ユーザーのパスワードをリセットできる |
| 課金管理者 | 請求と支払いを管理できる |

3. 条件付きアクセス
   - Azure ロールや Microsoft Entra ロールに条件を付与し、セキュリティを強化する仕組み

4. Microsoft Entra Privileged Identity Management（PIM）
   - Azure ロールや Microsoft Entra ロールの特権が悪用されないよう保護するサービス
   - ユーザーが必要になったタイミングで権限を割り当て、有効期間内のみ利用できるようにする「Just-in-Time アクセス」を提供する

5. Azure Bicep
   - Azure リソースを宣言的にデプロイするための専用言語
   - 管理グループやサブスクリプションなどを構造化かつ再利用可能な形で定義し、実行できる

#### Azure Storage の認証・認可ソリューション

1. ABAC（Attribute Based Access Control）
   - 属性ベースのアクセス制御
   - Azure Blob と Azure Queue のみ対応
   - カスタム ロールを使って、タグなどの属性を条件とし、ストレージ アカウント内の個々のデータに対するアクセス権を定義できる

2. アクセス キー
   - ストレージ アカウントへのフル アクセスが可能な 512 ビットの文字列

3. SAS（Shared Access Signature）
   - リソースへのアクセス権や有効期限を含む特別な文字列

| SAS | 説明 |
| --- | --- |
| アカウント SAS | Azure Storage の Blob、Files、Queue、Table のうち、1 つのサービスのリソースにのみアクセスできる。共有キーで署名される |
| サービス SAS | Azure Storage の Blob、Files、Queue、Table の複数サービスのリソースにアクセスできる。共有キーで署名される |
| ユーザー委任 SAS | Azure Storage の Blob サービスのリソースのみにアクセスできる |

#### アプリケーションの認証と認可

1. アプリ登録
   - Azure 外で実行されるアプリを対象に、Entra テナントにその ID を作成する機能

2. マネージド ID
   - Entra テナントにアプリ用の ID を作成し、Azure 内のアプリに割り当てる機能
   - VM、App Service、Azure Functions などのリソースに割り当てられる

3. Service Principal
   - アプリケーションまたはサービスが Azure リソースにアクセスするためのアイデンティティ

#### コンプライアンス管理のソリューション

コンプライアンス管理とは、法律や組織・業界のガイドラインに準拠するための継続的な管理を指す。

1. Azure Policy
   - Azure の各リソースがビジネス ルールに準拠するよう統制するサービス

2. 適用手順
   1. Policy Definition

| 効果（Effect） | 説明 |
| --- | --- |
| append | リソースのプロパティ変更を許可する |
| deny | リソースのプロパティ変更を禁止する |
| audit | Activity Log にイベントを記録する（リソース自体は変更しない） |
| auditIfNotExists | 関連するリソースが存在しない場合に Activity Log にイベントを記録する |
| deployIfNotExists | 関連するリソースが存在しない場合に作成する |
| disabled | ポリシーを無効化する（テスト用） |
| modify | リソースのタグなどを変更する |

   2. Initiative Definition
   3. Policy Definition または Initiative Definition をスコープに割り当てる（Assignment）
      - ポリシー定義が多数ある場合、個別に割り当てるよりも Initiative を利用した方が簡単
   4. Evaluation
      - Azure Policy はリソース変更時にリアルタイム評価を行い、定期的にバックグラウンド評価も行う

#### Secret 管理ソリューション

1. Azure Key Vault

| オブジェクト | 説明 | 例 |
| --- | --- | --- |
| シークレット | 汎用の文字列を格納する | パスワード、DB 接続文字列、API キー |
| キー | 暗号化キーを格納する | RSA キー、EC キー |
| 証明書 | X.509 証明書を格納する | CA 発行証明書、自己署名証明書 |

### データストレージソリューションを設計する（20〜25%）

#### データソリューションの基礎

1. 構造化データと非構造化データ

| データ | 説明 | 例 |
| --- | --- | --- |
| 構造化データ | 事前に定義されたルール（スキーマ）に基づいて形式が決まっているデータ | リレーショナル データベース |
| 非構造化データ | スキーマがない、形式が定まっていないデータ | メール、ソーシャルメディアの動画、画像 |
| 半構造化データ | 非構造化データの中でもある程度の形式が決まっているデータ | XML、JSON |

2. ストレージ
   - 非構造化データを長期保存するのに適した場所

| 区分 | Block Storage | File Storage | Object Storage |
| --- | --- | --- | --- |
| 説明 | データをブロック単位で保存する | データをファイル単位で保存する | データをオブジェクト単位で保存する |
| プロトコル | FC、iSCSI | CIFS、NFS | HTTP/HTTPS |
| 例 | HDD、SSD などのディスク | Windows や NFS ファイル サーバー | Azure Storage |

3. Relational Database = SQL Database
   - 複数のテーブルでデータを管理し、テーブル間の関係を定義する
   - RDB を管理するシステムは RDBMS（Relational Database Management System）

4. Non-Relational Database = NoSQL Database
   - 一部の整合性機能を緩くすることで、大容量かつ低レイテンシーのデータベースを実現する

| データ モデル | 説明 | データベース例 |
| --- | --- | --- |
| Key Value 型 | データをキーと値のペアで格納する | Redis |
| Wide Column 型 | データをキーと値のペアで格納するが、値が複数のカラムになる | Cassandra |
| Document 型 | JSON や XML などのドキュメント形式で格納する | MongoDB |
| Graph 型 | エンティティ（ノード）とその関係性（エッジ）を格納する | Neo4j |

| 区分 | Relational Database | Non-Relational Database |
| --- | --- | --- |
| データ種類 | 構造化データ | 非構造化データ |
| スキーマ | 必要 | 不要 |
| アクセス方法 | SQL クエリ | API |

5. Data Warehouse
   - 構造化データを分析用途で利用するためのストレージ

6. Data Lake
   - 生の形式で保存されたデータを扱うためのレポジトリ

| 区分 | Data Warehouse | Data Lake |
| --- | --- | --- |
| データの種類 | 構造化データ | 構造化データ、非構造化データ |
| スキーマ | 必要 | 不要 |
| データソース例 | OLTP Data、ERP Data | IoT Data、Social Media Data |

7. Delta Lake
   - Apache Spark ベースのストレージ レイヤー。主に Databricks 社によって開発された

#### Azure Storage

1. Azure Blob Storage（Binary Large Object Storage）
   - テキストやバイナリ データを格納するための大規模なオブジェクト ストア
   - Storage Tiers
     1. Hot：保管コストは高いがアクセス コストは低い
     2. Cool：最小保持期間 30 日。アクセス頻度が低いデータ向け
     3. Archive：保管コストは最も低いが回復コストが高い。オフライン状態となる
   - Immutable Storage：WORM（Write Once, Read Many）状態で重要なデータを保存できる

2. Azure Files
   - クラウドやオンプレミスの環境向けに管理されたファイル共有
   - SMB などで複数マシンからファイルへアクセス可能
   - 認証方式：SAS と Microsoft Entra
   - 永続ストレージが必要な場合、コンテナー外部にファイル共有をマウントしてデータを保持できる
   - Azure Storage Explorer を使って管理できるが、新しいストレージ アカウントを作成することはできない

3. 冗長化戦略
   - LRS、ZRS、GRS、GZRS などの冗長レベルが存在する
   - Azure ファイル共有では Premium SKU と SMB Multichannel が利用可能
   - Standard 汎用 v2 では ZRS をサポートし、Azure ポータルから LRS から ZRS へ変換可能

#### Azure SQL Database

1. Azure SQL Database
   - 単一データベースとして利用でき、クラウド アプリに適している
   - 料金モデル：DTU と vCore
   - vCore は仮想コア数を指定でき、コスト制御がしやすい

2. Azure SQL Database は serverless と elastic pool に対応
   - Serverless モード：負荷に応じて CPU を自動的に増減させる
   - クエリがない場合には自動的に停止し、実際に使用した秒数で課金される

3. Azure SQL Managed Instance
   - Azure SQL の PaaS 型デプロイ オプション
   - SQL Server インスタンス感覚で利用でき、より多くの互換性を持つ

4. SQL Server on Azure Virtual Machine
   - Azure VM 上で SQL Server を実行する仕組み
   - オンプレミスの Microsoft SQL Server を Azure に移行しやすい

| 区分 | SQL Database | SQL Managed Instance | SQL Server on Azure VM |
| --- | --- | --- | --- |
| シナリオ | モダンなクラウド アプリ、超大規模、サーバーレス構成向け | ほとんどのクラウド移行シナリオに適する | 迅速な移行や OS レベルのアクセスが必要なアプリ向け |
| 機能 | Serverless compute、フル マネージド、Elastic pool、OLTP に最適化 | Native virtual networks、フル マネージド、インスタンス プール、CLR をサポート | OS レベルのアクセス、SQL Server の多様なバージョンをサポート |

- Business Critical tier は、高性能 OLTP ワークロードと高速な障害復旧向けに最適化されている
- Azure SQL Database Hyperscale は複数の read-only replica を持ち、Read scale-out と高速 failover を提供する

1. Azure SQL Database のセキュリティ
   - 監査ログ：Azure ポータルから監査ログを有効化し、ストレージ アカウントに保存する
   - Firewall：アクセス可能な IP アドレスを許可する
   - アクセス制御：RBAC やユーザー権限による制御
   - Row-Level Security（RLS）：ユーザーが見られる行を制限する
   - Dynamic Data Masking：個人情報のようなデータをマスクする
   - 暗号化（転送中）：SSL/TLS による暗号化
   - TDE（Transparent Data Encryption）：データベース全体を暗号化する
   - Always Encrypted：クライアント側で機密データを暗号化し、Azure SQL に保存する

#### Azure の Non-Relational Database

1. Azure Cosmos DB
   - グローバル分散型の NoSQL データベース
   - NoSQL、MongoDB、Apache Cassandra、Apache Gremlin、Table API をサポートする
   - SQL コマンドをサポートし、マルチマスター書き込みや低遅延読み取りを保証できる

2. 設計パラメーター
   - Request Unit（RU）：Cosmos DB での操作性能の単位
   - 容量モード
     - Provisioned throughput：RU を自分で設定する
     - Auto-scaling：負荷に応じて RU を自動調整する
     - Serverless：RU を設定せず従量課金で利用する
   - アクセス制御
     - RBAC：Entra ID ユーザーとグループを使ったアクセス制御
     - プライマリ キー / セカンダリ キー
     - リソース トークン：特定の DB、コンテナー、項目への一時アクセスを提供する

3. Azure Cosmos DB の SQL API
   - JSON ドキュメントの保存と検索に最適

4. Azure Cosmos DB for PostgreSQL
   - Azure Cosmos DB のインフラを利用し、PostgreSQL を複数リージョンへ分散配置して高性能化を実現する

### データ分析ソリューションの基礎

1. Apache Hadoop
   - Hadoop は分散ストレージと分散計算の組み合わせ
   - HDFS：データ保存
   - MapReduce：分散処理

2. Apache Spark
   - Apache Hadoop を改良した分散処理基盤
   - すべてのデータをメモリ上で高速に処理できるため、リアルタイム分析に適している

3. Databricks
   - Apache Spark を基盤とするデータ分析プラットフォーム

#### Azure のデータ分析ソリューション

1. データ分析の流れ
   - ADF でデータを取得 → ADLS に保存 → Databricks/Spark で加工 → Synapse で分析 → Power BI で可視化

| ステップ | 説明 | 主な Azure サービス |
| --- | --- | --- |
| Ingest | 様々なデータ ソースからデータを収集する | Azure Data Factory |
| Store | データをストアに保存する | Azure Data Lake Storage Gen2 |
| Prep & Train | データ前処理や ML 用データ準備 | Azure Databricks、Azure Synapse Analytics |
| Model & Serve | 整理されたデータを分析用ストアへ保存し活用する | Azure Synapse Analytics、Azure Analysis Services、Power BI |

2. Azure Data Factory
   - クラウドベースのデータ統合サービス
   - データ移動、変換、書き出しを自動化する
   - ETL（Extract / Transform / Load）に利用される

3. Azure Data Lake
   - 自然な形式で保存されたデータを格納するためのデータ湖
   - Hierarchical Namespace、スケーラビリティ、セキュリティ、匿名アクセス禁止、ACL ベースのアクセス制御をサポートする

4. Azure Databricks
   - 完全マネージドな大規模データ処理と ML プラットフォーム
   - Control Plane と Data Plane を持つ
   - Standard / Premium 価格レベルがある

5. Azure Synapse Analytics
   - 大規模データ分析、データ保管、データ統合を組み合わせたサービス
   - SQL Pool、Spark Pool、Pipelines、Link、Studio などのコンポーネントを持つ

6. Azure Analysis Services
   - OLAP（Online Analytical Processing）を実行する

7. Azure Machine Learning
   - ML モデルの構築とデプロイを行うマネージド サービス

8. Azure Data Explorer
   - 大量データをほぼリアルタイムで収集し、分析と可視化を行うサービス

9. Azure Data Share
   - ADLS Gen2 や Synapse、SQL Database のデータのスナップショットへのアクセスを制限付きで提供する

### ビジネス継続性ソリューションを設計する（15〜20%）

#### Azure Site Recovery

1. RTO（Recovery Time Objective）
   - 障害発生後にどれだけ早く復旧できるかを表す目標時間

2. RPO（Recovery Point Objective）
   - どの時点までデータを復元できるかを表す目標

3. RLO（Recovery Level Objective）
   - 復旧水準を表し、RTO と組み合わせて利用する

4. Azure Site Recovery のレプリケーション方式

| 種類 | 説明 | レプリケーション間隔 |
| --- | --- | --- |
| クラッシュ整合性スナップショット | 仮想マシンのディスク データを単純に複製する | 5 分ごと |
| アプリ整合性スナップショット | アプリの動作を意識しながら VM のディスク データをレプリケートする | 1 時間〜12 時間ごと |

### 事業継続性ソリューションの設計

1. Availability Sets
   - 単一データセンター内でのメンテナンスや単一障害点に対して可用性を提供する
   - Update Domain と Fault Domain の概念がある

2. Availability Zones
   - リージョン内のデータセンター全体に障害が発生しても、別データセンターでサービスを継続できるようにする仕組み
   - VM の managed disk を前提とする

3. Virtual Machine Scale Sets
   - 単一リージョン内に複数 VM を一括作成・管理し、ヘルス モニタリングや自動修復を実現する

4. Azure Backup
   - Recovery Services コンテナーを作成し、バックアップ ポリシーを設定し、エージェントをインストールしてバックアップを実行する
   - オンプレミス バックアップの選択肢には Azure Backup Endpoint と Azure Backup Server がある

| オプション | 特徴 | 制限 | ストレージ |
| --- | --- | --- | --- |
| Azure Backup Endpoint | Windows OS のフォルダーとファイルをバックアップする | Linux 非対応、ファイルとフォルダーのみ | Recovery Services コンテナー |
| Azure Backup Server | アプリ整合性のあるバックアップをサポートする | 専用サーバーが必要 | Recovery Services コンテナー、ローカル ディスク |

- Azure Backup は同一リージョンの保管コンテナーへ保存する必要があり、Blob のバックアップには対応していない
- Azure Backup コンテナーには Recovery Services コンテナーとバックアップ コンテナーの 2 種類がある

| 区分 | Azure Backup | Azure Site Recovery |
| --- | --- | --- |
| 主な機能 | バックアップと復元 | レプリケーションとフェールオーバー |
| 最大復旧ポイント | 99 年 | 15 日 |
| 最短 RTO | VM のサイズによる（24 時間以上の場合もある） | 2 時間以内 |
| 最短 RPO | 24 時間（Standard） / 4 時間（Enhanced） | 5 分（クラッシュ整合性） / 1 時間（アプリ整合性） |

#### ストレージの事業継続性ソリューション

- セカンダリ リージョンへのコピー、冗長度、バックアップ時点の保持などを設計する
- Azure Storage の冗長化構成と障害時の回復策を検討する

#### アプリケーションの事業継続性ソリューション

- Web App をリージョン障害時にも継続して提供するには、グローバル サービス（Azure Front Door、Traffic Manager）を利用する必要がある
- Azure Front Door と Azure CDN は静的コンテンツのキャッシュに適している
- Azure Load Balancer と Azure Application Gateway はリージョン内で負荷分散する
- Azure Traffic Manager と Azure Front Door はリージョン間で負荷分散する
- Azure Application Gateway と Azure Front Door は SSL 処理をオフロードできる

#### Azure Key Vault の事業継続性ソリューション

1. キー コンテナーのレプリケーション
   - 障害発生時には自動的にペア リージョンへフェールオーバーする
   - フェールオーバー中は読み取り専用となる（Encrypt / Decrypt / Backup は可能、Create / Update / Delete は不可）

2. オブジェクトのバックアップ
   - バックアップ後は、元の Key Vault と同一リージョンの Key Vault にのみ復元可能

### インフラストラクチャーソリューションを設計（30〜35%）

#### Computing Solution の設計

1. 仮想マシン サービス
   - VM のバースト機能
   - B シリーズはバースト性能を持ち、低負荷時には低い CPU 性能を提供し、高負荷時には高い CPU 性能を動的に利用できる

2. 仮想マシンのディスクの種類

| ディスクの種類 | 最大ディスク サイズ | 最大スループット | 最大 IOPS | 説明 |
| --- | --- | --- | --- | --- |
| Standard HDD | 32GB | 500MB/s | 2,000 | HDD ベース |
| Standard SSD | 32GB | 750MB/s | 6,000 | SSD ベース |
| Premium SSD | 32GB | 900MB/s | 20,000 | SSD ベース |
| Premium SSD v2 | 64GB | 1,200MB/s | 80,000 | SSD ベース。OS ディスクには不可 |
| Ultra Disk | 64GB | 10,000MB/s | 400,000 | SSD ベース。OS ディスクには不可 |

3. Azure App Service
   - App Service Plan：価格レベルや OS 種別、冗長性などを定義する
   - Deployment Slot：複数のアプリ バージョンを同時にホストできる
   - Service Connector：Azure App Service と他の Azure サービスを接続する機能

| プラン | 説明 |
| --- | --- |
| Free | 無料プラン。SLA なし |
| Shared | Free より多くのリソースを割り当てる。SLA なし |
| Basic | 小規模ワークロード向け |
| Standard | 中規模ワークロード向け |
| Premium | 大規模ワークロード向け |
| Isolated | 仮想ネットワークを利用し、完全に分離された専用環境を提供する |

4. Azure Container Service
   - コンテナー化アプリケーションを Azure 上で実行するための仕組み

5. Serverless Service
   - サーバーをユーザー側で準備せず、クラウド側が提供するサービス

6. Azure Functions
   - Serverless + Event-driven 型の処理サービス

| 料金プラン | 説明 |
| --- | --- |
| 従量課金プラン | 実行回数と実行時間に応じて課金される。アプリ実行時間は最大 10 分 |
| 専用ホスティング プラン | 専用リソースを用意し固定料金で利用する。実行時間は最大 10 分 |
| Premium プラン | 従量課金に加え、仮想ネットワーク アクセスなどの高度な機能を提供する |

7. バッチ処理サービス
   - Azure Batch：ローカルまたはクラウド最適化された HPC ワークロードを Azure 上で実行する
   - ノードの種類

| 種類 | 説明 |
| --- | --- |
| 低優先度 VM | Azure の余剰容量を活用した安価な VM。短時間の開発や非厳密なタスク向け |
| スポット VM | 低優先度 VM と同様の特徴を持つ。低優先度 VM の廃止予定のため移行推奨 |
| 専用 VM | 専用 VM。本番環境の長時間実行タスク向け |

#### Application Architecture の設計

1. Messaging Architecture
   - Azure Queue Storage：送信者と受信者が 1 対 1 の非同期メッセージングを行う
   - Azure Service Bus：複数受信者がいる場合に利用する Pub/Sub 型メッセージング
   - セッション有効化により FIFO を保証できる

2. Event Driven Architecture
   - イベント発生時にサービスが反応して処理を進める設計パターン

3. Cache Solution
   - キャッシュは配置場所によってコンテンツ キャッシュとデータ キャッシュに分けられる
   - コンテンツ キャッシュ：クライアントと Web アプリの間に配置する
   - データ キャッシュ：Web アプリと DB の間に配置する
   - Azure CDN：静的コンテンツのキャッシュに利用
   - Azure Cache for Redis：インメモリ データベースとして高速処理を実現

4. 統合ソリューション
   - Azure API Management：複数のバックエンド API を一元管理し、レート制限、認証、認可などを実装する
   - Azure Logic Apps：低コード / ノーコードで複数クラウド サービスを組み合わせるワークフロー自動化サービス

5. アプリ構成管理ソリューション
   - Azure App Configuration と Azure Key Vault は設定値やシークレットの管理を行うが、App Configuration はアプリ構成の管理に特化している

#### Data Migration

1. Azure Migrate
   - オンプレミスや他クラウド環境から Azure へ移行するためのサービス
   - VM、物理サーバー、データベース、Web App、Virtual Desktop を対象にできる

2. AzCopy
   - Blob / Files / Storage データのコピーに利用する

3. Azure Data Share
   - 他組織やユーザーとのデータ共有と定期更新を行える

4. Azure Import/Export
   - オフラインでローカル記憶域と Azure Storage を転送する

5. Azure Data Box
   - 大量データ移行のための物理デバイス

#### デー���ベース移行の設計

1. Azure Data Studio
   - Windows / macOS / Linux で動作するデータベース管理ツール
   - Microsoft SQL Server と Azure SQL Database に対応
   - Azure SQL 移行拡張機能を入れると SQL Server から Azure SQL への移行を支援する

2. Azure Database Migration Service（DMS）
   - データベース移行のための専用サービス
   - オフライン移行とバッチ移行（最大 50 DB）に対応

3. Data Migration Assistant（DMA）
   - 移行前の診断と SQL Server からの移行支援ツール

4. SQL Server Migration Assistant（SSMA）
   - SQL Server 以外のデータベース（Access / DB2 / MySQL / Oracle / SAP ASE）から SQL Server への移行を支援する

5. Azure Cosmos DB Data Migration Tool
   - Azure Cosmos DB への簡単なデータ移行に向く

| 項目 | Azure Data Studio | DMS | DMA | SSMA | Azure Migrate |
| --- | --- | --- | --- | --- | --- |
| 移行調査 | ○ |  | ○ |  | ○ |
| SQL Server から Azure SQL Database へ移行 | ○ | ○ | ○ |  |  |
| SQL Server から Azure VM 上の SQL Server へ移行 |  |  | ○ |  |  |
| SQL Server から Azure VM 上の SQL Server へシフト |  |  |  |  | ○ |
| 非 SQL オブジェクトを移行 |  |  |  | ○ |  |
| オープンソース データを移行 |  | ○ |  |  |  |

#### ネットワークソリューションの設計

1. 仮想ネットワーク
   - リージョンごとに作成されるネットワーク境界

2. インターネット接続ソリューション
   - インターネットからの受信方向通信のための構成
   - パブリック IP アドレス
   - Azure Load Balancer：VM のヘルスチェックと負荷分散
   - Azure Application Gateway
   - Azure NAT Gateway：アウトバウンド専用。プライベート VM からインターネットへ積極的に接続する

3. オンプレミス ネットワーク接続ソリューション
   - Azure VPN Gateway：仮想ネットワークに配置される VPN デバイス
   - Azure ExpressRoute：オンプレミスと Azure 間の専用接続。インターネット VPN より高信頼性・高速・低遅延が特徴
   - Azure ExpressRoute Global Reach：異なるオンプレミス データセンター同士を ExpressRoute / Microsoft ネットワーク経由で通信させる
   - Azure Virtual WAN：仮想 WAN。Basic と Standard の 2 種類の SKU がある
     - Basic：Site-to-Site VPN のみ使用可能
     - Standard：ExpressRoute 回線も含められる

4. Azure Private Link
   - Azure リソースへのプライベート接続を提供する仕組み

5. ネットワーク パフォーマンスの最適化
   - AccelNet：SR-IOV を利用してネットワーク性能を大幅に向上させる
   - Receive Side Scaling（RSS）：複数 CPU コアへ負荷を分散する
   - Proximity Placement Group：物理位置を可能な限り近くに配置する

6. ネットワーク セキュリティの最適化
   - Azure DDoS Protection：パブリック IP アドレス レベルと仮想ネットワーク レベルの保護
   - Azure Web Application Firewall（WAF）：サービスではなく機能であり、Application Gateway、Front Door、CDN で有効化できる
   - Network Security Group：送信元・送信先 IP、ポート番号、プロトコルに基づいて許可/拒否を設定する
   - Azure Firewall：同一リージョンの Firewall Policy を利用する
   - Azure Firewall Manager：複数リージョンや複数サブスクリプションにまたがる Azure Firewall を一元管理する

---

付記：このファイルは元のメモの中で中国語が残っていた箇所を、日本語に翻訳しながら理解しやすいよう整理した版です。必要であれば、次に元の英語のまま残っている箇所も日本語に統一した「完全版日文メモ」に整えます。