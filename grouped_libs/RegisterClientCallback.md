---
for: client
---
## TriggerClientCallback

```lua
TriggerClientCallback(client,name,cb,...)
```

## Snippet Code
```
local Callbacks = {}
local CurrentIndex = 0
RegisterClientCallback = function(name,cb)
    local uuid = name
    Callbacks[CurrentIndex+1] = {
        uuid = uuid,
        callback = cb,
    }
    CurrentIndex = CurrentIndex + 1
    if CurrentIndex > 2^15 then 
        CurrentIndex = 0
    end
    return uuid
end

RegisterNetEvent("n"..GetCurrentResourceName()..':RequestClientCallback'..GetPlayerServerId(PlayerId()), function(uuid,...)
    local result
    local args = {...}
    for i,v in pairs(Callbacks) do 
        if v.uuid == uuid then 
            result = v.callback(function(...)
                --cb method
                TriggerServerEvent("n"..GetCurrentResourceName()..':ClientCallbackResultTo:'..uuid..GetPlayerServerId(PlayerId()),...)
            end,table.unpack(args))
            
            break
        end 
    end
    --sync method (return on RegisterServerCallback)
    if result then
        TriggerServerEvent("n"..GetCurrentResourceName()..':ClientCallbackResultTo:'..uuid,result)
    end 
end)
```

## Example 
```
RegisterClientCallback("test",function(cb,...)
    print(...)
    cb("wow",888,321)
end)
```
---
for: server
---
## RegisterServerCallback

```lua 
TriggerClientCallback = function(client,name,cb,...)
```

## Snippet Code 
```
TriggerClientCallback = function(name,client,cb,...)
    local p = promise.new() 
    local uuid = name
    TriggerClientEvent("n"..GetCurrentResourceName()..':RequestClientCallback'..client,client,uuid,...)
    local tempEvent; tempEvent=RegisterNetEvent("n"..GetCurrentResourceName()..':ClientCallbackResultTo:'..uuid..client, function(...)
        local args = {...}
        if args[1] == nil or tonumber(client) ~= tonumber(source) then 
            p:reject() 
            RemoveEventHandler(tempEvent)
        else 
            p:resolve({...})
            RemoveEventHandler(tempEvent)
        end 
    end)
    cb(table.unpack(Citizen.Await(p)))
end

TriggerClientCallbackSynced = function(name,client,cb,...)
    local p = promise.new() 
    TriggerClientCallback(name,client, function(...)
        if cb then cb(...) end
        p:resolve({...})
    end, ...)
    return table.unpack(Citizen.Await(p)) 
end 

```

## Example
```
CreateThread(function()
    Wait(1000)
    print(TriggerClientCallbackSynced(1,"test",function(...)
        print(333,...)
    end,777,777,444))

end)
```
