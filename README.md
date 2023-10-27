## What is this

This is example code to configure the following Azure Resources

- Virtual Network & Subnet
- Public IP address
- NAT Gateway
- NATGW & Subnet Association

This was developed to use as a beginner tutorial for Lindbergtech Blog

```Powershell
## Authenticate to Azure & select your subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId "<your_sub_id>"

## Set some variables
$rg = "rg-prod"
$vnet = "vnet-prod"
$subnet = "sn-prod"
$location = "westeurope"
$pip = "pip-natgw-prod"
$natgwname = "ng-prod"

## Create a virtual network
New-AzResourceGroup -Name $rg -Location $location
New-AzVirtualNetwork -Name "vnet-prod" -ResourceGroupName $rg -Location $location -AddressPrefix "10.0.0.0/24"

## Create NAT Gateway, PIP and associate with subnet
New-AzPublicIpAddress -Name $pip -ResourceGroupName $rg -Location $location -Sku "Standard" -AllocationMethod "Static" -Zone 1,2,3 
New-AzNatGateway -Name $natgwname -ResourceGroupName $rg -Location $location -IdleTimeoutInMinutes 4 -Sku "Standard" -PublicIpAddress (Get-AzPublicIpAddress -Name $pip -ResourceGroupName $rg)
$subnet_config = Add-AzVirtualNetworkSubnetConfig -Name $subnet -VirtualNetwork (Get-AzVirtualNetwork -Name $vnet -ResourceGroupName $rg) -NatGatewayId (Get-AzNatGateway -Name $natgwname -ResourceGroupName $rg).Id -AddressPrefix "10.0.0.0/26"
$subnet_config | Set-AzVirtualNetwork
```

