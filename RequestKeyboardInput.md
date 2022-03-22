---
for: client
---
## RequestKeyboardInput

```lua
RequestKeyboardInput(title, description, maxlength)
```

## Snippet Code
```
function RequestKeyboardInput(title, description, maxlength)
    --UpdateOnscreenKeyboard 2 = cancel 1 = enter 0 = normal
    AddTextEntry("RequestKeyboardInput", title .. " "..description)
    DisplayOnscreenKeyboard(1, "RequestKeyboardInput", "", "", "", "", "", maxlength)
    local p = promise.new()
    CreateThread(function()
        while p do
            Wait(100)
            if UpdateOnscreenKeyboard() == 1 then
                local result = GetOnscreenKeyboardResult()
                CancelOnscreenKeyboard()
                p:resolve(result)
                p = nil 
            elseif UpdateOnscreenKeyboard() == 2 then
                CancelOnscreenKeyboard()
                p:reject()
                p = nil 
            end 
        end
    end)
    return Citizen.Await(p)
end
```




