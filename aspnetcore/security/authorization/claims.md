---
title: Autorização baseada em declarações no ASP.NET Core
author: rick-anderson
description: Saiba como adicionar verificações de declarações para autorização em um aplicativo ASP.NET Core.
ms.author: riande
ms.date: 10/14/2016
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/claims
ms.openlocfilehash: 639cacbab2688adfe769ef2c6954ea877cf0c66a
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022349"
---
# <a name="claims-based-authorization-in-aspnet-core"></a><span data-ttu-id="be7c5-103">Autorização baseada em declarações no ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="be7c5-103">Claims-based authorization in ASP.NET Core</span></span>

<a name="security-authorization-claims-based"></a>

<span data-ttu-id="be7c5-104">Quando uma identidade é criada, ela pode ser atribuída a uma ou mais declarações emitidas por uma parte confiável.</span><span class="sxs-lookup"><span data-stu-id="be7c5-104">When an identity is created it may be assigned one or more claims issued by a trusted party.</span></span> <span data-ttu-id="be7c5-105">Uma declaração é um par de valor de nome que representa qual é o assunto, não o que o assunto pode fazer.</span><span class="sxs-lookup"><span data-stu-id="be7c5-105">A claim is a name value pair that represents what the subject is, not what the subject can do.</span></span> <span data-ttu-id="be7c5-106">Por exemplo, você pode ter uma licença de driver, emitida por uma autoridade de licença de condução local.</span><span class="sxs-lookup"><span data-stu-id="be7c5-106">For example, you may have a driver's license, issued by a local driving license authority.</span></span> <span data-ttu-id="be7c5-107">A licença do seu driver tem sua data de nascimento.</span><span class="sxs-lookup"><span data-stu-id="be7c5-107">Your driver's license has your date of birth on it.</span></span> <span data-ttu-id="be7c5-108">Nesse caso, o nome da declaração seria `DateOfBirth` , o valor da declaração seria a sua data de nascimento, por exemplo, `8th June 1970` e o emissor seria a autoridade de certificação de condução.</span><span class="sxs-lookup"><span data-stu-id="be7c5-108">In this case the claim name would be `DateOfBirth`, the claim value would be your date of birth, for example `8th June 1970` and the issuer would be the driving license authority.</span></span> <span data-ttu-id="be7c5-109">A autorização baseada em declarações, em sua mais simples, verifica o valor de uma declaração e permite o acesso a um recurso com base no valor.</span><span class="sxs-lookup"><span data-stu-id="be7c5-109">Claims based authorization, at its simplest, checks the value of a claim and allows access to a resource based upon that value.</span></span> <span data-ttu-id="be7c5-110">Por exemplo, se você quiser acesso a um clube noturno, o processo de autorização poderá ser:</span><span class="sxs-lookup"><span data-stu-id="be7c5-110">For example if you want access to a night club the authorization process might be:</span></span>

<span data-ttu-id="be7c5-111">O diretor de segurança de porta avaliaria o valor de sua declaração de data de nascimento e se confiará no emissor (a autoridade de licença de condução) antes de conceder acesso.</span><span class="sxs-lookup"><span data-stu-id="be7c5-111">The door security officer would evaluate the value of your date of birth claim and whether they trust the issuer (the driving license authority) before granting you access.</span></span>

<span data-ttu-id="be7c5-112">Uma identidade pode conter várias declarações com vários valores e pode conter várias declarações do mesmo tipo.</span><span class="sxs-lookup"><span data-stu-id="be7c5-112">An identity can contain multiple claims with multiple values and can contain multiple claims of the same type.</span></span>

## <a name="adding-claims-checks"></a><span data-ttu-id="be7c5-113">Adicionando verificações de declarações</span><span class="sxs-lookup"><span data-stu-id="be7c5-113">Adding claims checks</span></span>

<span data-ttu-id="be7c5-114">As verificações de autorização baseadas em declarações são declarativas-o desenvolvedor as incorpora em seu código, em um controlador ou em uma ação dentro de um controlador, especificando as declarações que o usuário atual deve possuir e, opcionalmente, o valor que a declaração deve manter para acessar o recurso solicitado.</span><span class="sxs-lookup"><span data-stu-id="be7c5-114">Claim based authorization checks are declarative - the developer embeds them within their code, against a controller or an action within a controller, specifying claims which the current user must possess, and optionally the value the claim must hold to access the requested resource.</span></span> <span data-ttu-id="be7c5-115">Os requisitos de declarações são baseados em políticas, o desenvolvedor deve criar e registrar uma política que expresse os requisitos de declarações.</span><span class="sxs-lookup"><span data-stu-id="be7c5-115">Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.</span></span>

<span data-ttu-id="be7c5-116">O tipo mais simples de política de declaração procura a presença de uma declaração e não verifica o valor.</span><span class="sxs-lookup"><span data-stu-id="be7c5-116">The simplest type of claim policy looks for the presence of a claim and doesn't check the value.</span></span>

<span data-ttu-id="be7c5-117">Primeiro, você precisa criar e registrar a política.</span><span class="sxs-lookup"><span data-stu-id="be7c5-117">First you need to build and register the policy.</span></span> <span data-ttu-id="be7c5-118">Isso ocorre como parte da configuração do serviço de autorização, que normalmente faz parte do `ConfigureServices()` seu arquivo *Startup.cs* .</span><span class="sxs-lookup"><span data-stu-id="be7c5-118">This takes place as part of the Authorization service configuration, which normally takes part in `ConfigureServices()` in your *Startup.cs* file.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
    });
}
```

::: moniker-end

<span data-ttu-id="be7c5-119">Nesse caso, a `EmployeeOnly` política verifica a presença de uma `EmployeeNumber` declaração na identidade atual.</span><span class="sxs-lookup"><span data-stu-id="be7c5-119">In this case the `EmployeeOnly` policy checks for the presence of an `EmployeeNumber` claim on the current identity.</span></span>

<span data-ttu-id="be7c5-120">Em seguida, aplique a política usando a `Policy` propriedade no `AuthorizeAttribute` atributo para especificar o nome da política;</span><span class="sxs-lookup"><span data-stu-id="be7c5-120">You then apply the policy using the `Policy` property on the `AuthorizeAttribute` attribute to specify the policy name;</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public IActionResult VacationBalance()
{
    return View();
}
```

<span data-ttu-id="be7c5-121">O `AuthorizeAttribute` atributo pode ser aplicado a um controlador inteiro, nessa instância, somente as identidades correspondentes à política terão permissão de acesso a qualquer ação no controlador.</span><span class="sxs-lookup"><span data-stu-id="be7c5-121">The `AuthorizeAttribute` attribute can be applied to an entire controller, in this instance only identities matching the policy will be allowed access to any Action on the controller.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }
}
```

<span data-ttu-id="be7c5-122">Se você tiver um controlador protegido pelo `AuthorizeAttribute` atributo, mas quiser permitir o acesso anônimo a determinadas ações, aplique o `AllowAnonymousAttribute` atributo.</span><span class="sxs-lookup"><span data-stu-id="be7c5-122">If you have a controller that's protected by the `AuthorizeAttribute` attribute, but want to allow anonymous access to particular actions you apply the `AllowAnonymousAttribute` attribute.</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class VacationController : Controller
{
    public ActionResult VacationBalance()
    {
    }

    [AllowAnonymous]
    public ActionResult VacationPolicy()
    {
    }
}
```

<span data-ttu-id="be7c5-123">A maioria das declarações vem com um valor.</span><span class="sxs-lookup"><span data-stu-id="be7c5-123">Most claims come with a value.</span></span> <span data-ttu-id="be7c5-124">Você pode especificar uma lista de valores permitidos ao criar a política.</span><span class="sxs-lookup"><span data-stu-id="be7c5-124">You can specify a list of allowed values when creating the policy.</span></span> <span data-ttu-id="be7c5-125">O exemplo a seguir só terá êxito para funcionários cujo número de funcionário fosse 1, 2, 3, 4 ou 5.</span><span class="sxs-lookup"><span data-stu-id="be7c5-125">The following example would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddRazorPages();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();

    services.AddAuthorization(options =>
    {
        options.AddPolicy("Founders", policy =>
                          policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
    });
}
```

::: moniker-end
### <a name="add-a-generic-claim-check"></a><span data-ttu-id="be7c5-126">Adicionar uma verificação de declaração genérica</span><span class="sxs-lookup"><span data-stu-id="be7c5-126">Add a generic claim check</span></span>

<span data-ttu-id="be7c5-127">Se o valor da declaração não for um valor único ou uma transformação for necessária, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span><span class="sxs-lookup"><span data-stu-id="be7c5-127">If the claim value isn't a single value or a transformation is required, use [RequireAssertion](/dotnet/api/microsoft.aspnetcore.authorization.authorizationpolicybuilder.requireassertion).</span></span> <span data-ttu-id="be7c5-128">Para obter mais informações, consulte [usar um Func para atender a uma política](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span><span class="sxs-lookup"><span data-stu-id="be7c5-128">For more information, see [Use a func to fulfill a policy](xref:security/authorization/policies#use-a-func-to-fulfill-a-policy).</span></span>

## <a name="multiple-policy-evaluation"></a><span data-ttu-id="be7c5-129">Avaliação de várias políticas</span><span class="sxs-lookup"><span data-stu-id="be7c5-129">Multiple Policy Evaluation</span></span>

<span data-ttu-id="be7c5-130">Se você aplicar várias políticas a um controlador ou uma ação, todas as políticas deverão passar antes que o acesso seja concedido.</span><span class="sxs-lookup"><span data-stu-id="be7c5-130">If you apply multiple policies to a controller or action, then all policies must pass before access is granted.</span></span> <span data-ttu-id="be7c5-131">Por exemplo:</span><span class="sxs-lookup"><span data-stu-id="be7c5-131">For example:</span></span>

```csharp
[Authorize(Policy = "EmployeeOnly")]
public class SalaryController : Controller
{
    public ActionResult Payslip()
    {
    }

    [Authorize(Policy = "HumanResources")]
    public ActionResult UpdateSalary()
    {
    }
}
```

<span data-ttu-id="be7c5-132">No exemplo acima, qualquer identidade que atenda à `EmployeeOnly` política pode acessar a `Payslip` ação, pois essa política é imposta no controlador.</span><span class="sxs-lookup"><span data-stu-id="be7c5-132">In the above example any identity which fulfills the `EmployeeOnly` policy can access the `Payslip` action as that policy is enforced on the controller.</span></span> <span data-ttu-id="be7c5-133">No entanto, para chamar a `UpdateSalary` ação, a identidade deve atender *tanto* à `EmployeeOnly` política quanto à `HumanResources` política.</span><span class="sxs-lookup"><span data-stu-id="be7c5-133">However in order to call the `UpdateSalary` action the identity must fulfill *both* the `EmployeeOnly` policy and the `HumanResources` policy.</span></span>

<span data-ttu-id="be7c5-134">Se você quiser políticas mais complicadas, como a obtenção de uma declaração de data de nascimento, calcular uma idade a partir dela, verificando se a idade é 21 ou mais antiga, você precisa escrever [manipuladores de política personalizados](xref:security/authorization/policies).</span><span class="sxs-lookup"><span data-stu-id="be7c5-134">If you want more complicated policies, such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write [custom policy handlers](xref:security/authorization/policies).</span></span>
