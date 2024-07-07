# Error Handling

## Utilize -ErrorAction Stop with Cmdlets&#x20;

When handling errors, use the `-ErrorAction Stop` parameter on cmdlets to ensure that errors generate terminating exceptions, which can be trapped and handled effectively.

## Set $ErrorActionPreference for Non-Cmdlets&#x20;

For non-cmdlet commands, set `$ErrorActionPreference = 'Stop'` before execution and reset it to `'Continue'` afterwards. If you're worried about using `-ErrorAction` because it might terminate the entire pipeline, reconsider the pipeline's complexity. Opt for a more script-based approach, as it allows for better automated error handling.&#x20;

Commands or code that might fail should handle a single task, such as querying one computer, deleting one file, or updating one user. This way, if an error occurs, you can manage it and proceed with the next task.

## Avoid Using Flags for Error Handling&#x20;

Avoid using flags to handle errors:

```
try {
    $continue = $true
    Do-Something -ErrorAction Stop
} catch {
    $continue = $false
}

if ($continue) {
    Do-This
    Set-That
    Get-Those
}
```

Instead, encapsulate the entire operation within the `try` block:

```
try {
    Do-Something -ErrorAction Stop
    Do-This
    Set-That
    Get-Those
} catch {
    Handle-Error
}
```

This approach makes the logic easier to follow.

## Avoid Using $?&#x20;

When examining errors, avoid using `$?`. This variable only indicates whether the last command considered itself successful, without providing error details.

## Refrain from Testing for Null Variables as Error Conditions&#x20;

Avoid using null variable checks as an error condition:

```
$user = Get-ADUser -Identity Bob

if ($user) {
    $user | Do-Something
} else {
    Write-Warning "Could not get user $user"
}
```

While sometimes necessary, especially if the command won't produce a trappable exception, this method can make debugging more complex.

## Copy $Error\[0] to Your Own Variable&#x20;

Within a `catch` block, use `$_` or `$Error[0]` to access the last error. Immediately copy this information into your own variable, as subsequent commands can overwrite `$_` or change `$Error[0]`. It's usually unnecessary to clear `$Error` since PowerShell maintains this collection automatically.
