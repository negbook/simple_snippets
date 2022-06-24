---
for: shared
---
## RequestLoopThread

```lua
handle = RequestLoopThread(duration)
```

## Snippet Code
```
--Credit: negbook
--https://github.com/negbook/simple_snippets/blob/main/grouped_libs/RequestLoopThread.md
local Loops = {}
local Fn = {}
local FnOnDelete = {}
local FnCustomDurations = {}

local GetDurationStringAndIndexFromLoopsIfFnNameExisted = function(id)
    for existeddurationStr,names in pairs(Loops) do 
        for i=1,#names do 
            local v = names[i]
            if v == id then 
                return existeddurationStr,i
            end 
        end 
    end 
end 

local removeFnNameFromDurationGroup = function(_durationStr,idx)
    table.remove(Loops[_durationStr],idx) 
    if #Loops[_durationStr] == 0 then 
        Loops[_durationStr] = nil
    end 
    
end 

local DeleteExistedFnNameIfDurationBecomeDiff = function(id,_durationStr)
    local existeddurationStr,idx = GetDurationStringAndIndexFromLoopsIfFnNameExisted(id)
    if existeddurationStr and _durationStr ~= existeddurationStr then 
        removeFnNameFromDurationGroup(existeddurationStr,idx)
        return true 
    end 
end 

local function newObject(default)
    local value = default or 0
    local haschange = false 
    local haskilled = false 
    return function(action,v) 
        if action == 'get' then 
            if v then 
                local temphaschange = haschange
                local temphaskilled = haskilled
                haschange = false
                haskilled = false
                return value , temphaschange, temphaskilled
            else
                return value  
            end 
        elseif action == 'set' then 
            if value ~= v then 
                haschange = true
                value = v 
            else 
                haschange = false 
            end 
        elseif action == 'kill' or action == 'break' then 
            haskilled = true 
        end 
    end 
end 

local __createNewThreadForNewDurationLoopFunctionsGroup = function(_durationStr)
    CreateThread(function()
        local loop = Loops[_durationStr]
        local _durationNum = tonumber(_durationStr)
        local e = {}
        local setgeters = {}
        setmetatable(setgeters,{__mode = "k"})
        repeat  
            local fnList = (loop or e)
            local isAnyJob = fnList[1]
            if isAnyJob then 
                for i=1,#fnList do 
                    local _nowfnname = fnList[i]
                    local f = Fn[_nowfnname]
                    if f then 
                        if setgeters[_nowfnname] == nil then 
                            setgeters[_nowfnname] = newObject(_durationNum) 
                            
                        end 
                        local duration_editor = setgeters[_nowfnname]
                        f(duration_editor)
                        local durationset,haschange,haskilled = duration_editor('get',true)
                        if haskilled then 
                            setgeters[_nowfnname] = nil
                            deleteloop(_nowfnname)
                        elseif haschange then 
                            setgeters[_nowfnname] = nil
                            setloopcustomduration(_nowfnname,durationset) ;
                        end 
                    end 
                end 
            end 
            Wait(_durationNum)
            
        until not isAnyJob 
        setgeters = nil
        --print("Deleted thread",_durationStr)
        return 
    end)
end     

local addloop = function(_duration,_fn,_fnondelete)
    local id = _fn
    if Fn[id] ~= nil  then 
        return 
    end
    
    Fn[id] = _fn  
    
    if _fnondelete then 
        FnOnDelete[id] = _fnondelete  
    end 
    
    local _durationStr = tostring(_duration)
    
    if Loops[_durationStr] == nil then 
        Loops[_durationStr] = {}; 
        __createNewThreadForNewDurationLoopFunctionsGroup(_durationStr)
    end 
    table.insert(Loops[_durationStr],id)
    return id
end 
local insertloop = addloop

local deleteloop = function(id)
    
    local durationStr,idx = GetDurationStringAndIndexFromLoopsIfFnNameExisted(id)
    if durationStr then 
        Fn[id] = nil 
        removeFnNameFromDurationGroup(durationStr,idx)
        if FnOnDelete[id] then 
            FnOnDelete[id]() 
            FnOnDelete[id] = nil 
        end 
    end 
end 

local isloopalive = function(id)
   
    local durationStr,idx = GetDurationStringAndIndexFromLoopsIfFnNameExisted(id)
    if durationStr then 
        return true 
    end 
     
    return false 
end 

local updateloop = function(id,_newduration)
    local _olddurationStr,idx = GetDurationStringAndIndexFromLoopsIfFnNameExisted(id)
    local _newdurationStr = tostring(_newduration)
    if _olddurationStr ~= _newdurationStr then  
        removeFnNameFromDurationGroup(_olddurationStr,idx)
        if Loops[_newdurationStr] == nil then 
            Loops[_newdurationStr] = {}; 
            -- this is important to wait after just set a new duration  
            Wait(tonumber(_olddurationStr))
            -- Very Important to Wait because CreateThread newduration is diff from oldduration
            __createNewThreadForNewDurationLoopFunctionsGroup(_newdurationStr)
        end 
        table.insert(Loops[_newdurationStr],id)
        return 
    end 
end 

setloopcustomduration = function(id,_duration)
    FnCustomDurations[id] = _duration
    updateloop(id,_duration)
end 
getloopcustomduration = function(id)
    return FnCustomDurations[id]
end 
hasloopcustomduration = function(id)
    return FnCustomDurations[id] ~= nil
end 


local RequestLoopThread = function(duration)
    local fn = nil
    local self = {}

    self.add = function(self,_fn,fnondelete)
        fn = _fn
        return addloop(duration,fn,fnondelete)
    end
    self.delete = function(self)
        deleteloop(fn)
    end
    self.set = function(self,newduration)
        setloopcustomduration(fn,newduration)
    end
    self.get = function(self)
        return getloopcustomduration(fn)
    end
    self.has = function(self)
        return hasloopcustomduration(fn)
    end
    return setmetatable(self,{__call = function(self,...)
        return self:add(...)
    end})
end
```

## Example 
```

local Loop = RequestLoopThread(1000)
local Loop2 = RequestLoopThread(1000)

Loop(function(duration)
    print("Loop test "..duration("get"))
    print(Loop)
end)

Loop2(function(duration)
    print("Loop2 test "..duration("get"))
    duration("set",500)

end )

Loop2:delete()

```

