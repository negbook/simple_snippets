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
  local ped = ped
  local player_coords = GetEntityCoords(ped)
  local distance = #(coords - player_coords)
  return distance <= radius / 2
end
```

