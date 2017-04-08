# opencover4vs.ps1
Smart powershell script for VisualStudio to generate code coverage report using OpenCore and ReportGenerator (and optionally publish them with Coveralls ).

Works with Classic and Core framework (NUnit and xUnit Test projects). Windows (not Linux) only.

HOW TO START
1. Install OpenCover and ReportGenerator as NUGET packages to one of your test projects (we need them to appear in packages folder).

2. Put script to the solution's root folder.

3. Configure those variables at the start
```
$TestProjectsGlobbing = @(,'*.Test.csproj')
$mstestPath = 'C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\MSTest.exe' 
$dotnetPath = 'C:\Program Files\dotnet\dotnet.exe'
$toolsFolder = 'packages'
$NamespaceInclusiveFilters = @(,'*') 
$netcoreapp = 'netcoreapp1.1'
```

4. Run as PS script

There are some hidden assumptions about VS test projects

   a) you can catch them and only them just using PS globbing, e.g. with pattern '*.Test.csproj'
   
   b) test projects are already COMPILED and theirs binaries are in project's output folder. 
```   
   $classicProjectOutput = "bin\Debug"
   $coreProjectOutput = "bin\Debug\$netcoreapp"
```
   c) test project's code (unit test classes) use specific namespaces (e.g. MyApp.Test) that will be not included into the coverage report. If you want to include unit test's code to the coverage report set `$BuildNamespaceExclusiveFilters = $false` . For classic framework this script browse the test assembly's type's namespaces when for Core projects exclusive filter will be setuped with projects "Defaut Namespace"

   d) all test core projects (xUnit) use the same core framework (configured with `$netcoreapp` default 'netcoreapp1.1') and put them to the same path (e.g. bin\Debug\$netcoreapp)
      
   e) you should setup for ALL your Core projects full PDB format (Project Propertes\Build\Advanced). Not only for test project, but for all Core projects (opencover doesn't understand portable PDB)

KNOWN PROBLEMS

NUnit attribute `Microsoft.VisualStudio.TestTools.UnitTesting.DeploymentItemAttribute` doesn't work properly. In scenarios like

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

P.S. In case of /nointegration option MSTest.exe working folder is the MSTest.exe location ('C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\'); otherwise the working folder is MSTest.exe output folder e.g. $SolutionFolder\TestResults\mstest\roman_DESKTOP-RLA3VAF 2017-04-04 04_22_07 but in any case those folders doesn't contain `appsettings.json`. Changing OpenCover's working folder doesn't change this.

