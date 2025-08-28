# <center>Custom-ESP-IDF-Installation-Tutorial</center>



Custom ESP-IDF Installation Tutorial
Overview
This tutorial provides a step-by-step guide for installing ESP-IDF with full control over the installation process. This approach enables you to:
·	Run multiple ESP-IDF versions independently for ESP32 development
·	Create custom shortcuts for common commands (e.g., flash4 instead of idf.py -p COM4 flash monitor)
·	Use PowerShell Core in VS Code terminal for all development tasks
·	Have complete control over installation directories for IDF versions and toolchains
Prerequisites
·	Windows 10/11
·	Git installed and accessible from command line
·	Administrative privileges for PowerShell execution policy changes
·	Visual Studio Code (recommended)
Step 1: Prepare Directory Structure
1.	Create two main folders: 
o	D:\IOT\IDF55 (for ESP-IDF source code)
o	D:\IOT\IDF55Tools (for toolchain)
Note: Adjust paths according to your preferred location
Step 2: Download ESP-IDF Source Code
1.	Open Windows PowerShell
2.	Navigate to the IDF folder: cd D:\IOT\IDF55
3.	
4.	Clone the specific ESP-IDF version: git clone -b v5.5 --recursive https://github.com/espressif/esp-idf.git
5.	
6.	Important: Move all contents from the created esp-idf-v5.5 subfolder directly into D:\IOT\IDF55 to ensure scripts work correctly.
Step 3: Configure PowerShell Execution Policy
Set the execution policy to allow script execution:
Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope CurrentUser

Step 4: Install ESP-IDF Toolchain
1.	Set the custom tools path (temporary for current session):
2.	$Env:IDF_TOOLS_PATH = "D:\IOT\IDF55Tools"
3.	
4.	Verify the path is set correctly:
5.	$Env:IDF_TOOLS_PATH
6.	
7.	Install the toolchain from within the IDF directory:
8.	cd D:\IOT\IDF55
9.	.\install.ps1
10.	
11.	Set up environment variables:
12.	.\export.ps1
13.	
Note: Use .\export.ps1 (with dot-slash) to execute the script in the current directory
Step 5: Install and Configure PowerShell Core
Install PowerShell Core
1.	Download PowerShell Core from the official Microsoft repository
2.	Install the latest version (PowerShell 7.x recommended)
Configure VS Code to Use PowerShell Core
1.	Open VS Code settings (File → Preferences → Settings)
2.	Click "Open Settings (JSON)" in the top-right corner
3.	Add or modify the terminal configuration: {    "terminal.integrated.defaultProfile.windows": "PowerShell",    "terminal.integrated.profiles.windows": {        "PowerShell": {            "source": "PowerShell",            "path": "C:\\Program Files\\PowerShell\\7\\pwsh.exe"        }    }}
4.	
Step 6: Create PowerShell Profile with Custom Functions
1.	Locate your PowerShell profile:
2.	$PROFILE
3.	
4.	Create or edit the profile file (use Notepad++ or VS Code):
5.	notepad $PROFILE
6.	
7.	Add the ESP-IDF initialization function:
8.	function idf55 {
9.	    $Env:IDF_TOOLS_PATH = "D:\IOT\IDF55Tools"
10.	    . D:\IOT\IDF55\export.ps1
11.	}
12.	
13.	Add useful shortcut functions:
14.	# Monitor functions
15.	function monit3 { idf.py -p COM3 monitor }
16.	function monit4 { idf.py -p COM4 monitor }
17.	function monit5 { idf.py -p COM5 monitor }
18.	function monit6 { idf.py -p COM6 monitor }
19.	
20.	# Flash and monitor functions
21.	function flash3 { idf.py -p COM3 flash monitor }
22.	function flash4 { idf.py -p COM4 flash monitor }
23.	function flash5 { idf.py -p COM5 flash monitor }
24.	function flash6 { idf.py -p COM6 flash monitor }
25.	
26.	# Erase flash functions
27.	function ef3 { idf.py -p COM3 erase-flash }
28.	function ef4 { idf.py -p COM4 erase-flash }
29.	function ef5 { idf.py -p COM5 erase-flash }
30.	function ef6 { idf.py -p COM6 erase-flash }
31.	
32.	# Utility functions
33.	function fclean { idf.py fullclean }
34.	function getsize { idf.py size-components }
35.	
36.	# Port listing function
37.	function prts {
38.	    $portList = Get-PnpDevice -Class Ports -ErrorAction SilentlyContinue
39.	    if ($portList) {
40.	        foreach($device in $portList) {
41.	            if ($device.Present) {
42.	                Write-Host $device.Name "(Manufacturer: $($device.Manufacturer))"
43.	            }
44.	        }
45.	    }
46.	}
47.	
48.	# Create new project function
49.	function cproj {
50.	    $ProjectName = Read-Host -Prompt 'Enter new project name'
51.	    idf.py create-project $ProjectName
52.	}
53.	
54.	# Target selection functions
55.	function c3 { idf.py set-target esp32c3 }
56.	function s3 { idf.py set-target esp32s3 }
57.	function esp32 { idf.py set-target esp32 }

Step 7: Configure VS Code for ESP-IDF Development
Update IntelliSense Configuration
1.	In your project folder, create or update .vscode/c_cpp_properties.json: {    "configurations": [        {            "name": "ESP-IDF",            "includePath": [                "${workspaceFolder}/**",                "D:/IOT/IDF55/components/**"            ],            "defines": [                "_DEBUG",                "UNICODE",                "_UNICODE"            ],            "cStandard": "c17",            "cppStandard": "gnu++17",            "intelliSenseMode": "gcc-x64"        }    ],    "version": 4}
2.	
Configure IntelliSense Engine
1.	Open VS Code settings
2.	Search for "C_Cpp.intelliSenseEngine"
3.	Set it to "Tag Parser" for better performance with ESP-IDF projects
Step 8: Usage Instructions
1.	Start a new development session:
o	Open VS Code
o	Open terminal (PowerShell Core should be default)
o	Run: idf521
o	You should see ESP-IDF environment activation messages
1.	Use custom shortcuts: Once your PowerShell profile is loaded, you can use simple commands instead of typing full ESP-IDF commands: 
o	Type flash3 and press Enter → Executes idf.py -p COM3 flash monitor
o	Type flash4 and press Enter → Executes idf.py -p COM4 flash monitor
o	Type monit4 and press Enter → Executes idf.py -p COM4 monitor
o	Type ef3 and press Enter → Executes idf.py -p COM3 erase-flash
o	Type prts and press Enter → Lists available COM ports
o	Type cproj and press Enter → Create a new project interactively
o	Type fclean and press Enter → Executes idf.py fullclean
o	Type c3 and press Enter → Executes idf.py set-target esp32c3
1.	Example workflow:
2.	powershell
3.	# Initialize ESP-IDF environment
4.	idf521
5.	
6.	# Check available ports
7.	prts
8.	
9.	# Flash your project to COM4 and start monitoring
10.	flash4
o	
Troubleshooting
Common Issues and Solutions
Problem: export.ps1 not recognized
·	Solution: Ensure you're in the correct directory and use .\export.ps1
Problem: Tools not found after running idf55
·	Solution: Verify IDF_TOOLS_PATH is correctly set and toolchain installation completed successfully
Problem: IntelliSense not working
·	Solution: Check include paths in c_cpp_properties.json and ensure Tag Parser is selected
Problem: PowerShell execution policy errors
·	Solution: Run Set-ExecutionPolicy -ExecutionPolicy Bypass -Scope CurrentUser
Benefits of This Setup
·	Multiple versions: Easy switching between different ESP-IDF versions
·	Portable: Self-contained installation with known paths
·	Efficient workflow: Custom shortcuts reduce typing and increase productivity
·	Professional environment: PowerShell Core provides better scripting capabilities
·	Full control: Complete visibility and control over the installation process
Adding Additional ESP-IDF Versions
To add another version (e.g., v5.3.0):
1.	Create new folders: IDF530 and IDF530Tools
2.	Repeat the installation process with the new version
3.	Add a new function to your PowerShell profile: function idf530 {    $Env:IDF_TOOLS_PATH = "D:\IOT\IDF530Tools"    . D:\IOT\IDF530\export.ps1}
4.	
This approach allows you to maintain multiple ESP-IDF versions simultaneously and switch between them as needed for different projects.


