# GridDB CE 5.5

## 変更点

GridDB CE V5.5の主な変更点は以下のとおりです。

- コストベースに基づくジョイン順序の最適化

    検索を実行する際のジョイン順序の最適化を強化しました。従来のSQLプランは、ユーザから与えられたSELECT文の記述内容や記述順に従い、ルールを用いて生成されていました。そのため、ユーザはルールを理解したうえで、テーブル規模や絞込み条件に合わせて、好ましいプランが生成されるようにSELECT文を記述する必要がありました。今回の強化では、検索実行の過程で求められるロウ数(テーブル規模や絞込み条件を勘案して概算)をコストとして計算し、複数テーブルの最適なジョイン順序を決定してプランを生成できるようになりました。

- パーティショニングテーブルに関する索引ジョインの最適化

  パーティショニングテーブルを含む場合でも、可能な限り索引ジョインを実行するように強化しました。従来は、対象のテーブルに索引が付与されている場合でも、GridDB内部の制約により索引ジョインを行えないケース(テーブルの分割数が大きすぎる場合など)があり、テーブルスキャンを行うことがありました。今回の強化では、内部の詳細な条件(テーブル分割数、分割種別や索引種別)によらず、可能な場合には索引ジョインを行うプランを生成、実行できるようになりました。

- SQLのバッチ更新のサポート

    バッチ更新を行うJDBCメソッド(addBatchなど)を新たにサポートしました。

- Zabbix向け監視テンプレート

    GridDBの監視を行うためのZabbix向けのテンプレートをサンプルとして追加しました。

- ETLによる時系列データの自動集計ガイドの提供

    蓄積された大量の時系列データを自動的に集計して集計結果を蓄積しておくことで、ユーザが集計結果を取得する際に、処理時間を短縮できるようになります。この時系列データの自動的な集計をETLを用いて実現するガイドを提供します。

---
## コストベースに基づくジョイン順序の最適化

コストベースによる方法とルールベースによる方法のいずれを用いるかは切り替え可能で、以下を使って設定できます。
- クラスタ定義ファイル(gs_cluster.json)
- ヒント

クラスタ定義ファイル(gs_cluster.json)を使う場合：

  * /sql/costBasedJoin：SQLプラン生成の際、ジョイン順序の決定にコストベースによる方法を用いるかどうかを指定します。用いない(false)場合は、ルールベースを用いてジョイン順を決定します。デフォルト値はコストベースによる方法(true)です。

詳細は「SQLチューニングガイド」の[コストベースによる方法](https://github.com/griddb/docs-ja/blob/master/manuals/md_sql_tuning_guide/md_sql_tuning_guide.md#%E3%82%B3%E3%82%B9%E3%83%88%E3%83%99%E3%83%BC%E3%82%B9%E3%81%AB%E3%82%88%E3%82%8B%E6%96%B9%E6%B3%95)をご参考願います。

## SQLのバッチ更新のサポート

PreparedStatementインターフェースについて以下のメソッドをサポートします。
設定されていないパラメータがある状態でexecuteQueryなどクエリ実行APIを呼び出すと、SQLExceptionが発生します。

- addBatch()
- clearBatch()
- executeBatch()
    - ResultSetを含まないSQLに対してのみバッチ更新が可能であり、含む場合はエラーを返します。この場合エラーを返すタイミングはaddBatch()/clearBatch()ではなく、executeBatch()のタイミングとなります。また、対象SQLが複文となるケースもエラーとなります。

データ登録を行う以下のサンプルコードをご参考願います。

- JDBCドライバ: [JDBCAddBatch.java](https://github.com/griddb/jdbc/blob/master/sample/ja/jdbc/JDBCAddBatch.java)

## Zabbix向け監視テンプレート

「[GridDB 監視テンプレート for Zabbix 説明書](https://github.com/griddb/griddb/blob/master/sample/zabbix/GridDB_ZabbixTemplateGuide_ja.md)」をご参考願います。

## ETLによる時系列データの自動集計ガイドの提供

「[ETL自動集計ガイド](https://github.com/griddb/docs-ja/blob/master/manuals/md_etl_auto_aggregation_guide/md_etl_auto_aggregation_guide.md)」をご参考願います。

