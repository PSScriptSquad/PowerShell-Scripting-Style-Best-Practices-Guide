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
function Write-Host {
    <#
    .SYNOPSIS
        Writes customized output to a host.
    .DESCRIPTION
        The Write-Host cmdlet customizes output. You can specify the color of text by using
        the ForegroundColor parameter, and you can specify the background color by using the
        BackgroundColor parameter. The Separator parameter lets you specify a string to use to
        separate displayed objects. The particular result depends on the program that is
        hosting Windows PowerShell.
    #>
    [CmdletBinding()]
    param(
        [Parameter(Position = 0, ValueFromPipeline = $true, ValueFromRemainingArguments = $true)]
        [psobject]$Object,
 
        [switch]$NoNewline,
 
        [psobject]$Separator,
 
        [System.ConsoleColor]$ForegroundColor,
 
        [System.ConsoleColor]$BackgroundColor
    )
    begin {
…
```

### **Special Cases**

Two-letter acronyms are fully capitalized, such as in the variable \`$PSBoundParameters\` or the cmdlet \`Get-PSDrive\`. However, this does not apply to commonly capitalized words like "OK" and "ID." Compound acronyms, such as when Azure's Resource Manager (RM) meets a Virtual Machine (VM) in \`Start-AzureRmVM\`, should also not extend this rule.

## One True Brace Style

This guide recommends the "One True Brace Style" (OTBS), which places opening braces at the end of a line and closing braces at the beginning of a new line. Exceptions include small scriptblocks passed to parameters, which can be on a single line.

Example:

```powershell
enum Color {
    Black,
    White
}
 
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

\` Get-WmiObject -Class "Win32\_LogicalDisk" -Filter "DriveType=3" -ComputerName "SERVER2"\`

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
 
$i = 0
$i ++
 
# Instead write:
$yesterdaysDate = (Get-Date).AddDays(-1)
 
$i = 0
$i++
 
# Same principle should be applied when using a variable.
$yesterdaysDate = (Get-Date).AddDays(-$i)
```

### **Spaces around special characters**

&#x20;Use a single space after commas and semicolons, and inside curly braces. Avoid unnecessary spaces inside parentheses or square braces.

```powershell
$Var = 1
"This is a string with one (${Var}) delimited variable."
"There are $( (Get-ChildItem).Count ) files."
```

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
