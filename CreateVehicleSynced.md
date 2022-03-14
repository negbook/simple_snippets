---
for: client
---
## CreateVehicleSynced

```lua
CreateVehicleSynced(veh,DriveImmediately)
```

## Snippet Code
```
function CreateVehicleSynced(veh,DriveImmediately)
    local x,y,z = table.unpack(GetOffsetFromEntityInWorldCoords(PlayerPedId(), 0.0, 8.0, 0.5))
    if veh == nil then veh = `adder` end
    hash = type(veh) == 'number' and veh or GetHashKey(veh)
    RequestModel(hash)
    local p = promise.new()
    CreateThread(function()
      local attempt = 0
      local loaded = HasModelLoaded(hash)
      while not loaded and attempt < 10 do 
          loaded = HasModelLoaded(hash)
          attempt = attempt + 1
          Wait(50)
      end 
      if p then p:resolve(loaded) end
    end)
    local success = Citizen.Await(p)
    if success then 
        if DriveImmediately then 
            local vehicle = CreateVehicle(hash, x, y, z, GetEntityHeading(PlayerPedId()), 1, 0)
            SetPedIntoVehicle(PlayerPedId(), vehicle, -1);
            return vehicle
        else 
            local vehicle = CreateVehicle(hash, x, y, z, GetEntityHeading(PlayerPedId())+90, 1, 0)
            return vehicle
        end 
    else 
        error("Something wrong with loading vehicle"..hash,1)
    end 
end 
```

