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

## Test
```
function GameRotationToQuaternion(rotation)
    local x,y,z = rotation.x,rotation.y,rotation.z
    local q1 = quat(y,vector3(0,1,0))
    local q2 = quat(x,vector3(1,0,0))
    local q3 = quat(z,vector3(0,0,1))
    local q = q1 * q2 * q3
    return q
end
function GetSphereVertices (size, rotation)
    local vertices = {}
    local sx,sy,sz = size.x/2, size.y/2, size.z/2
    local x,y,z = rotation.x,rotation.y,rotation.z
    local q = GameRotationToQuaternion(rotation)
    
    local i = 0
    for theta = 0, math.pi*2, math.pi/8 do
        for phi = 0, math.pi*2, math.pi/8 do
            for z = 0, math.pi*2, math.pi/8 do
                local x = sx * math.sin(theta) * math.cos(phi)
                local y = sy * math.sin(theta) * math.sin(phi)
                local z = sz * math.cos(theta)
                local v = vector3(x,y,z)
                vertices[i] =  q * v 
                i = i + 1
            end
        end 
    end
    return vertices
end
DrawVertices = function(pos, vertices, r,g,b,a)
    for i, vertex in ipairs(vertices) do
        if vertex and vertices[i+1] then 
            DrawLine(vertex.x + pos.x, vertex.y + pos.y, vertex.z + pos.z, vertices[i+1].x + pos.x, vertices[i+1].y + pos.y, vertices[i+1].z + pos.z, r,g,b,a)
        end 
    end
    DrawLine(pos.x, pos.y, pos.z, vertices[1].x + pos.x, vertices[1].y + pos.y, vertices[1].z + pos.z, r,g,b,a)
end
DrawSphere = function(center, size, rotation, r,g,b,a)
    DrawMarker(28, center.x,center.y,center.z, 0.0, 0.0, 0.0,rotation.x,rotation.y,rotation.z, size.x/2, size.y/2, size.z/2, r,g,b,a, false, false, 2, false, false, false, false)
end 
CreateThread(function()
    local i = 0
    while true do Wait(0)
        local pos = GetEntityCoords(PlayerPedId())
        local size = vector3(1, 1, 2)
        local rotation = vector3(math.random(0,360), math.random(0,360), math.random(0,360))
        local vertices = GetSphereVertices(size, rotation)
        DrawVertices(pos, vertices, 255,0,0,100)
        DrawSphere(pos, size, rotation, 0,0,255,100)
    end 
end)
```