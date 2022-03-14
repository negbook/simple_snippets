---
for: client
---
## IsPedNearPointOnFoot

```lua
IsPedNearPointOnFoot(ped, coords, radius)
```

## Snippet Code
```
function IsPedNearPointOnFoot(ped, coords, radius)
  local ped = ped
  if not IsPedOnFoot(ped) then return false end 
  local player_coords = GetEntityCoords(ped)
  local distance = #(coords - player_coords)
  return distance <= radius / 2
end
```
