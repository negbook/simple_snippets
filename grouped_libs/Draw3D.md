---
for: client
---
## Draw3D

```lua
DrawRectangle3D (point1,point2,point3,point4, r, g, b, a, isoutline)
DrawPoly3D (x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a, isoutline)
DrawSphere3D (x,y,z, radius, r,g,b,a)
DrawCircle3D (x,y,z, radius, rotation, r,g,b,a, height)
DrawLine3D (x1,y1,z1, x2,y2,z2, r, g, b, a)
DrawPixel3D (x,y,z, r, g, b, a)
DrawBox3D (center, size, rotation, r, g, b, a)
DrawText3D (x, y, z, text, size, font, r, g, b, a, isfloating)
```

## Snippet Code

```

local function _drawRectangle3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a, isoutline)
    DrawPoly(x1,y1,z1,x2,y2,z2, x3,y3,z3, r, g, b, a) -- ABC
    DrawPoly(x3,y3,z3,x4,y4,z4, x1,y1,z1, r, g, b, a) -- CDA
    DrawPoly(x1,y1,z1,x4,y4,z4, x3,y3,z3, r, g, b, a) -- ACD
    DrawPoly(x3,y3,z3,x2,y2,z2, x1,y1,z1, r, g, b, a) -- BAC
 
    if isoutline then 
        DrawLine(x1,y1,z1,x2,y2,z2,r,g,b,a*2 ) -- AB
        DrawLine(x2,y2,z2,x3,y3,z3,r,g,b,a*2 ) -- BC
        DrawLine(x3,y3,z3,x4,y4,z4,r,g,b,a*2 ) -- CD
        DrawLine(x4,y4,z4,x1,y1,z1,r,g,b,a*2 ) -- DA
    end 

end

function DrawRectangle3D (...)
    local args = {...}
    local n = #args
    if not (n > 8) then 
        local coords1,coords2,coords3,coords4, r, g, b, a, isoutline = ...
        x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4 = coords1.x,coords1.y,coords1.z, coords2.x,coords2.y,coords2.z, coords3.x,coords3.y,coords3.z, coords4.x,coords4.y,coords4.z
        _drawRectangle3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a, isoutline)
    else 
        local x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a, isoutline = ...
        _drawRectangle3D(x1,y1,z1, x2,y2,z2 , x3,y3,z3, x4,y4,z4, r, g, b, a, isoutline)
    end 
    
end

function DrawPoly3D(...)
    local args = {...}
    local n = #args
    
    if not (n > 8) then 
        local coords1, coords2, coords3, r, g, b, a, isoutline = ...
        x1,y1,z1, x2,y2,z2 , x3,y3,z3 = coords1.x, coords1.y, coords1.z, coords2.x, coords2.y, coords2.z, coords3.x, coords3.y, coords3.z
        DrawPoly(x1,y1,z1,x2,y2,z2, x3,y3,z3, r, g, b, a) -- ABC
        DrawPoly(x3,y3,z3,x2,y2,z2, x1,y1,z1, r, g, b, a) -- BAC
        if isoutline then 
            DrawLine(x1,y1,z1,x2,y2,z2,r,g,b,a*2 ) -- AB
            DrawLine(x2,y2,z2,x3,y3,z3,r,g,b,a*2 ) -- BC
        end 
    else 
        local x1,y1,z1, x2,y2,z2 , x3,y3,z3, r, g, b, a, isoutline = ...
        DrawPoly(x1,y1,z1,x2,y2,z2, x3,y3,z3, r, g, b, a) -- ABC
        DrawPoly(x3,y3,z3,x2,y2,z2, x1,y1,z1, r, g, b, a) -- BAC
        if isoutline then 
            DrawLine(x1,y1,z1,x2,y2,z2,r,g,b,a*2 ) -- AB
            DrawLine(x2,y2,z2,x3,y3,z3,r,g,b,a*2 ) -- BC
        end 
    end 
    
end


function DrawSphere3D (x,y,z, radius, r,g,b,a)
    --DrawSphere(x,y,z, radius, r,g,b,_a)
    DrawMarker(28, x,y,z, 0.0, 0.0, 0.0, 0.0, 0.0, 0.0, radius, radius, radius, r,g,b,a, false, false, 2, false, false, false, false)
end 

local _drawCircle = function(x,y,z, radius, rotation, r,g,b,a, height)
    DrawMarker(1, x,y,z, 0.0, 0.0, 0.0, rotation.x,rotation.y,rotation.z, radius*2, radius*2, height or 0.1, r,g,b,a, false, false, 2, false, false, false, false)
end

DrawCircle3D = function(x,y,z, radius, rotation, r,g,b,a, height)
    _drawCircle(x,y,z, radius, rotation, r,g,b,a, height)
end

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
    local quaternionXaxis = quat(rotation.x, vector3(1, 0, 0))
    local quaternionYaxis = quat(rotation.y, vector3(0, 1, 0))
    local quaternionZaxis = quat(rotation.z, vector3(0, 0, 1))
    local quaternion = quaternionXaxis * quaternionYaxis * quaternionZaxis
    local point1 = vector3(x-w/2,y-h/2,z-d/2)
    local point2 = vector3(x+w/2,y-h/2,z-d/2)
    local point3 = vector3(x+w/2,y+h/2,z-d/2)
    local point4 = vector3(x-w/2,y+h/2,z-d/2)
    local point5 = vector3(x-w/2,y-h/2,z+d/2)
    local point6 = vector3(x+w/2,y-h/2,z+d/2)
    local point7 = vector3(x+w/2,y+h/2,z+d/2)
    local point8 = vector3(x-w/2,y+h/2,z+d/2)
    point1 = point1 * quaternion + center 
    point2 = point2 * quaternion + center
    point3 = point3 * quaternion + center
    point4 = point4 * quaternion + center
    point5 = point5 * quaternion + center
    point6 = point6 * quaternion + center
    point7 = point7 * quaternion + center
    point8 = point8 * quaternion + center
    DrawRectangle3D(point1, point2 , point3, point4, r, g, b, a)
    DrawRectangle3D(point5, point6 , point7, point8, r, g, b, a)
    DrawRectangle3D(point1, point5 , point6, point2, r, g, b, a)
    DrawRectangle3D(point2, point6 , point7, point3, r, g, b, a)
    DrawRectangle3D(point3, point7 , point8, point4, r, g, b, a)
    DrawRectangle3D(point4, point8 , point5, point1, r, g, b, a)
end

function DrawPolygon(coords,vertices,hight,r,g,b,a)
    local r,g,b,a = r or 255, g or 255, b or 255, a or 255
    local center = vector3(coords.x, coords.y, coords.z)
    
    local pos = coords
    local rotation= vector3(0.0,0.0,0.0)
    local points = vertices
    
    
     --draw first edge
     DrawLine(points[#points].x,points[#points].y,pos.z,points[1].x,points[1].y,pos.z,r,g,b,a)
     DrawLine(points[#points].x,points[#points].y,pos.z+hight,points[1].x,points[1].y,pos.z+hight,r,g,b,a)
     DrawLine(points[#points].x,points[#points].y,pos.z,points[#points].x,points[#points].y,pos.z+hight,r,g,b,a)
     DrawLine(points[1].x,points[1].y,pos.z,points[1].x,points[1].y,pos.z+hight,r,g,b,a)
     --draw first edge end 
 
     for i=1,#points do
         local next = i+1
         if next > #points then next = 1 end
         DrawLine(points[i].x,points[i].y,pos.z,points[next].x,points[next].y,pos.z,r,g,b,a)
         DrawLine(points[i].x,points[i].y,pos.z+hight,points[next].x,points[next].y,pos.z+hight,r,g,b,a)
         DrawLine(points[i].x,points[i].y,pos.z,points[i].x,points[i].y,pos.z+hight,r,g,b,a)
         DrawLine(points[next].x,points[next].y,pos.z,points[next].x,points[next].y,pos.z+hight,r,g,b,a)
     end
 
     --also draw first edge with DrawRectangle3DReverseOutline 
     local point1 = vector3(points[#points].x,points[#points].y,pos.z)
     local point2 = vector3(points[1].x,points[1].y,pos.z)
     local point3 = vector3(points[1].x,points[1].y,pos.z+hight)
     local point4 = vector3(points[#points].x,points[#points].y,pos.z+hight)
     DrawRectangle3DReverseOutline(point1.x,point1.y,point1.z,point2.x,point2.y,point2.z,point3.x,point3.y,point3.z,point4.x,point4.y,point4.z,r,g,b,a)
     --end 
 
     for i=1,#points do
         local next = i+1
         if next > #points then next = 1 end
         local point1 = vector3(points[i].x,points[i].y,pos.z)
         local point2 = vector3(points[next].x,points[next].y,pos.z)
         local point3 = vector3(points[next].x,points[next].y,pos.z+hight)
         local point4 = vector3(points[i].x,points[i].y,pos.z+hight)
         DrawRectangle3DReverseOutline(point1.x,point1.y,point1.z,point2.x,point2.y,point2.z,point3.x,point3.y,point3.z,point4.x,point4.y,point4.z,r,g,b,a)
     end
 
     --drawbottom and top with DrawPoly3DReverse 
 
     for i=1,#points do
         local next = i+1
         if next > #points then next = 1 end
         local point1 = vector3(points[i].x,points[i].y,pos.z)
         local point2 = vector3(points[next].x,points[next].y,pos.z)
         DrawPoly3DReverse(center.x,center.y,center.z,point1.x,point1.y,point1.z,point2.x,point2.y,point2.z,r,g,b,50)
         DrawPoly3DReverse(center.x,center.y,center.z + hight,point1.x,point1.y,point1.z+ hight,point2.x,point2.y,point2.z+ hight,r,g,b,50)
     end


    

    
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