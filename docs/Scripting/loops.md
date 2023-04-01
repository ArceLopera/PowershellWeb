While there are some simple scripts that use only simple Windows PowerShell commands, most scripts use scripting constructs to perform more complex actions. You can use the ForEach construct to process all of the objects in an array. You can use If..Else and Switch constructs to make decisions in your scripts. Finally, there are less common looping constructs such as For, While, and Do..While loops that can be used when appropriate.

## ForEach

When you perform piping, the commands in the pipeline are applied to each object. In some cases, you might need to use the ForEach-Object cmdlet to process the data in the pipeline. When you store data in an array, the ForEach construct allows you to process each item in the array.

The ForEach construct uses the following syntax:

``` pwsh
ForEach ($user in $users) {
     Set-ADUser $user -Department "Marketing"
}
```

In a script, the ForEach construct is the most common way to process items that you've placed into an array. It's easy to use because you don't need to know the number of items to process them.

### Parallel performance
In PowerShell 7, the -Parallel parameter was added to the ForEach-Object cmdlet. This allows the pipeline to process multiple objects simultaneously. Processing multiple objects simultaneously can provide better performance than a standard ForEach loop.

``` pwsh
$users | ForEach-Object -Parallel { Set-ADUser $user -Department "Marketing" }
```

By default, the -Parallel parameter allows five items to be processed at a time. You can modify this to be larger or smaller by using the -ThrottleLimit parameter.

## For

The For construct performs a series of loops similar to a ForEach construct. However, when using the For construct, you must define how many loops occur, which is useful when you want an action to be performed a specific number of times. For example, you could create a specific number of user accounts in a test environment.

The For construct uses the following syntax:

``` pwsh
For($i=1; $i -le 10; $i++) {
   Write-Host "Creating User $i"
}
```

The For construct uses an initial state, a condition, and an action. In the previous example, the initial state is $i=1. The condition is $i -le 10. When the condition specified is true, another loop is processed. After each loop is processed, the action is performed. In this example, the action is $i++, which increments $i by 1.

The script block inside the braces is run each time the loop is processed. In the previous example, this loop is processed 10 times.

When you're processing an array of objects, using the ForEach construct is preferred because you don't need to calculate the number of items in the array before processing.

## Other Loops

There are other less common looping constructs that you can use. These looping constructs are Do..While, Do..Until, and While. All these looping constructs process a script block until a condition is met, but they vary in how they do it.

### Do..While
The Do..While construct runs a script block until a specified condition isn't true. This construct guarantees that the script block is run at least once.

The Do..While construct uses the following syntax:

``` pwsh
Do {
   Write-Host "Script block to process"
} While ($answer -eq "go")
```

### Do..Until
The Do..Until construct runs a script block until a specified condition is true. This construct guarantees that the script block is run at least once.

The Do..Until construct uses the following syntax:

``` pwsh
Do {
   Write-Host "Script block to process"
} Until ($answer -eq "stop")
```

### While
The While construct runs a script block until a specified condition is false. While it's similar to the Do..While construct, it doesn't guarantee that the script block is run.

The While construct uses the following syntax:

``` pwsh
While ($answer -eq "go") {
   Write-Host "Script block to process"
}
```