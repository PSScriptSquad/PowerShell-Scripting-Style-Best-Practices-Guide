# Code Layout and Formatting

These guidelines prioritize readability. While some rules may seem arbitrary, they are based on decades of programming traditions. For instance, leaving an empty line after a closing function brace or two lines before functions isn't capricious; it helps experienced developers quickly scan your code.

## Maintain Consistency in Layout

Consistency in indentation, line length, and capitalization improves code readability and understanding. Familiar-looking code reduces distractions, benefiting the entire community. Always follow individual project rules, even if they differ from these guidelines. If reformatting legacy code, make whitespace changes in a single commit to keep content changes clear.

## Capitalization Conventions

While PowerShell is not case-sensitive, following capitalization conventions ensures that code remains easy to read and understand. These conventions are derived from Microsoft's .NET framework guidelines, as PowerShell is a .NET scripting language, and its cmdlets are primarily written in .NET languages.

### **Terminology**&#x20;

* **lowercase:** All lowercase, no word separation
* **UPPERCASE:** All capitals, no word separation
* **PascalCase:** Capitalize the first letter of each word.
* **camelCase:** Capitalize the first letter of each word except the first.

### **PowerShell Capitalization Practices**&#x20;

PowerShell uses PascalCase for all public identifiers, including module names, function or cmdlet names, classes, enums, attribute names, public fields, properties, global variables, and constants. Since PowerShell command parameters are properties of .NET classes, even parameters use PascalCase rather than camelCase.&#x20;

PowerShell language constructs, such as \`foreach\` , \`if\`, \`switch\`, are written in lowercase, as are operators like \`-eq\` and \`-match\`. In comment-based help documentation, keywords are written in UPPERCASE for visibility. &#x20;

```powershell
function Get-UserInfo {
    <#
        .SYNOPSIS
            Gets information about a user.
        
        .DESCRIPTION
            The Get-UserInfo function retrieves the full name, email address,
            and department of a specified user.
        
        .PARAMETER UserName
            The name of the user whose information is to be retrieved.
        
        .EXAMPLE
            $info = Get-UserInfo -UserName "JohnDoe"
            Write-Output $info
        
        .NOTES
            This is a sample function demonstrating PowerShell conventions.
    #>
    [CmdletBinding()]
    param (
        [Parameter(Mandatory = $true)]
        [string]$UserName
    )
    
    # Initialize variables
    $UserInfo = @{
        FullName = $null
        Email = $null
        Department = $null
    }
    
    # Use lowercase language constructs and operators
    if ($null -eq $UserName) {
        Write-Error "UserName parameter cannot be null."
        return
    }

    # Simulate fetching user information (replace this with actual logic)
    switch ($UserName) {
        "JohnDoe" {
            $UserInfo.FullName = "John Doe"
            $UserInfo.Email = "john.doe@example.com"
            $UserInfo.Department = "IT"
        }
        "JaneSmith" {
            $UserInfo.FullName = "Jane Smith"
            $UserInfo.Email = "jane.smith@example.com"
            $UserInfo.Department = "HR"
        }
        default {
            Write-Error "User not found."
            return
        }
    }
    
    # Output the user information
    Write-Output $UserInfo
}
```

### **Special Cases**

Two-letter acronyms are fully capitalized, such as in the variable \`$PSBoundParameters\` or the cmdlet \`Get-PSDrive\`. However, this does not apply to commonly capitalized words like "OK" and "ID." Compound acronyms, such as when Azure's Resource Manager (RM) meets a Virtual Machine (VM) in \`Start-AzureRmVM\`, should also not extend this rule.

## One True Brace Style

This guide recommends the "One True Brace Style" (OTBS), which places opening braces at the end of a line and closing braces at the beginning of a new line. Exceptions include small scriptblocks passed to parameters, which can be on a single line.

Example:

```powershell
function Test-Code {
    [CmdletBinding()]
    param (
        [int]$ParameterOne
    )
    end {
        if (10 -gt $ParameterOne) {
            "Greater"
        } else {
            "Lesser"
        }
    }
}
# An Exception case:
Get-ChildItem | Where-Object { $_.Length -gt 10mb }
```

OTBS allows new lines of code can be inserted between any two lines with no risk of accidentally breaking the code by separating braces from their statement blocks.

## &#x20;Indentation

Use four spaces per indentation level. Most editors can be configured to insert spaces instead of tabs. Indenting more than four spaces is acceptable for continuation lines. The convention is to indent within constructs, to make it clearer what "belongs to" the construct.

```powershell
function Test-Code {
    foreach ($exponent in 1..10) {
        [Math]::Pow(2, $exponent)
    }
}
```

Avoid nesting operations deeper than three or four levels.

## Maximum Line Length

Limit lines to 115 characters whenever possible to maintain readability and avoid horizontal scrolling. When contributing to other projects, adhere to their specific guidelines.

To prevent long lines, prefer using splatting and PowerShell's implied line continuation within parentheses, brackets, or braces. When working with pipelines, use the pipe ("|") operator for continuation. These methods should always be used over the backtick for line continuation, even for strings.

## &#x20;Avoid Using Backticks for Line Continuation

To improve readability and avoid potential errors, refrain from using backticks (\`) as line continuation characters. Backticks can be difficult to read, easily overlooked, and prone to typographical errors. Additionally, if a stray space is added after the backtick, it can cause the command to fail in ways that are challenging to diagnose.

**Example of using backticks (not recommended):**

```powershell
Get-WmiObject -Class Win32_LogicalDisk `
              -Filter "DriveType=3" `
              -ComputerName SERVER2
```

**Preferred alternative using splatting:**

```powershell
$GetWmiObjectParams = @{
    Class = "Win32_LogicalDisk"
    Filter = "DriveType=3"
    ComputerName = "SERVER2"
}
Get-WmiObject @GetWmiObjectParams
```

Instead of using backticks, consider employing the splatting technique. Splatting allows you to achieve the same clean, formatted code without relying on backticks. Note that in the splatting example, with only three short parameters, it would be best to place the entire command on one line, such as:

```powershell
Get-WmiObject -Class "Win32_LogicalDisk" -Filter "DriveType=3" -ComputerName "SERVER2"
```

This one-liner is within the maximum line length recommendation. The splatting example serves to illustrate how to use splatting when it is needed.&#x20;

## Blank Lines and Whitespace

* Surround function and class definitions with one or two blank lines.
* Use a single blank line between method definitions within a class.
* Omit blank lines between related one-liners.
* Use additional blank lines sparingly to separate groups of related functions or within functions to indicate logical sections.

### **Trailing spaces**

Avoid trailing whitespace to prevent unnecessary edits.

&#x20;**Spaces around parameters and operators**

Use a single space around parameter names and operators, including comparison, math, and assignment operators. Exceptions include colons for switch parameters and [Unary Operators](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about\_operators#unary-operators).

Example:

```powershell
# Do not write:
$variable=Get-Content $FilePath -Wait:($ReadCount-gt0) -First($ReadCount*5)

# Instead write:
$variable = Get-Content -Path $FilePath -Wait:($ReadCount -gt 0) -TotalCount ($ReadCount * 5)
```

&#x20;

```powershell
# Do not write:
$yesterdaysDate = (Get-Date).AddDays( - 1)
 
# Instead write:
$yesterdaysDate = (Get-Date).AddDays(-1)
 
# Same principle should be applied when using a variable.
$yesterdaysDate = (Get-Date).AddDays(-$i)
```

### **Spaces around special characters**

&#x20;Use a single space after commas and semicolons, and inside curly braces. Avoid unnecessary spaces inside parentheses or square braces.

```powershell
# This script gets a list of services and filters them based on their status

# Define a function to get services by status
function Get-ServicesByStatus {
    param (
        [string]$Status
    )

    # Get all services and filter by the provided status
    $services = Get-Service | Where-Object { $_.Status -eq $Status }

    # Return the filtered services
    return $services
}

# Call the function to get running services
$runningServices = Get-ServicesByStatus -Status 'Running'

# Display the running services
$runningServices | ForEach-Object { Write-Output "Service: $($_.Name), Status: $($_.Status)" }
```

#### Explanation:

1. **Whitespace around commas and semicolons**:
   * `param ( [string]$Status )` has spaces after the comma.
   * `Write-Output "Service: $($_.Name), Status: $($_.Status)"` has spaces after the comma.
2. **Spaces inside curly braces**:
   * In script blocks, such as `Where-Object { $_.Status -eq $Status }` and `ForEach-Object { Write-Output "Service: $($_.Name), Status: $($_.Status)" }`, there's a single space inside the curly braces.
3. **Subexpressions**:
   * Subexpression `$( ... )` in `Write-Output "Service: $($_.Name), Status: $($_.Status)"` has a single space inside the parentheses.
4. **Avoid unnecessary spaces inside parentheses or square braces**:
   * The parentheses and square braces are used without unnecessary spaces, for example, `param ( [string]$Status )` and `Where-Object { $_.Status -eq $Status }`.

## Avoid Using Semicolons (;) as Line Terminators

Avoid unnecessary semicolons to prevent editing complications. They are also unnecessary in multi-line hashtable declarations.

Example:

```powershell
# This is the preferred way to declare a hashtable if it extends past one line:
$Options = @{
    Margin   = 2
    Padding  = 2
    FontSize = 24
}
```

