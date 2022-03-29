---
for: client
---
## TriggerServerCallback

```lua
TriggerServerCallback(name,cb,...)
```

## Snippet Code
```
TriggerServerCallback = function(name,cb,...)
    local p = promise.new() 
    local uuid = GenByString(name)
    TriggerServerEvent('nPrefix:RequestServerCallback',uuid,...)
    local tempEvent; tempEvent=RegisterNetEvent('nPrefix:ServerCallbackResultTo:'..uuid, function(...)
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
	local uuid = GenByString(name)
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

RegisterNetEvent("nPrefix:RequestServerCallback", function(uuid,...)
	local Client = source
	local result
	local args = {...}
	for i,v in pairs(Callbacks) do 
		if v.uuid == uuid then 
			result = v.callback(Client,function(result)
				--cb method
				TriggerClientEvent("nPrefix:ServerCallbackResultTo:"..uuid,Client,result)
			end,table.unpack(args))
			
			break
		end 
	end
	--sync method
	TriggerClientEvent("nPrefix:ServerCallbackResultTo:"..uuid,Client,result)
end)

```