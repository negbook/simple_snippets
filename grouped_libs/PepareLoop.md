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
local _M_ = {}
do 
local Tasksync = _M_
local Loops = {}
local e = {}
setmetatable(Loops,{__newindex=function(t,k,v) rawset(t,tostring(k),v) end,__index=function(t,k) return rawget(t,tostring(k)) end})
setmetatable(e,{__call=function()end})

local GetDurationAndIndex = function(obj,cb) for duration,names in pairs(Loops) do for i=1,#names do local v = names[i] if v == obj then local duration_tonumber = tonumber(duration) if cb then cb(duration_tonumber,i) end return duration_tonumber,i end end end end
local remove_manual = function(duration,index) local indexs = Loops[duration] table.remove(indexs,index) if #indexs == 0 then Loops[duration] = nil end end 
local remove = function(obj,cb) GetDurationAndIndex(obj,function(duration,index) remove_manual(duration,index) if cb then cb() end end) end 
local init = function(duration,obj,cb) if Loops[duration] == nil then Loops[duration] = {}; if cb then cb() end end table.insert(Loops[duration],obj) end 
local newloopobject = function(duration,onaction,ondelete)
    local onaction = onaction 
    local ondelete = ondelete 
    local duration = duration 
    local releaseobject = nil 
    local ref = nil 
    if onaction and ondelete then 
        return function (action,value)
            if not action or action == "onaction" then 
                return onaction(ref)
            elseif action == "ondelete" then 
                return ondelete()
            elseif action == "setduration" then 
                duration = value 
            elseif action == "getduration" then 
                return duration 
            elseif action == "getfn" then 
                return onaction 
            elseif action == "setref" then 
                ref = value
            elseif action == "setreleasetimerobject" then 
                releaseobject = value 
            elseif action == "getreleasetimerobject" then 
                return releaseobject
            end 
        end 
    elseif onaction and not ondelete then 
        return function (action,value)
            if action == "onaction" then 
                return onaction(ref)
            elseif action == "setduration" then 
                duration = value 
            elseif action == "getduration" then 
                return duration 
            elseif action == "getfn" then 
                return onaction 
            elseif action == "setref" then 
                ref = value
            elseif action == "setreleasetimerobject" then 
                releaseobject = value 
            elseif action == "getreleasetimerobject" then 
                return releaseobject
            end 
        end 
    end 
end 


local updateloop = function(obj,new_duration,cb)
    remove(obj,function()
        init(new_duration,obj,function()
            Tasksync.__createNewThreadForNewDurationLoopFunctionsGroup(new_duration,cb)
        end)
    end)
end 

local ref = function (default,obj)
    return function(action,v) 
        if action == 'get' then 
            return obj("getduration") 
        elseif action == 'set' then 
            return Tasksync.transferobject(obj,v)  
        elseif action == 'kill' or action == 'break' then 
            Tasksync.deleteloop(obj)
        end 
    end 
end 

Tasksync.__createNewThreadForNewDurationLoopFunctionsGroup = function(duration,init)
    local init = init   
    CreateThread(function()
        local loop = Loops[duration]
        
        if init then init() init = nil end
        repeat 
            local Objects = (loop or e)
            local n = #Objects
            for i=1,n do 
                (Objects[i] or e)()
            end 
            Wait(duration)
            
        until n == 0 
        --print("Deleted thread",duration)
        return 
    end)
end     

Tasksync.__createNewThreadForNewDurationLoopFunctionsGroupDebug = function(duration,init)
    local init = init   
    CreateThread(function()
        local loop = Loops[duration]
        local e = {}
        if init then init() init = nil end
        repeat 
            local Objects = (loop or e)
            local n = #Objects
            for i=1,n do 
                (Objects[i] or e)()
            end 
        until n == 0 
        --print("Deleted thread",duration)
        return 
    end)
end     

Tasksync.addloop = function(duration,fn,fnondelete,isreplace)
    local obj = newloopobject(duration,fn,fnondelete)
    obj("setref",ref(duration,obj))
    local indexs = Loops[duration]
    if isreplace and Loops[duration] then 
        for i=1,#indexs do 
            if indexs[i]("getfn") == fn then 
                remove(indexs[i])
            end 
        end 
    end 
    init(duration,obj,function()
        if duration < 0 then Tasksync.__createNewThreadForNewDurationLoopFunctionsGroupDebug(duration) else 
            Tasksync.__createNewThreadForNewDurationLoopFunctionsGroup(duration)
        end 
    end)
    return obj
end 
Tasksync.insertloop = Tasksync.addloop

Tasksync.deleteloop = function(obj)
    remove(obj,function()
        obj("ondelete")
    end)
end 
Tasksync.removeloop = Tasksync.deleteloop

Tasksync.transferobject = function(obj,duration)
    local old_duration = obj("getduration")
    if duration ~= old_duration then 
        updateloop(obj,duration,function()
            obj("setduration",duration)
            Wait(old_duration)
        end)
    end 
end 
 
local newreleasetimer = function(obj,timer,cb)
    local releasetimer = timer   + GetGameTimer()
    local obj = obj 
    local tempcheck = Tasksync.PepareLoop(250)  
    tempcheck(function(duration)
        if GetGameTimer() > releasetimer then 
            tempcheck:delete()
            Tasksync.deleteloop(obj)
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


Tasksync.setreleasetimer = function(obj,releasetimer)
    if not obj("getreleasetimerobject") then 
        obj("setreleasetimerobject",newreleasetimer(obj,releasetimer,function()
            obj("setreleasetimerobject",nil)
        end))
    else 
        obj("getreleasetimerobject")("set",releasetimer)
    end 

end 

Tasksync.PepareLoop = function(duration,releasecb)
    local self = {}
    local obj = nil 
    self.add = function(self,_fn,_fnondelete)
        local ontaskdelete = nil
        if not _fnondelete then 
            if releasecb then 
                ontaskdelete = function(obj)
                    releasecb(obj)
                end 
            end
        else 
            if releasecb then 
                ontaskdelete = function(obj)
                    releasecb(obj)
                    _fnondelete(obj)
                end 
            else 
                ontaskdelete = _fnondelete
            end
        end
        obj = Tasksync.addloop(duration,_fn,ontaskdelete)
        return obj
    end
    self.delete = function(self,duration)
        if obj then 
            if duration then 
                Tasksync.setreleasetimer(obj,duration) 
            else 
                Tasksync.deleteloop(obj) 
            end 
        end
    end
    self.release = self.delete
    self.remove = self.delete
    self.kill = self.delete
    self.set = function(self,newduration)
        if obj then Tasksync.transferobject(obj,newduration) end 
    end
    self.get = function(self)
        if obj then return obj("getduration") end 
    end

    return setmetatable(self,{__call = function(self,...)
        return self:add(...)
    end})
end
end 

PepareLoop = _M_.PepareLoop
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

