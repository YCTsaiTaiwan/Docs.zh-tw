---
title: "ASP.NET Core 中的網頁伺服器實作"
author: tdykstra
description: "介紹適用於 ASP.NET Core 的網頁伺服器 Kestrel 和 WebListener。 提供如何選擇網頁伺服器，以及何時搭配使用網頁伺服器與反向 Proxy 伺服器的指引。"
ms.author: tdykstra
manager: wpickett
ms.date: 08/03/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: fundamentals/servers/index
ms.openlocfilehash: 807e60e61d4ce4d5755987cffe65d130c9bbbd42
ms.sourcegitcommit: 3e303620a125325bb9abd4b2d315c106fb8c47fd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2018
---
# <a name="web-server-implementations-in-aspnet-core"></a><span data-ttu-id="7ddd9-104">ASP.NET Core 中的網頁伺服器實作</span><span class="sxs-lookup"><span data-stu-id="7ddd9-104">Web server implementations in ASP.NET Core</span></span>

<span data-ttu-id="7ddd9-105">由 [Tom Dykstra](https://github.com/tdykstra)、[Steve Smith](https://ardalis.com/)、[Stephen Halter](https://twitter.com/halter73) 和 [Chris Ross](https://github.com/Tratcher) 提供</span><span class="sxs-lookup"><span data-stu-id="7ddd9-105">By [Tom Dykstra](https://github.com/tdykstra), [Steve Smith](https://ardalis.com/), [Stephen Halter](https://twitter.com/halter73), and [Chris Ross](https://github.com/Tratcher)</span></span>

<span data-ttu-id="7ddd9-106">ASP.NET Core 應用程式執行時，需使用同處理序 HTTP 伺服器實作。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-106">An ASP.NET Core application runs with an in-process HTTP server implementation.</span></span> <span data-ttu-id="7ddd9-107">伺服器實作會接聽 HTTP 要求，並以組成 `HttpContext` 的[要求功能](https://docs.microsoft.com/aspnet/core/fundamentals/request-features)集合形式，將它們呈現給應用程式。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-107">The server implementation listens for HTTP requests and surfaces them to the application as sets of [request features](https://docs.microsoft.com/aspnet/core/fundamentals/request-features) composed into an `HttpContext`.</span></span>

<span data-ttu-id="7ddd9-108">ASP.NET Core 提供兩個伺服器實作：</span><span class="sxs-lookup"><span data-stu-id="7ddd9-108">ASP.NET Core ships two server implementations:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7ddd9-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7ddd9-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

* <span data-ttu-id="7ddd9-110">[Kestrel](kestrel.md) 是以 [libuv](https://github.com/libuv/libuv) (跨平台非同步 I/O 程式庫) 為基礎的跨平台 HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-110">[Kestrel](kestrel.md) is a cross-platform HTTP server based on [libuv](https://github.com/libuv/libuv), a cross-platform asynchronous I/O library.</span></span>

* <span data-ttu-id="7ddd9-111">[HTTP.sys](httpsys.md) 是以 [Http.Sys 核心驅動程式](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)為基礎的僅限 Windows HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-111">[HTTP.sys](httpsys.md) is a Windows-only HTTP server based on the [Http.Sys kernel driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7ddd9-112">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7ddd9-112">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

* <span data-ttu-id="7ddd9-113">[Kestrel](kestrel.md) 是以 [libuv](https://github.com/libuv/libuv) (跨平台非同步 I/O 程式庫) 為基礎的跨平台 HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-113">[Kestrel](kestrel.md) is a cross-platform HTTP server based on [libuv](https://github.com/libuv/libuv), a cross-platform asynchronous I/O library.</span></span>

* <span data-ttu-id="7ddd9-114">[WebListener](weblistener.md) 是以 [Http.Sys 核心驅動程式](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)為基礎的僅限 Windows HTTP 伺服器。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-114">[WebListener](weblistener.md) is a Windows-only HTTP server based on the [Http.Sys kernel driver](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx).</span></span>

---

## <a name="kestrel"></a><span data-ttu-id="7ddd9-115">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ddd9-115">Kestrel</span></span>

<span data-ttu-id="7ddd9-116">Kestrel 是 ASP.NET Core 新專案範本中預設隨附的網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-116">Kestrel is the web server that is included by default in ASP.NET Core new-project templates.</span></span> 

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7ddd9-117">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7ddd9-117">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="7ddd9-118">您可以單獨使用 Kestrel，或與 IIS、Nginx 或 Apache 等「反向 Proxy 伺服器」搭配使用。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-118">You can use Kestrel by itself or with a *reverse proxy server*, such as IIS, Nginx, or Apache.</span></span> <span data-ttu-id="7ddd9-119">反向 Proxy 伺服器會從網際網路接收 HTTP 要求，並在進行一些初步處理後，將其轉送至 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-119">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling.</span></span>

![Kestrel 不使用反向 Proxy 伺服器直接與網際網路通訊](kestrel/_static/kestrel-to-internet2.png)

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7ddd9-122">如果 Kestrel 只公開到內部網路，則不論組態是否使用反向 Proxy 伺服器，皆可使用。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-122">Either configuration &mdash; with or without a reverse proxy server &mdash; can also be used if Kestrel is exposed only to an internal network.</span></span>

<span data-ttu-id="7ddd9-123">如需何時搭配使用 Kestrel 與反向 Proxy 的資訊，請參閱 [Kestrel 簡介](kestrel.md)。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-123">For information about when to use Kestrel with a reverse proxy, see [Introduction to Kestrel](kestrel.md).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7ddd9-124">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7ddd9-124">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="7ddd9-125">如果應用程式只接受來自內部網路的要求，您可以單獨使用 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-125">If your application accepts requests only from an internal network, you can use Kestrel by itself.</span></span>

![Kestrel 直接與內部網路通訊](kestrel/_static/kestrel-to-internal.png)

<span data-ttu-id="7ddd9-127">如果將應用程式公開到網際網路，您必須使用 IIS、Nginx 或 Apache 作為「反向 Proxy 伺服器」。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-127">If you expose your application to the Internet, you must use IIS, Nginx, or Apache as a *reverse proxy server*.</span></span> <span data-ttu-id="7ddd9-128">反向 Proxy 伺服器會從網際網路接收 HTTP 要求，並在進行一些初步處理後，將其轉送至 Kestrel，如下圖所示。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-128">A reverse proxy server receives HTTP requests from the Internet and forwards them to Kestrel after some preliminary handling, as shown in the following diagram.</span></span>

![Kestrel 透過 IIS、Nginx 或 Apache 等反向 Proxy 伺服器間接與網際網路通訊](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="7ddd9-130">使用反向 Proxy 進行邊緣部署 (公開到網際網路中的流量) 的最重要理由是安全性。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-130">The most important reason for using a reverse proxy for edge deployments (exposed to traffic from the Internet) is security.</span></span> <span data-ttu-id="7ddd9-131">Kestrel 的 1.x 版對於攻擊的防禦並不充足。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-131">The 1.x versions of Kestrel don't have a full complement of defenses against attacks.</span></span> <span data-ttu-id="7ddd9-132">這包括但不限於適當的逾時、大小限制和同時連線限制。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-132">This includes, but isn't limited to, appropriate timeouts, size limits, and concurrent connection limits.</span></span>

<span data-ttu-id="7ddd9-133">如需何時搭配使用 Kestrel 與反向 Proxy 的資訊，請參閱 [Kestrel 簡介](kestrel.md)。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-133">For information about when to use Kestrel with a reverse proxy, see [Introduction to Kestrel](kestrel.md).</span></span>

---

<span data-ttu-id="7ddd9-134">您無法在沒有 Kestrel 或[自訂伺服器實作](#custom-servers)的情況下使用 IIS、Nginx 或 Apache。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-134">You can't use IIS, Nginx, or Apache without Kestrel or a [custom server implementation](#custom-servers).</span></span> <span data-ttu-id="7ddd9-135">ASP.NET Core 是設計用來在自己的處理序中執行，使其行為可在平台之間保持一致。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-135">ASP.NET Core was designed to run in its own process so that it can behave consistently across platforms.</span></span> <span data-ttu-id="7ddd9-136">IIS、Nginx 和 Apache 會指定他們自己的啟動處理序和環境；若要直接使用它們，ASP.NET Core 必須進行調整以符合每一個的需求。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-136">IIS, Nginx, and Apache dictate their own startup process and environment; to use them directly, ASP.NET Core would have to adapt to the needs of each one.</span></span> <span data-ttu-id="7ddd9-137">使用 Kestrel 等網頁伺服器實作，可讓 ASP.NET Core 控制啟動處理序和環境。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-137">Using a web server implementation such as Kestrel gives ASP.NET Core control over the startup process and environment.</span></span> <span data-ttu-id="7ddd9-138">因此，只需要設定 IIS、Nginx 或 Apache 來代理對 Kestrel 的要求，而不必嘗試調整 ASP.NET Core 以符合這些網頁伺服器。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-138">So rather than trying to adapt ASP.NET Core to IIS, Nginx, or Apache, you just set up those web servers to proxy requests to Kestrel.</span></span> <span data-ttu-id="7ddd9-139">無論您是在哪裡部署，這種安排可讓 `Program.Main` 和`Startup` 類別基本上相同。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-139">This arrangement allows your `Program.Main` and `Startup` classes to be essentially the same no matter where you deploy.</span></span>

### <a name="iis-with-kestrel"></a><span data-ttu-id="7ddd9-140">IIS 與 Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ddd9-140">IIS with Kestrel</span></span>

<span data-ttu-id="7ddd9-141">當您使用 IIS 或 IIS Express 作為 ASP.NET Core 的反向 Proxy 時，ASP.NET Core 應用程式會在與 IIS 工作者處理序不同的處理序中執行。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-141">When you use IIS or IIS Express as a reverse proxy for ASP.NET Core, the ASP.NET Core application runs in a process separate from the IIS worker process.</span></span> <span data-ttu-id="7ddd9-142">在 IIS 處理序中，將執行一個特殊的 IIS 模組來協調反向 Proxy 關聯性。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-142">In the IIS process, a special IIS module runs to coordinate the reverse proxy relationship.</span></span>  <span data-ttu-id="7ddd9-143">這是 *ASP.NET Core Module*。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-143">This is the *ASP.NET Core Module*.</span></span> <span data-ttu-id="7ddd9-144">ASP.NET Core Module 的主要功能是要啟動 ASP.NET Core 應用程式、在其損毀時進行重新啟動，以及將 HTTP 流量轉送到其中。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-144">The primary functions of the ASP.NET Core Module are to start the ASP.NET Core application, restart it when it crashes, and forward HTTP traffic to it.</span></span> <span data-ttu-id="7ddd9-145">如需詳細資訊，請參閱 [ASP.NET 核心模組](aspnet-core-module.md)。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-145">For more information, see [ASP.NET Core Module](aspnet-core-module.md).</span></span> 

### <a name="nginx-with-kestrel"></a><span data-ttu-id="7ddd9-146">Nginx 與 Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ddd9-146">Nginx with Kestrel</span></span>

<span data-ttu-id="7ddd9-147">如需如何在 Linux 上使用 Nginx 作為 Kestrel 之反向 Proxy 伺服器的資訊，請參閱 [Linux 上使用 Nginx 的主機](xref:host-and-deploy/linux-nginx)。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-147">For information about how to use Nginx on Linux as a reverse proxy server for Kestrel, see [Host on Linux with Nginx](xref:host-and-deploy/linux-nginx).</span></span>

### <a name="apache-with-kestrel"></a><span data-ttu-id="7ddd9-148">Apache 與 Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ddd9-148">Apache with Kestrel</span></span>

<span data-ttu-id="7ddd9-149">如需如何在 Linux 上使用 Apache 作為 Kestrel 之反向 Proxy 伺服器的資訊，請參閱 [Linux 上使用 Apache 的主機](xref:host-and-deploy/linux-apache)。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-149">For information about how to use Apache on Linux as a reverse proxy server for Kestrel, see [Host on Linux with Apache](xref:host-and-deploy/linux-apache).</span></span>

## <a name="httpsys"></a><span data-ttu-id="7ddd9-150">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7ddd9-150">HTTP.sys</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7ddd9-151">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7ddd9-151">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="7ddd9-152">如果您在 Windows 上執行 ASP.NET Core 應用程式，則 HTTP.sys 是 Kestrel 的替代方案。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-152">If you run your ASP.NET Core app on Windows, HTTP.sys is an alternative to Kestrel.</span></span> <span data-ttu-id="7ddd9-153">針對將應用程式公開到網際網路，且需要 Kestrel 不支援之 HTTP.sys 功能的情況，您可以使用 HTTP.sys。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-153">You can use HTTP.sys for scenarios where you expose your app to the Internet and you need HTTP.sys features that Kestrel doesn't support.</span></span> 

![HTTP.sys 直接與網際網路通訊](httpsys/_static/httpsys-to-internet.png)

<span data-ttu-id="7ddd9-155">HTTP.sys 也可用於只公開到內部網路的應用程式。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-155">HTTP.sys can also be used for applications that are exposed only to an internal network.</span></span> 

![HTTP.sys 直接與內部網路通訊](httpsys/_static/httpsys-to-internal.png)

<span data-ttu-id="7ddd9-157">對於內部網路案例，通常建議使用 Kestrel 以達到最佳效能；但在某些情況下，您可以使用只有 HTTP.sys 才提供的功能。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-157">For internal network scenarios, Kestrel is generally recommended for best performance; but in some scenarios, you might want to use a feature that only HTTP.sys offers.</span></span> <span data-ttu-id="7ddd9-158">如需 HTTP.sys 功能的資訊，請參閱 [HTTP.sys](httpsys.md)。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-158">For information about HTTP.sys features, see [HTTP.sys](httpsys.md).</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7ddd9-159">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7ddd9-159">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="7ddd9-160">HTTP.sys 在 ASP.NET Core 1.x 中名為 WebListener。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-160">HTTP.sys is named WebListener in ASP.NET Core 1.x.</span></span> <span data-ttu-id="7ddd9-161">如果在 Windows 上執行 ASP.NET Core 應用程式，對於您要將應用程式公開到網際網路，但卻無法使用 IIS 的情況，WebListener 是您可以使用的替代方案。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-161">If you run your ASP.NET Core app on Windows, WebListener is an alternative that you can use for scenarios where you want to expose your app to the Internet but you can't use IIS.</span></span>

![Weblistener 直接與網際網路通訊](weblistener/_static/weblistener-to-internet.png)

<span data-ttu-id="7ddd9-163">如果您需要 Kestrel 不支援的 WebListener 功能，對於只公開到內部網路的應用程式，WebListener 也可用來取代 Kestrel。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-163">WebListener can also be used in place of Kestrel for applications that are exposed only to an internal network, if you need WebListener features that Kestrel doesn't support.</span></span> 

![Weblistener 直接與內部網路通訊](weblistener/_static/weblistener-to-internal.png)

<span data-ttu-id="7ddd9-165">對於內部網路案例，通常建議使用 Kestrel 以達到最佳效能；但在某些情況下，您可以使用只有 WebListener 才提供的功能。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-165">For internal network scenarios, Kestrel is generally recommended for best performance, but in some scenarios you might want to use a feature that only WebListener offers.</span></span> <span data-ttu-id="7ddd9-166">如需 WebListener 功能的資訊，請參閱 [WebListener](weblistener.md)。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-166">For information about WebListener features, see [WebListener](weblistener.md).</span></span>

---

## <a name="notes-about-aspnet-core-server-infrastructure"></a><span data-ttu-id="7ddd9-167">ASP.NET Core 伺服器基礎結構的相關注意事項</span><span class="sxs-lookup"><span data-stu-id="7ddd9-167">Notes about ASP.NET Core server infrastructure</span></span>

<span data-ttu-id="7ddd9-168">`Startup` 類別 `Configure` 方法中提供的 [`IApplicationBuilder`](/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder) 會公開 [`IFeatureCollection`](/aspnet/core/api/microsoft.aspnetcore.http.features.ifeaturecollection) 類型的 `ServerFeatures` 屬性。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-168">The [`IApplicationBuilder`](/aspnet/core/api/microsoft.aspnetcore.builder.iapplicationbuilder) available in the `Startup` class `Configure` method exposes the `ServerFeatures` property of type [`IFeatureCollection`](/aspnet/core/api/microsoft.aspnetcore.http.features.ifeaturecollection).</span></span> <span data-ttu-id="7ddd9-169">Kestrel 和 WebListener 都只公開單一功能 [`IServerAddressesFeature`](/aspnet/core/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature)，但不同的伺服器實作可能會公開其他的功能。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-169">Kestrel and WebListener both expose only a single feature, [`IServerAddressesFeature`](/aspnet/core/api/microsoft.aspnetcore.hosting.server.features.iserveraddressesfeature), but different server implementations may expose additional functionality.</span></span>

<span data-ttu-id="7ddd9-170">`IServerAddressesFeature` 可用來找出伺服器實作在執行階段已繫結的連接埠。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-170">`IServerAddressesFeature` can be used to find out which port the server implementation has bound to at runtime.</span></span>

## <a name="custom-servers"></a><span data-ttu-id="7ddd9-171">自訂伺服器</span><span class="sxs-lookup"><span data-stu-id="7ddd9-171">Custom servers</span></span>

<span data-ttu-id="7ddd9-172">如果內建伺服器不符合您的需求，您可以建立自訂伺服器實作。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-172">If the built-in servers don't meet your needs, you can create a custom server implementation.</span></span> <span data-ttu-id="7ddd9-173">[Open Web Interface for .NET (OWIN) 指南](../owin.md)示範如何撰寫以 [Nowin](https://github.com/Bobris/Nowin) 為基礎的 [IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) 實作。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-173">The [Open Web Interface for .NET (OWIN) guide](../owin.md) demonstrates how to write a [Nowin](https://github.com/Bobris/Nowin)-based [IServer](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.hosting.server.iserver) implementation.</span></span> <span data-ttu-id="7ddd9-174">您可以隨意實作應用程式所需的功能介面，但至少必須支援 [IHttpRequestFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) 和 [IHttpResponseFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature)。</span><span class="sxs-lookup"><span data-stu-id="7ddd9-174">You're free to implement just the feature interfaces your application needs, though at a minimum you must support [IHttpRequestFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttprequestfeature) and [IHttpResponseFeature](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.http.features.ihttpresponsefeature).</span></span>

## <a name="next-steps"></a><span data-ttu-id="7ddd9-175">後續步驟</span><span class="sxs-lookup"><span data-stu-id="7ddd9-175">Next steps</span></span>

<span data-ttu-id="7ddd9-176">如需詳細資訊，請參閱下列資源：</span><span class="sxs-lookup"><span data-stu-id="7ddd9-176">For more information, see the following resources:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="7ddd9-177">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="7ddd9-177">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

- [<span data-ttu-id="7ddd9-178">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ddd9-178">Kestrel</span></span>](kestrel.md)
- [<span data-ttu-id="7ddd9-179">Kestrel 與 IIS</span><span class="sxs-lookup"><span data-stu-id="7ddd9-179">Kestrel with IIS</span></span>](aspnet-core-module.md)
- [<span data-ttu-id="7ddd9-180">Linux 上使用 Nginx 的主機</span><span class="sxs-lookup"><span data-stu-id="7ddd9-180">Host on Linux with Nginx</span></span>](xref:host-and-deploy/linux-nginx)
- [<span data-ttu-id="7ddd9-181">Linux 上使用 Apache 的主機</span><span class="sxs-lookup"><span data-stu-id="7ddd9-181">Host on Linux with Apache</span></span>](xref:host-and-deploy/linux-apache)
- [<span data-ttu-id="7ddd9-182">HTTP.sys</span><span class="sxs-lookup"><span data-stu-id="7ddd9-182">HTTP.sys</span></span>](httpsys.md)

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="7ddd9-183">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="7ddd9-183">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

- [<span data-ttu-id="7ddd9-184">Kestrel</span><span class="sxs-lookup"><span data-stu-id="7ddd9-184">Kestrel</span></span>](kestrel.md)
- [<span data-ttu-id="7ddd9-185">Kestrel 與 IIS</span><span class="sxs-lookup"><span data-stu-id="7ddd9-185">Kestrel with IIS</span></span>](aspnet-core-module.md)
- [<span data-ttu-id="7ddd9-186">Linux 上使用 Nginx 的主機</span><span class="sxs-lookup"><span data-stu-id="7ddd9-186">Host on Linux with Nginx</span></span>](xref:host-and-deploy/linux-nginx)
- [<span data-ttu-id="7ddd9-187">Linux 上使用 Apache 的主機</span><span class="sxs-lookup"><span data-stu-id="7ddd9-187">Host on Linux with Apache</span></span>](xref:host-and-deploy/linux-apache)
- [<span data-ttu-id="7ddd9-188">WebListener</span><span class="sxs-lookup"><span data-stu-id="7ddd9-188">WebListener</span></span>](weblistener.md)

---