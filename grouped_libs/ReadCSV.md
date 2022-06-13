---
for: shared
---
## ReadCSV

```lua
ReadCSV(path,translationkeys)
```

## Snippet Code
```
local function fromLine(s)
  --http://lua-users.org/wiki/CsvUtils
  s = s .. ','        -- ending comma
  local t = {}        -- table to collect fields
  local fieldstart = 1
  repeat
	-- next field is quoted? (start with `"'?)
	if string.find(s, '^"', fieldstart) then
	  local a, c
	  local i  = fieldstart
	  repeat
		-- find closing quote
		a, i, c = string.find(s, '"("?)', i+1)
	  until c ~= '"'    -- quote not followed by quote?
	  if not i then error('unmatched "') end
	  local f = string.sub(s, fieldstart+1, i-1)
	  table.insert(t, (string.gsub(f, '""', '"')))
	  fieldstart = string.find(s, ',', i) + 1
	else                -- unquoted; find next comma
	  local nexti = string.find(s, ',', fieldstart)
	  table.insert(t, string.sub(s, fieldstart, nexti-1))
	  fieldstart = nexti + 1
	end
  until fieldstart > string.len(s)
  return t
end
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
            local linedata = fromLine(line)
            for i=1,#linedata do
                local v = linedata[i]
                if not data[ln-1] then data[ln-1] = {} end 
                local isNumber = tostring(tonumber(v)) == v 
                data[ln-1][keys[k]] = isNumber and tonumber(v) or v 
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

