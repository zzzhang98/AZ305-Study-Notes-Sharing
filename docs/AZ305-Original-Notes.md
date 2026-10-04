# AZ305 Original Notes

Source: the user-provided export of the specified AZ305 Notion page.

# AZ305

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
        | Application Insight | 通过Azure Monitor中提供的可扩展应用性能管理（APM）服务，监控你在任何平台上的实时Web应用 |
        | Container Insight | 检查部署到Azure Container Instances或托管在Azure Kubernetes Service（AKS）上的托管Kubernetes集群的容器工作负载的性能 |
        | Networks Insight | 获取所有网络资源的健康状况和指标的全面信息。使用高级搜索功能来识别资源依赖关系。通过你的网站名称搜索，寻找托管你网站的资源 |
        | Resource Group Insight | 对你的各个资源遇到的问题进行分诊和诊断，同时提供整体资源群体的健康和表现背景 |
        | VM Insight | 监控你的Azure虚拟机、虚拟机规模集和其他虚拟机。分析Windows和Linux虚拟机的性能与健康状况，监控其进程及对其他资源和外部进程的依赖情况 |
        | Azure Cache for Redis Insight | 数据库查询缓存、Session存储、实时排行榜 Cache the data so users can achieve them quicker |
        | Azure Cosmos DB Insight | 在统一的互动体验中获取所有Azure Cosmos DB 资源的整体性能、故障、容量和运营健康状况信息 |
        | Azure Key Vault Insight | 通过统一报告你的密钥库请求、性能、故障和延迟来监控你的密钥库 |
        | Azure Storage Insight | 通过统一的存储性能、容量和可用性报告，全面监控您的存储账户 |
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
    2. **Azure Lighthouse：Azure Lighthouseを使用すれば、外部のEntraテナントのユーザやグループに自社のAzure Subscriptionのアクセス権を簡単に割り当てることができる (Collect the event logs)  是一项支持跨租户管理 Azure 资源的服务，因此非常适合从链接到不同租户的多个订阅中收集事件日志**
3. シングルサインオン：
    1. Federation SSO：Entra IDは、SAML (Security Assertion Markup Language）やOpenID Connectなどのシングルサインオン規格をサポートしています。
    2. Password Based SSO：EntraIDでは、ユーザーが開発したシンプルなアプリのSSOも可能です
4. Microsoft Entra Connect：多くの企業では、オンプレミス（企業内ネットワーク）のユーザー管理サービスとして、Windows Serverの標準機能であるActive Directory Domain Serviceを採用する。Entraテナントを導入するサービスが、Microsoft Entra Connectにより、AD DSのユーザーとグループをEntraテナントへ定期的にコピーできます
    1. Password WriteBack: 密码回写功能允许将云端所做的密码更改写回本地 Active Directory。这有助于确保本地环境和云环境之间的密码保持同步，从而减少服务台手动管理密码的需求。
    2. Self-service password reset: 自助密码重置功能允许用户自行重置密码，无需联系服务台。这减轻了服务台人员的工作量，并使用户能够更高效地管理密码，最终降低网络基础设施的管理开销。
5. Microsoft Entra Connect Health：Microsoft Entra Connectの複数コンポーネントを一元的に監視するサービス
6. **Microsoft Entra Application proxy：外部用户 + 访问公司内部 Web App + 不使用 VPN  把 on-premises 的内部 Web Application 安全发布到 Internet。**
7. **Microsoft Entra ID Governance：**
    1. **Entitlement Management：従業員の入社、昇格、異動、退職などのライフサイクルに応じて、必要な権限をアクセス権として自動的に割り当てる機能です**
    2. **Access Review：アクセス権限の定期的に評価する**
8. Microsoft Entra Managed Identity：我的 Azure 应用/资源需要访问另一个 Azure 资源，怎么认证
9. Microsoft APP Registration：把 App1 注册到 Entra ID，让 Entra ID 认识这个应用。让用户访问应用时使用 Entra ID 身份认证 / SSO

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
5. Azure Bicep 是一种特定领域的语言，用于以声明式的方式部署 Azure 资源。它允许您以结构化且可重复的方式定义和部署所有必需的组件，包括管理组、订阅和资源组。通过使用 Azure Bicep，您可以轻松设置问题中所述的整个 Azure 环境，且只需极少的管理工作

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
        | modify | リソースのタグを変更する ⭐ 修改/添加 Tag 等属性 |
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
5. Data Warehouse： 结构化数据 用于分析
    
    ERP ─┐
    SQL ─┼→ Data Warehouse → BI / Analysis
    CRM ─┘
    
6. Data Lake：原始格式数据
    
    
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
        - ✅ 适用账号：**StorageV2、Blob Storage**；**不支持** StorageV1。
        - ✅ 冗余支持：**LRS/GRS/RA-GRS**；**不支持** ZRS/GZRS/RA-GZRS。
        - ⚠️ **Premium BlockBlob** 账号**不支持换层**（只能删）。
        - ⚠️ Archive 层**不能创建快照**
    - Immutable Storage：ユーザーはビジネスに不可欠なデータを WORM (Write Once, Read Many) 状態で保存できます。 WORM の状態では、ユーザーが指定した期間、データを変更、削除することができないため、上書きや削除からデータを保護することができます
2. **Azure Files**: Managed file shares for cloud or on-premises deployments. Access files across multiple machines. Access to shared folders via SMB (Server Message Block protocol 445), not only via API, but also directly from Windows 10, macOS, and Linux.
    - **Authentication method for File service: SAS and Microsoft Entra**
    - **永続ストレージ**を必要とする場合、ストレージアカウントのファイル共有をマウントしてコンテナーの外部にデータを保存するように構成することができます
    - **Azure Storage Explorer is a graphical tool to manage Azure Storage Resources (Blobs, files, queues, tables). But cannot create new storage accounts**
    - Azureコンテナーインスタンスの**外部ボリュームとしてサポート**されているのは、Azure Filesで作成された Azureファイル共有のみです
    
    | Premium | File shares use SSD and provide consistent high performance and low latency. Can be used with both Server Message Block(SMB) and Network file system(NFS) protocols |
    | --- | --- |
    | Transaction optimized | 事务优化：用于交易量大的工作负载，不需要高级文件共享的延迟。文件共享提供在由硬盘驱动器（HDD）支持的标准存储硬件上 |
    | Hot access tier | 为通用文件共享场景（如团队共享）优化的存储。提供在标准存储硬件上的HDD |
    | cool access tier | 针对在线档案存储场景优化的经济高效存储。提供在使用硬盘的存储硬件上 |
    - **Azure File Sync**: 是一项服务，用于缓存并使用存储在 Windows 服务器上 Azure 文件共享（Azure 存储中文件）中的数据，例如本地部署（Azure File Sync 部署区域内需要 Azure 文件共享）
        - 将本地文件服务器扩展到云端
        - 云端集中管理，并跨多个地点共享
        - 备份与灾害准备
3.  **Four Replication Strategies:** 
    
    ![Untitled](../images/az305-05.png)
    
    ![image.png](../images/az305-06.png)
    
    - **SMB Multichannel only for Premium Azure File**
    - **Standard 汎用 v2 は「ゾーン冗長ストレージ (ZRS)」をサポートしており、Azureポータルから LRS → ZRS に変換することができます**
    - 只有启用了 GRS/GZRS 类跨 Region 冗余的 Storage Account 才涉及这种 Failover

#### Azure SQL Database

1. Azure SQL Database: 单独数据库 适合新开发的云应用
    - Two primary pricing options for SQL Database:
        - **DTU( Database Transaction Unit) is a combined measure of compute, storage, and I/O resources.**
        - **vCore is a virtual core. You choose the number of virtual cores and have greater control over your compute costs**
2. A single Azure SQL Database支持serverless  支持elastic pool 
    - **Serverless 模式：
    根据负载自动扩展/缩减 CPU ✅
    没有查询时自动暂停 ✅
    按实际使用的秒数计费 ✅ ← 满足"按秒计费"**
    - 支持 General Purpose 和 Hyperscale
3. Azure SQL Managed Instance:  是 Azure SQL 的一个 PaaS 部署选项。与Azure SQL Database一样，Azure SQL托管实例是一个完全托管的服务。它提供了SQL Server实例，但大大减少了管理虚拟机的开销  适合on-prem SQL server迁移
4. SQL server on Azure virtual machine: 运行在 Azure 虚拟机（VM）上的 SQL Server 版本。这项服务让你无需管理本地机器，即可在云端使用完整版本的SQL Server。
    1. オンプレミスのMicrosoft SQL Serverを最小限の工数でAzureへ移行できるという特徴がある

| Compare | SQL Database | SQL Managed Instance  | SQL Server on Azure Virtual Machines |
| --- | --- | --- | --- |
| Scenarios | 最适合现代云应用、超大规模或无服务器配置 | 最适合大多数迁移到云端的实例范围功能 | 最适合快速迁移和需要操作系统级访问的应用 |
| Features | Serverless compute
Fully managed service
Elastic pool
**OLTP に最適化** | Native virtual networks
Fully managed service
Instance pool
**支持CLR （Common Language Runtime）** | OS-Level server access
Expansive version support for SQL server |
- The Business Critical tier in Azure SQL Database is specifically optimized for high-performance OLTP workloads that require the fastest recovery time in case of failures Azure SQL 数据库中的业务关键层专为高性能 OLTP 工作负载而优化，这些工作负载在**发生故障时需要最快的恢复速度**。它提供内存技术和加速数据库恢复等功能，以确保最短的停机时间和快速恢复。
- Azure SQL Database Hyperscale适合多个只读副本（Read scale-out）
    - 多个 read-only replicas
    - 自动同步/复制数据
    - Read scale-out
    - 快速 failover
    - Be optimized for online transaction processing (OLTP)

![image.png](../images/az305-07.png)

![image.png](../images/az305-08.png)

1. Azure SQL Databaseのセキュリティ
    1. Azure SQL Database監査ログ：Azureポータルから監査ログを有効化し、ストレージアカウントを選択または新規作成する場合、ストレージアカウントはデータベースやサーバーと同じリージョンに限定されたので注意が必要です
    2. Firewall：哪些 IP 地址可以连接 SQL Database
    3. アクセス制御
    4. Row-level Security（RLS）：用户可以看到哪些行（Row）
    5. Dynamic Data Masking：電話番号や住所などの個人情報PII (Personally Identifiable Information) の例に対して動的データマスキングを設定することで、プライバシーを保護できる
    6. 転送中の暗号化：クライアントとAzure SQL Databaseの通信を、SSL/TLSを使用して暗号化することができます
    7. Transparent Data Entryption：Azure SQL Dataのデータベース全体を暗号化する機能。規定、ユーザ独自のキーもある。ユーザーが独自のキーを用意する場合、アルゴリズムとして非対称、RSA、RSA HSMを指定でき、キーサイズは2048, 3072をサポートします 
    8. Always Encrypted：クライアント側で機密データを暗号化した上で、Azure SQL Databaseのデータベースへ書き込む機能

#### AzureのNon-Relational Database

1. Azure Cosmos DB：全球分布式 NoSQL 数据库
    1. NoSQLデータベースの多くのAPIオブションをサポートする NoSQL, MongoDB, Apache Cassandra, Apache Gremlin, Table
        
        ✑ Support SQL commands.
        
        ✑ Support multi-master writes.
        
        ✑ Guarantee low latency read operations.
        
    2. 設計パラメーター：
        1. Request Unit: Cosmos DB 衡量数据库操作性能的“单位”
        2. 容量モード：
            - Provisioning **throughput** mode:自己设定 Request Unit （RU)
            - Auto-scaling mode: RU 自动随负载变化
            - Severless mode: 不需要设置 RU（従量課金）
        3. アクセス制御：
            - RBAC: Entra IDのユーザやグループを使用したAzure RBACによるアクセス制御
            - プライマリキー／セカンダリキー
            - リソーストークン：特定のデータベース、コンテナー、項目への一時的なアクセスを提供する
    3. Azure Cosmos DBはNoSQLとSQLの２種類のデータベースをサポートする
    NoSQLには
        1. SQL API是处理JSON文档的最佳选择。它允许您以原生方式存储 JSON 数据，并使用 SQL 语法进行查询，使其成为高效处理 JSON 文档的灵活之选。
        2. Gremlin API专为图数据设计，针对图遍历和查询进行了优化
        3. Cassandra API专为列族数据模型而设计，更适合处理具有固定模式的结构化数据
        4. MongoDB API 适合高效地储存和查询JSON文档
2. Azure Cosmos DB 　SQLについては、Azure Cosmos DB for PostgreSQL
    
    通过使用Azure Cosmos DB的服务基础架构，PostgreSQL可以将资料库分散到多个区域，并通过水平扩展 Horizontal Scaling提供高效能，以及通过多区域复写提供高可用性
    
    ![image.png](../images/az305-09.png)
    

#### データ分析Solutionの基礎

1. Apache Hadoop主要由两个重要部分组成： Hadoop = 分布式存储 + 分布式计算
    - **HDFS** (Hadoop Distributed File System)→ 存储数据
    - **MapReduce** → 分布式处理数据
2. Apache Spark：Apache Hadoopを改良したものです。データはすべてメモリで高速処理できるため、**リアルタイムの分析可能**     Spark 是**数据处理/分析引擎**，不是数据库
    
    ![image.png](../images/az305-10.png)
    
3. Databricks：基于 Apache Spark 的数据分析平台

#### **Azureのデータ分析ソリューション**

1. データ分析のフロー  **ADF 搬数据 → ADLS 存数据 → Databricks/Spark 加工 → Synapse 分析 → Power BI 展示**
    
    
    | データ分析のステップ | 説明 | 主なAzureサービス |
    | --- | --- | --- |
    | Ingest | 様々なデータソースからデータを収集する | Azure Data Factory ⭐ 收集/搬运/转换数据
    Azure Synapse Analytics ⭐ 分析型数据仓库 |
    | Store | ストアに保存する | Azure Data Lake Storage Gen2 ⭐ 保存大量原始数据 |
    | Prep & Train | データの分析のためのデータ加工や、機械学習のためのデータの前処理を行う | Azure Databricks ⭐ Spark 数据处理/机器学习
    Azure Synapse Analytics (Spark Pool) |
    | Model & Serv | 整理されたデータを分析用ストアに保存する | Azure Synapse Analytics (SQL pool)
    Auzre Analysis Services  ⭐ 多维分析
    Azure Data Explorer   ⭐ 大量数据近实时分析
    Azure Data Share   ⭐ 和其他组织共享数据
    Azure Machine Learning
    Power BI |
2. **Azure Data Factory: 一项基于云的数据集成服务，可以帮助你创建和调度数据驱动的工作流程。你可以使用 Azure Data Factory 来协调数据的移动并大规模转换数据。数据驱动的工作流程或管道会从不同的数据存储中导入数据**
    - Azure Data Factoryは収集するデータを変換・加工して、別の場所に保存することを、**Extract（抽出）、Transform（変換）、Load（書き出し）   SSIS** = SQL Server Integration Services → **ETL 数据集成**
    - **Azure Data Factory转换数据并导出到Azure Data lake Storage 需要 Integration runtime 数据搬运引擎      Azure Data Factory (ADF)** 可以托管和运行 **SSIS packages**，也就是 **Azure-SSIS Integration Runtime**。
    
    ![image.png](../images/az305-11.png)
    
3. **Azure Data Lake:  以自然格式存储的数据，通常以blob或文件的形式存储. Azure Data Lake Storage 结合了文件系统和存储平台，帮助您快速识别数据洞察。该解决方案基于 Azure Blob 存储能力，为分析工作负载提供优化**
    
    ![image.png](../images/az305-12.png)
    
    - Azure Data Lake Storage characteristics:
        - Hierarchical Namespace
        - Scalability
        - Security: Azure AD for identity and access management, RBAC and so on. Also supports Azure Private Link
        - **支持不可变存储 immutable storage**
        - **禁止匿名访问 disable anonymous access**
        - **Supports access control list (ACL)-based Azure AD permissions**
    - Azure Data lake storage three important steps:
        - Ingest data:
            - For unplanned data, you can use tools like AzCopy, the Azure CLI, PowerShell, and Azure Storage Explorer.
            - For relational data, the Azure Data Factory service can be used. You can transfer data from any source, such as Azure Cosmos DB, SQL Database, Azure SQL Managed instances, and more
            - For streaming data, you can use tools like Apache Storm on Azure HDInsight, Azure Stream Analytics, and so on
        - Access stored data: 访问数据最简单的方式是使用Azure存储资源管理器。Storage Explorer 是一个独立应用程序，带有图形用户界面（GUI），用于访问您的 Azure Data Lake 存储数据。你也可以使用PowerShell、Azure CLI、**HDFS** CLI或其他编程语言SDK来访问数据
        - Configure access control: 通过实施授权机制，控制谁可以访问 Azure 数据湖存储中存储的数据。你可以选择Azure RBAC或ACL
        - Azure Blob storage or Azure Data Lake comparison
4. **Azure Databricks SKU : 完全托管的云端大数据和机器学习平台，赋能开发者加速人工智能和创新**
    - Azure Databricks has a Control plane and Data plane:
        - **Azure Databricksの価格レベルには、Standard とPremium がありますが、「Azure Data Lake Storage 資格情報 Credential Passthrough」を使用するには Premium プランが必要です**
        - Service Principal 配置服务主体是为应用程序访问 Azure Databricks 工作区配置身份验证的正确选择。服务主体是应用程序或服务用于访问特定 Azure 资源的安全标识。它允许进行安全身份验证，而无需在应用程序中存储凭据，从而最大限度地减少管理工作量并提高安全性
        - Use for Big Data analytics, Machine Learning, Real-time Analytics, ETL processes, Data Exploration and Visualization
        
        ![image.png](../images/az305-13.png)
        
5. Azure Synapse Analytics: 结合了大数据分析、企业数据存储和数据集成等功能。该服务允许你对无服务器数据或大规模数据运行查询。Azure Synapse 支持数据摄取、探索、转换和管理，并支持分析以满足您所有的商业智能和机器学习需求
    
    ![image.png](../images/az305-14.png)
    
    - Components of Azure Synapse Analytics:
        - Azure Synapse SQL pool: 提供无服务器和专用资源模型，支持基于节点的架构。为了实现可预测的性能和成本，你可以创建专用的SQL池。对于不规律或未计划的工作负载，你可以使用始终可用的无服务器SQL端点。
        - Azure Synapse Spark pool: 是一个运行 Apache Spark 处理数据的服务器集群。你可以通过四种支持的语言之一来编写数据处理逻辑：Python、Scala、SQL 和 C#（通过 Apache Spark 的 .NET 语言）。Azure Synapse 版 Apache Spark 集成了 Apache Spark（用于数据准备、数据工程、ETL 和机器学习的开源大数据引擎）。
        
        ![image.png](../images/az305-15.png)
        
        - Azure Synapse Pipelines:
            - 从 SQL Server 等数据源读取数据
            - Copy data 到 Azure Data Lake Storage Gen2
            - 在数据移动过程中使用 Mapping Data Flow 等进行转换
            - 将转换后的数据写入目标 Data Lake
        - Azure Synapse Link: **此组件允许您连接到Azure Cosmos数据库。你可以用它对存储在Azure Cosmos数据库中的运营数据进行近实时分析。**
            
            ![image.png](../images/az305-16.png)
            
        - Azure Synapse Studio: 一个基于网页的集成开发环境（IDE），可集中使用，配合 Azure Synapse Analytics 的所有功能。你可以使用 Azure Synapse Studio 创建 SQL 和 Spark 池，定义并运行管道，配置指向外部数据源的链接
    - Managed Workspace仮想ネットワーク：ユーザーの代わりにAzure Synapse Analyticsによって管理されるため、セキュリティやパフォーマンスなどの管理が不要となり、管理負荷が軽減します。
6. Azure Analysis Services：オンライン分析処理OLAP（Online Analytical Processing）を実行します
7. Azure Machine Learning: MLのモデルの構築とデプロイを行うManaged Service
8. **Azure Data Explorer：大量のデータをほぼリアルタイムで収集し、分析と可視化を行うオールインワンのサービスです   大量数据 + Near real-time analysis**
9. Azure Data Share：ADLS Gen2やAzure Synapse Analytics、Azure SQL Databaseなどのデータのスナップショットへのアクセスを、provide restricted access

### ビジネス継続性ソリューションを設計する（15~20%）

#### **Azure Site Recovery**

1. Recovery time objective RTO: 指在停电或问题发生后，可用来恢复资源的最长时间。如果这个流程比RTO时间长，可能会有经济罚款、无法完成工作等后果。RTO可以为整个解决方案（包括所有资源）以及单个组件（如SQL Server实例和数据库）指定。
2. Recovery point objective RPO: 指数据库应恢复的时间点，并对应企业愿意接受的最大数据丢失量。例如，假设一个包含SQL Server的IaaS虚拟机在上午10点发生故障，且SQL Server实例内的数据库RPO为15分钟。无论使用什么功能或技术来恢复该实例及其数据库，预计最多会丢失15分钟的数据。这意味着数据库可以恢复到上午9：45或更晚，确保数据丢失达到规定的RPO。可能有一些因素决定该RPO是否可达
3. Recovery Level Objective RLO: 目標復旧レベル。RLOはRTOとセットで使用し、RLOの段階ごとにRTOを定義する
4. Azure Site Recoveryのレプリケーション方法
    
    
    | 種類 | 説明 | レプリケーション |
    | --- | --- | --- |
    | クラッシュ整合性スナップショット | 単純に仮想マシンのディスクデータをレプリケーションする | 5分ごと |
    | アプリ整合性スナップショット | アプリの動作を意識した上で仮想マシンのディスクデータをレプリケーションする | 1時間〜12時間ごと |

### 事業継続性ソリューションの設計

1. Availability Sets: 为单一数据中心的 Azure 相关维护和单点故障提供运行时间
    - **Availability Set**
        - The availability set is prepared as a dedicated resource and allocated at the time of virtual machine creation. **Availability sets cannot be assigned or changed after the virtual machine is created.**
            - Parameters of an availability set:
                - **Update Domain: maximum number of update domains that can accommodate planned maintenance of the host server is 20**
                - **Fault Domain: can accommodate server rack failures Maximum value for a fault domain is 3**
                
                ![image.png](../images/az305-17.png)
                
2. **Availability Zones are mechanisms that allow services to continue in another data center even if there is a data center-wide failure within a region.可用性ゾーンは、リージョン内のデータセンター規模の障害があっても別のデータセンターでサービスを継続するための仕組みです。**
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

- **要求：Azure 如何让 Web App 在 Region 故障时继续提供服务**
    - **⭐ 如果要防止整个 Region 故障 → 需要 Global（全球级）服务  → Azure Front Door, Traffic manager**
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
    - ⭐ **Backup 后，只能恢复到与原 Key Vault 相同 Region 的 Key Vault**

### インフラストラクチャーソリューションを設計（30~35%）

#### Computing Solutionの設計

1. 仮想マシンサービス：
    
    ![image.png](../images/az305-23.png)
    
- 仮想マシンのバースト：
    - Bシリーズには**Burst（突发性能）**。仮想マシンが低負荷ときに、低いCPUパフォーマンスを提供し、高い負荷時に、高いCPUパフォーマンスを動的に提供するものです
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
    - Azure Batch：将本地部署的、云优化的高性能计算 (HPC) 工作负载应用程序迁移到云端，Azure Batch 是理想之选。Azure Batch 使您能够在云端高效运行大规模并行和高性能计算 (HPC) 应用程序。它提供作业调度、计算资源自动缩放和任务管理功能，使其成为 HPC 工作负载的理想选择
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
        - enable the cloud services to asynchronously communicate transaction  云服务之间需要**异步通信**
    - Azure Service Bus: 受信者が複数になる場合、1つのAzure Service Busトピックを使用してパブリッシュ/サブスクライブ（Pub/Sub）方式でメッセージを送受信するように構成します.  **Service Busキューは1対1、Service Busトピックは1対多の形式の通信となります**
        - enable the cloud services to asynchronously communicate transaction  云服务之间需要**异步通信**
        - Azure Service Busのキューの「セッションの有効化」オブションを使用すれば、メッセージの先入れ先出しがFIFOが保証され、メッセージを送信した順番で確実に受信することができます
2. Event Driven Architecture
    
    ![image.png](../images/az305-28.png)
    
3. Cache Solution
    
    キャッシュは、配置する場所によって２種類に分かれています
    
    １）コンテンツキャッシュ：クライアントとWebアプリの間に配置し、HTMLベージなどのWebコンテンツをキャッシュする
    
    ２）データキャッシュ：Webアプリとデータベースの間に配置し、データベースなどのデータをキャッシュする
    
    - Azure Content Delivery Network: WebコンテンツのキャッシュサービスがAzure Content Delivery Network (Azure CDN)。Azure CDNでは、全世界に配置されたPoPと呼ばれる配信サーバでWebコンテンツをキャッシュする  ⭐ **Web 内容**
    - Azure Cache for Redis：インメモリデータベースであるため、すべてのデータをメモリにキャッシュし、高速な処理を実現する    ⭐ **数据库/应用数据**
4. 統合ソリューション
    - Azure API Management：仮想マシンやコンテナー上のアプリ、Azure Functionsの関数アプリなどで提供されているバックエンとサービスへのAPI要求をまとめて管理・保護する
        - Azure API Managementには、いくつかの価格レベルがあり、価格レベルがPremiumの場合、仮想ネットワークがサポートされます
        - **Azure API Management:**
            
            → 需要对 API 做**速率限制**
            → 需要支持外部/第三方身份验证
            → 不想修改后端服务（Logic Apps / Function Apps等
            
            → OAuth / JWT(JSON Web Token) 検証
            
            ![image.png](../images/az305-29.png)
            
            ![image.png](../images/az305-30.png)
            
    - **Azure Logic Apps：**インターネット上の複数のクラウドサービスを連携し、ワークフローを簡単に作成する   工作流自动化
        - **是什么**:一个**低代码/无代码的工作流编排工具**,通过**拖拽可视化界面**,把多个系统/服务"串联"起来。
        - **特点**:
            - 内置几百种**连接器(Connector)**:Office 365、SharePoint、Salesforce、SQL Server、Twitter 等
            - 适合**业务流程自动化**,不需要写代码(虽然也可以写代码)
            - 比 Functions 更"重",但更适合复杂的多步骤业务流程
        - **典型场景**:"当有人在 SharePoint 上传文件 → 自动发邮件通知经理 → 经理批准后 → 自动写入 SQL 数据库"这种多步骤业务流程。
        - **和 Functions 的区别**:
            - Functions:**写代码**,适合技术性、逻辑性强的单一任务
            - Logic Apps: **拖拽配置**,适合业务流程编排,整合多个 SaaS 系统
        - **一句话记忆**:**"不想写代码,用拖拽的方式把多个系统串起来"**。
5. アプリ構成管理ソリューション
    - Azure App ConfigurationとAzure Key Vaultはどちらも設定やパラメーターを管理するサービスだが、Azure App configurationはアプリ構成の管理に特化されており、Azure Key Vaultは機密情報の管理に特化されている

#### Data Migration

1. Azure Migrateは、把整个 IT 环境从 On-premises / 其他 Cloud 迁移到 Azure
    - VM
    - Physical Server
    - Database
    - Web App
    - Virtual Desktop
2. AzCopy：**复制 Blob / Files / Storage 数据**
3. Azure Data Share: 把数据共享给其他组织/用户，并可以定期共享/更新数据。
4. Azure Import/Export：通过离线方式，在本地 Storage 和 Azure Storage 之间传输数据。
5. Azure Data Box：微软提供给你的实体设备，用来搬运大量数据

#### データベース移行の設計

1. Azure Data Studio：Windows, macOS, Linuxで動作するデータベース管理ツールです。
    - 標準でMicrosoft SQL ServerとAzure SQL databaseに対応し、OptionでMySQL, PostgreSQL, Azure Cosmos DBなどに対応します
    - 「**Azure SQL移行拡張機能」をインストールすることで、Microsoft SQL ServerからAzure SQL DatabaseやAzure SQL Managed Instanceへの移行がサポートされる**
    
    ![image.png](../images/az305-31.png)
    
2. Azure DataBase Migration Service (DMS): 专门用于数据库迁移  ✔ 支持 **离线迁移**  ✔ 支持 **批量迁移（50个数据库）**
    
    ![image.png](../images/az305-32.png)
    
3. Data Migration Assistant (DMA)は、迁移前的“检查工具” + SQL Server 迁移工具
4. SQL Server Migration Assistant (SSMA)：SQLServer**以外**（Microsoft Access, DB2, MySQL, Oracle, SAP ASE）からSQL Serverへの移行をサポートするツールです
    
    → 用于将其他数据库（Oracle、MySQL等）
    一次性迁移到 SQL Server 的工具
    
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
    - Azure NAT Gateway: アウトバウンド専用＋⭐ **让 Private VM 主动访问 Internet**
    
    ![image.png](../images/az305-33.png)
    
3. オンプレミスネットワーク接続ソリューション：
    - Azure VPN Gateway：仮想ネットワークに配置するVPNデバイス
        
        ![image.png](../images/az305-34.png)
        
    - Azure ExpressRoute：On-premises ↔ Azure 的私有连接　インタネットVPN接続と比べて「高信頼性」「高速」「低遅延」という利点があります。
        
        ![image.png](../images/az305-35.png)
        
    - Azure ExpressRoute Global Reach：⭐ **让不同 On-premises 数据中心之间通过 ExpressRoute / Microsoft 网络通信**
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
    - Receive Side Scaling (RSS)：分散到多个 CPU Core
    - Proximity Placement Group：⭐ **物理位置尽可能靠近**
6. ネットワークセキュリティーの最適化
    - Azure DDoS Protection：「パブリックIPアドレスレベル」「仮想ネットワークレベル保護」
    - Azure Web Application Firewall：Azure WAFは「サービス」ではなく「機能」です。Azure WAFを有効化できるサービス例を以下に示します：
        - Azure Application Gateway
        - Azure Front Door
        - Azure Content Delivery Network (CDN): 缓存内容（加速静态资源）
            - Azure CDN エンドユーザーの近くにコンテンツを保管
            - Azure Cache for Redis: アプリケーションの近くにコンテンすを保管
            
            ![image.png](../images/az305-39.png)
            
    - **Network Security Group：送信元と宛先それぞれのIPアドレスとポート番号、およびプロトコル種類を条件に、許可または拒否を設定します**
    - **Azure Firewall: Azure Firewall policy must be in the same region**
    - Azure Firewall manager：複数のリージョンやサブスクリプションにまたがるAzure Firewallインスタンスのデプロイと一元管理を行います