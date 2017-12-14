---
uid: web-forms/overview/presenting-and-managing-data/model-binding/updating-deleting-and-creating-data
title: "更新、 刪除和建立資料模型繫結與 web form |Microsoft 文件"
author: tfitzmac
description: "此教學課程系列示範與 ASP.NET Web Form 專案中使用模型繫結的基本層面。 模型繫結進行資料互動詳細直線-..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 02/27/2014
ms.topic: article
ms.assetid: 602baa94-5a4f-46eb-a717-7a9e539c1db4
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/updating-deleting-and-creating-data
msc.type: authoredcontent
ms.openlocfilehash: 18c065b44524e7738c048b5908fa50c592188064
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/10/2017
---
<a name="updating-deleting-and-creating-data-with-model-binding-and-web-forms"></a><span data-ttu-id="4fb52-104">更新、 刪除和建立資料模型繫結與 web form</span><span class="sxs-lookup"><span data-stu-id="4fb52-104">Updating, deleting, and creating data with model binding and web forms</span></span>
====================
<span data-ttu-id="4fb52-105">由[Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="4fb52-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

> <span data-ttu-id="4fb52-106">此教學課程系列示範與 ASP.NET Web Form 專案中使用模型繫結的基本層面。</span><span class="sxs-lookup"><span data-stu-id="4fb52-106">This tutorial series demonstrates basic aspects of using model binding with an ASP.NET Web Forms project.</span></span> <span data-ttu-id="4fb52-107">模型繫結進行更直覺比處理的資料來源物件 （例如 ObjectDataSource 或 SqlDataSource） 的資料互動。</span><span class="sxs-lookup"><span data-stu-id="4fb52-107">Model binding makes data interaction more straight-forward than dealing with data source objects (such as ObjectDataSource or SqlDataSource).</span></span> <span data-ttu-id="4fb52-108">此數列開頭簡介資料，並會移到更進階的概念中之後的教學課程。</span><span class="sxs-lookup"><span data-stu-id="4fb52-108">This series starts with introductory material and moves to more advanced concepts in later tutorials.</span></span>
> 
> <span data-ttu-id="4fb52-109">本教學課程會示範如何建立、 更新和刪除與模型繫結的資料。</span><span class="sxs-lookup"><span data-stu-id="4fb52-109">This tutorial shows how to create, update, and delete data with model binding.</span></span> <span data-ttu-id="4fb52-110">您會設定下列屬性：</span><span class="sxs-lookup"><span data-stu-id="4fb52-110">You will set the following properties:</span></span>
> 
> - <span data-ttu-id="4fb52-111">DeleteMethod</span><span class="sxs-lookup"><span data-stu-id="4fb52-111">DeleteMethod</span></span>
> - <span data-ttu-id="4fb52-112">InsertMethod</span><span class="sxs-lookup"><span data-stu-id="4fb52-112">InsertMethod</span></span>
> - <span data-ttu-id="4fb52-113">UpdateMethod</span><span class="sxs-lookup"><span data-stu-id="4fb52-113">UpdateMethod</span></span>
> 
> <span data-ttu-id="4fb52-114">這些屬性會接收對應的作業會處理方法的名稱。</span><span class="sxs-lookup"><span data-stu-id="4fb52-114">These properties receive the name of the method that handles the corresponding operation.</span></span> <span data-ttu-id="4fb52-115">在該方法中，您提供的邏輯與資料互動。</span><span class="sxs-lookup"><span data-stu-id="4fb52-115">Within that method, you provide the logic for interacting with the data.</span></span>
> 
> <span data-ttu-id="4fb52-116">本教學課程是在第一個建立的專案[一部分](retrieving-data.md)的數列。</span><span class="sxs-lookup"><span data-stu-id="4fb52-116">This tutorial builds on the project created in the first [part](retrieving-data.md) of the series.</span></span>
> 
> <span data-ttu-id="4fb52-117">您可以[下載](https://go.microsoft.com/fwlink/?LinkId=286116)以 C# 或 VB 完整的專案</span><span class="sxs-lookup"><span data-stu-id="4fb52-117">You can [download](https://go.microsoft.com/fwlink/?LinkId=286116) the complete project in C# or VB.</span></span> <span data-ttu-id="4fb52-118">可下載的程式碼適用於 Visual Studio 2012 或 Visual Studio 2013。</span><span class="sxs-lookup"><span data-stu-id="4fb52-118">The downloadable code works with either Visual Studio 2012 or Visual Studio 2013.</span></span> <span data-ttu-id="4fb52-119">它會使用 Visual Studio 2012 範本，這可能會比 Visual Studio 2013 範本顯示在本教學課程稍有不同。</span><span class="sxs-lookup"><span data-stu-id="4fb52-119">It uses the Visual Studio 2012 template, which is slightly different than the Visual Studio 2013 template shown in this tutorial.</span></span>


## <a name="what-youll-build"></a><span data-ttu-id="4fb52-120">您將建置</span><span class="sxs-lookup"><span data-stu-id="4fb52-120">What you'll build</span></span>

<span data-ttu-id="4fb52-121">在此教學課程中，您將會：</span><span class="sxs-lookup"><span data-stu-id="4fb52-121">In this tutorial, you'll:</span></span>

1. <span data-ttu-id="4fb52-122">加入動態資料範本</span><span class="sxs-lookup"><span data-stu-id="4fb52-122">Add dynamic data templates</span></span>
2. <span data-ttu-id="4fb52-123">透過模型繫結方法啟用更新和刪除資料</span><span class="sxs-lookup"><span data-stu-id="4fb52-123">Enable updating and deleting data through model binding methods</span></span>
3. <span data-ttu-id="4fb52-124">將資料驗證規則套用-啟用在資料庫中建立新的記錄</span><span class="sxs-lookup"><span data-stu-id="4fb52-124">Apply data validation rules - Enable creating a new record in the database</span></span>

## <a name="add-dynamic-data-templates"></a><span data-ttu-id="4fb52-125">加入動態資料範本</span><span class="sxs-lookup"><span data-stu-id="4fb52-125">Add dynamic data templates</span></span>

<span data-ttu-id="4fb52-126">若要提供最佳使用者體驗，並減少重複的程式碼，您將使用動態資料範本。</span><span class="sxs-lookup"><span data-stu-id="4fb52-126">To provide the best user experience and minimize code repetition, you will use dynamic data templates.</span></span> <span data-ttu-id="4fb52-127">您還可以安裝 NuGet 封裝，將預先建立的動態資料範本輕鬆整合到現有的站台。</span><span class="sxs-lookup"><span data-stu-id="4fb52-127">You can easily integrate pre-built dynamic data templates into your existing site by installing a NuGet package.</span></span>

<span data-ttu-id="4fb52-128">從**管理 NuGet 封裝**，安裝**DynamicDataTemplatesCS**。</span><span class="sxs-lookup"><span data-stu-id="4fb52-128">From the **Manage NuGet Packages**, install the **DynamicDataTemplatesCS**.</span></span>

![動態資料範本](updating-deleting-and-creating-data/_static/image1.png)

<span data-ttu-id="4fb52-130">請注意，您的專案現在會包含名為的資料夾**動態**。</span><span class="sxs-lookup"><span data-stu-id="4fb52-130">Notice that your project now includes a folder named **DynamicData**.</span></span> <span data-ttu-id="4fb52-131">在該資料夾中，您會發現會自動套用到動態控制項，web form 中的範本。</span><span class="sxs-lookup"><span data-stu-id="4fb52-131">In that folder, you will find the templates that are automatically applied to dynamic controls in your web forms.</span></span>

![動態資料資料夾](updating-deleting-and-creating-data/_static/image2.png)

## <a name="enable-updating-and-deleting"></a><span data-ttu-id="4fb52-133">啟用更新和刪除</span><span class="sxs-lookup"><span data-stu-id="4fb52-133">Enable updating and deleting</span></span>

<span data-ttu-id="4fb52-134">讓使用者更新和刪除資料庫中的記錄會擷取資料的程序非常類似。</span><span class="sxs-lookup"><span data-stu-id="4fb52-134">Enabling users to update and delete records in the database is very similar to the process for retrieving data.</span></span> <span data-ttu-id="4fb52-135">在**UpdateMethod**和**DeleteMethod**屬性，指定執行這些作業的方法名稱。</span><span class="sxs-lookup"><span data-stu-id="4fb52-135">In the **UpdateMethod** and **DeleteMethod** properties, you specify the names of the methods that perform those operations.</span></span> <span data-ttu-id="4fb52-136">GridView 控制項，您可以也指定自動產生編輯和刪除按鈕。</span><span class="sxs-lookup"><span data-stu-id="4fb52-136">With a GridView control, you can also specify the automatic generation of edit and delete buttons.</span></span> <span data-ttu-id="4fb52-137">下列反白顯示的程式碼顯示新增內容至 GridView 程式碼。</span><span class="sxs-lookup"><span data-stu-id="4fb52-137">The following highlighted code shows the additions to the GridView code.</span></span>

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample1.aspx?highlight=4-5)]

<span data-ttu-id="4fb52-138">在程式碼後置檔案中，加入 using 陳述式**System.Data.Entity.Infrastructure**。</span><span class="sxs-lookup"><span data-stu-id="4fb52-138">In the code-behind file, add a using statement for **System.Data.Entity.Infrastructure**.</span></span>

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample2.cs)]

<span data-ttu-id="4fb52-139">接著，新增下列更新與刪除方法。</span><span class="sxs-lookup"><span data-stu-id="4fb52-139">Then, add the following update and delete methods.</span></span>

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample3.cs)]

<span data-ttu-id="4fb52-140">**TryUpdateModel**方法相符的繫結的資料值從 web 表單套用至資料的項目。</span><span class="sxs-lookup"><span data-stu-id="4fb52-140">The **TryUpdateModel** method applies the matching data-bound values from the web form to the data item.</span></span> <span data-ttu-id="4fb52-141">根據識別碼參數的值，擷取資料的項目。</span><span class="sxs-lookup"><span data-stu-id="4fb52-141">The data item is retrieved based on the value of the id parameter.</span></span>

## <a name="enforce-validation-requirements"></a><span data-ttu-id="4fb52-142">強制執行驗證需求</span><span class="sxs-lookup"><span data-stu-id="4fb52-142">Enforce validation requirements</span></span>

<span data-ttu-id="4fb52-143">更新資料時，會自動強制執行您套用至 Student 類別中的 FirstName、 LastName 和年份屬性的驗證屬性。</span><span class="sxs-lookup"><span data-stu-id="4fb52-143">The validation attributes that you applied to the FirstName, LastName, and Year properties in the Student class are automatically enforced when updating the data.</span></span> <span data-ttu-id="4fb52-144">DynamicField 控制項加入驗證屬性為基礎的用戶端和伺服器驗證。</span><span class="sxs-lookup"><span data-stu-id="4fb52-144">The DynamicField controls add client and server validators based on the validation attributes.</span></span> <span data-ttu-id="4fb52-145">FirstName 和 LastName 屬性都需要。</span><span class="sxs-lookup"><span data-stu-id="4fb52-145">The FirstName and LastName properties are both required.</span></span> <span data-ttu-id="4fb52-146">FirstName 不能超過 20 個字元的長度，和 [姓氏] 不能超過 40 個字元。</span><span class="sxs-lookup"><span data-stu-id="4fb52-146">FirstName cannot exceed 20 characters in length, and LastName cannot exceed 40 characters.</span></span> <span data-ttu-id="4fb52-147">年份必須 AcademicYear 列舉的有效值。</span><span class="sxs-lookup"><span data-stu-id="4fb52-147">Year must be a valid value for the AcademicYear enumeration.</span></span>

<span data-ttu-id="4fb52-148">如果使用者違反了其中一項驗證需求，就不會繼續更新。</span><span class="sxs-lookup"><span data-stu-id="4fb52-148">If the user violates one of the validation requirements, the update does not proceed.</span></span> <span data-ttu-id="4fb52-149">若要查看錯誤訊息，新增 ValidationSummary 控制項上方 GridView。</span><span class="sxs-lookup"><span data-stu-id="4fb52-149">To see the error message, add a ValidationSummary control above the GridView.</span></span> <span data-ttu-id="4fb52-150">若要顯示在模型繫結的驗證錯誤，請設定**ShowModelStateErrors**屬性設定為**true**。</span><span class="sxs-lookup"><span data-stu-id="4fb52-150">To display the validation errors from model binding, set the **ShowModelStateErrors** property set to **true**.</span></span> 

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample4.aspx)]

<span data-ttu-id="4fb52-151">執行 web 應用程式中，更新和刪除任何記錄。</span><span class="sxs-lookup"><span data-stu-id="4fb52-151">Run the web application, and update and delete any of the records.</span></span>

![更新資料](updating-deleting-and-creating-data/_static/image3.png)

<span data-ttu-id="4fb52-153">請注意，當編輯模式中年度屬性的值會自動轉譯為下拉式清單。</span><span class="sxs-lookup"><span data-stu-id="4fb52-153">Notice that when in the edit mode the value for the Year property is automatically rendered as a drop down list.</span></span> <span data-ttu-id="4fb52-154">[年] 屬性是列舉值，並列舉值的動態資料範本指定的下拉式清單進行編輯。</span><span class="sxs-lookup"><span data-stu-id="4fb52-154">The Year property is an enumeration value, and the dynamic data template for an enumeration value specifies a drop down list for editing.</span></span> <span data-ttu-id="4fb52-155">您可以找到該範本開啟**列舉\_Edit.ascx**檔案**動態**/**FieldTemplates**資料夾。</span><span class="sxs-lookup"><span data-stu-id="4fb52-155">You can find that template by opening the **Enumeration\_Edit.ascx** file in the **DynamicData**/**FieldTemplates** folder.</span></span>

<span data-ttu-id="4fb52-156">如果您提供有效的值，更新就會順利完成。</span><span class="sxs-lookup"><span data-stu-id="4fb52-156">If you provide valid values, the update completes successfully.</span></span> <span data-ttu-id="4fb52-157">如果您違反其中一項驗證需求，不會繼續更新和方格上方顯示錯誤訊息。</span><span class="sxs-lookup"><span data-stu-id="4fb52-157">If you violate one of the validation requirements, the update does not proceed and an error message is displayed above the grid.</span></span>

![錯誤訊息](updating-deleting-and-creating-data/_static/image4.png)

## <a name="add-new-records"></a><span data-ttu-id="4fb52-159">新增新的記錄</span><span class="sxs-lookup"><span data-stu-id="4fb52-159">Add new records</span></span>

<span data-ttu-id="4fb52-160">GridView 控制項不包含**InsertMethod**屬性，因此無法用來加入新的記錄與模型繫結。</span><span class="sxs-lookup"><span data-stu-id="4fb52-160">The GridView control does not include the **InsertMethod** property and therefore cannot be used for adding a new record with model binding.</span></span> <span data-ttu-id="4fb52-161">您可以找到 InsertMethod 屬性**FormView**， **DetailsView**，或**ListView**控制項。</span><span class="sxs-lookup"><span data-stu-id="4fb52-161">You can find the InsertMethod property in the **FormView**, **DetailsView**, or **ListView** controls.</span></span> <span data-ttu-id="4fb52-162">在本教學課程中，您將使用 FormView 控制項來加入新的記錄。</span><span class="sxs-lookup"><span data-stu-id="4fb52-162">In this tutorial, you will use a FormView control to add a new record.</span></span>

<span data-ttu-id="4fb52-163">首先，加入新的頁面，您將加入新的記錄建立的連結。</span><span class="sxs-lookup"><span data-stu-id="4fb52-163">First, add a link to the new page you will create for adding a new record.</span></span> <span data-ttu-id="4fb52-164">Aggregate 上方加入：</span><span class="sxs-lookup"><span data-stu-id="4fb52-164">Above the ValidationSummary, add:</span></span>

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample5.aspx)]

<span data-ttu-id="4fb52-165">新的連結會出現在頂端的學生頁面的內容。</span><span class="sxs-lookup"><span data-stu-id="4fb52-165">The new link will appear at the top of the content for the Students page.</span></span>

![新的連結](updating-deleting-and-creating-data/_static/image5.png)

<span data-ttu-id="4fb52-167">然後，加入新的 web 表單使用主版頁面中，並將其命名**AddStudent**。</span><span class="sxs-lookup"><span data-stu-id="4fb52-167">Then, add a new web form using a master page, and name it **AddStudent**.</span></span> <span data-ttu-id="4fb52-168">選取 Site.Master 做為主版頁面。</span><span class="sxs-lookup"><span data-stu-id="4fb52-168">Select Site.Master as the master page.</span></span>

<span data-ttu-id="4fb52-169">您將轉譯的欄位加入使用新的學生**DynamicEntity**控制項。</span><span class="sxs-lookup"><span data-stu-id="4fb52-169">You will render the fields for adding a new student by using a **DynamicEntity** control.</span></span> <span data-ttu-id="4fb52-170">Dynamicentity 呈現 ItemType 屬性中指定的類別中的可編輯的屬性。</span><span class="sxs-lookup"><span data-stu-id="4fb52-170">The DynamicEntity control renders that editable properties in the class specified in the ItemType property.</span></span> <span data-ttu-id="4fb52-171">StudentID 屬性被標記為**[ScaffoldColumn(false)]**屬性，讓它不會轉譯。</span><span class="sxs-lookup"><span data-stu-id="4fb52-171">The StudentID property was marked with the **[ScaffoldColumn(false)]** attribute so it is not rendered.</span></span> <span data-ttu-id="4fb52-172">在 AddStudent 頁面 MainContent 預留位置，加入下列程式碼。</span><span class="sxs-lookup"><span data-stu-id="4fb52-172">In the MainContent placeholder of the AddStudent page, add the following code.</span></span>

[!code-aspx[Main](updating-deleting-and-creating-data/samples/sample6.aspx)]

<span data-ttu-id="4fb52-173">在程式碼後置檔案 (AddStudent.aspx.cs) 中，加入**使用**陳述式**ContosoUniversityModelBinding.Models**命名空間。</span><span class="sxs-lookup"><span data-stu-id="4fb52-173">In the code-behind file (AddStudent.aspx.cs), add a **using** statement for the **ContosoUniversityModelBinding.Models** namespace.</span></span>

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample7.cs)]

<span data-ttu-id="4fb52-174">然後，加入下列方法來指定如何插入新的記錄 和 取消 5d; 按鈕的事件處理常式。</span><span class="sxs-lookup"><span data-stu-id="4fb52-174">Then, add the following methods to specify how to insert a new record and an event handler for the cancel button.</span></span>

[!code-csharp[Main](updating-deleting-and-creating-data/samples/sample8.cs)]

<span data-ttu-id="4fb52-175">將儲存的所有變更。</span><span class="sxs-lookup"><span data-stu-id="4fb52-175">Save all of the changes.</span></span>

<span data-ttu-id="4fb52-176">執行 web 應用程式並建立新的學生。</span><span class="sxs-lookup"><span data-stu-id="4fb52-176">Run the web application and create a new student.</span></span>

![加入新的學生](updating-deleting-and-creating-data/_static/image6.png)

<span data-ttu-id="4fb52-178">按一下**插入**，並注意已建立新的學生。</span><span class="sxs-lookup"><span data-stu-id="4fb52-178">Click **Insert** and notice the new student has been created.</span></span>

![顯示新的學生](updating-deleting-and-creating-data/_static/image7.png)

## <a name="conclusion"></a><span data-ttu-id="4fb52-180">結論</span><span class="sxs-lookup"><span data-stu-id="4fb52-180">Conclusion</span></span>

<span data-ttu-id="4fb52-181">在本教學課程中，您可以啟用更新、 刪除和建立的資料。</span><span class="sxs-lookup"><span data-stu-id="4fb52-181">In this tutorial, you enabled updating, deleting, and creating data.</span></span> <span data-ttu-id="4fb52-182">您可以確保與資料互動時，會套用驗證規則。</span><span class="sxs-lookup"><span data-stu-id="4fb52-182">You ensured validation rules are applied when interacting with the data.</span></span>

<span data-ttu-id="4fb52-183">在接下來[教學課程](sorting-paging-and-filtering-data.md)在此數列，您將啟用排序、 分頁和篩選資料。</span><span class="sxs-lookup"><span data-stu-id="4fb52-183">In the next [tutorial](sorting-paging-and-filtering-data.md) in this series, you will enable sorting, paging, and filtering data.</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="4fb52-184">[上一頁](retrieving-data.md)
[下一頁](sorting-paging-and-filtering-data.md)</span><span class="sxs-lookup"><span data-stu-id="4fb52-184">[Previous](retrieving-data.md)
[Next](sorting-paging-and-filtering-data.md)</span></span>