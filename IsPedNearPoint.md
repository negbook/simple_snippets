---
for: client
---
## IsPedNearPoint

```lua
IsPedNearPoint(ped, coords, radius)
```

## Snippet Code
```
function IsPedNearPoint(ped, coords, radius)
  local x , y , z = coords.x, coords.y, coords.z
  local near  = IsAnyPedNearPoint( x , y , z , radius )
  if not near then return false end 
  local ped = ped
  local player_coords = GetEntityCoords(ped)
  local distance = #(coords - player_coords) * 2
  return distance <= radius
end
```
