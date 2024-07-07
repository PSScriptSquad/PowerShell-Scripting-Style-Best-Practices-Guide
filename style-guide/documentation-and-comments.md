# Documentation and Comments

## Write Comment-based Help <a href="#toc171089040" id="toc171089040"></a>

Always include comment-based help in your scripts and functions. To ensure the documentation remains with the function and is easy to update, place the documentation comments inside the function, ideally at the top. Comment-based help is formatted as follows:

```powershell
function Get-Example {
    <#
    .SYNOPSIS
        A brief description of the function or script.

    .DESCRIPTION
        A longer description.

    .PARAMETER FirstParameter
        Description of each of the parameters.

    .PARAMETER SecondParameter
        Description of each of the parameters.

    .INPUTS
        Description of objects that can be piped to the script.

    .OUTPUTS
        Description of objects that are output by the script.

    .EXAMPLE
        Example of how to run the script.

    .LINK
        Links to further documentation.

    .NOTES
           Name: Get-Example
            Author: [Author]
            Date: MM.DD.YYYY
            Version: 1.0
            Changes: Initial release  
    #>
```

Users can view comment-based help by typing help `Get-Example` or `Get-Example -?`. Ensure your help is genuinely helpful. For instance, if you have a tool named `Get-LOBAppUser`, don't just write "Gets LOB App Users." Provide meaningful descriptions and usage examples.
