---
for: shared
---
## ReadCSV

```lua
ReadCSV(path,translationkeys)
```

## Snippet Code
```
ReadCSV = function(path,translationkeys)
    local ln = 1
    local keys = translationkeys or {}
    local k = 1 
    local data = {}
    for line in io.lines(GetResourcePath(GetCurrentResourceName()).."/"..path) do
        local s = line 
        local delimiter = ","
        if ln == 1 then 
            if not translationkeys then
                for match in (s..delimiter):gmatch('(.-)'..delimiter) do
                    table.insert(keys, match);
                end
            end 
        else 
            k = 1
            for match in (s..delimiter):gmatch('(.-)'..delimiter) do
                if not data[ln-1] then data[ln-1] = {} end 
                local isNumber = tostring(tonumber(match)) == match 
                data[ln-1][keys[k]] = isNumber and tonumber(match) or match 
                k = k + 1
            end
        end 
        ln = ln + 1
    end
    ln = 1
    return data
end 




```

## Example 
```
RegisterServerCallback("getList",function(source,cb,name)
    local List = ReadCSV(name..".csv")
    cb(List)
end) 

```

