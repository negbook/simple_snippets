---
for: client
---
## DrawQuaternion

```lua
DrawQuaternion(position,linescale,quaternion)
```

## Snippet Code
```
function DrawQuaternion(position,linescale,quaternion)
    local Xaxis = vector3(1.0,0.0,0.0)
    local Yaxis = vector3(0.0,1.0,0.0)
    local Zaxis = vector3(0.0,0.0,1.0)

    local Xaxis_rotated = quaternion * Xaxis
    local Yaxis_rotated = quaternion * Yaxis
    local Zaxis_rotated = quaternion * Zaxis

    local Xaxis_rotated_normalized = norm(Xaxis_rotated)
    local Yaxis_rotated_normalized = norm(Yaxis_rotated)
    local Zaxis_rotated_normalized = norm(Zaxis_rotated)

    local Xaxis_rotated_normalized_scaled = Xaxis_rotated_normalized * linescale
    local Yaxis_rotated_normalized_scaled = Yaxis_rotated_normalized * linescale
    local Zaxis_rotated_normalized_scaled = Zaxis_rotated_normalized * linescale
   
    DrawLine(position.x, position.y, position.z, position.x + Xaxis_rotated_normalized_scaled.x, position.y + Xaxis_rotated_normalized_scaled.y, position.z + Xaxis_rotated_normalized_scaled.z, 255,0,0,255)
    DrawLine(position.x, position.y, position.z, position.x + Yaxis_rotated_normalized_scaled.x, position.y + Yaxis_rotated_normalized_scaled.y, position.z + Yaxis_rotated_normalized_scaled.z, 0,255,0,255)
    DrawLine(position.x, position.y, position.z, position.x + Zaxis_rotated_normalized_scaled.x, position.y + Zaxis_rotated_normalized_scaled.y, position.z + Zaxis_rotated_normalized_scaled.z, 0,0,255,255)
    --DrawText3D(position.x + Xaxis_rotated_normalized_scaled.x, position.y + Xaxis_rotated_normalized_scaled.y, position.z + Xaxis_rotated_normalized_scaled.z, "X", 255,0,0,255)
    --DrawText3D(position.x + Yaxis_rotated_normalized_scaled.x, position.y + Yaxis_rotated_normalized_scaled.y, position.z + Yaxis_rotated_normalized_scaled.z, "Y", 0,255,0,255)
    --DrawText3D(position.x + Zaxis_rotated_normalized_scaled.x, position.y + Zaxis_rotated_normalized_scaled.y, position.z + Zaxis_rotated_normalized_scaled.z, "Z", 0,0,255,255)
end
```

