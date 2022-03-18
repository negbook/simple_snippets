---
for: client
---
## Draw3D

```lua
DrawRectangle3DOutline (x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a)
DrawRectangle3D (x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a)
DrawRectangle3DReverseOutline (x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a)
DrawRectangle3DReverse (x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a)
DrawPoly3DOutline (x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a)
DrawPoly3D (x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a)
DrawPoly3DReverseOutline (x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a)
DrawPoly3DReverse (x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a)
DrawSphere3D (x,y,z, radius, r,g,b,a)
DrawCircle3D (x,y,z, radius, r,g,b,a)
DrawLine3D (x1,y1,z1, x2,y2,z2, r, g, b, a)
DrawPixel3D (x,y,z, r, g, b, a)
DrawBox3D (center, size, rotation, r, g, b, a)
DrawText3D (x, y, z, text, size, font, r, g, b, a, isfloating)
```

## Snippet Code

```
local function _drawRectangle3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a, isoutline, isreverse)
    DrawPoly(x1,y1,z1,x2,y2,z2, x3,y3,z3, r, g, b, a) -- ABC
    DrawPoly(x3,y3,z3,x4,y4,z4, x1,y1,z1, r, g, b, a) -- CDA
 
    if isoutline then 
        DrawLine(x1,y1,z1,x2,y2,z2,r,g,b,a*2 ) -- AB
        DrawLine(x2,y2,z2,x3,y3,z3,r,g,b,a*2 ) -- BC
        DrawLine(x3,y3,z3,x4,y4,z4,r,g,b,a*2 ) -- CD
        DrawLine(x4,y4,z4,x1,y1,z1,r,g,b,a*2 ) -- DA
    end 

    --reverse
    if isreverse then 
        DrawPoly(x1,y1,z1,x4,y4,z4, x3,y3,z3, r, g, b, a) -- ACD
        DrawPoly(x3,y3,z3,x2,y2,z2, x1,y1,z1, r, g, b, a) -- BAC
    end 
end

function DrawRectangle3DOutline (x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a)
    _drawRectangle3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a, true, false)
end

function DrawRectangle3D (x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a)
    _drawRectangle3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a, false, false)
end

function DrawRectangle3DReverseOutline (x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a)
    _drawRectangle3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a, true, true)
end

function DrawRectangle3DReverse (x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a)
    _drawRectangle3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a, false, true)
end

local function _drawPoly3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a, isoutline, isreverse)
    DrawPoly(x1,y1,z1,x2,y2,z2, x3,y3,z3, r, g, b, a) -- ABC
 
    if isoutline then 
        DrawLine(x1,y1,z1,x2,y2,z2,r,g,b,a*2 ) -- AB
        DrawLine(x2,y2,z2,x3,y3,z3,r,g,b,a*2 ) -- BC
    end 

    --reverse
    if isreverse then 
        DrawPoly(x3,y3,z3,x2,y2,z2, x1,y1,z1, r, g, b, a) -- BAC
    end 
end

function DrawPoly3DOutline (x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a)
    _drawPoly3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a, true, false)
end

function DrawPoly3D (x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a)
    _drawPoly3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a, false, false)
end

function DrawPoly3DReverseOutline (x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a)
    _drawPoly3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a, true, true)
end

function DrawPoly3DReverse (x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a)
    _drawPoly3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a, false, true)
end


function DrawSphere3D (x,y,z, radius, r,g,b,a)
    local _a = a/255
    DrawSphere(x,y,z, radius, r,g,b,_a)
end 

local _drawCircle = function(x,y,z, radius, r,g,b,a)
    DrawMarker(1, x,y,z, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, radius, radius, 0.1, r,g,b,a, false, false, 2, false, false, false, false)
end

DrawCircle3D = _drawCircle

function DrawLine3D (x1,y1,z1, x2,y2,z2, r, g, b, a)
    DrawLine(x1,y1,z1,x2,y2,z2,r,g,b,a)
end

function DrawPixel3D (x,y,z, r, g, b, a)
    DrawBox(x,y,z, x+0.05,y+0.05,z+0.05, r,g,b,a)
end

function DrawBox3D (center, size, rotation, r, g, b, a)
    local x,y,z = 0,0,0
    local w,h,d = size.x, size.y, size.z
    local r,g,b,a = r,g,b,a
    local Pitch, Roll, Yaw = rotation.x, rotation.y, rotation.z --order 2
    local rad = math.rad 
    local cos = math.cos
    local sin = math.sin
    local cosr = cos(rad(Roll))
    local sinr = sin(rad(Roll))
    local cosp = cos(rad(Pitch))
    local sinp = sin(rad(Pitch))
    local cosy = cos(rad(Yaw))
    local siny = sin(rad(Yaw))
    local x1,y1,z1 = x-w/2, y-h/2, z-d/2
    local x2,y2,z2 = x+w/2, y-h/2, z-d/2
    local x3,y3,z3 = x+w/2, y+h/2, z-d/2
    local x4,y4,z4 = x-w/2, y+h/2, z-d/2
    local x5,y5,z5 = x-w/2, y-h/2, z+d/2
    local x6,y6,z6 = x+w/2, y-h/2, z+d/2
    local x7,y7,z7 = x+w/2, y+h/2, z+d/2
    local x8,y8,z8 = x-w/2, y+h/2, z+d/2

    -- rotateYaw
    x1,y1,z1 = x1*cosy-y1*siny, x1*siny+y1*cosy, z1 
    x2,y2,z2 = x2*cosy-y2*siny, x2*siny+y2*cosy, z2
    x3,y3,z3 = x3*cosy-y3*siny, x3*siny+y3*cosy, z3
    x4,y4,z4 = x4*cosy-y4*siny, x4*siny+y4*cosy, z4
    x5,y5,z5 = x5*cosy-y5*siny, x5*siny+y5*cosy, z5
    x6,y6,z6 = x6*cosy-y6*siny, x6*siny+y6*cosy, z6
    x7,y7,z7 = x7*cosy-y7*siny, x7*siny+y7*cosy, z7
    x8,y8,z8 = x8*cosy-y8*siny, x8*siny+y8*cosy, z8
    
    -- rotatePitch
    x1,y1,z1 = x1, y1*cosp-z1*sinp, z1*cosp+y1*sinp
    x2,y2,z2 = x2, y2*cosp-z2*sinp, z2*cosp+y2*sinp
    x3,y3,z3 = x3, y3*cosp-z3*sinp, z3*cosp+y3*sinp
    x4,y4,z4 = x4, y4*cosp-z4*sinp, z4*cosp+y4*sinp
    x5,y5,z5 = x5, y5*cosp-z5*sinp, z5*cosp+y5*sinp
    x6,y6,z6 = x6, y6*cosp-z6*sinp, z6*cosp+y6*sinp
    x7,y7,z7 = x7, y7*cosp-z7*sinp, z7*cosp+y7*sinp
    x8,y8,z8 = x8, y8*cosp-z8*sinp, z8*cosp+y8*sinp

    -- rotateRoll
    x1,y1,z1 = x1*cosr-z1*sinr, y1, z1*cosr+x1*sinr
    x2,y2,z2 = x2*cosr-z2*sinr, y2, z2*cosr+x2*sinr
    x3,y3,z3 = x3*cosr-z3*sinr, y3, z3*cosr+x3*sinr
    x4,y4,z4 = x4*cosr-z4*sinr, y4, z4*cosr+x4*sinr
    x5,y5,z5 = x5*cosr-z5*sinr, y5, z5*cosr+x5*sinr
    x6,y6,z6 = x6*cosr-z6*sinr, y6, z6*cosr+x6*sinr
    x7,y7,z7 = x7*cosr-z7*sinr, y7, z7*cosr+x7*sinr
    x8,y8,z8 = x8*cosr-z8*sinr, y8, z8*cosr+x8*sinr
    

    --offset transform
    x1,y1,z1 = center.x + x1, center.y + y1, center.z + z1
    x2,y2,z2 = center.x + x2, center.y + y2, center.z + z2
    x3,y3,z3 = center.x + x3, center.y + y3, center.z + z3
    x4,y4,z4 = center.x + x4, center.y + y4, center.z + z4
    x5,y5,z5 = center.x + x5, center.y + y5, center.z + z5
    x6,y6,z6 = center.x + x6, center.y + y6, center.z + z6
    x7,y7,z7 = center.x + x7, center.y + y7, center.z + z7
    x8,y8,z8 = center.x + x8, center.y + y8, center.z + z8

    DrawRectangle3DReverse(x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a)
    DrawRectangle3DReverse(x5,y5,z5, x6,y6,z6 , x7,y7,z7, x8,y8,z8, r, g, b, a)
    DrawRectangle3DReverse(x1,y1,z1, x5,y5,z5 , x6,y6,z6, x2,y2,z2, r, g, b, a)
    DrawRectangle3DReverse(x2,y2,z2, x6,y6,z6 , x7,y7,z7, x3,y3,z3, r, g, b, a)
    DrawRectangle3DReverse(x3,y3,z3, x7,y7,z7 , x8,y8,z8, x4,y4,z4, r, g, b, a)
    DrawRectangle3DReverse(x4,y4,z4, x8,y8,z8 , x5,y5,z5, x1,y1,z1, r, g, b, a)

end

function DrawText3D (x, y, z, text, size, font, r, g, b, a, isfloating)
    local coords = vector3(x, y, z)
    local camCoords = GetGameplayCamCoords()
    local distance = #(coords - camCoords)

    if not size then size = 1 end
    if not font then font = 0 end

    local scale = (size / distance) * 2
    local fov = (1 / GetGameplayCamFov()) * 100
    scale = scale * fov

    SetTextScale(0.0 * scale, 0.55 * scale)
    SetTextFont(font)
    SetTextColour(255, 255, 255, 255)
    SetTextDropshadow(0, 0, 0, 0, 255)
    SetTextDropShadow()
    SetTextOutline()
    SetTextCentre(true)
    if isfloating then 
        SetDrawOrigin(coords, 0)
        BeginTextCommandDisplayText('STRING')
        AddTextComponentSubstringPlayerName(text)
        EndTextCommandDisplayText(0,0)
        ClearDrawOrigin()
    else 
        BeginTextCommandDisplayText('STRING')
        AddTextComponentSubstringPlayerName(text)
        local _,_x,_y = GetHudScreenPositionFromWorldPosition(x, y, z)
        EndTextCommandDisplayText(_x,_y)
    end 
end


```