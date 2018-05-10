---
title: NuGet.Server を使用して NuGet フィードをホストする
description: HTTP および OData 経由でパッケージを利用できるようにして、NuGet.Server を使用し、IIS を実行している任意のサーバー上に NuGet パッケージ フィードを作成およびホストする方法です。
author: kraigb
ms.author: kraigb
manager: douge
ms.date: 03/13/2018
ms.topic: conceptual
ms.openlocfilehash: 55501eedf6c5fdf054a602536ee8c03e7048d5d5
ms.sourcegitcommit: 3eab9c4dd41ea7ccd2c28bb5ab16f6fbbec13708
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2018
---
# <a name="nugetserver"></a><span data-ttu-id="eb884-103">NuGet.Server</span><span class="sxs-lookup"><span data-stu-id="eb884-103">NuGet.Server</span></span>

<span data-ttu-id="eb884-104">NuGet.Server は、.NET Foundation によって提供されるパッケージです。このパッケージでは、IIS を実行する任意のサーバー上でパッケージ フィードをホストできる、ASP.NET アプリケーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="eb884-104">NuGet.Server is a package provided by the .NET Foundation that creates an ASP.NET application that can host a package feed on any server that runs IIS.</span></span> <span data-ttu-id="eb884-105">単純に言うと、NuGet.Server では HTTP (具体的には OData) 経由で利用できるサーバーにフォルダーを作成します。</span><span class="sxs-lookup"><span data-stu-id="eb884-105">Simply said, NuGet.Server makes a folder on the server available through HTTP(S) (specifically OData).</span></span> <span data-ttu-id="eb884-106">設定は簡単で、単純なシナリオに最適です。</span><span class="sxs-lookup"><span data-stu-id="eb884-106">It's easy to set up and is best for simple scenarios.</span></span>

1. <span data-ttu-id="eb884-107">Visual Studio で空の ASP.NET Web アプリケーションを作成して、NuGet.Server パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="eb884-107">Create an empty ASP.NET Web application in Visual Studio and add the NuGet.Server package to it.</span></span>
1. <span data-ttu-id="eb884-108">アプリケーションで `Packages` フォルダーを設定し、パッケージを追加します。</span><span class="sxs-lookup"><span data-stu-id="eb884-108">Configure the `Packages` folder in the application and add packages.</span></span>
1. <span data-ttu-id="eb884-109">アプリケーションを適切なサーバーに展開します。</span><span class="sxs-lookup"><span data-stu-id="eb884-109">Deploy the application to a suitable server.</span></span>

<span data-ttu-id="eb884-110">次のセクションでは、C# を使用して、このプロセスを詳しく確認します。</span><span class="sxs-lookup"><span data-stu-id="eb884-110">The following sections walk through this process in detail, using C#.</span></span>

<span data-ttu-id="eb884-111">NuGet.Server についてさらに質問がある場合は、[https://github.com/nuget/NuGetGallery/issues](https://github.com/nuget/NuGetGallery/issues) で問題を作成してください。</span><span class="sxs-lookup"><span data-stu-id="eb884-111">If you have further questions about NuGet.Server, create an issue on [https://github.com/nuget/NuGetGallery/issues](https://github.com/nuget/NuGetGallery/issues).</span></span>

## <a name="create-and-deploy-an-aspnet-web-application-with-nugetserver"></a><span data-ttu-id="eb884-112">NuGet.Server を使用して ASP.NET Web アプリケーションを作成して配置する</span><span class="sxs-lookup"><span data-stu-id="eb884-112">Create and deploy an ASP.NET Web application with NuGet.Server</span></span>

1. <span data-ttu-id="eb884-113">Visual Studio で、**[ファイル] > [新規] > [プロジェクト]** を選択し、「ASP.NET」を検索し、C# 用に **[ASP.NET Web アプリケーション (.NET Framework)]** テンプレートを選択し、**[フレームワーク]** を [.NET Framework 4.6] に設定します。</span><span class="sxs-lookup"><span data-stu-id="eb884-113">In Visual Studio, select **File > New > Project**, search for "ASP.NET", select the **ASP.NET Web Application (.NET Framework)** template for C#, and set **Framework** to ".NET Framework 4.6":</span></span>

    ![新しいプロジェクトのターゲット フレームワークを設定する](media/Hosting_01-NuGet.Server-Set4.6.png)

1. <span data-ttu-id="eb884-115">アプリケーションに NuGet.Server *以外*の適切な名前を付けて [OK] を選択し、次のダイアログで**空**のテンプレートを選択して **[OK]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="eb884-115">Give the application a suitable name *other* than NuGet.Server, select OK, and in the next dialog select the **Empty** template, then select **OK**.</span></span>

1. <span data-ttu-id="eb884-116">プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="eb884-116">Right-click the project, select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="eb884-117">.NET Framework 4.6 を対象にしている場合は、パッケージ マネージャー UI で **[参照]** タブを選択し、最新バージョンの NuGet.Server パッケージを検索してインストールします</span><span class="sxs-lookup"><span data-stu-id="eb884-117">In the Package Manager UI, select the **Browse** tab, then search and install the latest version of the NuGet.Server package if you're targeting .NET Framework 4.6.</span></span> <span data-ttu-id="eb884-118">(また、`Install-Package NuGet.Server` を使用して、パッケージ マネージャー コンソールからインストールすることもできます。)メッセージに従って、ライセンス条項に同意します。</span><span class="sxs-lookup"><span data-stu-id="eb884-118">(You can also install it from the Package Manager Console with `Install-Package NuGet.Server`.) Accept the license terms if prompted.</span></span>

    ![NuGet.Server パッケージをインストールする](media/Hosting_02-NuGet.Server-Package.png)

1. <span data-ttu-id="eb884-120">NuGet.Server をインストールすると、空の Web アプリケーションはパッケージ ソースに変換されます。</span><span class="sxs-lookup"><span data-stu-id="eb884-120">Installing NuGet.Server converts the empty Web application into a package source.</span></span> <span data-ttu-id="eb884-121">この操作を行うと、他の多様なパッケージがインストールされ、アプリケーション内に `Packages` フォルダーが作成され、追加の設定を含めるために `web.config` が変更されます (詳細については、ファイルのコメントを参照してください)。</span><span class="sxs-lookup"><span data-stu-id="eb884-121">It installs a variety of other packages, creates a `Packages` folder in the application, and modifies `web.config` to include additional settings (see the comments in that file for details).</span></span>

    > [!Important]
    > <span data-ttu-id="eb884-122">NuGet.Server パッケージでファイルの変更が完了した後は、`web.config` をよく確認してください。</span><span class="sxs-lookup"><span data-stu-id="eb884-122">Carefully inspect `web.config` after the NuGet.Server package has completed its modifications to that file.</span></span> <span data-ttu-id="eb884-123">NuGet.Server で既存の要素を上書きすることはできません。代わりに重複する要素が作成されます。</span><span class="sxs-lookup"><span data-stu-id="eb884-123">NuGet.Server may not overwrite existing elements but instead create duplicate elements.</span></span> <span data-ttu-id="eb884-124">このような重複があると、後でプロジェクトを実行しようとしたときに "内部サーバー エラー" が発生します。</span><span class="sxs-lookup"><span data-stu-id="eb884-124">Those duplicates will cause an "Internal Server Error" when you later try to run the project.</span></span> <span data-ttu-id="eb884-125">たとえば、NuGet.Server をインストールする前に `web.config` に `<compilation debug="true" targetFramework="4.5.2" />` を含めると、パッケージによって上書きされませんが 2 つ目の `<compilation debug="true" targetFramework="4.6" />` が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="eb884-125">For example, if your `web.config` contains `<compilation debug="true" targetFramework="4.5.2" />` before installing NuGet.Server, the package doesn't overwrite it but inserts a second `<compilation debug="true" targetFramework="4.6" />`.</span></span> <span data-ttu-id="eb884-126">この場合は、以前の Framework バージョンの要素を削除してください。</span><span class="sxs-lookup"><span data-stu-id="eb884-126">In that case, delete the element with the older framework version.</span></span>

1. <span data-ttu-id="eb884-127">アプリケーションをサーバーに公開するときに、パッケージをフィードで使用できるようにするには、Visual Studio の `Packages` フォルダーに各 `.nupkg` ファイルを追加して、それぞれの **[ビルド アクション]** を **[コンテンツ]** に、**[出力ディレクトリにコピー]** を **[常にコピーする]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="eb884-127">To make packages available in the feed when you publish the application to a server, add each `.nupkg` files to the `Packages` folder in Visual Studio, then set each one's **Build Action** to **Content** and **Copy to Output Directory** to **Copy always**:</span></span>

    ![プロジェクト内の [Packages] フォルダーにパッケージをコピーする](media/Hosting_03-NuGet.Server-Package-Folder.png)

1. <span data-ttu-id="eb884-129">Visual Studio のローカルでサイトを実行します (**[デバッグ] > [デバッグなしで開始]** を使用するか Ctrl キーを押しながら F5 キーを押します)。</span><span class="sxs-lookup"><span data-stu-id="eb884-129">Run the site locally in Visual Studio (using **Debug > Start Without Debugging** or Ctrl+F5).</span></span> <span data-ttu-id="eb884-130">ホーム ページには、パッケージ フィードの URL が次のように表示されます。</span><span class="sxs-lookup"><span data-stu-id="eb884-130">The home page provides the package feed URLs as shown below.</span></span> <span data-ttu-id="eb884-131">エラーが表示される場合は、手順 5 で説明したような重複する要素がないか `web.config` をよく確認します。</span><span class="sxs-lookup"><span data-stu-id="eb884-131">If you see errors, carefully inspect your `web.config` for duplicate elements are noted earlier with step 5.</span></span>

    ![NuGet.Server を使用したアプリケーションの既定のホーム ページ](media/Hosting_04-NuGet.Server-FeedHomePage.png)

1. <span data-ttu-id="eb884-133">パッケージの OData フィードを表示するには、上記の囲まれた領域内にある **[ここ]** をクリックします。</span><span class="sxs-lookup"><span data-stu-id="eb884-133">Click on **here** in the area outlined above to see the OData feed of packages.</span></span>

1. <span data-ttu-id="eb884-134">初めてアプリケーションを実行すると、NuGet.Server では各パッケージにフォルダーが含まれるように、`Packages` フォルダーが再構築されます。</span><span class="sxs-lookup"><span data-stu-id="eb884-134">The first time you run the application, NuGet.Server restructures the `Packages` folder to contain a folder for each package.</span></span> <span data-ttu-id="eb884-135">これは、パフォーマンスを向上させるために、NuGet 3.3 で導入された[ローカル記憶域のレイアウト](http://blog.nuget.org/20151118/nuget-3.3.html#folder-based-repository-commands)に一致します。</span><span class="sxs-lookup"><span data-stu-id="eb884-135">This matches the [local storage layout](http://blog.nuget.org/20151118/nuget-3.3.html#folder-based-repository-commands) introduced with NuGet 3.3 to improve performance.</span></span> <span data-ttu-id="eb884-136">さらにパッケージを追加する場合、継続してこの構造に従います。</span><span class="sxs-lookup"><span data-stu-id="eb884-136">When adding more packages, continue to follow this structure.</span></span>

1. <span data-ttu-id="eb884-137">ローカルの配置をテストしたら、必要に応じてアプリケーションをその他の内部または外部のサイトに展開します。</span><span class="sxs-lookup"><span data-stu-id="eb884-137">Once you've tested your local deployment, deploy the application to any other internal or external site as needed.</span></span>

1. <span data-ttu-id="eb884-138">`http://<domain>` に展開されると、パッケージ ソースに使用する URL は `http://<domain>/nuget` になります。</span><span class="sxs-lookup"><span data-stu-id="eb884-138">Once deployed to `http://<domain>`, the URL that you use for the package source will be `http://<domain>/nuget`.</span></span>

## <a name="configuring-the-packages-folder"></a><span data-ttu-id="eb884-139">パッケージ フォルダーを構成する</span><span class="sxs-lookup"><span data-stu-id="eb884-139">Configuring the Packages folder</span></span>

<span data-ttu-id="eb884-140">`NuGet.Server` 1.5 以降を使用して、`web.config` 内の `appSetting/packagesPath` 値を使用して、パッケージ フォルダーをより具体的に構成できます。</span><span class="sxs-lookup"><span data-stu-id="eb884-140">With `NuGet.Server` 1.5 and later, you can more specifically configure the package folder using the `appSetting/packagesPath` value in `web.config`:</span></span>

```xml
<appSettings>
    <!-- Set the value here to specify your custom packages folder. -->
    <add key="packagesPath" value="C:\MyPackages" />
</appSettings>
```

<span data-ttu-id="eb884-141">`packagesPath` は絶対パスまたは仮想パスにすることができます。</span><span class="sxs-lookup"><span data-stu-id="eb884-141">`packagesPath` can be an absolute or virtual path.</span></span>

<span data-ttu-id="eb884-142">`packagesPath` が省略された場合、または空白のままの場合は、パッケージ フォルダーは既定の `~/Packages` です。</span><span class="sxs-lookup"><span data-stu-id="eb884-142">When `packagesPath` is omitted or left blank, the packages folder is the default `~/Packages`.</span></span>

## <a name="adding-packages-to-the-feed-externally"></a><span data-ttu-id="eb884-143">パッケージを外部からフィードに追加する</span><span class="sxs-lookup"><span data-stu-id="eb884-143">Adding packages to the feed externally</span></span>

<span data-ttu-id="eb884-144">`web.config` で API キーの値を設定した場合、NuGet.Server サイトが実行されているときに、[nuget push](../tools/cli-ref-push.md) を使用してパッケージを追加できます。</span><span class="sxs-lookup"><span data-stu-id="eb884-144">Once a NuGet.Server site is running, you can add packages using [nuget push](../tools/cli-ref-push.md) provided that you set an API key value in `web.config`.</span></span>

<span data-ttu-id="eb884-145">NuGet.Server パッケージをインストールした後、`web.config` に空の `appSetting/apiKey` 値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="eb884-145">After installing the NuGet.Server package, `web.config` contains an empty `appSetting/apiKey` value:</span></span>

```xml
<appSettings>
    <add key="apiKey" value="" />
</appSettings>
```

<span data-ttu-id="eb884-146">`apiKey` が省略された場合、または空白の場合は、パッケージのフィードへのプッシュは無効になります。</span><span class="sxs-lookup"><span data-stu-id="eb884-146">When `apiKey` is omitted or blank, pushing packages to the feed is disabled.</span></span>

<span data-ttu-id="eb884-147">この機能を有効にするには、`apiKey` に値 (理想的には、強力なパスワード) を設定し、`true` の値を含む `appSettings/requireApiKey` と呼ばれるキーを追加します。</span><span class="sxs-lookup"><span data-stu-id="eb884-147">To enable this capability, set the `apiKey` to a value (ideally a strong password) and add a key called `appSettings/requireApiKey` with the value of `true`:</span></span>

```xml
<appSettings>
        <!-- Sets whether an API Key is required to push/delete packages -->
    <add key="requireApiKey" value="true" />

    <!-- Set a shared password (for all users) to push/delete packages -->
    <add key="apiKey" value="" />
</appSettings>
```

<span data-ttu-id="eb884-148">サーバーが既にセキュリティで保護されているか、または、API キーが必要ない場合 (たとえば、ローカル チーム ネットワーク上でプライベート サーバーを使用している場合)、`requireApiKey` を `false` に設定できます。</span><span class="sxs-lookup"><span data-stu-id="eb884-148">If your server is already secured or you do not otherwise require an API key (for example, when using a private server on a local team network), you can set `requireApiKey` to `false`.</span></span> <span data-ttu-id="eb884-149">これで、サーバーへのアクセス権を持つすべてのユーザーは、パッケージをプッシュできるようになります。</span><span class="sxs-lookup"><span data-stu-id="eb884-149">All users with access to the server can then push packages.</span></span>

## <a name="removing-packages-from-the-feed"></a><span data-ttu-id="eb884-150">フィードからパッケージを削除する</span><span class="sxs-lookup"><span data-stu-id="eb884-150">Removing packages from the feed</span></span>

<span data-ttu-id="eb884-151">NuGet.Server で、API キーをコメントに追加して [nuget delete](../tools/cli-ref-delete.md) コマンドを実行すると、リポジトリからパッケージを削除できます。</span><span class="sxs-lookup"><span data-stu-id="eb884-151">With NuGet.Server, the [nuget delete](../tools/cli-ref-delete.md) command removes a package from the repository provided that you include the API key with the comment.</span></span>

<span data-ttu-id="eb884-152">代わりにパッケージをリストから削除する (パッケージの復元のために使用できる状態のままにする) ように動作を変更する場合は、`web.config` の `enableDelisting` キーを true に変更します。</span><span class="sxs-lookup"><span data-stu-id="eb884-152">If you want to change the behavior to delist the package instead (leaving it available for package restore), change the `enableDelisting` key in `web.config` to true.</span></span>

## <a name="nugetserver-support"></a><span data-ttu-id="eb884-153">NuGet.Server のサポート</span><span class="sxs-lookup"><span data-stu-id="eb884-153">NuGet.Server support</span></span>

<span data-ttu-id="eb884-154">NuGet.Server の使用についてその他のサポートが必要な場合は、[https://github.com/nuget/NuGetGallery/issues](https://github.com/nuget/NuGetGallery/issues) で問題を作成してください。</span><span class="sxs-lookup"><span data-stu-id="eb884-154">For additional help using NuGet.Server, create an issue on [https://github.com/nuget/NuGetGallery/issues](https://github.com/nuget/NuGetGallery/issues).</span></span>