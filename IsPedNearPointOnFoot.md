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
  local x , y , z = coords.x, coords.y, coords.z
  local near  = IsAnyPedNearPoint( x , y , z , radius )
  if not near then return false end 
  local ped = ped
  if not IsPedOnFoot(ped) then return false end 
  local player_coords = GetEntityCoords(ped)
  local distance = #(coords - player_coords) * 2
  return distance <= radius
end
```
