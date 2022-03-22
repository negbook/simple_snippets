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
	AddTextEntry("RequestKeyboardInput", title .. " "..description) --Sets the Text above the typing field in the black square
	DisplayOnscreenKeyboard(1, "RequestKeyboardInput", "", "", "", "", "", maxlength) --Actually calls the Keyboard Input
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




