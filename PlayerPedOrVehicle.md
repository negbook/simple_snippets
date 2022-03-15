---
for: client
---
## PlayerPedOrVehicle

```lua
PlayerPedOrVehicle()
```

## Snippet Code
```
function PlayerPedOrVehicle()
  local ped = PlayerPedId()
  local veh = GetVehiclePedIsIn(ped)
  return veh~=0 and veh or ped
end
```

