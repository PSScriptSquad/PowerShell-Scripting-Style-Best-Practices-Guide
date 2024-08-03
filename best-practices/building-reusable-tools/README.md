# Building Reusable Tools

## Decide Whether you're Coding a 'tool' or a 'controller' Script&#x20;

When creating scripts, it's essential to differentiate between a 'tool' script and a 'controller' script. Although these terms aren't universally standardized, the community generally recognizes their distinction:&#x20;

* &#x20;Tool Scripts: These are meant to be reusable and are typically functions or advanced functions contained in a script module or function library. Tool scripts are designed for high reusability, allowing them to be employed in various scenarios.
* Controller Scripts: These scripts automate specific business processes by utilizing one or more tools (functions, commands, etc.). They are not intended to be reusable but rather to leverage reusable components. For example, a "New-CorpUser" script might call various functions to create a user account, mailbox-enable them, provision a home folder, and more. This script automates a single process without needing to exhibit reusability concepts.

&#x20;Controller scripts often produce output directly to the screen for interactive use or log to a file when designed to run unattended.

## Make Your Code Modular&#x20;

Modularizing your code is key to enhancing reusability and maintainability. Organize your code into functions and store them in script modules. This approach offers several benefits:&#x20;

* Parameter Input: Functions should accept input exclusively through parameters.&#x20;
* Pipeline Output: Functions should produce output as objects to the pipeline.&#x20;
* Single Responsibility: Each function should focus on doing one thing exceptionally well.

&#x20;If a function becomes too large (over a couple of hundred lines) this maybe an indicator that it is doing too much and needs to be broken into smaller, more manageable functions. Likewise, if a script becomes extensive and lacks functions, refactor it to include them. This practice makes the code easier to follow, maintain, and troubleshoot, while also increasing reusability.&#x20;

Additionally, if you notice that you are reusing the same code with only minor differences, it's a clear sign that you should convert that code into a function with parameterized variables. This will streamline your code and reduce redundancy.

## Output Raw Data from Tools&#x20;

The consensus in the community is that tools should output raw data, with minimal manipulation. This practice ensures that tools remain reusable in a broader range of situations. For instance, a tool retrieving data in bytes should output bytes rather than converting the value to another unit of measure.

## Controllers Should Output Formatted Data&#x20;

Controllers, unlike tools, may reformat or manipulate data to achieve the best results for a particular task. For example, a `Get-DiskInfo` function might return disk sizing information in bytes, while a controller creating a disk space inventory might convert that to gigabytes for ease of viewing.

## Intermediate Step: Views&#x20;

For tools packaged in script modules, consider using views. By including a custom `.format.ps1xml` view definition file in the module's manifest, you can specify manipulated data values for display purposes. This approach leaves the raw data intact while providing a customized view, similar to how PowerShell displays the output of `Get-Process`.

## Path Management&#x20;

### Use Full, Explicit Paths Whenever Possible&#x20;

When writing scripts, avoid using `..` or `.` in paths unless you have explicitly set the location within the current function or script. Even then, be cautious with relative paths when calling .NET methods or legacy/native applications, as they rely on `[Environment]::CurrentDirectory`, which doesn't automatically update to PowerShell's present working directory (`$PWD`).&#x20;

To prevent these types of errors—which can be tedious to troubleshoot and easy to overlook—it's best to avoid relative paths altogether. Instead, base your paths on `$PSScriptRoot` (the folder your script is in) when necessary.

```
# Do not write:
Get-Content .\README.md

# Especially do not write:
[System.IO.File]::ReadAllText(".\README.md")

# Although you can write:
Push-Location $PSScriptRoot
Get-Content README.md

# It would be better to write:
Get-Content -Path (Join-Path -Path $PSScriptRoot -ChildPath README.md)

# Or to use string concatenation:
Get-Content "$PSScriptRoot\README.md"

# For calling .NET methods, pass full paths:
[System.IO.File]::ReadAllText("$PSScriptRoot\README.md")

# Optionally by calling Convert-Path
Push-Location $PSScriptRoot
[System.IO.File]::ReadAllText((Convert-Path README.md))
```

### Avoid Using `~` to Represent the Home Folder

The meaning of `~` depends on the current provider at the time of execution. This isn't just a style issue—it's crucial for code you intend to share. Instead, use `${ Env:UserProfile }` or `(Get-PSProvider -PSProvider FileSystem).Home` to represent the home folder.
