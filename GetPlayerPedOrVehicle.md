---
for: client
---
## GetPlayerPedOrVehicle

```lua
GetPlayerPedOrVehicle(player)
```

## Snippet Code
```
function GetPlayerPedOrVehicle(player)
  local ped = GetPlayerPed(player)
  local veh = GetVehiclePedIsIn(ped,false)
  return veh~=0 and veh or ped
end
```

