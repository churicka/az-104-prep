# Adding tags to a resource group using powershell

## Setup your context/environment.

```powershell
$location = "eastus2"
$rg = "tempchu_rg"
```

## Connect to your subscription

```powershell
$ErrorActionPreference = "Stop"
Connect-AzAccount -UseDeviceAuthentication
```

## Create a new resource group

```powershell
New-AzResourceGroup -Name $rg -Location $location -Tag @{'RG'='APP'}
```


## Add a tag to a resource group
### The prior tags will be overlaid.

```powershell
set-azresourcegroup -name $rg -tag @{costcenter="111222333";ManagedBy="Bob"}
```

## Append new tags to the existing tags.

```powershell
$tags = (get-azresourcegroup -name $rg).tags
$tags.Add("Project","Marketing")
set-azresourcegroup -name $rg -tag $tags
```

## Create a vm

```powershell
New-AzVm `
    -ResourceGroupName $rg `
    -Name "tempchu-ubuntu-vm" `
    -Location $location `
    -Image UbuntuLTS `
    -size Standard_B2s `
    -PublicIpAddressName myPubIP `
    -OpenPorts 22 `
    -Credential (Get-Credential)
```

## Get the resource ID of the VM

```powershell
$r = get-azresource `
   -resourcename "tempchu-ubuntu-vm" `
   -resourcegroupname $rg `
   -resourcetype "Microsoft.Compute/virtualMachines"
```

## Modify tags on a resource

```powershell
set-azresource `
  -resourceid $r.resourceid `
  -tag @{costcenter="111222"; ManagedBy="RickyRick"}
```

## Verify that the tags were created.

```powershell
get-azresource -name $r.name
```

## Append tags to a resource that already has tags

```powershell
$r.tags
$r.tags.add("Owner","RickRick")
set-azresource -resourceid $r.resourceid -tag $r.tags -Force
```

## Verify that the new tag was added

```powershell
get-azresource -name $r.name
```

## Remove the vm

```powershell
Remove-AzResourceGroup -Name $rg
```


