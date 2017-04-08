# opencover4vs.ps1
Smart powershell script for VisualStudio to generate code coverage report using OpenCore and ReportGenerator (and optionally publish them with Coveralls ).

Works with Classic and Core framework. Tested on Windows only (not Linux).

HOW TO START
1. Install OpenCover and ReportGenerator as NUGET packages to one of your test projects (we need them to appear in packages folder).

2. Put script to the solution's root folder.

3. Configure those variables at the start
$TestProjectsGlobbing = @(,'*.Test.csproj')
$mstestPath = 'C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\MSTest.exe' 
$dotnetPath = 'C:\Program Files\dotnet\dotnet.exe'
$toolsFolder = 'packages'
$NamespaceInclusiveFilters = @(,'*') 
$netcoreapp = 'netcoreapp1.1'

4. Run as PS script

KNOWN PROBLEMS

NUinit attribute `Microsoft.VisualStudio.TestTools.UnitTesting.DeploymentItemAttribute` doesn't work properly. In scenarios like

    [TestClass]
    public class StandardConfigurationUnitTest
    {
        [TestMethod]
        [DeploymentItem("appsettings.json")]
        public void JsonConfigurationTest(){
                // ...    
        }
    }

This will not move `appsettings.json` to the `MSTest.exe` execution folder, therefore it will be unaccessible for test code.

Workouround: recreate files from resources in unit test constructor:

    [TestClass]
    public class StandardConfigurationUnitTest
    {
        public StandardConfigurationUnitTest()
        {
            string fileName = "appsettings.json";
            using (var reader = new StreamReader(Assembly.GetExecutingAssembly()
                .GetManifestResourceStream(this.GetType().Namespace+"."+fileName)))
                using (var fileStream = new FileStream(fileName, FileMode.Create))
                    reader.BaseStream.CopyTo(fileStream);
        }
    }

P.S. In case of /nointegration option MSTest.exe working folder is  MSTest.exe location ('C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\'); otherwise the working folder is MSTest.exe output folder e.g. $SolutionFolder\TestResults\mstest\roman_DESKTOP-RLA3VAF 2017-04-04 04_22_07 but in any case those folders doesn't contain `appsettings.json`. Changing OpenCover's working folder doesn't change this.

