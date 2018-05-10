---
title: NuGet シンボル パッケージの作成方法
description: シンボルのみを含む NuGet パッケージを作成し、Visual Studio でその他の NuGet パッケージをデバッグする方法。
author: kraigb
ms.author: kraigb
manager: douge
ms.date: 09/12/2017
ms.topic: conceptual
ms.reviewer: anangaur
ms.openlocfilehash: cf8761ac4c994d864cd49a8fb31b3be626d4c0a6
ms.sourcegitcommit: a6ca160b1e7e5c58b135af4eba0e9463127a59e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="creating-symbol-packages"></a><span data-ttu-id="db130-103">シンボル パッケージを作成する</span><span class="sxs-lookup"><span data-stu-id="db130-103">Creating symbol packages</span></span>

<span data-ttu-id="db130-104">nuget.org やその他のソースのためにパッケージをビルドするだけでなく、NuGet では、関連するシンボル パッケージを作成し、それを SymbolSource リポジトリに公開することもできます。</span><span class="sxs-lookup"><span data-stu-id="db130-104">In addition to building packages for nuget.org or other sources, NuGet also supports creating associated symbol packages and publishing them to the SymbolSource repository.</span></span>

<span data-ttu-id="db130-105">公開後、パッケージ コンシューマーは Visual Studio で自分のシンボル ソースに `https://nuget.smbsrc.net` を追加できます。それにより、Visual Studio デバッガーでパッケージ コードに入ることができます。</span><span class="sxs-lookup"><span data-stu-id="db130-105">Package consumers can then add `https://nuget.smbsrc.net` to their symbol sources in Visual Studio, which allows stepping into package code in the Visual Studio debugger.</span></span> <span data-ttu-id="db130-106">このプロセスの詳細については、「[Visual Studio デバッガーでのシンボル (.pdb) ファイルとソース ファイルの指定](/visualstudio/debugger/specify-symbol-dot-pdb-and-source-files-in-the-visual-studio-debugger)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="db130-106">See [Specify symbol (.pdb) and source files in the Visual Studio debugger](/visualstudio/debugger/specify-symbol-dot-pdb-and-source-files-in-the-visual-studio-debugger) for details on that process.</span></span>

## <a name="creating-a-symbol-package"></a><span data-ttu-id="db130-107">シンボル パッケージを作成する</span><span class="sxs-lookup"><span data-stu-id="db130-107">Creating a symbol package</span></span>

<span data-ttu-id="db130-108">シンボル パッケージを作成するには、次の規則に従います。</span><span class="sxs-lookup"><span data-stu-id="db130-108">To create a symbol package, follow these conventions:</span></span>

- <span data-ttu-id="db130-109">(コードを含む) プライマリ パッケージに `{identifier}.nupkg` という名前を付け、`.pdb` ファイルを除くすべてのファイルを含めます。</span><span class="sxs-lookup"><span data-stu-id="db130-109">Name the primary package (with your code) `{identifier}.nupkg` and include all your files except `.pdb` files.</span></span>
- <span data-ttu-id="db130-110">シンボル パッケージに `{identifier}.symbols.nupkg` という名前を付け、アセンブリ DLL、`.pdb` ファイル、XMLDOC ファイル、ソース ファイルを含めます (後続のセクションを参照してください)。</span><span class="sxs-lookup"><span data-stu-id="db130-110">Name the symbol package `{identifier}.symbols.nupkg` and include your assembly DLL, `.pdb` files, XMLDOC files, source files (see the sections that follow).</span></span>

<span data-ttu-id="db130-111">いずれのパッケージも、`.nuspec` ファイルまたはプロジェクト ファイルから、`-Symbols` オプションで作成できます。</span><span class="sxs-lookup"><span data-stu-id="db130-111">You can create both packages with the `-Symbols` option, either from a `.nuspec` file or a project file:</span></span>

```cli
nuget pack MyPackage.nuspec -Symbols

nuget pack MyProject.csproj -Symbols
```

<span data-ttu-id="db130-112">`pack` には Mac OS X の場合は Mono 4.4.2 が必要であり、Linux 1 システムでは動作しないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="db130-112">Note that `pack` requires Mono 4.4.2 on Mac OS X and does not work on Linux systems.</span></span> <span data-ttu-id="db130-113">Mac の場合、`.nuspec` ファイルの Windows パス名を Unix 形式のパスに変換する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="db130-113">On a Mac, you must also convert Windows pathnames in the `.nuspec` file to Unix-style paths.</span></span>

## <a name="symbol-package-structure"></a><span data-ttu-id="db130-114">シンボル パッケージ構造</span><span class="sxs-lookup"><span data-stu-id="db130-114">Symbol package structure</span></span>

<span data-ttu-id="db130-115">シンボル パッケージは、ライブラリ パッケージの場合と同じように、複数のターゲット フレームワークを対象とすることができます。そのため、`lib` フォルダーの構造はプライマリ パッケージとまったく同じになります (`.pdb` ファイルは DLL と共に含まれます)。</span><span class="sxs-lookup"><span data-stu-id="db130-115">A symbol package can target multiple target frameworks in the same way that a library package does, so the structure of the `lib` folder should be exactly the same as the primary package, only including `.pdb` files alongside the DLL.</span></span>

<span data-ttu-id="db130-116">たとえば、NET 4.0 と Silverlight 4 を対象とするシンボル パッケージのレイアウトは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="db130-116">For example, a symbol package that targets .NET 4.0 and Silverlight 4 would have this layout:</span></span>

    \lib
        \net40
            \MyAssembly.dll
            \MyAssembly.pdb
        \sl40
            \MyAssembly.dll
            \MyAssembly.pdb

<span data-ttu-id="db130-117">ソース ファイルは、`src` という名前の別個の特別なフォルダーに置かれます。このフォルダーはソース リポジトリの相対的構造に従う必要があります。</span><span class="sxs-lookup"><span data-stu-id="db130-117">Source files are then placed in a separate special folder named `src`, which must follow the relative structure of your source repository.</span></span> <span data-ttu-id="db130-118">これは PDB に、一致する DLL のコンパイルに使用されるソース ファイルの絶対パスが含まれるためです。このパスは公開プロセス中に検出される必要があります。</span><span class="sxs-lookup"><span data-stu-id="db130-118">This is because PDBs contain absolute paths to source files used to compile the matching DLL, and they need to be found during the publishing process.</span></span> <span data-ttu-id="db130-119">基本パス (共通パス プレフィックス) は取り除くことができます。たとえば、これらのファイルからライブラリが構築されるとします。</span><span class="sxs-lookup"><span data-stu-id="db130-119">A base path (common path prefix) can be stripped out. For example, consider a library built from these files:</span></span>

    C:\Projects
        \MyProject
            \Common
                \MyClass.cs
            \Full
                \Properties
                    \AssemblyInfo.cs
                \MyAssembly.csproj (producing \lib\net40\MyAssembly.dll)
            \Silverlight
                \Properties
                    \AssemblyInfo.cs
                \MySilverlightExtensions.cs
                \MyAssembly.csproj (producing \lib\sl4\MyAssembly.dll)

<span data-ttu-id="db130-120">`lib` フォルダーを除き、シンボル パッケージにはこのレイアウトが含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="db130-120">Apart from the `lib` folder, a symbol package would need to contain this layout:</span></span>

    \src
        \Common
            \MyClass.cs
        \Full
            \Properties
                \AssemblyInfo.cs
        \Silverlight
            \Properties
                \AssemblyInfo.cs
            \MySilverlightExtensions.cs

## <a name="referring-to-files-in-the-nuspec"></a><span data-ttu-id="db130-121">nuspec でファイルを参照する</span><span class="sxs-lookup"><span data-stu-id="db130-121">Referring to files in the nuspec</span></span>

<span data-ttu-id="db130-122">シンボル パッケージは規則によって、前のセクションで説明したフォルダー構造から構築できます。あるいは、マニフェストの `files` セクションでその内容を指定することで構築できます。</span><span class="sxs-lookup"><span data-stu-id="db130-122">A symbol package can be built by conventions, from a folder structure as described in the previous section, or by specifying its contents in the `files` section of the manifest.</span></span> <span data-ttu-id="db130-123">たとえば、前のセクションのようなパッケージを構築するには、`.nuspec` ファイルで次を使用します。</span><span class="sxs-lookup"><span data-stu-id="db130-123">For example, to build the package shown in the previous section, use the following in the `.nuspec` file:</span></span>

```xml
<files>
    <file src="Full\bin\Debug\*.dll" target="lib\net40" />
    <file src="Full\bin\Debug\*.pdb" target="lib\net40" />
    <file src="Silverlight\bin\Debug\*.dll" target="lib\sl40" />
    <file src="Silverlight\bin\Debug\*.pdb" target="lib\sl40" />
    <file src="**\*.cs" target="src" />
</files>
```

## <a name="publishing-a-symbol-package"></a><span data-ttu-id="db130-124">シンボル パッケージを公開する</span><span class="sxs-lookup"><span data-stu-id="db130-124">Publishing a symbol package</span></span>

> [!Important]
> <span data-ttu-id="db130-125">nuget.org にパッケージをプッシュするには、[nuget.exe v4.1.0 以降](https://www.nuget.org/downloads)を使用する必要があります。これは必須の [NuGet プロトコル](../api/nuget-protocols.md)を実装します。</span><span class="sxs-lookup"><span data-stu-id="db130-125">To push packages to nuget.org you must use [nuget.exe v4.1.0 or above](https://www.nuget.org/downloads), which implements the required [NuGet protocols](../api/nuget-protocols.md).</span></span>

1. <span data-ttu-id="db130-126">便宜上、最初に NuGet で API キーを保存します ([パッケージの公開](../create-packages/publish-a-package.md)ページを参照してください)。この保存は nuget.org と symbolsource.org の両方に適用されます。symbolsource.org はあなたがパッケージ所有者であることを nuget.org に確認するためです。</span><span class="sxs-lookup"><span data-stu-id="db130-126">For convenience, first save your API key with NuGet (see [publish a package](../create-packages/publish-a-package.md), which will apply to both nuget.org and symbolsource.org, because symbolsource.org will check with nuget.org to verify that you are the package owner.</span></span>

    ```cli
    nuget SetApiKey Your-API-Key
    ```

2. <span data-ttu-id="db130-127">プライマリ パッケージを nuget.org に公開したら、シンボル パッケージを次のようにプッシュします。ファイル名の `.symbols` に起因し、symbolsource.org がターゲットとして自動的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="db130-127">After publishing your primary package to nuget.org, push the symbol package as follows, which will automatically use symbolsource.org as the target because of the `.symbols` in the filename:</span></span>

    ```cli
    nuget push MyPackage.symbols.nupkg
    ```

   > [!Note]
   > <span data-ttu-id="db130-128">nuget.exe 4.5.0 以上の場合、シンボル パッケージが symbolsource.org に自動的にプッシュされることはありません。次の手順で説明するように、シンボル パッケージを個別にプッシュする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="db130-128">With nuget.exe 4.5.0 or above, the symbols packages are not automatically pushed to symbolsource.org. You would need to push the symbols packages separately as explained in the next step.</span></span>

3. <span data-ttu-id="db130-129">別のシンボル リポジトリに公開するには、あるいは命名規則に従わないシンボル パッケージをプッシュするには、`-Source` オプションを使用します。</span><span class="sxs-lookup"><span data-stu-id="db130-129">To publish to a different symbol repository, or to push a symbol package that doesn't follow the naming convention, use the `-Source` option:</span></span>

    ```cli
    nuget push MyPackage.symbols.nupkg -source https://nuget.smbsrc.net/
    ```

4. <span data-ttu-id="db130-130">次を利用し、プライマリ パッケージとシンボル パッケージの両方を両方のリポジトリに同時にプッシュすることもできます。</span><span class="sxs-lookup"><span data-stu-id="db130-130">You can also push both primary and symbol packages to both repositories at the same time using the following:</span></span>

    ```cli
    nuget push MyPackage.nupkg
    ```

<span data-ttu-id="db130-131">その場合、NuGet は、nuget.org にプライマリ パッケージを公開した後、`MyPackage.symbols.nupkg` があればそれを https://nuget.smbsrc.net/ (symbolsource.org のプッシュ URL) に公開します。</span><span class="sxs-lookup"><span data-stu-id="db130-131">In this case, NuGet will publish `MyPackage.symbols.nupkg`, if present, to https://nuget.smbsrc.net/ (the push URL for symbolsource.org), after it publishes the primary package to nuget.org.</span></span>

## <a name="see-also"></a><span data-ttu-id="db130-132">参照</span><span class="sxs-lookup"><span data-stu-id="db130-132">See Also</span></span>

<span data-ttu-id="db130-133">[Moving to the new SymbolSource engine](https://tripleemcoder.com/2015/10/04/moving-to-the-new-symbolsource-engine/) (新しい SymbolSource エンジンへの移行) (symbolsource.org)</span><span class="sxs-lookup"><span data-stu-id="db130-133">[Moving to the new SymbolSource engine](https://tripleemcoder.com/2015/10/04/moving-to-the-new-symbolsource-engine/) (symbolsource.org)</span></span>