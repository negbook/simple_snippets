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