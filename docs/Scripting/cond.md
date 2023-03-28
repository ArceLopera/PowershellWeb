Flow control refers to how your code runs in your console or script.

## If, ElseIf, and Else

You can use an If construct to determine if an expression is True or False.
$True indicates that an expression is True.
$False indicates that an expression is False.

``` pwsh
# _FullyTax.ps1_
# Possible values: 'Minor', 'Adult', 'Senior Citizen'
$Status = 'Minor'
If ($Status -eq 'Minor') 
{
  Write-Host $False
} ElseIf ($Status -eq 'Adult') {
  Write-Host $True
} Else {
  Write-Host $False
}

```