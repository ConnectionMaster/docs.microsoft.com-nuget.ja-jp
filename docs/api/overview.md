---
title: NuGet サーバー API の概要
description: NuGet サーバー API は、パッケージのダウンロード、メタデータのフェッチ、新しいパッケージの発行などに使用できる HTTP エンドポイントのセットです。
author: joelverhagen
ms.author: jver
ms.date: 10/26/2017
ms.topic: reference
ms.reviewer: kraigb
ms.openlocfilehash: aacf56a5dc5af9abf6f60d42bc7fd530a128d0d8
ms.sourcegitcommit: b138bc1d49fbf13b63d975c581a53be4283b7ebf
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2020
ms.locfileid: "93237810"
---
# <a name="nuget-server-api"></a>NuGet サーバー API

NuGet サーバー API は、パッケージのダウンロード、メタデータのフェッチ、新しいパッケージの発行、および公式の NuGet クライアントで利用可能なその他のほとんどの操作を実行するために使用できる一連の HTTP エンドポイントです。

この API は、Visual studio、nuget.exe、および .NET CLI の NuGet クライアントによって使用され、などの NuGet 操作 [`dotnet restore`](/dotnet/core/tools/dotnet-restore?tabs=netcore2x) 、Visual STUDIO UI での検索、およびの検索を実行し [`nuget.exe push`](../reference/cli-reference/cli-ref-push.md) ます。

注 nuget.org には、他のパッケージソースによって適用されない追加の要件がある場合があります。 これらの違いについては、 [Nuget.org プロトコル](nuget-protocols.md)によって説明されています。

使用可能な nuget.exe バージョンの簡単な列挙とダウンロードについては、エンドポイントの [tools.js](tools-json.md) を参照してください。

## <a name="service-index"></a>サービス インデックス

API のエントリポイントは、既知の場所にある JSON ドキュメントです。 このドキュメントは、 **サービスインデックス** と呼ばれています。 Nuget.org のサービスインデックスの場所は `https://api.nuget.org/v3/index.json` です。

この JSON ドキュメントには、さまざまな機能を提供し、さまざまなユースケースを満たす *リソース* の一覧が含まれています。

API をサポートするクライアントは、それぞれのパッケージソースに接続する手段として、これらのサービスインデックスの URL の1つ以上を受け入れる必要があります。

サービスインデックスの詳細については、 [API リファレンス](service-index.md)を参照してください。

## <a name="versioning"></a>バージョン管理

API は、NuGet の HTTP プロトコルのバージョン3です。 このプロトコルは、"V3 API" と呼ばれることもあります。 これらのリファレンスドキュメントでは、このバージョンのプロトコルを単に "API" と呼びます。

サービスインデックスのスキーマバージョンは、サービスインデックスのプロパティによって示され `version` ます。 API は、バージョン文字列のメジャーバージョン番号がであることを指定し `3` ます。 サービスインデックススキーマに重大でない変更が加えられると、バージョン文字列のマイナーバージョンが増加します。

古いクライアント (nuget.exe 2.x など) は V3 API をサポートしていないため、ここに記載されていない古い V2 API のみをサポートしています。

NuGet V3 API は、V2 API の後継であるため、このような名前が付けられています。これは、公式の NuGet クライアントの2.x バージョンによって実装された OData ベースのプロトコルでした。 V3 API は、公式の NuGet クライアントの3.0 バージョンで最初にサポートされていましたが、NuGet クライアント、4.0、およびでサポートされている最新のメジャープロトコルバージョンです。 

API は最初にリリースされた後、API に対して互換性のないプロトコルの変更が行われました。

## <a name="resources-and-schema"></a>リソースとスキーマ

**サービスインデックス** には、さまざまなリソースが記述されています。 現在サポートされているリソースのセットは次のとおりです。

リソース名                                                        | 必須 | 説明
-------------------------------------------------------------------- | -------- | -----------
[カタログ](catalog-resource.md)                                       | no       | すべてのパッケージイベントの完全な記録。
[PackageBaseAddress](package-base-address-resource.md)               | yes      | パッケージコンテンツ (. nupkg) を取得します。
[PackageDetailsUriTemplate](package-details-template-resource.md)    | no       | パッケージの詳細 web ページにアクセスするための URL を作成します。
[PackagePublish](package-publish-resource.md)                        | yes      | パッケージのプッシュおよび削除 (または一覧から削除) を行います。
[RegistrationsBaseUrl](registration-base-url-resource.md)            | yes      | パッケージメタデータを取得します。
[ReportAbuseUriTemplate](report-abuse-resource.md)                   | no       | 不正使用の web ページにアクセスするための URL を作成します。
[RepositorySignatures](repository-signatures-resource.md)            | no       | リポジトリの署名に使用される証明書を取得します。
[SearchAutocompleteService](search-autocomplete-service-resource.md) | no       | パッケージ Id とバージョンを部分文字列で検出します。
[SearchQueryService](search-query-service-resource.md)               | yes      | キーワードを使用してパッケージをフィルター処理し、検索します。
[シンボル Packagepublish](symbol-package-publish-resource.md)           | no       | シンボルパッケージをプッシュします。

一般に、API リソースによって返されるすべての非バイナリデータは、JSON を使用してシリアル化されます。 サービスインデックスの各リソースによって返される応答スキーマは、そのリソースに対して個別に定義されます。 各リソースの詳細については、上記のトピックを参照してください。

今後、プロトコルが進化するにつれて、新しいプロパティが JSON 応答に追加される可能性があります。 クライアントを将来の証明として使用する場合、実装では、応答スキーマが最終的なものであり、追加のデータを含めることができないと想定しないでください。 実装で認識されないすべてのプロパティを無視する必要があります。

> [!Note]
> 変換元が実装していない場合は、 `SearchAutocompleteService` オートコンプリート動作を正常に無効にする必要があります。 `ReportAbuseUriTemplate`が実装されていない場合、公式の nuget クライアントは nuget にフォールバックします ( [Nuget/Home # 4924](https://github.com/NuGet/Home/issues/4924)によって追跡されます)。 他のクライアントは、ユーザーにレポートの不正利用の URL を表示しないように選択できます。

### <a name="undocumented-resources-on-nugetorg"></a>Nuget.org のドキュメントに記載されるリソース

Nuget.org の V3 サービスインデックスには、上に記載されていないいくつかのリソースがあります。 リソースをドキュメント化しない理由はいくつかあります。

まず、nuget.org の実装の詳細として使用されるリソースについては説明しません。は、 `SearchGalleryQueryService` このカテゴリに分類されます。 [NuGetGallery](https://github.com/NuGet/NuGetGallery) は、このリソースを使用して、データベースを使用するのではなく、一部の V2 (OData) クエリを検索インデックスに委任します。 このリソースは、スケーラビリティ上の理由から導入されたものであり、外部で使用するためのものではありません。

2つ目の方法として、公式クライアントの RTM バージョンには同梱されていないリソースについては説明しません。
`PackageDisplayMetadataUriTemplate` とは `PackageVersionDisplayMetadataUriTemplate` このカテゴリに分類されます。

Thirdly、V2 プロトコルと密接に関連付けられているリソースについては説明しません。このリソース自体は意図的に文書化されていません。 `LegacyGallery`リソースはこのカテゴリに分類されます。 このリソースにより、V3 サービスインデックスは、対応する V2 ソース URL を指すことができます。 このリソースは、をサポートして `nuget.exe list` います。

リソースがここに記載されていない場合は、依存関係を取得しないことを *強く* お勧めします。 このようなドキュメントに記載されていないリソースの動作を削除したり、変更したりすると、予期しない方法で実装が中断される可能性があります。

## <a name="timestamps"></a>タイムスタンプ

API によって返されるすべてのタイムスタンプは UTC であるか、 [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) 表現を使用して指定されます。 

## <a name="http-methods"></a>HTTP メソッド

動詞   | 使用
------ | -----------
GET    | 読み取り専用の操作を実行します。通常はデータを取得します。
HEAD   | 対応する要求の応答ヘッダーをフェッチ `GET` します。
PUT    | 存在しないリソースを作成します。存在する場合は、リソースを更新します。 一部のリソースでは、更新プログラムがサポートされない場合があります。
DELETE | リソースを削除または一覧から削除します。

## <a name="http-status-codes"></a>HTTP 状態コード

コード | 説明
---- | -----
200  | 成功し、応答の本文があります。
201  | 成功し、リソースが作成されました。
202  | 成功、要求は受け入れられましたが、一部の作業はまだ完了しておらず、非同期的に完了している可能性があります。
204  | 成功しましたが、応答本文がありません。
301  | 永続的なリダイレクト。
302  | 一時的なリダイレクト。
400  | URL または要求本文内のパラメーターが無効です。
401  | 指定された資格情報が無効です。
403  | 指定された資格情報を指定した場合、アクションは許可されません。
404  | 要求されたリソースは存在しません。
409  | 要求が既存のリソースと競合しています。
500  | サービスで予期しないエラーが発生しました。
503  | サービスは一時的に利用できません。

`GET`API エンドポイントに対して行われた要求は、HTTP リダイレクト (301 または 302) を返す場合があります。 クライアントは、ヘッダーを観察 `Location` し、後続のを発行することで、このようなリダイレクトを適切に処理する必要があり `GET` ます。 特定のエンドポイントに関するドキュメントでは、リダイレクトが使用される場所を明示的に呼び出すことはありません。

500レベルのステータスコードの場合、クライアントは適切な再試行メカニズムを実装できます。 正式な NuGet クライアントは、500レベルの状態コードまたは TCP/DNS エラーが発生すると、3回再試行します。

## <a name="http-request-headers"></a>HTTP 要求ヘッダー

Name                     | 説明
------------------------ | -----------
X-NuGet-ApiKey           | プッシュと削除には必須です。「 [ `PackagePublish` リソース](package-publish-resource.md)」を参照してください。
X-NuGet-クライアント-バージョン   | **非推奨** と置き換え `X-NuGet-Protocol-Version`
NuGet-プロトコル-バージョン | Nuget.org のみで必要な場合は、「 [nuget.org プロトコル](NuGet-Protocols.md)」を参照してください。
NuGet-セッション Id       | *オプション* 。 NuGet クライアント v1.0 + 同じ NuGet クライアントセッションの一部である HTTP 要求を識別します。

では、の `X-NuGet-Session-Id` 1 回の復元に関連するすべての操作に対して1つの値が使用さ `PackageReference` れます。 オートコンプリートや復元などの他のシナリオでは `packages.config` 、コードがどのように考慮されるかによって、複数の異なるセッション ID が存在する可能性があります。

## <a name="authentication"></a>認証

認証は、を定義するために、パッケージソースの実装に残されます。 Nuget.org では、 `PackagePublish` リソースのみが特別な API キーヘッダーによる認証を必要とします。 詳細については、「 [ `PackagePublish` リソース](package-publish-resource.md)」を参照してください。
