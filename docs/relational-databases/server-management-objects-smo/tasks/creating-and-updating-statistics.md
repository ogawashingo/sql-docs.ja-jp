---
title: 統計の作成と更新 |Microsoft Docs
ms.custom: ''
ms.date: 08/06/2017
ms.prod: sql
ms.prod_service: database-engine
ms.reviewer: ''
ms.technology: ''
ms.topic: reference
helpviewer_keywords:
- statistical information [SMO]
ms.assetid: 47a0a172-a969-4deb-bca9-dd04401a0fe1
author: markingmyname
ms.author: maghan
monikerRange: =azuresqldb-current||=azure-sqldw-latest||>=sql-server-2016||=sqlallproducts-allversions||>=sql-server-linux-2017||=azuresqldb-mi-current
ms.openlocfilehash: 7a1a48bd559ee3af794129d7a6559efba65b30c8
ms.sourcegitcommit: f3f83ef95399d1570851cd1360dc2f072736bef6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "70911156"
---
# <a name="creating-and-updating-statistics"></a>統計の作成と更新
[!INCLUDE[appliesto-ss-asdb-asdw-xxx-md](../../../includes/appliesto-ss-asdb-asdw-xxx-md.md)]
  SMO では、<xref:Microsoft.SqlServer.Management.Smo.Statistic> オブジェクトを使用して、データベース内のクエリの処理に関する統計情報を収集することができます。  
  
 任意の列に対する統計の作成は、<xref:Microsoft.SqlServer.Management.Smo.Statistic> および <xref:Microsoft.SqlServer.Management.Smo.StatisticColumn> オブジェクトを使用して行うことができます。 <xref:Microsoft.SqlServer.Management.Smo.Statistic.Update%2A> メソッドを実行して、<xref:Microsoft.SqlServer.Management.Smo.Statistic> オブジェクト内の統計を更新することができます。 結果は、クエリ オプティマイザーで表示できます。  
  
## <a name="example"></a>例  
 提供されているコード例を使用するには、アプリケーションを作成するプログラミング環境、プログラミング テンプレート、およびプログラミング言語を選択する必要があります。 詳細については、「 [Visual Studio&#35; .Net での Visual C SMO プロジェクトの作成](../../../relational-databases/server-management-objects-smo/how-to-create-a-visual-csharp-smo-project-in-visual-studio-net.md)」を参照してください。  
  
## <a name="creating-and-update-statistics-in-visual-basic"></a>Visual Basic での統計の作成および更新  
 このコード例では、既存のデータベースに新しいテーブルを作成し、<xref:Microsoft.SqlServer.Management.Smo.Statistic> オブジェクトおよび <xref:Microsoft.SqlServer.Management.Smo.StatisticColumn> オブジェクトを作成しています。  
  
```VBNET
'Connect to the local, default instance of SQL Server.
Dim srv As Server
srv = New Server
'Reference the AdventureWorks2012 database.
Dim db As Database
db = srv.Databases("AdventureWorks2012")
'Reference the CreditCard table.
Dim tb As Table
tb = db.Tables("CreditCard", "Sales")
'Define a Statistic object by supplying the parent table and name arguments in the constructor.
Dim stat As Statistic
stat = New Statistic(tb, "Test_Statistics")
'Define a StatisticColumn object variable for the CardType column and add to the Statistic object variable.
Dim statcol As StatisticColumn
statcol = New StatisticColumn(stat, "CardType")
stat.StatisticColumns.Add(statcol)
'Create the statistic counter on the instance of SQL Server.
stat.Create()
``` 
  
## <a name="creating-and-update-statistics-in-visual-c"></a>Visual C# での統計の作成および更新  
 このコード例では、既存のデータベースに新しいテーブルを作成し、<xref:Microsoft.SqlServer.Management.Smo.Statistic> オブジェクトおよび <xref:Microsoft.SqlServer.Management.Smo.StatisticColumn> オブジェクトを作成しています。  
  
```csharp  
{  
            //Connect to the local, default instance of SQL Server.  
            Server srv = new Server();  
            //Reference the AdventureWorks2012 database.   
            Database db = default(Database);  
            db = srv.Databases["AdventureWorks2012"];  
            //Reference the CreditCard table.   
  
           Table tb = db.Tables["CreditCard", "Sales"];  
            //Define a Statistic object by supplying the parent table and name   
            //arguments in the constructor.   
            Statistic stat = default(Statistic);  
            stat = new Statistic(tb, "Test_Statistics");  
            //Define a StatisticColumn object variable for the CardType column   
            //and add to the Statistic object variable.   
            StatisticColumn statcol = default(StatisticColumn);  
            statcol = new StatisticColumn(stat, "CardType");  
            stat.StatisticColumns.Add(statcol);  
            //Create the statistic counter on the instance of SQL Server.   
            stat.Create();  
        }  
```  
  
## <a name="creating-and-update-statistics-in-powershell"></a>PowerShell での統計の作成および更新  
 このコード例では、既存のデータベースに新しいテーブルを作成し、<xref:Microsoft.SqlServer.Management.Smo.Statistic> オブジェクトおよび <xref:Microsoft.SqlServer.Management.Smo.StatisticColumn> オブジェクトを作成しています。  
  
```powershell  
# Example of implementing a full text search on the default instance.  
# Set the path context to the local, default instance of SQL Server and database tables  
  
CD \sql\localhost\default\databases  
$db = get-item AdventureWorks2012  
  
CD AdventureWorks2012\tables  
  
#Get a reference to the table  
$tb = get-item Production.ProductCategory  
  
# Define a FullTextCatalog object variable by specifying the parent database and name arguments in the constructor.  
  
$ftc = New-Object -TypeName Microsoft.SqlServer.Management.SMO.FullTextCatalog -argumentlist $db, "Test_Catalog2"  
$ftc.IsDefault = $true  
  
# Create the Full Text Search catalog on the instance of SQL Server.  
$ftc.Create()  
  
# Define a FullTextIndex object variable by supplying the parent table argument in the constructor.  
$fti = New-Object -TypeName Microsoft.SqlServer.Management.SMO.FullTextIndex -argumentlist $tb  
  
#  Define a FullTextIndexColumn object variable by supplying the parent index   
#  and column name arguments in the constructor.  
  
$ftic = New-Object -TypeName Microsoft.SqlServer.Management.SMO.FullTextIndexColumn -argumentlist $fti, "Name"  
  
# Add the indexed column to the index.  
$fti.IndexedColumns.Add($ftic)  
  
# Set change tracking  
$fti.ChangeTracking = [Microsoft.SqlServer.Management.SMO.ChangeTracking]::Automatic  
  
# Specify the unique index on the table that is required by the Full Text Search index.  
$fti.UniqueIndexName = "AK_ProductCategory_Name"  
  
# Specify the catalog associated with the index.  
$fti.CatalogName = "Test_Catalog2"  
  
# Create the Full Text Search Index  
$fti.Create()  
```  
  
  
