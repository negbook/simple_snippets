---
for: client
---
## TriggerServerCallback

```lua
TriggerServerCallback(name,cb,...)
```

## Snippet Code
```
TriggerServerCallback =   function(name,cb,...)
    local p = promise.new() 
    local uuid = name
    TriggerServerEvent("n"..GetCurrentResourceName()..':RequestServerCallback',uuid,...)
    local tempEvent; tempEvent=RegisterNetEvent("n"..GetCurrentResourceName()..':ServerCallbackResultTo:'..uuid, function(...)
        local args = {...}
        if args[1] == nil then 
            p:reject() 
            RemoveEventHandler(tempEvent)
        else 
            p:resolve({...})
            RemoveEventHandler(tempEvent)
        end 
    end)
    cb(table.unpack(Citizen.Await(p)))
end

TriggerServerCallbackSynced = function(name,cb,...)
    local p = promise.new() 
    TriggerServerCallback(name, function(...)
    	cb(...)
        p:resolve({...})
    end, ...)
    return table.unpack(Citizen.Await(p)) 
end  
```

## Example 
```
CreateThread(function()
    TriggerServerCallback("Register",function(data)
        print(data)
    end,1,2,3)
end)
```
---
for: server
---
## RegisterServerCallback

```lua 
RegisterServerCallback = function(name,cb)
```

## Snippet Code 
```
Callbacks = {}
CurrentIndex = 0
RegisterServerCallback = function(name,cb)
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

RegisterNetEvent("n"..GetCurrentResourceName()..':RequestServerCallback', function(uuid,...)
    local Client = source
    local result
    local args = {...}
    for i,v in pairs(Callbacks) do 
        if v.uuid == uuid then 
            result = v.callback(Client,function(...)
                --cb method
                TriggerClientEvent("n"..GetCurrentResourceName()..':ServerCallbackResultTo:'..uuid,Client,...)
            end,table.unpack(args))
            
            break
        end 
    end
    --sync method (return on RegisterServerCallback)
    if result then
        TriggerClientEvent("n"..GetCurrentResourceName()..':ServerCallbackResultTo:'..uuid,Client,result)
    end 
end)

```

## Example
```
RegisterServerCallback("Register",function(source,cb,...)
	print(...)
    cb(999)
end )
```
