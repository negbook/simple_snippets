---
for: shared
---
## TriggerServerCallback

```lua
TriggerServerCallback(name,cb,...)
```

## Snippet Code
```
if IsDuplicityVersionv() then 
    ClientCommand = setmetatable({},{__newindex=function(t,k,fn) RegisterCommand(k,function(source, args, raw) if source>0 then fn(source,table.unpack(args)) end end) return end })
    ServerCommand = setmetatable({},{__newindex=function(t,k,fn) RegisterCommand(k,function(source, args, raw) if source>0 then else fn(table.unpack(args)) end end) return end })
    SharedCommand = setmetatable({},{__newindex=function(t,k,fn) RegisterCommand(k,function(source, args, raw) fn(source,table.unpack(args)) end) return end })
else 
    Command = setmetatable({},{__newindex=function(t,k,fn) RegisterCommand(k,function(source, args, raw) fn(table.unpack(args)) end) return end })
end 
```

## Example 
```
Command["tp"] = function(x,y,z)
    if x then 
        SetPedCoordsKeepVehicle(PlayerPedId(), x+0.0, y+0.0, z+0.0)
    else 
        local WaypointHandle = GetFirstBlipInfoId(8)
        if DoesBlipExist(WaypointHandle) then
            local coords = GetBlipInfoIdCoord(WaypointHandle)
            local x,y,z = coords.x,coords.y,coords.z 
            local bottom,top = GetHeightmapBottomZForPosition(x,y), GetHeightmapTopZForPosition(x,y)
            local steps = (top-bottom)/100
            local foundGround
            local height = bottom + 0.0
            while not foundGround and height < top  do 
                SetPedCoordsKeepVehicle(PlayerPedId(), x,y, height )
                foundGround, zPos = GetGroundZFor_3dCoord(x,y, height )
                height = height + steps
                Wait(0)
            end 
            SetPedCoordsKeepVehicle(PlayerPedId(), x,y, height )
            print('TP(Marker/GPS)',vector3(x,y, height))
        else 
            local coords = FindRandomPointInSpace(PlayerPedId())
            local x,y,z = coords.x,coords.y,coords.z 
            local bottom,top = GetHeightmapBottomZForPosition(x,y), GetHeightmapTopZForPosition(x,y)
            local steps = (top-bottom)/100
            local foundGround
            local height = bottom + 0.0
            while not foundGround and height < top  do 
                SetPedCoordsKeepVehicle(PlayerPedId(), x,y, height )
                foundGround, zPos = GetGroundZFor_3dCoord(x,y, height )
                height = height + steps
                Wait(0)
            end 
            SetPedCoordsKeepVehicle(PlayerPedId(), x,y, height )
            print('TP(Random)',vector3(x,y, height))
        end 
    end 
end
```

```
ServerCommand["trace"] = function(a)   
    local rawprint = print
    if a then 
        local text = a:gmatch("`(.-)`")()
        if string.find(a,"`") then 
            local hash = GetHashKey(text)
            print("Hash",hash,"Hash in HEX",string.format("0x%x", hash))
        elseif string.find(a,"0x")==1 then 
            print("Hex",a,"Number",tonumber(a))
        elseif tostring(tonumber(a)) == tostring(a) then 
            print("Number",a,"HEX",string.format("0x%x", tonumber(a)))
        else 
        end 
    end 
end
```