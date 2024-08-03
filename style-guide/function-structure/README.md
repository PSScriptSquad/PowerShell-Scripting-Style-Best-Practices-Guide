# Function Structure

## Always Start with CmdletBinding <a href="#always-start-with-cmdletbinding" id="always-start-with-cmdletbinding"></a>

Using CmdletBinding is particularly important, especially when utilizing Write-Verbose and Write-Debug. These output streams are off by default, and the `[CmdletBinding()]` attribute enables the common `-Verbose` and `-Debug` switches, turning those streams on. It also activates switches for the Warning and Error streams, and provides methods to collect these streams into variables.

Start your scripts or functions with:

```powershell
[CmdletBinding()]
param ()
process {
}
end {
}
```

You can always delete or ignore one of the blocks (or add the begin block), add parameters with necessary validation, and so on, but always consider using `[CmdletBinding()]`.

## Prefer: param (), begin, process, end

Writing a script in the order of execution (param, begin, process, end) clarifies its intent. Even though PowerShell will execute these blocks in the correct order regardless, writing them out of sequence can cause confusion and make the code harder to maintain and debug. Explicit code is more maintainable. Although PowerShell allows omitting the explicit end block name and offers a filter keyword that converts an anonymous block to a process block, it is recommended to avoid these features as they result in less explicit and thus less maintainable code.

## Do Not Use the Keyword \`return\` to Return an Object

In PowerShell, `return` is a flow-control statement that unconditionally exits the enclosing function, script block, or script. To ensure your functions participate in the pipeline properly and their output is handled consistently, use `Write-Output` to emit objects to the pipeline. While PowerShell implicitly returns data from functions, explicitly using `Write-Output` enhances readability and maintainability by making the function's behavior clear. Reserve `return` for exiting a scope, and use `Write-Output` for outputting data.

## Emit objects inside the \`Process {}\` block

To fully leverage the advantages of the pipeline, ensure that any objects you want to return are emitted inside the `Process {}` block. Returning objects in the `Begin {}` or `End {}` blocks defeats the purpose of the pipeline and can lead to unexpected behaviors. The `Process {}` block is specifically designed to handle pipeline input and output, ensuring smooth and efficient data processing.

## Cmdlet Naming

PowerShell cmdlets follow a verb-noun naming convention, where the verb identifies the action and the noun identifies the entity. For example, the `Get-Command` cmdlet retrieves all registered commands. You can view a list of standard verbs by typing `Get-Verb` at the command line.

## Parameter Naming

Maintain consistency with PowerShell native cmdlets by using standard parameter names. For example, use `$ComputerName` and `$ServerInstance` instead of `$Param_Computer` or `$InstanceName`.

## ParameterSetName

When a ParameterSetName is used in any of the parameters, always provide a DefaultParameterSetName in the CmdletBinding attribute.

```powershell
function Get-User {
    [CmdletBinding(DefaultParameterSetName = "ID")]
    [OutputType("System.Int32", ParameterSetName = "ID")]
    [OutputType([String], ParameterSetName = "Name")]
    param (
        [parameter(Mandatory = $true, ParameterSetName = "ID")]
        [Int[]]$UserID,
        
        [parameter(Mandatory = $true, ParameterSetName = "Name")]
        [String[]]$UserName
    )
<# function body #>
}
```

## Parameter Validation

When using advanced functions or scripts with the CmdletBinding attribute, avoid validating parameters in the body of the script when possible. Use parameter validation attributes instead.

### **AllowNull Validation Attribute**

The AllowNull attribute allows the value of a mandatory parameter to be null (`$null`).

```powershell
param (
[Parameter(Mandatory = $true)]
[AllowNull()]
[String]$ComputerName
)
```

### **AllowEmptyString Validation Attribute**

The AllowEmptyString attribute allows the value of a mandatory parameter to be an empty string (`""`).

```powershell
param (
[Parameter(Mandatory = $true)]
[AllowEmptyString()]
[String]$ComputerName
)
```

## Specify \`OutputType\`

When an advanced function returns an object or collection of objects, use the OutputType attribute to specify the type of the return value. This enhances clarity and aids tools like PowerShell ISE or Visual Studio Code in providing better IntelliSense support.

If the function returns different object types based on the parameter set, provide a separate OutputType attribute for each parameter set:

```powershell
[OutputType([<TypeLiteral>], ParameterSetName = "<Name>")]
[OutputType("<TypeNameString>", ParameterSetName = "<Name>")]
```

## Only Output one "kind" of thing at a time

Avoid mixing different types of objects in the output of a single command. This practice may lead to empty rows in your output or cause table output to break into list output, etc. Indicate the output type(s) of your scripts, functions, or cmdlets with the `[OutputType()]` attribute.

When combining the output of multiple types of objects, they should generally be derived from a common base type (e.g., FileInfo and DirectoryInfo both derive from System.IO.FileSystemInfo) or should have format or type files that cause them to output the same columns. Avoid outputting strings interspersed in your output.

### **Two important exceptions to the single-type rule**

1. **Internal Functions:** It's acceptable to return multiple different types from internal functions since they won't be "output" to the user/host. This can offer significant performance savings (e.g., one database call with three table joins, instead of three database calls with two or three joins each). You can then call these functions and assign the output to multiple variables, like so:

`$user, $group, $org = Get-UserGroupOrg`

2. **Out-Default:** If you must return multiple object types from an external command, name your function in such a way that it's obvious to users that you're returning multiple things. You must call `Out-Default` separately for each type of object to ensure that the outputs don't get mixed up by the formatter.
