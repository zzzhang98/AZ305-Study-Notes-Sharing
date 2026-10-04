# AZ305 Original Notes

### ID、ガバナンス、及び監視ソリューションを設計する（25~30%）

### **Azure Monitor**

![image.png](../images/az305-01.png)

- データ種別
    - メトリック：数値データ、軽量、リアルタイムに近い収集頻度、アラート
    - ログ：テキストまたは数値データ、イベント発生時などに散発的に収集、原因分析
    - 分散トレース：監視対象となるアプリ内の個々のコンポーネントのやり取りを追跡するもの
    - 変更点：監視対象となるリソースの様々な変更を記録したもの
- Azure Monitorによる監視データの活用
    - 通知と自動処理：異常なメトリックやログを検出し、自動的に警告を発する
    - 可視化：Azure Workbooksを使用し、監視データを手軽に可視化する
    - より詳細な分析：Azure Monitor Insights
        
        
        | Insight | Description |
        | --- | --- |
        | Application Insight | Azure Monitor が提供する拡張可能なアプリケーション パフォーマンス管理（APM）サービス。あらゆるプラットフォーム上の Web アプリをリアルタイムで監視する |
        | Container Insight | Azure Container Instances または Azure Kubernetes Service（AKS）上の Kubernetes クラスターにデプロイされたコンテナー ワークロードのパフォーマンスを確認する |
        | Networks Insight | すべてのネットワーク リソースの正常性とメトリックを包括的に把握する。高度な検索でリソース間の依存関係を特定し、Web サイト名からホスト リソースを検索できる |
        | Resource Group Insight | 各リソースの問題をトリアージして診断し、リソース グループ全体の正常性とパフォーマンスの状況を確認する |
        | VM Insight | Azure 仮想マシンや仮想マシン スケール セットの Windows / Linux のパフォーマンスと正常性を分析し、プロセスや他リソース・外部プロセスへの依存関係を監視する |
        | Azure Cache for Redis Insight | データベース クエリのキャッシュ、セッション保存、リアルタイム ランキングなどに利用する。キャッシュによりデータをより速く利用できる |
        | Azure Cosmos DB Insight | 統合された対話型エクスペリエンスで、すべての Azure Cosmos DB リソースのパフォーマンス、障害、容量、運用上の正常性を把握する |
        | Azure Key Vault Insight | Key Vault の要求、パフォーマンス、障害、待機時間を統合レポートで監視する |
        | Azure Storage Insight | ストレージ アカウントのパフォーマンス、容量、可用性を統合レポートで包括的に監視する |
    - パートナーツールでの分析：Azure Monitorの監視データを外部の監視サービスで分析することもできる **Azure Event Hubs**
- Azure Monitorログによる監視データの分析手順
    1. **Log Analytics Workspaceを作成する**：Log Analytics WorkspaceはAzure Monitorログ専用のデータストア
        
        
        | 価格レベル | 説明 |
        | --- | --- |
        | 従量課金制モデル | 既定の価格レベル |
        | コミットメントレベル | 予めデータボリュームを予約することで、従量課金制モデルよりも30%の割引である
        1日あたり100GBから50TB |
        
        **一つのLog Analytics workspaceを用意すれば、仮想マシンの監視、ネットワークの監視、ストレージの監視など、複数の用途で使用することができる。複数のInsightsで、一つのLog Analytics Workspaceを共有利用することが可能**
        
    2. ComputerにAzure Monitor Agent（AMA)をインストールする
    3. データ収集ルール（Data Collection Rule DCR) を作成する：
        1. 収集可能なデータソース
            - Heartbeat：エージェントの正常性を示すログ
            - Perf：パフォーマンスカウンター
            - **Event：Windowsのイベントログ**
            - **Syslog：Linuxのイベントログ**
            - W3CIISLog: Internet Information Servicesテキストファイルのログを収集
            - テーブル名_CL (Custom Log): text file logを収集する。例えばApacheのアクセスログを収集する
        2. DCR実体は、JSONで、このJSONドキュメントをカスタマイズすることで、監視データをフィルタリングしたり、加工した上で、Log Analytics Workspaceに格納することができます
            - データをフィルタリングして必要なデータだけを収集するには、DCR内にXPath (XML Path Language) クエリーを記述する
            - **データを加工するには、KQL (Kusto Query Language)クエリーを記述する**
            - DCRでは、データソースがカスタムログまたはIISの場合、「Data collection endpoint DCE 」を指定する。DCEはAzure Monitor Agentがデータを送信する先であり、事前に作成しておく必要がある
            - **DCRとDCEはリージョンごとに作成する必要がある**
    4. データを分析する：
        1. ログの保持期間は30日から730日(２年)の間で設定可能。最大12年間のログを保持するアーカイブがある
    5. Azureの監視データを収集する

#### Azureの認証ソリューションとMicrosoft Entra

1. Microsoft Entra：Microsoft EntraはID管理とアクセス管理製品のファミリーです
    
    
    | サービス | 説明 |
    | --- | --- |
    | Microsoft Entra ID=Azure Active Directory (Azure AD)  | Microsoft Entraの中心的なサービス。クラウドベースのID管理を行う |
    | Microsoft Entra Domain Services | WindowsのActive DirtectoryドメインコントローラーをAzureで簡単に展開できる |
    | Microsoft Entra Private Access | オンプレミスのアプリにインターネットから安全にアクセスできる |
    | Microsoft Entra Internet Access | WebコンテンツフィルタリングによりSaaSアプリへの安全なアクセスを実現する |
    | Microsoft Entra ID Governance | ID、アクセス権、特権のライフサイクルを管理する |
    | Microsoft Entra ID保護 | 機器学習を使ってIDを保護する |
    | Microsoft Entra Verified ID | デジタル資格情報の発行と検証を行う |
    | Microsoft Entra外部ID | 組織間でアクセスやアプリを共有する際、ゲストユーザーではなくEntraテナント間で信頼関係を結ぶ |
    | Microsoft Entra Permissions Management | マルチクラウドのアクセス権の管理、可視化、過剰な権限の検出を行う
    このようなソリューションはCIEM Cloud Infrastructure Entitlement Management呼ぶ |
    | Microsoft Entra ワークロードID | ワークロード用のIDに対して、条件付きアクセス、ID保護、アクセスレビューを提供 |
2. 外部ユーザー管理：直接Entraテナントにそれらのメンバーユーザーを作成することは、推奨されません
    1. ゲストユーザー：Entraテナントに作成できる特別なユーザーです
    2. **Azure Lighthouse：外部の Entra テナントのユーザーやグループに、自社の Azure サブスクリプションへのアクセス権を簡単に割り当てられます。（イベント ログの収集）Azure Lighthouse はテナントをまたいで Azure リソースを管理するサービスであり、異なるテナントにリンクされた複数のサブスクリプションからイベント ログを収集する用途に適しています。**
3. シングルサインオン：
    1. Federation SSO：Entra IDは、SAML (Security Assertion Markup Language）やOpenID Connectなどのシングルサインオン規格をサポートしています。
    2. Password Based SSO：EntraIDでは、ユーザーが開発したシンプルなアプリのSSOも可能です
4. Microsoft Entra Connect：多くの企業では、オンプレミス（企業内ネットワーク）のユーザー管理サービスとして、Windows Serverの標準機能であるActive Directory Domain Serviceを採用する。Entraテナントを導入するサービスが、Microsoft Entra Connectにより、AD DSのユーザーとグループをEntraテナントへ定期的にコピーできます
    1. Password WriteBack: クラウドで変更したパスワードをオンプレミスの Active Directory に書き戻す機能です。オンプレミスとクラウドのパスワードを同期し、ヘルプデスクによる手動管理の負担を減らします。
    2. Self-service password reset: ユーザーがヘルプデスクに連絡せずに自分でパスワードをリセットできます。ヘルプデスクの負担を軽減し、ユーザー自身による効率的な管理を可能にして、ネットワーク インフラの運用コストを抑えます。
5. Microsoft Entra Connect Health：Microsoft Entra Connectの複数コンポーネントを一元的に監視するサービス
6. **Microsoft Entra Application Proxy：外部ユーザーが VPN を使わずに社内 Web アプリへアクセスできるよう、オンプレミスの内部 Web アプリケーションをインターネットに安全に公開します。**
7. **Microsoft Entra ID Governance：**
    1. **Entitlement Management：従業員の入社、昇格、異動、退職などのライフサイクルに応じて、必要な権限をアクセス権として自動的に割り当てる機能です**
    2. **Access Review：アクセス権限の定期的に評価する**
8. Microsoft Entra Managed Identity：自分の Azure アプリ／リソースが別の Azure リソースにアクセスするとき、どのように認証するか。
9. Microsoft App Registration：App1 を Entra ID に登録してアプリを認識させます。ユーザーがアプリにアクセスするとき、Entra ID による認証／SSO を利用できます。

![image.png](../images/az305-02.png)

#### Azureの認可ソリューション (Role Based Access Control)

1. Azureロール：Owner, Co-Contributor, Reader
2. Microsoft Entraロール：
    
    
    | 組み込みロール | 説明 |
    | --- | --- |
    | 全体管理者　global admin | Entra IDのすべてを管理できる |
    | ユーザー管理者　global viewer | ユーザーとグループを管理できる |
    | ヘルプデスク管理者　user admin | ユーザーのパスワードをリセットできる |
    | 課金管理者 | 請求と支払いを管理できる |
3. 条件付きアクセス：AzureロールやMicrosoft Entraロールに条件を追加することでセキュリティを強化する機能です。
4. **Microsoft Entra Privileged Identity Management (PIM)：AzureロールやMicrosoft Entraロールは、「特権」が悪用されないように保護するサービスです。**
    1. **ユーザが必要となったタイミングで割り当てて、有効期間内にみ利用できるようにする「Just-in-Timeアクセス」を提供**
5. Azure Bicep は、宣言型の方法で Azure リソースをデプロイするドメイン固有言語です。管理グループ、サブスクリプション、リソース グループなど、必要なコンポーネントを構造化された再現可能な方法で定義・デプロイできます。Azure Bicep を使うと、問題で示された Azure 環境全体を最小限の運用負荷で簡単に構成できます。

#### Azure Storageの認証・認可ソリューション

1. ABAC：Attribute Based Access Control → Azure BlobとAzure Queueのみ対応
    1. カスタムロールを使えば、タグなどの属性を条件とし、ストレージアカウント内の個々のデータにアクセス権を定義する
2. アクセスキー：ストレージアカウントへのフルアクセスが可能な512ビットの文字列
3. SAS：リソースへのアクセス権や有効期限を含む特別な文字列
    
    
    | SAS | 説明 |
    | --- | --- |
    | アカウントSAS | Azure storageのBlob, files, Queue, Tableのうち、一つのサービスのリソースのみにアクセスできる。共有キーで署名される |
    | サービスSAS | Azure StorageのBlob, files, Queue, Tableの複数サービスのリソースにアクセスできる。共有キーで署名される |
    | ユーザ委任SAS | Azure StorageのBlobサービスのリソースのみにアクセスできる。 |

#### アプリケーションの認証と認可

1. アプリ登録：Azure外で実行されるアプリを対象に、EntraテナントにそのIDを作成する機能です
2.  マネージドID：Entraテナントにアプリ用のIDを作成し、Azure内のアプリに割り当てる機能です
    1. 具体的には、仮想マシンやApp Service, Azure Functionsなどのリソースに対して マネージドIDを割り当てることができ、これらのリソース内のアプリにIDを引き継ぐことが可能
    2. **マネージドIDの種類：**
    
    ![image.png](../images/az305-03.png)
    
3. Service Principal
    
    ![image.png](../images/az305-04.png)
    

#### コンプライアンス管理のソリューション

コンプライアンス管理とは、法律や、組織・業界のガイドラインに準拠するための継続的な管理のこと

1. Azure Policy：Azureの各リソースがビジネスルールに準拠するよう統制するサービスです
2. 適用手順：
    1. Policy Definition:
        
        
        | 効果 effect | 説明 |
        | --- | --- |
        | append | リソースのプロパティの変更を許可する |
        | deny | リソースのプロパティの変更を禁止する |
        | audit | Activity logにイベントを記録する（リソースは変更しない） |
        | auditIfNotExists | 関連するリソースがなかった場合、ActivityLogにイベントを記録する |
        | deployIfNotExits | 関連するリソースがなかった場合、リソースを作成する |
        | disabled | ポリシーを無効にする（テスト用） |
        | modify | リソースのタグを変更する ⭐ タグなどの属性を変更または追加する |
    2. Initiative Definition
    3. ポリシー定義またはイニシアチブ定義をスコープに割り当てる Assignment: 
        - ポリシー定義が多数ある場合、一つ一つを割り当てると手間がかかります。この時、「イニシアチブ定義」を使用すれば、複数のポリシーをグループ化し、割り当てを一回にまとめることができる
    4. Evaluation：
        - Azure policyはリソースに変更が加えられた時にリアルタイム評価スキャンを行ったり、定期的にバックグラウンド評価スキャンを行います。Azure CLIやAzure PowerShellを使用すれば、オンデマンドの評価スキャンも実行できます

#### Secret管理ソリューション

1. Azure Key Vault：

| オブジェクト | 説明 | 例 |
| --- | --- | --- |
| シークレット | 汎用的な文字列を格納する | パスワード、データベースの接続文字列、APIキー |
| キー | 暗号化キーを格納する | RSAキー、ECキー |
| 証明書 | X.509証明書を格納する | CAによって発行された証明書、自己署名証明書 |

### データストレージソリューションを設計する（20~25%）

#### データソリューションの基礎

1. 構造化データと非構造化データ
    
    
    | データ | 説明 | 例 |
    | --- | --- | --- |
    | 構造化データ | 事前定義のルール（スキーマ）による、形式が定まったデータ | リレーショナルデータベース |
    | 非構造かデータ | スキーマのない、形式が定まっていない | メール、ソーシャルメディアの動画、画像 |
    | 半構造化データ | 非構造化データに含まれるが、ある程度の形式が定まったデータ | XMLデータ、JSONデータ |
2. ストレージ：非構造かデータを長期間保管するのに適した場所です
    
    
    |  | Block Storage | File Storage | Object Storage |
    | --- | --- | --- | --- |
    | 説明 | データをブロック単位で保存 | データをファイル単位で保存 | データをオブジェクト単位で保存 |
    | プロトコル | FC, iSCSI | CIFS, NFS | HTTP/ HTTPS |
    | 例 | HarddiskやSSDなどのDisk | WindowsやNFSファイルサーバー | Azure Storage |
3. Relational Database = SQL database
    1. データを複数のテーブルで管理し、デーブル間の関係を定義したものです。
    2. RDBを管理するシステムはRDBMS (RDB Management System) 例：MySQL, PostgreSQL, MariaDB 
4. Non-Relational Database = NoSQL Database
    1. データ整合性などの一部の機能を緩和することで、大容量かつ低レイテンシーのデータベース
    
    | データモデル | 説明 | データベース例 |
    | --- | --- | --- |
    | Key Value 型 | データをキーと値のペアで格納する | Redis |
    | Widecolumn 型 | データをキーと値のペアで格納するが、値が複数のカラムになる | Cassandra |
    | Document 型 | データをJSONやXMLなどのドキュメント形式で格納する | MongoDB |
    | グラフ型 | データの実体（ノード）とデータの関係性（エッジ）を格納する | Neo4j |
    
    |  | Relational Database | Non-Relational Database |
    | --- | --- | --- |
    | データの種類 | 構造化データ | 非構造化データ |
    | スキーマ | 必要 | 不要 |
    | アクセス方法 | SQLクエリー | API |
5. Data Warehouse：分析用の構造化データを格納する
    
    ERP ─┐
    SQL ─┼→ Data Warehouse → BI / Analysis
    CRM ─┘
    
6. Data Lake：生データを元の形式で格納する
    
    
    |  | Data WareHouse | Data Lake |
    | --- | --- | --- |
    | データの種類 | 構造化データ | 構造化データ、非構造化データ |
    | スキーマ | 必要 | 不要 |
    | データソース例 | OLTP Data, ERP Data | IoT data, Social Media Data |
7. Delta Lake: Apache Sparkベースのストレージレイヤーとして主にDatabricks社によって開発されました

#### **Azure Storage**

1. **Azure Blob （Binary Large Object) Storage (containers)**: A massively scalable object store for text and binary data.
    - Storage Tiers:
    1. Hot: Higher storage costs & Lower access costs
    2. Cool: Minimun storage duration 30 days. Cold: Lower storage costs & Higher access costs & Intended for data that will remain cool for 90 days or more
        - **クールアクセス層を使用する：Standard 汎用 v2　Blob Storage**
    3.  Archive: Lowest storage costs & Highest retrieval costs & when a blob is in archive storage it is offline and cannot be read
        - ✅ 対応アカウント：**StorageV2、Blob Storage**。StorageV1 は**非対応**。
        - ✅ 対応する冗長化：**LRS/GRS/RA-GRS**。ZRS/GZRS/RA-GZRS は**非対応**。
        - ⚠️ **Premium BlockBlob** アカウントは層の変更に**非対応**（削除のみ）。
        - ⚠️ Archive 層ではスナップショットを作成できない。
    - Immutable Storage：ユーザーはビジネスに不可欠なデータを WORM (Write Once, Read Many) 状態で保存できます。 WORM の状態では、ユーザーが指定した期間、データを変更、削除することができないため、上書きや削除からデータを保護することができます
2. **Azure Files**: Managed file shares for cloud or on-premises deployments. Access files across multiple machines. Access to shared folders via SMB (Server Message Block protocol 445), not only via API, but also directly from Windows 10, macOS, and Linux.
    - **Authentication method for File service: SAS and Microsoft Entra**
    - **永続ストレージ**を必要とする場合、ストレージアカウントのファイル共有をマウントしてコンテナーの外部にデータを保存するように構成することができます
    - **Azure Storage Explorer is a graphical tool to manage Azure Storage Resources (Blobs, files, queues, tables). But cannot create new storage accounts**
    - Azureコンテナーインスタンスの**外部ボリュームとしてサポート**されているのは、Azure Filesで作成された Azureファイル共有のみです
    
    | Premium | File shares use SSD and provide consistent high performance and low latency. Can be used with both Server Message Block(SMB) and Network file system(NFS) protocols |
    | --- | --- |
    | Transaction optimized | トランザクション量の多いワークロード向け。Premium ファイル共有ほどの低遅延が不要な場合に適する。HDD ベースの標準ストレージ ハードウェアで提供される |
    | Hot access tier | チーム共有など、一般的なファイル共有向けに最適化されたストレージ。標準ストレージ ハードウェア上の HDD で提供される |
    | cool access tier | オンライン アーカイブ向けに最適化された低コストのストレージ。HDD ベースのストレージ ハードウェアで提供される |
    - **Azure File Sync**: Windows Server 上の Azure ファイル共有（Azure Storage 内のファイル）に保存されたデータをキャッシュして利用するサービスです。オンプレミスに展開する場合、展開先リージョン内に Azure ファイル共有が必要です。
        - オンプレミスのファイル サーバーをクラウドに拡張する
        - クラウドで一元管理し、複数拠点間で共有する
        - バックアップと災害対策
3.  **Four Replication Strategies:** 
    
    ![Untitled](../images/az305-05.png)
    
    ![image.png](../images/az305-06.png)
    
    - **SMB Multichannel only for Premium Azure File**
    - **Standard 汎用 v2 は「ゾーン冗長ストレージ (ZRS)」をサポートしており、Azureポータルから LRS → ZRS に変換することができます**
    - GRS/GZRS など、リージョン間冗長化を有効にしたストレージ アカウントでのみ、このフェールオーバーが関係します。

#### Azure SQL Database

1. Azure SQL Database: 単一データベース。新規のクラウド アプリケーションに適しています。
    - Two primary pricing options for SQL Database:
        - **DTU( Database Transaction Unit) is a combined measure of compute, storage, and I/O resources.**
        - **vCore is a virtual core. You choose the number of virtual cores and have greater control over your compute costs**
2. A single Azure SQL Database は serverless と elastic pool に対応する
    - **Serverless モード：
    ワークロードに応じて CPU を自動でスケールアップ／ダウン ✅
    クエリがないときは自動一時停止 ✅
    実際の使用秒数に応じて課金 ✅ ←「秒単位課金」の要件を満たす**
    - General Purpose と Hyperscale に対応する
3. Azure SQL Managed Instance: Azure SQL の PaaS デプロイ オプションです。Azure SQL Database と同様に完全管理型で、SQL Server インスタンスを提供しながら、仮想マシンの管理負荷を大幅に減らします。オンプレミス SQL Server の移行に適しています。
4. SQL server on Azure virtual machine: Azure 仮想マシン（VM）上で動作する SQL Server です。オンプレミスのマシンを管理せずに、クラウドで SQL Server のフル バージョンを使用できます。
    1. オンプレミスのMicrosoft SQL Serverを最小限の工数でAzureへ移行できるという特徴がある

| Compare | SQL Database | SQL Managed Instance  | SQL Server on Azure Virtual Machines |
| --- | --- | --- | --- |
| Scenarios | 最新のクラウド アプリ、大規模構成、またはサーバーレス構成に最適 | クラウドへ移行する大半のインスタンス レベル機能に最適 | 迅速な移行や OS レベルのアクセスが必要なアプリに最適 |
| Features | Serverless compute
Fully managed service
Elastic pool
**OLTP に最適化** | Native virtual networks
Fully managed service
Instance pool
**CLR（Common Language Runtime）に対応** | OS-Level server access
Expansive version support for SQL server |
- Azure SQL Database の Business Critical 層は、高性能 OLTP ワークロード向けに最適化されています。障害時に最速の復旧が必要な場合に適し、インメモリ技術や高速データベース復旧などにより、ダウンタイムを最小限に抑えて迅速に復旧します。
- Azure SQL Database Hyperscale は、複数の読み取り専用レプリカ（Read scale-out）に適している
    - 複数の読み取り専用レプリカ
    - データを自動的に同期／複製する
    - Read scale-out
    - 高速フェールオーバー
    - Be optimized for online transaction processing (OLTP)

![image.png](../images/az305-07.png)

![image.png](../images/az305-08.png)

1. Azure SQL Databaseのセキュリティ
    1. Azure SQL Database監査ログ：Azureポータルから監査ログを有効化し、ストレージアカウントを選択または新規作成する場合、ストレージアカウントはデータベースやサーバーと同じリージョンに限定されたので注意が必要です
    2. Firewall：SQL Database への接続を許可する IP アドレスを指定する
    3. アクセス制御
    4. Row-level Security（RLS）：ユーザーが閲覧できる行（Row）を制御する
    5. Dynamic Data Masking：電話番号や住所などの個人情報PII (Personally Identifiable Information) の例に対して動的データマスキングを設定することで、プライバシーを保護できる
    6. 転送中の暗号化：クライアントとAzure SQL Databaseの通信を、SSL/TLSを使用して暗号化することができます
    7. Transparent Data Entryption：Azure SQL Dataのデータベース全体を暗号化する機能。規定、ユーザ独自のキーもある。ユーザーが独自のキーを用意する場合、アルゴリズムとして非対称、RSA、RSA HSMを指定でき、キーサイズは2048, 3072をサポートします 
    8. Always Encrypted：クライアント側で機密データを暗号化した上で、Azure SQL Databaseのデータベースへ書き込む機能

#### AzureのNon-Relational Database

1. Azure Cosmos DB：グローバル分散型 NoSQL データベース
    1. NoSQLデータベースの多くのAPIオブションをサポートする NoSQL, MongoDB, Apache Cassandra, Apache Gremlin, Table
        
        ✑ Support SQL commands.
        
        ✑ Support multi-master writes.
        
        ✑ Guarantee low latency read operations.
        
    2. 設計パラメーター：
        1. Request Unit: Cosmos DB におけるデータベース操作性能の測定単位
        2. 容量モード：
            - Provisioning **throughput** mode: Request Unit（RU）を自分で設定する
            - Auto-scaling mode: RU が負荷に応じて自動的に変化する
            - Serverless mode: RU の設定不要（従量課金）
        3. アクセス制御：
            - RBAC: Entra IDのユーザやグループを使用したAzure RBACによるアクセス制御
            - プライマリキー／セカンダリキー
            - リソーストークン：特定のデータベース、コンテナー、項目への一時的なアクセスを提供する
    3. Azure Cosmos DBはNoSQLとSQLの２種類のデータベースをサポートする
    NoSQLには
        1. SQL API は JSON ドキュメントの処理に最適です。JSON データをネイティブ形式で保存し、SQL 構文でクエリできるため、JSON ドキュメントを効率よく柔軟に扱えます。
        2. Gremlin API はグラフ データ向けに設計され、グラフの走査とクエリに最適化されています。
        3. Cassandra API は列ファミリー データ モデル向けに設計され、固定スキーマを持つ構造化データの処理に適しています。
        4. MongoDB API は JSON ドキュメントの効率的な保存とクエリに適しています。
2. Azure Cosmos DB 　SQLについては、Azure Cosmos DB for PostgreSQL
    
    Azure Cosmos DB のサービス基盤を利用すると、PostgreSQL データベースを複数リージョンに分散できます。水平スケーリングにより高いパフォーマンスを実現し、マルチリージョン レプリケーションにより高可用性を提供します。
    
    ![image.png](../images/az305-09.png)
    

#### データ分析Solutionの基礎

1. Apache Hadoop は主に 2 つの重要な部分で構成されます：Hadoop = 分散ストレージ + 分散コンピューティング
    - **HDFS** (Hadoop Distributed File System) → データを保存
    - **MapReduce** → 分散データ処理
2. Apache Spark：Apache Hadoop を改良したものです。すべてのデータをメモリ上で高速処理できるため、**リアルタイム分析が可能**です。Spark は**データ処理／分析エンジン**であり、データベースではありません。
    
    ![image.png](../images/az305-10.png)
    
3. Databricks：Apache Spark ベースのデータ分析プラットフォーム

#### **Azureのデータ分析ソリューション**

1. データ分析の流れ：**ADF でデータ移動 → ADLS に保存 → Databricks/Spark で加工 → Synapse で分析 → Power BI で表示**
    
    
    | データ分析のステップ | 説明 | 主なAzureサービス |
    | --- | --- | --- |
    | Ingest | さまざまなデータ ソースからデータを収集する | Azure Data Factory ⭐ 収集／移動／変換
    Azure Synapse Analytics ⭐ 分析用データ ウェアハウス |
    Azure Synapse Analytics ⭐ 分析型数据仓库 |
    Azure Synapse Analytics ⭐ 分析用データ ウェアハウス |
    | Prep & Train | データ分析や機械学習のためのデータ加工・前処理を行う | Azure Databricks ⭐ Spark によるデータ処理／機械学習 |
    Azure Synapse Analytics (Spark Pool) |
    | Model & Serv | 整理されたデータを分析用ストアに保存する | Azure Synapse Analytics (SQL pool)
    Azure Analysis Services ⭐ 多次元分析
    Azure Data Explorer ⭐ 大量データのほぼリアルタイム分析
    Azure Data Share ⭐ 他組織とのデータ共有
    Azure Machine Learning
    Power BI |
2. **Azure Data Factory：クラウドベースのデータ統合サービスです。データ駆動型ワークフローを作成・スケジュールでき、データの移動を調整し、大規模なデータ変換を実行します。パイプラインはさまざまなデータ ストアからデータを取り込みます。**
    - Azure Data Factory は収集したデータを変換・加工して別の場所に保存します。**Extract（抽出）、Transform（変換）、Load（書き出し）＝ ETL データ統合**。SSIS = SQL Server Integration Services。
    - **Azure Data Factory でデータを変換して Azure Data Lake Storage にエクスポートするには、データ移動エンジンである Integration Runtime が必要です。Azure Data Factory（ADF）は SSIS パッケージをホストして実行できます。これは Azure-SSIS Integration Runtime です。**
    
    ![image.png](../images/az305-11.png)
    
3. **Azure Data Lake：データを通常 Blob またはファイルとして自然な形式で保存します。Azure Data Lake Storage はファイル システムとストレージ プラットフォームを組み合わせ、データから迅速に洞察を得られるようにします。Azure Blob Storage を基盤とし、分析ワークロード向けに最適化されています。**
    
    ![image.png](../images/az305-12.png)
    
    - Azure Data Lake Storage characteristics:
        - Hierarchical Namespace
        - Scalability
        - Security: Azure AD for identity and access management, RBAC and so on. Also supports Azure Private Link
        - **イミュータブル ストレージに対応**
        - **匿名アクセスを無効化する**
        - **Supports access control list (ACL)-based Azure AD permissions**
    - Azure Data lake storage three important steps:
        - Ingest data:
            - For unplanned data, you can use tools like AzCopy, the Azure CLI, PowerShell, and Azure Storage Explorer.
            - For relational data, the Azure Data Factory service can be used. You can transfer data from any source, such as Azure Cosmos DB, SQL Database, Azure SQL Managed instances, and more
            - For streaming data, you can use tools like Apache Storm on Azure HDInsight, Azure Stream Analytics, and so on
        - Access stored data: データへの最も簡単なアクセス方法は Azure Storage Explorer です。GUI を備えた独立アプリケーションで、Azure Data Lake のデータにアクセスできます。PowerShell、Azure CLI、**HDFS** CLI、各種言語 SDK も利用できます。
        - Configure access control: 認可を設定し、Azure Data Lake Storage 内のデータにアクセスできるユーザーを制御します。Azure RBAC または ACL を選択できます。
        - Azure Blob storage or Azure Data Lake comparison
4. **Azure Databricks SKU：完全管理型のクラウド ビッグデータ／機械学習プラットフォームで、開発者による AI とイノベーションを加速します。**
    - Azure Databricks has a Control plane and Data plane:
        - **Azure Databricksの価格レベルには、Standard とPremium がありますが、「Azure Data Lake Storage 資格情報 Credential Passthrough」を使用するには Premium プランが必要です**
    - Service Principal: アプリケーションから Azure Databricks ワークスペースへアクセスする認証には、サービス プリンシパルを構成します。これは Azure リソースへアクセスするアプリやサービスのセキュリティ ID です。アプリ内に資格情報を保存せず安全に認証できるため、管理負荷を抑えセキュリティを高めます。
        - Use for Big Data analytics, Machine Learning, Real-time Analytics, ETL processes, Data Exploration and Visualization
        
        ![image.png](../images/az305-13.png)
        
5. Azure Synapse Analytics：ビッグデータ分析、エンタープライズ データ ウェアハウス、データ統合を組み合わせたサービスです。サーバーレス データや大規模データに対してクエリを実行できます。データの取り込み、探索、変換、管理に加え、BI と機械学習の分析を支援します。
    
    ![image.png](../images/az305-14.png)
    
    - Components of Azure Synapse Analytics:
        - Azure Synapse SQL pool: サーバーレスと専用リソースのモデルを提供し、ノードベースのアーキテクチャに対応します。予測可能な性能とコストが必要なら専用 SQL プール、不定期・予測不能なワークロードには常時利用可能なサーバーレス SQL エンドポイントを使用できます。
        - Azure Synapse Spark pool: Apache Spark でデータを処理するサーバー クラスターです。Python、Scala、SQL、C#（Apache Spark の .NET 言語）のいずれかで処理ロジックを記述できます。Synapse 版 Apache Spark は、データ準備、データ エンジニアリング、ETL、機械学習向けのオープンソース エンジンを統合しています。
        
        ![image.png](../images/az305-15.png)
        
        - Azure Synapse Pipelines:
            - SQL Server などのデータ ソースからデータを読み取る
            - Azure Data Lake Storage Gen2 にデータをコピーする
            - データ移動中に Mapping Data Flow などで変換する
            - 変換後のデータをターゲットの Data Lake に書き込む
        - Azure Synapse Link: **Azure Cosmos DB に接続するコンポーネントです。Cosmos DB に保存された運用データをほぼリアルタイムで分析できます。**
            
            ![image.png](../images/az305-16.png)
            
        - Azure Synapse Studio: Web ベースの統合開発環境（IDE）です。Azure Synapse Analytics の機能を一元的に利用できます。SQL／Spark プールの作成、パイプラインの定義と実行、外部データ ソースへのリンク設定ができます。
    - Managed Workspace仮想ネットワーク：ユーザーの代わりにAzure Synapse Analyticsによって管理されるため、セキュリティやパフォーマンスなどの管理が不要となり、管理負荷が軽減します。
6. Azure Analysis Services：オンライン分析処理OLAP（Online Analytical Processing）を実行します
7. Azure Machine Learning: MLのモデルの構築とデプロイを行うManaged Service
8. **Azure Data Explorer：大量データをほぼリアルタイムで取り込み、分析・可視化するオールインワン サービスです。大量データ + ほぼリアルタイム分析に適しています。**
9. Azure Data Share：ADLS Gen2やAzure Synapse Analytics、Azure SQL Databaseなどのデータのスナップショットへのアクセスを、provide restricted access

### ビジネス継続性ソリューションを設計する（15~20%）

#### **Azure Site Recovery**

1. Recovery time objective (RTO)：停電や問題の発生後、リソースを復旧するまでに許容される最大時間です。復旧に RTO より長くかかると、金銭的なペナルティや業務停止につながる可能性があります。ソリューション全体（すべてのリソース）にも、SQL Server インスタンスやデータベースなど個別コンポーネントにも設定できます。
2. Recovery point objective (RPO)：データベースをどの時点まで復旧する必要があるかを示し、許容できる最大データ損失量に対応します。たとえば SQL Server を含む IaaS VM が午前 10 時に障害となり、データベースの RPO が 15 分の場合、復旧で失うデータは最大 15 分です。つまり午前 9 時 45 分以降の状態に復旧できる必要があります。RPO を達成できるかどうかは複数の要因に左右されます。
3. Recovery Level Objective RLO: 目標復旧レベル。RLOはRTOとセットで使用し、RLOの段階ごとにRTOを定義する
4. Azure Site Recoveryのレプリケーション方法
    
    
    | 種類 | 説明 | レプリケーション |
    | --- | --- | --- |
    | クラッシュ整合性スナップショット | 単純に仮想マシンのディスクデータをレプリケーションする | 5分ごと |
    | アプリ整合性スナップショット | アプリの動作を意識した上で仮想マシンのディスクデータをレプリケーションする | 1時間〜12時間ごと |

### 事業継続性ソリューションの設計

1. Availability Sets：単一データセンター内の Azure における計画メンテナンスや単一障害点に対して、稼働時間を確保します。
    - **Availability Set**
        - The availability set is prepared as a dedicated resource and allocated at the time of virtual machine creation. **Availability sets cannot be assigned or changed after the virtual machine is created.**
            - Parameters of an availability set:
                - **Update Domain: maximum number of update domains that can accommodate planned maintenance of the host server is 20**
                - **Fault Domain: can accommodate server rack failures Maximum value for a fault domain is 3**
                
                ![image.png](../images/az305-17.png)
                
2. **Availability Zones は、リージョン内のデータセンター全体に障害が発生しても、別のデータセンターでサービスを継続できる仕組みです。**
    - Available regions are limited. In Asia, only East and Southeast Asia Regions are available
    - Virtual machines with unmanaged disk type are not supported by Availability Zones, so convert them to managed disks in advance.
        - Managed disk: The disk is created in a storage account managed by Azure.
        - Unmanaged disk: Create a disk in a storage account managed by the user
        
        ![image.png](../images/az305-18.png)
        
3. Virtual Machine Scale Sets：単一のリージョン内に複数の仮想マシンを一括で作成し、管理します。「正常性監視機能」と「自動修復ポリシー」
4. Azure Backup
    - バックアップ手順：
        - Recovery Servicesコンテナーの作成  **(Recovery Services コンテナーの数は仮想マシン、ファイル共有が存在しているリージョン数による）**
        - バックアップポリシーの設定　**（バックアップポリシーはバックアップするリソース種別ごとに（仮想マシンとファイル共有は別々で）作成する必要があります　Recovery Services コンテナー x ポリシーの種類）**
        - バックアップエージェントのインストール、登録
        - バックアップの実行
    - オンプレミスバックアップのオプジョンの違い
        
        
        | オプジョン | 特徴 | 制限 | ストレージ |
        | --- | --- | --- | --- |
        | Azure Backup Endpoint | Windows OSのフォルダとファイルのバックアップ
        専有サーバーの構築不要 | Linuxのサポートなし
        フォルダとファイルのバックアップのみ | Recovery Servicesコンテナー |
        | Azure Backup Server | アプリ一貫性のあるバックアップのサポート
        Windows Linuxのサポート | 専有サーバーの構築が必要 | Recovery Servicesコンテナ
        ローカルディスク |
    - **Azure Backup only can backup to a vault in the same region. Blob not supported for backup by Recovery Services Vaults.**
        - **Storage Accounts can be in the different region**
        - **Log Analytics workspaces must be in the same region**
    - **Azure Backup Containerは二つ種類があります：**
        - **Recovery Services コンテナー：仮想マシンに接続されているすべてのディスク（仮想マシン全体）をバックアップできますが、OS ディスクを除外して、データディスクのみをバックアップすることはできません**
        - **バックアップコンテナー：仮想マシンのマネージドディスクをバックアップするには、最初に「バックアップコンテナー」を作成する必要があります**
    
    |  | Azure Backup | Azure Site Recovery |
    | --- | --- | --- |
    | 基本機能 | バックアップと復元 | レプリケーションとフェールオーバー |
    | 最大復旧ポイント | 99年 | 15日 |
    | 最短RTO | 仮想マシンの規模による（24時間以上の場合もある） | 2時間以内 |
    | 最短RPO | 24H Standard 
    4H  Enhanced | 5Min (クラッシュ整合性）
    1時間（アプリ整合性） |

#### ストレージの事業継続性ソリューション

![Untitled](../images/az305-19.png)

![image.png](../images/az305-20.png)

#### **アプリケーションの事業継続性ソリューション**

![image.png](../images/az305-21.png)

- **要件：リージョン障害時も Azure Web App のサービスを継続するにはどうするか**
    - **⭐ リージョン全体の障害に備えるには、グローバル サービスが必要 → Azure Front Door、Traffic Manager**
- **Azure Front DoorとAzure CDNは、主に静的コンテンツ（javascript、css、画像ファイルなど）をキャッシュするためのサービスで、バックエンドのデータベースのデータをキャッシュする用途**

![image.png](../images/az305-22.png)

Azure load balancerとAzure Application Gatewayは、リージョン内の負荷分散ができる

Azure Traffic ManagerとAzure Front doorは、リージョン間の負荷分散ができる

Azure application GatewayとAzure Front DoorはSSL処理をオフロードすることができる

#### Azure Key Vaultの事業継続性ソリューション

1. キーコンテナーのレプリケーション：
    - 障害が発生した場合は、自動的にペアリージョンにフェールオーバーが行われるため、操作不要
    - フェールオーバー中は、キーコンテナーが読み取り専用となるため OK (Encrypt, Decrypt, Backup) NG (Create, Update, Delete)
2. オブジェクトのバックアップ：
    - ⭐ **バックアップからの復元先は、元の Key Vault と同じリージョンの Key Vault に限られます。**

### インフラストラクチャーソリューションを設計（30~35%）

#### Computing Solutionの設計

1. 仮想マシンサービス：
    
    ![image.png](../images/az305-23.png)
    
- 仮想マシンのバースト：
    - B シリーズには **Burst（バースト性能）** があります。低負荷時は低い CPU 性能で動作し、高負荷時には CPU 性能を動的に高めます。
- 仮想マシンのディスクの種類：
    
    
    | ディスクの種類 | 最大ディスクサイズ | 最大スループット | 最大IOPS | 説明 |
    | --- | --- | --- | --- | --- |
    | Standard HDD | 32GB | 500MB/s | 2,000 | HDDベース |
    | Standard SSD | 32GB  | 750MB/s | 6,000 | SSDベース |
    | Premium SSD | 32GB | 900MB/s | 20,000 | SSDベース |
    | Premium SSD V2 | 64GB | 1200MB/s | 80,000 | SSDベース。OSディスクとしては使用できない |
    | Ultra Disk | 64GB | 10,000MB/s | 400,000 | SSDベース。OSディスクとしては使用できない |
1. Azure APP Service
    - App Service Plan: 価格レベルやOSの種類、冗長性などの設定を定義したものです（複数リージョンはサポートしません、**リージョンごとにApp Service Planを作成する必要**）
        
        
        | プラン | 説明 |
        | --- | --- |
        | Free | 無料のプラン。SLAがない |
        | Shared | Freeよりも割り当てられるリソース量は多い。SLAがない |
        | Basic | 小規模なワークロード向けのプラン |
        | Standard | 中規模なワークロード向けのプラン |
        | Premium | 大規模なワークロード向けのプラン |
        | Isolated | 仮想ネットワークを使用し、完全い分離された専用環境が提供されるプラン |
    - Deployment Slot: アプリケーションの複数のバージョンを同時にホスティングするAzure App Serviceの機能です（App Service Plan Standard以上）
        - **Hosts multiple versions of a single web app simultaneously**
        - **Available for App Service plans with SKUs Standard or higher**
        - **Would revert to the previous version can replace the slot.**
    - Service Connector: Azure App Serviceと他のAzureサービスを接続する機能です
2. Azure Container Service
    
    ![photo.heic](../images/az305-24.png)
    
    - **Azure Container**
        
        ![image.png](../images/az305-25.png)
        
3. Serverless Service: アプリを実行するためのサーバをユーザ側で準備せず、クラウド側が提供するサービスを「サーバレスサービス」と呼びます
    - Azure Functions:  **Serverless + Event-driven**
        
        
        | 料金プラン  | 説明 |
        | --- | --- |
        | 従量課金プラン | 実行回数と実行時間に基づくオンデマンドの従量課金のプラン。アプリの実行時間は最大１０分 |
        | 専用ホスティングプラン | 専用のリソースを用意してアプリを実行する固定料金のプラン。アプリの実行時間は最大１０分 |
        | Premiumプラン | 従量課金のプランの一種だが、仮想ネットワークへのアクセスのサポートなどの特別な機能が用意されている。また、アプリの実行時間は最大30分に延長されている |
        
        ![image.png](../images/az305-26.png)
        
4. バッチ処理サービス：
    - Azure Batch：オンプレミスで動作するクラウド最適化済みの高性能コンピューティング（HPC）ワークロードをクラウドへ移行する場合に最適です。大規模な並列処理や HPC アプリケーションを効率よく実行できます。ジョブ スケジューリング、計算リソースの自動スケーリング、タスク管理を提供します。
    - ノードの種類
        
        
        | 種類 | 説明 |
        | --- | --- |
        | 低優先度VM | Azureの余剰容量を活用した安価な仮想マシン。時間的な制約の少ない開発環境などの短時間実行タスクに向いている |
        | スポットVM | 低優先度VMと同様の特徴を持つ。なお、低優先度VMは廃止予定のため、スポットVMへの移行が推薦されている |
        | 専用VM | 専用の仮想マシン。本番環境の長時間実行タスクに向かている |
        
        Azure batchでは、プールをユーザ自身で管理することも、Azure Batchに管理させることもできる。これは、プールの作成時に指定する「プール割り当てモード」により決定する
        
        | 種類 | 説明 |
        | --- | --- |
        | User Subscription | ユーザ自身でプールを管理するモード。仮想マシンのサイズや数はユーザが指定する。専用VMまたはスポットVMが利用可能。オンプレミスのWindows ServerライセンスをAzureで利用できる「Azureハイブリッド特典」を活用できる |
        | Batch Service | Azure Batchがプールを管理する既定のモード。専用VMまたは低優先度VMが利用可能 |
    - **Azure CycleCloud：大規模なHPCクラスターのデプロイと管理を行います。Azure Cyclecloudは、業界標準のサードパーティー製のスケジューラを使用できるため、オンプレミスからの容易な移行が可能です**
        
        ![image.png](../images/az305-27.png)
        

#### Application Architectureの設計

1. Messaging Architecture
    - Azure Queue Storage: 送信者と受信者が1対1のサービスです。
        - クラウド サービス間のトランザクションを**非同期通信**させる
    - Azure Service Bus: 受信者が複数になる場合、1つのAzure Service Busトピックを使用してパブリッシュ/サブスクライブ（Pub/Sub）方式でメッセージを送受信するように構成します.  **Service Busキューは1対1、Service Busトピックは1対多の形式の通信となります**
        - クラウド サービス間のトランザクションを**非同期通信**させる
        - Azure Service Busのキューの「セッションの有効化」オブションを使用すれば、メッセージの先入れ先出しがFIFOが保証され、メッセージを送信した順番で確実に受信することができます
2. Event Driven Architecture
    
    ![image.png](../images/az305-28.png)
    
3. Cache Solution
    
    キャッシュは、配置する場所によって２種類に分かれています
    
    １）コンテンツキャッシュ：クライアントとWebアプリの間に配置し、HTMLベージなどのWebコンテンツをキャッシュする
    
    ２）データキャッシュ：Webアプリとデータベースの間に配置し、データベースなどのデータをキャッシュする
    
    - Azure Content Delivery Network: Web コンテンツのキャッシュ サービスが Azure Content Delivery Network（Azure CDN）です。世界各地の PoP と呼ばれる配信サーバーで Web コンテンツをキャッシュします。⭐ **Web コンテンツ向け**
    - Azure Cache for Redis：インメモリ データベースで、すべてのデータをメモリにキャッシュして高速に処理します。⭐ **データベース／アプリケーション データ向け**
4. 統合ソリューション
    - Azure API Management：仮想マシンやコンテナー上のアプリ、Azure Functionsの関数アプリなどで提供されているバックエンとサービスへのAPI要求をまとめて管理・保護する
        - Azure API Managementには、いくつかの価格レベルがあり、価格レベルがPremiumの場合、仮想ネットワークがサポートされます
        - **Azure API Management:**
            
            → API の**レート制限**が必要
            → 外部／サードパーティの認証に対応する必要がある
            → バックエンド サービス（Logic Apps／Function Apps など）を変更したくない
            
            → OAuth / JWT(JSON Web Token) 検証
            
            ![image.png](../images/az305-29.png)
            
            ![image.png](../images/az305-30.png)
            
    - **Azure Logic Apps：**インターネット上の複数のクラウド サービスを連携し、ワークフローを簡単に作成するサービスです。**ワークフローの自動化**
        - **概要**：**ローコード／ノーコードのワークフロー オーケストレーション ツール**です。**視覚的な画面でドラッグ＆ドロップ**し、複数のシステム／サービスをつなぎます。
        - **特徴**：
            - Office 365、SharePoint、Salesforce、SQL Server、Twitter など、数百種類の**コネクタ（Connector）**を内蔵
            - **業務プロセスの自動化**に適し、コードを書かずに利用できる（コードの記述も可能）
            - Functions より機能が豊富で、複雑な複数ステップの業務プロセスに適している
        - **典型例**：「SharePoint にファイルがアップロードされたらマネージャーにメール通知 → 承認後に SQL データベースへ自動登録」という複数ステップの業務フロー。
        - **Functions との違い**：
            - Functions：**コードを記述**し、技術的・論理的な単一タスクに適している
            - Logic Apps：**ドラッグ＆ドロップで構成**し、業務フローの調整や複数 SaaS システムの統合に適している
        - **ひとことで覚える**：**「コードを書かずに、ドラッグ＆ドロップで複数のシステムをつなぐ」**。
5. アプリ構成管理ソリューション
    - Azure App ConfigurationとAzure Key Vaultはどちらも設定やパラメーターを管理するサービスだが、Azure App configurationはアプリ構成の管理に特化されており、Azure Key Vaultは機密情報の管理に特化されている

#### Data Migration

1. Azure Migrate は、IT 環境全体をオンプレミス／他のクラウドから Azure へ移行するサービスです。
    - VM
    - Physical Server
    - Database
    - Web App
    - Virtual Desktop
2. AzCopy：**Blob／Files／Storage のデータをコピーする**
3. Azure Data Share：データを他の組織／ユーザーと共有し、定期的に共有・更新する。
4. Azure Import/Export：オフライン方式でオンプレミスの Storage と Azure Storage の間でデータを転送する。
5. Azure Data Box：大量データの移送に使う、Microsoft が提供する物理デバイス。

#### データベース移行の設計

1. Azure Data Studio：Windows, macOS, Linuxで動作するデータベース管理ツールです。
    - 標準でMicrosoft SQL ServerとAzure SQL databaseに対応し、OptionでMySQL, PostgreSQL, Azure Cosmos DBなどに対応します
    - 「**Azure SQL移行拡張機能」をインストールすることで、Microsoft SQL ServerからAzure SQL DatabaseやAzure SQL Managed Instanceへの移行がサポートされる**
    
    ![image.png](../images/az305-31.png)
    
2. Azure Database Migration Service (DMS): データベース移行専用サービス。✔ **オフライン移行**に対応。✔ **一括移行（50 データベース）**に対応。
    
    ![image.png](../images/az305-32.png)
    
3. Data Migration Assistant (DMA) は、移行前の「評価ツール」および SQL Server 移行ツールです。
4. SQL Server Migration Assistant (SSMA)：SQLServer**以外**（Microsoft Access, DB2, MySQL, Oracle, SAP ASE）からSQL Serverへの移行をサポートするツールです
    
    → Oracle、MySQL などの他のデータベースを
    SQL Server に一括移行するためのツール
    
5. Azure Cosmos DB data migration toolを使用すると、**Azure Cosmos DB**に簡単にデータを移行することができます

| 項目 | Azure Data Studio | DMS | DMA | SSMA | Azure Migrate |
| --- | --- | --- | --- | --- | --- |
| 移行の調査を行う | ⭕️ |  | ⭕️ |  | ⭕️ |
| SQL ServerからAzure SQL Databaseへ移行する | ⭕️ | ⭕️ | ⭕️ |  |  |
| SQL ServerからAzure仮想マシン上のSQL Serverへ移行する |  |  | ⭕️ |  |  |
| SQL ServerからAzure仮想マシン上のSQL Serverへシフトする |  |  |  |  | ⭕️ |
| 非SQL Objectを移行する |  |  |  | ⭕️ |  |
| Open Source Dataを移行する |  | ⭕️ |  |  |  |

#### ネットワークソリューションの設計

1. 仮想ネットワーク：仮想ネットワークは、リージョンごとに作成される
2. インターネット接続ソリューション：インターネットからの受信方向の通信するため
    - パブリックIPアドレス
    - Azure Load Balancer：仮想マシンの死活監視と負荷分散を行う
    - Azure Application Gateway
    - Azure NAT Gateway: アウトバウンド専用＋⭐ **プライベート VM からインターネットへ接続する**
    
    ![image.png](../images/az305-33.png)
    
3. オンプレミスネットワーク接続ソリューション：
    - Azure VPN Gateway：仮想ネットワークに配置するVPNデバイス
        
        ![image.png](../images/az305-34.png)
        
    - Azure ExpressRoute：オンプレミス ↔ Azure のプライベート接続。インターネット VPN と比べ、「高信頼性」「高速」「低遅延」という利点があります。
        
        ![image.png](../images/az305-35.png)
        
    - Azure ExpressRoute Global Reach：⭐ **異なるオンプレミス データセンター間を ExpressRoute／Microsoft ネットワーク経由で接続する**
    - **Azure Virtual WAN:**
        - **Azure Virtual WAN（仮想WAN）の種類には、Basic、Standardの2種類SKU (Stock Keeping Unit) があります**
            - **Basicの仮想WANは、Site-to-Site VPNにのみ使用することができます**
            - **ExpressRoute回線を含めるには、Standardにアップグレードする必要がある**
        - リージョンごとに作成する「仮想ハブ」と呼ばれるルーターを起点にして、Azure VPN GatewayによるインターネットVPN接続やAzure ExpressRouteによるプライベート接続な一元的に管理します
        
        ![image.png](../images/az305-36.png)
        
    - セキュリティ保護付きハブ：Azure Virtual WANの仮装ハブはオプション追加機能：Azure Firewall、ネットワーク仮想アプライアンス、SaaSソリューション
4. Azure Private Link
    
    ![image.png](../images/az305-37.png)
    
    - Azure Monitor Private Link Scope：
    
    ![image.png](../images/az305-38.png)
    
5. ネットワークパフォーマンスの最適化
    - 高速ネットワーク (AccelNet)：「シングルルートI/O仮想化 SR-IOV」と呼ばれる技術を使用し、ネットワークパフォーマンスを大幅に向上させるものです
    - Receive Side Scaling (RSS)：複数の CPU コアに処理を分散する
    - Proximity Placement Group：⭐ **物理的にできるだけ近い場所に配置する**
6. ネットワークセキュリティーの最適化
    - Azure DDoS Protection：「パブリックIPアドレスレベル」「仮想ネットワークレベル保護」
    - Azure Web Application Firewall：Azure WAFは「サービス」ではなく「機能」です。Azure WAFを有効化できるサービス例を以下に示します：
        - Azure Application Gateway
        - Azure Front Door
        - Azure Content Delivery Network (CDN): コンテンツをキャッシュする（静的リソースを高速化）
            - Azure CDN エンドユーザーの近くにコンテンツを保管
            - Azure Cache for Redis: アプリケーションの近くにコンテンすを保管
            
            ![image.png](../images/az305-39.png)
            
    - **Network Security Group：送信元と宛先それぞれのIPアドレスとポート番号、およびプロトコル種類を条件に、許可または拒否を設定します**
    - **Azure Firewall: Azure Firewall policy must be in the same region**
    - Azure Firewall manager：複数のリージョンやサブスクリプションにまたがるAzure Firewallインスタンスのデプロイと一元管理を行います
