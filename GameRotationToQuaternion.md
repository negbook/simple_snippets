---
for: client
---
## GameRotationToQuaternion

```lua
GameRotationToQuaternion(rotation)
```

## Snippet Code
```
function GameRotationToQuaternion(rotation)
    local x,y,z = rotation.x,rotation.y,rotation.z
    local q1 = quat(y,vector3(0,1,0))
    local q2 = quat(x,vector3(1,0,0))
    local q3 = quat(z,vector3(0,0,1))
    local q = q1 * q2 * q3
    return q
end
```

## Example
```
    local rotation = vector3(90,140,210)
    local q = GameRotationToQuaternion(rotation)
    vertice =  q * vertice 
```