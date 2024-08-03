# Performance

When discussing PowerShell performance, it's crucial to balance performance optimization with code readability. Novice scripters often inadvertently hinder PowerShell's efficiency by not utilizing cmdlets as intended or simply being unaware of more effective methods. Enhancing your script's performance requires understanding and leveraging the full potential of PowerShell's cmdlets and best practices.

## Proper Use of Invoke-Command&#x20;

The primary use of `Invoke-Command` is to run commands on remote computers and return the results to the local machine. However, some users incorrectly wrap `Invoke-Command` in a `Foreach` loop to iterate over multiple computers, which forces sequential execution and results in inefficient performance.

Instead, the `ComputerName` parameter of `Invoke-Command` can handle an array of computers, allowing simultaneous execution across multiple machines, vastly improving performance. For example, running a `Get-PSDrive` command on 23 computers took nearly 30 seconds using a `Foreach` loop but only 2.8 seconds using the proper method.

Remember, `Invoke-Command` can run against all specified computers concurrently, leveraging the `ThrottleLimit` parameter to manage concurrency effectively.

**Do not:**

```powershell
foreach ($Computer in $Computers) { 
    Invoke-Command -ComputerName $Computer -ScriptBlock {Get-PSDrive -PSProvider FileSystem}
}
```

**Instead:**&#x20;

```powershell
Invoke-Command -ComputerName $Computers -ScriptBlock { Get-PSDrive -PSProvider FileSystem }
```

## Filter Left&#x20;

When working with PowerShell commands, it's common to receive more objects than necessary, which can impact performance. The best practice of "filter left" emphasizes limiting output as close to the data source as possible to enhance speed and efficiency.

Using the `Filter` parameter, available in commands like `Get-ChildItem` and `Get-Ad*`, is more efficient than using the generic `Where-Object` because it restricts output at the provider level. This avoids the overhead of processing all objects through the pipeline.

For instance, finding files with "1" in their name using `Get-ChildItem -Filter '*1*.txt'` is significantly faster than using `Where-Object` or the `where()` method.

**Inefficient Method:**

```powershell
Get-ChildItem -Path "C:\Path\To\Directory" | Where-Object { $_.Name -like '*1*.txt' }
```

**Efficient Method:**

```powershell
Get-ChildItem -Path "C:\Path\To\Directory" -Filter '*1*.txt'
```

Always use the `Filter` parameter or similar built-in filtering options when available to optimize script performance.

## Array Addition

When generating a list of items in PowerShell, you might be tempted to use array addition like this:

**Inefficient Method:**

```powershell
$Results = @()
$Servers = @('Server1', 'Server2', 'Server3')

foreach ($Server in $Servers) {
    $Results += Get-Something -ComputerName $Server
}
$Results
```

Array addition is inefficient because arrays have a fixed size. Each addition creates a new array large enough to hold all elements of both the left and right operands, and all elements are copied into the new array. While this overhead might not matter for small collections, performance can suffer significantly for large collections.

Instead, use PowerShell's explicit assignment, which is more efficient for handling large collections:

**Efficient Method:**

```powershell
$Servers = @('Server1', 'Server2', 'Server3')
$Results = foreach ($Server in $Servers) {
    Get-Something -ComputerName $Server
}
```

Using `foreach` with explicit assignment eliminates the overhead of repeatedly resizing and copying arrays, making it a much more efficient approach for building large collections.

## Lookup of Entries by Property in Large Collections

When working with large collections, it's often necessary to match records across different collections using a shared property, such as using a name to retrieve an ID from one list and a phone number from another. Iterating over one list to find the matching record in another can be slow, especially if repeatedly filtering the second collection.

Given two collections, one with ID and Name, and another with Name and Phone:

```powershell
$Employees = 1..10000 | ForEach-Object {
    [PSCustomObject]@{
        Id   = $_
        Name = "Employee$_"
    }
}

$Contacts = 2000..8000 | ForEach-Object {
    [PSCustomObject]@{
        Name  = "Employee$_"
        Phone = "555-$_"
    }
}
```

### Slow Method

A common approach to reconcile these collections might look like this:

```powershell
$Results = $Employees | ForEach-Object -Process {
    $Employee = $_

    $Contact = $Contacts | Where-Object -FilterScript {
        $_.Name -eq $Employee.Name
    }

    [PSCustomObject]@{
        Id    = $Employee.Id
        Name  = $Employee.Name
        Phone = $Contact.Phone
    }
}
```

This method filters all items in the `$Contacts` collection once for every item in the `$Employees` collection, which is very slow.

### Fast Method

Instead, you can use a hash table with the shared Name property as a key and the matching contact as the value:

```powershell
$LookupHash = @{}
foreach ($Contact in $Contacts) {
    $LookupHash[$Contact.Name] = $Contact
}
```

Looking up keys in a hash table is much faster than filtering a collection. PowerShell can quickly check if the key exists and use its value:

```powershell
$Results = $Employees | ForEach-Object -Process {
    $Phone = $LookupHash[$_.Name].Phone
    [PSCustomObject]@{
        Id    = $_.Id
        Name  = $_.Name
        Phone = $Phone
    }
}
```

This method is much faster, reducing the operation time from minutes to less than a second.
