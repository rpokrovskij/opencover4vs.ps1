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
