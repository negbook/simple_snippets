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

RequestLoopThread = function(...)
    return load(
        [===[
local _M_ = {}
do 
    local Tasksync = _M_
    local Loops = {}
    local FnTask = {}
    local FnOnDelete = {}
    local FnCustomDurations = {}
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

    local function getsetter(default,current_id)
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
                            local obj = getsetter(duration,current_id) 
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
    Tasksync.hasloopcustomduration = function(id)
        return FnCustomDurations[id] ~= nil
    end 



    Tasksync.RequestLoopThread = function(duration,realeaseduration,releasecb)
        local self = {}
        local releasetimer = realeaseduration 
        self.add = function(self,_fn)
            self.fn = _fn
            local id = Tasksync.addloop(duration,self.fn,releasecb)
            if releasetimer then 
                local endtimer = GetGameTimer() + releasetimer
                local temp;temp = Tasksync.addloop(250,function()
                    if GetGameTimer() > endtimer then 
                        Tasksync.deleteloop(temp)
                    end 
                 end,function()
                    releasetimer = nil
                    Tasksync.deleteloop(id)
                end)
            end 
            return id
        end
        self.delete = function(self)
            if self.fn then Tasksync.deleteloop(self.fn) end
        end
        self.set = function(self,newduration)
            Tasksync.setloopcustomduration(fn,newduration)
        end
        self.get = function(self)
            return Tasksync.getloopcustomduration(fn)
        end
        self.has = function(self)
            return Tasksync.hasloopcustomduration(fn)
        end
        return setmetatable(self,{__call = function(self,...)
            return self:add(...)
        end})
    end
end

RequestLoopThread = _M_.RequestLoopThread

return RequestLoopThread(...)
            ]===]
    )(...)
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

