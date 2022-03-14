---
for: client
---
## CreateObjectAttachedBone

```lua
CreateObjectAttachedBone(distance)
```

## Snippet Code

```
function CreateObjectAttachedBone(ped,objectHash,boneName,offsetx,offsety,offsetz,rotationx,rotationy,rotationz)
  local hash = objectHash
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
      local pos = GetOffsetFromEntityInWorldCoords(ped, 0.0, 0.0, 0.0) --may need
      local object = CreateObject(hash, pos.x, pos.y, pos.z, true, false)
      AttachEntityToEntity(object, ped, GetEntityBoneIndexByName(ped,boneName), offsetx,offsety,offsetz,rotationx,rotationy,rotationz, true, true, false, true, 1, true)
      return object
  else 
     error("Something wrong with loading object"..hash,1)
  end
end 
```

### Example
```
CreateThread(function()
    local object = CreateObjectAttachedBone(PlayerPedId(),`prop_knife`,"BONETAG_R_PH_HAND",0.1,0.1,0.0,0.0,0.0,0.0)
    Wait(5000)
    DetachEntity(object, true, true)
end)
```

