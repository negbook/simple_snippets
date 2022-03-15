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
  local ped = (player == nil or player== -1) and PlayerPedId() or GetPlayerPed(player)
  local veh = GetVehiclePedIsIn(ped)
  return veh~=0 and veh or ped
end
```

