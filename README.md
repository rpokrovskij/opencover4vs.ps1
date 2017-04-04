# opencover4vs.ps1
OpenCover+ReportGenerator smart powershell script for VisualStudio.
Works for tests based on testing classic framework dll only (not Core).

HOW TO START
1. Install OpenCover and ReportGenerator as NUGET packages to one of your test projects first (we need them to appear in packages location).

2. Put script to the solution root folder.

3. Configure those three variables at the start
$mstestLocation = 'C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\mstest.exe' 
$TestDllsPatterns = @(,'*\bin\Debug\Vse.*.Test.dll')  
$TestableCodeNamespacePatterns = @(,'*') 

4. Run as PS script

KNOWN PROBLEMS
NUinit attribute `Microsoft.VisualStudio.TestTools.UnitTesting.DeploymentItemAttribute` doesn't work properly. In scenarios like

        [TestMethod]
        [DeploymentItem("appsettings.json")]
        public void JsonConfigurationTest(){
        
        }

it doesn't move appsettings.json to the MSTest.exe execution folder.

P.S. In case of /nointegration option MSTest.exe working folder is  MSTest.exe location ('C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\Common7\IDE\'); In case of /nointegration ommited the working folder is MSTest.exe output folder e.g. $SolutionFolder\TestResults\mstest\roman_DESKTOP-RLA3VAF 2017-04-04 04_22_07 but in any case this folder will not contain `appsettings.json`
