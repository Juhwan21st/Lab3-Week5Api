# WEEK 5 LAB: Deploying a .NET 9 or .NET 10 Web API to Azure App Service Using GitHub CI/CD and Creating a C# Client | Week 5 실습: GitHub CI/CD를 사용하여 .NET 9 또는 .NET 10 Web API를 Azure App Service에 배포하고 C# 클라이언트 만들기

**Duration:** 1 to 2 hours  
**소요 시간:** 1~2시간

## Outcomes | 학습 목표

5.1 Create, test and deploy simple services  
간단한 서비스를 생성, 테스트 및 배포합니다

5.2 Create clients that consume simple services  
간단한 서비스를 사용하는 클라이언트를 생성합니다

---

## PART 1 - Prerequisites (5 minutes) | 1부 - 사전 준비사항 (5분)

Make sure the following are installed or available:  
다음 항목들이 설치되어 있거나 사용 가능한지 확인하세요:

- GitHub account  
  GitHub 계정
- Azure account (free tier is enough)  
  Azure 계정 (무료 등급으로 충분합니다)
- .NET 9 SDK or .NET 10 SDK (preview or final release depending on date)  
  .NET 9 SDK 또는 .NET 10 SDK (날짜에 따라 프리뷰 또는 최종 릴리스)
- VS Code or Visual Studio  
  VS Code 또는 Visual Studio
- Git installed  
  Git 설치

---

## PART 2 - Create a Simple .NET 9 or .NET 10 Web API (15 minutes) | 2부 - 간단한 .NET 9 또는 .NET 10 Web API 만들기 (15분)

### 1. Create a new API project | 새 API 프로젝트 만들기

In terminal:  
터미널에서:

```bash
dotnet new webapi -n Week5Api --use-program-main
```

This creates a .NET 9 or .NET 10 Web API template depending on your installed SDK.  
설치된 SDK에 따라 .NET 9 또는 .NET 10 Web API 템플릿이 생성됩니다.

### 2. Open the project | 프로젝트 열기

```bash
cd Week5Api
```

### 3. Ensure Swagger is enabled | Swagger가 활성화되어 있는지 확인

If you haven't installed swagger before or forget how to use ChatGPT to add it...  
이전에 Swagger를 설치한 적이 없거나 ChatGPT를 사용하여 추가하는 방법을 잊어버린 경우...

```bash
dotnet add package Swashbuckle.AspNetCore
```

and modify your `program.cs`:  
그리고 `program.cs`를 수정합니다:

```csharp
var builder = WebApplication.CreateBuilder(args);

// Add services
builder.Services.AddControllers();

// ...

// Swagger services
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Enable Swagger in development
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

// ...

app.UseSwagger();
```

Open `Program.cs` and confirm:  
`Program.cs`를 열고 다음을 확인하세요:

```csharp
app.UseSwagger();
app.UseSwaggerUI();
```

### 4. Add a simple endpoint | 간단한 엔드포인트 추가

Inside Controllers or a new Minimal API endpoint:  
Controllers 내부 또는 새로운 Minimal API 엔드포인트에:

```csharp
app.MapGet("/hello", () => "Hello from your Azure API running .NET 9");
```

*(If using .NET 10 SDK, update message to ".NET 10".)*  
*(.NET 10 SDK를 사용하는 경우 메시지를 ".NET 10"으로 업데이트하세요.)*

### 5. Test locally | 로컬에서 테스트

Run:  
실행:

```bash
dotnet run
```

Open:  
열기:

- `http://localhost:5000/swagger`  
  or  
  또는
- `http://localhost:8080/swagger`

Confirm that `/hello` works.  
`/hello`가 작동하는지 확인하세요.

---

## PART 3 - Push Code to GitHub (10 minutes) | 3부 - GitHub에 코드 푸시하기 (10분)

> **NOTE:** Don't forget to add a `.gitignore` for the `obj` and `bin` directories  
> **참고:** `obj` 및 `bin` 디렉토리에 대한 `.gitignore`를 추가하는 것을 잊지 마세요

### 1. Initialize Git | Git 초기화

```bash
git init
git add .
git commit -m "Initial commit of Week 5 API"
```

### 2. Create a GitHub repository | GitHub 저장소 만들기

Create an empty repository on GitHub.  
GitHub에서 빈 저장소를 생성하세요.

### 3. Push the project | 프로젝트 푸시

```bash
git remote add origin https://github.com/YOURNAME/Week5Api.git
git branch -M main
git push -u origin main
```

Your .NET 9 or .NET 10 API source code is now online.  
이제 .NET 9 또는 .NET 10 API 소스 코드가 온라인에 업로드되었습니다.

---

## PART 4 - Deploy to Azure App Service Using CI/CD (30 minutes) | 4부 - CI/CD를 사용하여 Azure App Service에 배포 (30분)

> **⚠️ STOP** - Some people have said they cannot launch on Azure without a credit card attached to their account (even though it's free still). If you don't have a credit card or aren't comfortable putting yours in, then find an alternative. The deployments should be similar.  
> **⚠️ 주의** - 일부 사용자는 계정에 신용카드를 연결하지 않으면 Azure에서 시작할 수 없다고 말했습니다 (무료임에도 불구하고). 신용카드가 없거나 등록하기 불편하다면 대안을 찾으세요. 배포 방식은 유사할 것입니다.

### Alternatives: | 대안:

- Render.com
- Railway.app
- Fly.io
- Google Cloud Run
- AWS Elastic Beanstalk
- Koyeb
- Deta Space

### 1. Log in to Azure Portal | Azure Portal에 로그인

https://portal.azure.com

### 2. Create a new App Service | 새 App Service 만들기

Search for **App Service** and click **Create**.  
**App Service**를 검색하고 **만들기**를 클릭하세요.

Fill out:  
다음 항목을 입력하세요:

- **Resource Group:** `Week5RG`  
  **리소스 그룹:** `Week5RG`
- **Name:** must be unique  
  **이름:** 고유해야 합니다
- **Publish:** Code  
  **게시:** 코드
- **Runtime Stack:** choose .NET 9 (LTS) or .NET 10 (Preview)  
  **런타임 스택:** .NET 9 (LTS) 또는 .NET 10 (Preview) 선택
- **OS:** Linux recommended  
  **OS:** Linux 권장
- **Region:** closest to you  
  **지역:** 가장 가까운 지역
- **Pricing:** Free F1  
  **가격 책정:** 무료 F1

Click **Review and Create**, then **Create**.  
**검토 및 만들기**를 클릭한 다음 **만들기**를 클릭하세요.

---

## PART 5 - Enable GitHub CI/CD Integration | 5부 - GitHub CI/CD 통합 활성화

### 1. Open your App Service | App Service 열기

Go to **Deployment Center**.  
**배포 센터**로 이동하세요.

### 2. Choose GitHub as source | 소스로 GitHub 선택

Azure will prompt for:  
Azure에서 다음을 요청합니다:

- GitHub account  
  GitHub 계정
- Repository  
  저장소
- Branch name (`main`)  
  브랜치 이름 (`main`)

### 3. Azure generates a GitHub Actions workflow | Azure가 GitHub Actions 워크플로우 생성

This YAML file appears in your repo at:  
이 YAML 파일이 저장소의 다음 위치에 나타납니다:

```
.github/workflows/azure-webapps.yml
```

It will contain something like:  
다음과 같은 내용이 포함됩니다:

```yaml
DOTNET_VERSION: '9.x'
```

or  
또는

```yaml
DOTNET_VERSION: '10.x'
```

### 4. Save the configuration | 구성 저장

Azure now auto deploys whenever you push new commits.  
이제 Azure는 새 커밋을 푸시할 때마다 자동으로 배포합니다.

### 5. Verify deployment | 배포 확인

In **Overview**, copy your App Service URL:  
**개요**에서 App Service URL을 복사하세요:

```
https://your-app-name.azurewebsites.net
```

Test it by opening:  
다음을 열어서 테스트하세요:

```
https://your-app-name.azurewebsites.net/swagger
```

You should see your API running in the cloud using .NET 9 or .NET 10.  
.NET 9 또는 .NET 10을 사용하여 클라우드에서 실행되는 API를 볼 수 있습니다.

---

## PART 6 - Create a C# Client That Consumes Your Live API (20 minutes) | 6부 - 실시간 API를 사용하는 C# 클라이언트 만들기 (20분)

### 1. Create a console app | 콘솔 앱 만들기

```bash
dotnet new console -n Week5Client
cd Week5Client
```

### 2. Update Program.cs | Program.cs 업데이트

```csharp
using System.Net.Http;
using System.Threading.Tasks;

var client = new HttpClient();

string url = "https://your-app-name.azurewebsites.net/hello";

Console.WriteLine("Calling API...");
string response = await client.GetStringAsync(url);

Console.WriteLine("Response:");
Console.WriteLine(response);
```

### 3. Run the client | 클라이언트 실행

```bash
dotnet run
```

**Expected result:**  
**예상 결과:**

```
Calling API...
Response:
Hello from your Azure API running .NET 9
```

or .NET 10 depending on your runtime.  
또는 런타임에 따라 .NET 10

---

## PART 7 - Make a Change and Watch the CI/CD Pipeline (Optional but recommended) | 7부 - 변경사항 적용 및 CI/CD 파이프라인 관찰 (선택사항이지만 권장)

### 1. Modify your API endpoint | API 엔드포인트 수정

Change message to:  
메시지를 다음과 같이 변경하세요:

```csharp
"Your API has been updated through CI and CD"
```

### 2. Commit and push | 커밋 및 푸시

```bash
git add .
git commit -m "Updated hello endpoint for CI test"
git push
```

### 3. Watch GitHub Actions | GitHub Actions 관찰

Go to:  
다음으로 이동하세요:

- GitHub repo  
  GitHub 저장소
- **Actions** tab  
  **Actions** 탭
- Observe the Azure deployment job running  
  Azure 배포 작업이 실행되는 것을 관찰하세요

### 4. Test updated endpoint | 업데이트된 엔드포인트 테스트

```
https://your-app-name.azurewebsites.net/hello
```

The message should now be updated automatically.  
메시지가 자동으로 업데이트되어 있어야 합니다.

---

## PART 8 - Student Deliverables | 8부 - 학생 제출물

Students must submit:  
학생은 다음을 제출해야 합니다:

1. GitHub repository URL  
   GitHub 저장소 URL
2. Azure App Service URL  
   Azure App Service URL
3. Screenshot of Swagger running on Azure  
   Azure에서 실행 중인 Swagger의 스크린샷
4. Screenshot of console client output  
   콘솔 클라이언트 출력의 스크린샷
5. One paragraph explaining how CI and CD work in Azure  
   Azure에서 CI 및 CD가 어떻게 작동하는지 설명하는 한 단락
