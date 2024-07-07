# Naming Conventions

## Use Full Command Names&#x20;

Prefer using the full, explicit names for commands and parameters rather than relying on aliases or abbreviations. This practice enhances the readability and maintainability of your scripts, especially for those who may not be familiar with all the available PowerShell commands.

```
# Do not write:
gps -Name Explorer

# Instead write:
Get-Process -Name Explorer
```

## Use Full Parameter Names&#x20;

PowerShell scripts are often read and maintained by different people over time. Using full parameter names ensures clarity and reduces the risk of misunderstandings or errors, particularly for readers who might not be familiar with the command you are using. Additionally, being explicit with parameter names helps prevent bugs that could arise if future updates to the command change the parameter sets.

```
# Do not write:
Get-Process Explorer

# Instead write:
Get-Process -Name Explorer
```

## Capitalization Conventions&#x20;

In the Code Layout and Formatting chapter, there is a section on [capitalization conventions](../code-layout-and-formatting/#capitalization-conventions).

## Cmdlet Naming&#x20;

In the Function Structure chapter, there is a section on [Cmdlet Naming](../function-structure/#cmdlet-naming).

## Parameter Naming&#x20;

In the Function Structure chapter, there is a section on [Parameter Naming](../function-structure/#parameter-naming).
