# Output and Formatting

## Avoid Using Write-Host Unless Absolutely Necessary&#x20;

Prior to PowerShell 5, `Write-Host` has no functionality in non-interactive scripts. It cannot be captured or redirected, making it unsuitable for generating script output. `Write-Host` should only be used in functions that "Show" or "Format" output, or as part of an interactive prompt for the user.&#x20;

Specifically, you should avoid using `Write-Host` to generate script output unless your script (or function, etc.) uses the "Show" verb (e.g., `Show-Performance`) or the "Format" verb (e.g., `Format-Hex`), or includes a `-Formatted` switch parameter. It is also appropriate for user interaction scenarios, such as displaying additional information before prompting for user input. In general, consider using other `Write-*` commands when conveying information to the user.

## Using Write-Progress for Real-Time Updates&#x20;

To keep users informed about the script's progress or to ensure they know something is happening, use the Write-Progress cmdlet. This command is particularly useful in graphical hosts or remote jobs, as it provides real-time updates to the user, even when verbose and other output streams are being logged. Keep in mind that progress output is ephemeral and does not persist after the script completes. Therefore, avoid placing critical information exclusively in the progress stream, as users won't be able to review it later.

## Enhance Script Clarity with Write-Verbose&#x20;

Use Write-Verbose to provide detailed information to users running your script. Verbose output should offer insights into computation values or explain why a specific execution path was taken. This information, while helpful, is not essential for running the script. It can include status updates like "Server1 processed" or logic details such as "Server2 up-to-date, skipped." However, avoid using verbose output for actual results or critical information.

## Leverage Write-Debug for Maintenance and Troubleshooting&#x20;

To aid in script maintenance and debugging, utilize the `Write-Debug` cmdlet for providing information that helps trace the script's execution flow (e.g., "Entering main loop" or "Result was null, skipping to end of loop"). This can also include variable values before conditional statements, allowing maintainers to break into the debugger if necessary. TIP: For a smoother debugging experience, set `$DebugPreference = "Continue"` to view debug messages on screen without triggering a breakpoint prompt.
