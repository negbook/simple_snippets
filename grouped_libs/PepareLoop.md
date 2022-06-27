---
for: shared
---
## PepareLoop

```lua
handle = PepareLoop(duration)
```

## Snippet Code
```
--Credit: negbook
--https://github.com/negbook/simple_snippets/blob/main/grouped_libs/PepareLoop.md
local init = load(
    [===[
local _M_ = {}
do 
local Tasksync = _M_
local Loops = {}
local FnTask = {}
local FnOnDelete = {}
local FnCustomDurations = {}

local FnReleaseTimer = {}
setmetatable(Loops,{__newindex=function(t,k,v) rawset(t,tostring(k),v) end,__index=function(t,k) return rawget(t,tostring(k)) end})

local GetDurationAndIndex = function(id,cb) for duration,names in pairs(Loops) do for i=1,#names do local v = names[i] if v == id then local duration_tonumber = tonumber(duration) if cb then cb(duration_tonumber,i) end return duration_tonumber,i end end end end
local remove_manual = function(duration,index) local indexs = Loops[duration] table.remove(indexs,index) if #indexs == 0 then Loops[duration] = nil end end 
local remove = function(id,cb) GetDurationAndIndex(id,function(duration,index) remove_manual(duration,index) if cb then cb() end end) end 
local init = function(duration,id,cb) if Loops[duration] == nil then Loops[duration] = {}; if cb then cb() end end table.insert(Loops[duration],id) end 

local updateloop = function(id,new_duration,cb)
    remove(id,function()
        init(new_duration,id,function()
            Tasksync.__createNewThreadForNewDurationLoopFunctionsGroup(new_duration,cb)
        end)
    end)
end 

local ref = function (default,current_id)
    local value = default or 0 
    return function(action,v) 
        if action == 'get' then 
            return value 
        elseif action == 'set' then 
            if value ~= v then 
                value = v 
                Tasksync.setloopcustomduration(current_id,v) ;
            end 
        elseif action == 'kill' or action == 'break' then 
            Tasksync.deleteloop(current_id)
        end 
    end 
end 

Tasksync.__createNewThreadForNewDurationLoopFunctionsGroup = function(duration,init)
    local init = init   
    CreateThread(function()
        local loop = Loops[duration]
        local e = {}
        repeat if init then init() init = nil end
            local ID_list = (loop or e)
            local isAnyJob = ID_list[1]
            if isAnyJob then 
                for i=1,#ID_list do 
                    local current_id = ID_list[i]
                    local f = FnTask[current_id]
                    if f then 
                        local obj = ref(duration,current_id) 
                        f(obj)
                    end 
                end 
            end 
            Wait(duration)
            
        until not isAnyJob 
        --print("Deleted thread",duration)
        return 
    end)
end     

Tasksync.addloop = function(duration,fn,fnondelete,isreplace)
    local id = fn ; if FnTask[id] ~= nil and isreplace then remove(id) end 
    
    FnTask[id] = fn  

    if fnondelete then 
        FnOnDelete[id] = fnondelete  
    end 
    
    FnReleaseTimer[id] = nil 
    
    init(duration,id,function()
        Tasksync.__createNewThreadForNewDurationLoopFunctionsGroup(duration)
    end)
    return id
end 
Tasksync.insertloop = Tasksync.addloop

Tasksync.deleteloop = function(id)
    local id = id
    remove(id,function()
        FnTask[id] = nil  
        if FnOnDelete[id] then 
            FnOnDelete[id](id) 
            FnOnDelete[id] = nil 
        end 
        if FnReleaseTimer[id] then 
            FnReleaseTimer[id] = nil 
            
        end 
    end)
end 
Tasksync.removeloop = Tasksync.deleteloop

Tasksync.setloopcustomduration = function(id,duration)
    local old_duration = FnCustomDurations[id]
    if duration ~= old_duration then 
        FnCustomDurations[id] = duration
        updateloop(id,duration,function()
            Wait(old_duration)
        end)
    end 
end 
Tasksync.getloopcustomduration = function(id)
    return FnCustomDurations[id]
end 

 
local newreleasetimer = function(id,timer,cb)
    local releasetimer = timer   + GetGameTimer()
    local id = id 
    local tempcheck = Tasksync.PepareLoop(250)  
    tempcheck(function(duration)
        if GetGameTimer() > releasetimer then 
            tempcheck:delete()
            Tasksync.deleteloop(id)
        end 
    end,cb)
    return function(action,value)
        if action == "get" then 
            return releasetimer
        elseif action == "set" then 
            releasetimer = timer + GetGameTimer()
        end 
    end 
end  
Tasksync.setreleasetimer = function(id,releasetimer)
    if not FnReleaseTimer[id] then 
        FnReleaseTimer[id] = newreleasetimer(id,releasetimer,function()
            FnReleaseTimer[id] = nil
        end) 
    else 
        FnReleaseTimer[id]("set",releasetimer)
    end 

end 

Tasksync.PepareLoop = function(duration,releasecb)
    local self = {}
    local fn = nil 
    self.add = function(self,_fn,_fnondelete)
        fn = _fn
        local ontaskdelete = nil
        if not _fnondelete then 
            if releasecb then 
                ontaskdelete = function(id)
                    releasecb(id)
                end 
            end
        else 
            if releasecb then 
                ontaskdelete = function(id)
                    releasecb(id)
                    _fnondelete(id)
                end 
            else 
                ontaskdelete = _fnondelete
            end
        end

        return Tasksync.addloop(duration,fn,ontaskdelete)
    end
    self.delete = function(self,duration)
        if fn then 
            if duration then 
                Tasksync.setreleasetimer(fn,duration) 
            else 
                Tasksync.deleteloop(fn) 
            end 
        end
    end
    self.release = self.delete
    self.remove = self.delete
    self.kill = self.delete
    self.set = function(self,newduration)
        if fn then Tasksync.setloopcustomduration(fn,newduration) end 
    end
    self.get = function(self)
        if fn then return Tasksync.getloopcustomduration(fn) end 
    end

    return setmetatable(self,{__call = function(self,...)
        return self:add(...)
    end})
end
end

PepareLoop = _M_.PepareLoop

return PepareLoop(...)
        ]===]
)
PepareLoop = PepareLoop or function(...)
    return init(...)
end
```

## Example 
```

local Loop = PepareLoop(1000)
local Loop2 = PepareLoop(1000)

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

