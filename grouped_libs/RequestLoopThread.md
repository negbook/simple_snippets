---
for: shared
---
## RequestLoopThread

```lua
handle = RequestLoopThread(duration)
```

## Snippet Code
```
local e,loops,taskduration,task = {},{},{},{}
local task_inner_onupdate = {}
local task_inner_identity = {}
local threads_total = 0
local GetThreadsTotal = function() return ("threads_total"..threads_total) end 

local GetDurationByTaskName = function(handle)
    if not handle then return end 
    for duration,name_list in pairs(loops) do 
        for i=1,#name_list do 
            if name_list[i] == handle then 
                return duration 
            end 
        end 
    end 
end 

local GetTaskIndexFromLoopGroup = function(handle,duration)
    if not handle then return end 
    local name_list = loops[duration]
    if name_list then 
        for idx=1,#name_list do 
            local v = name_list[idx]
            if v == handle then 
                return idx
            end 
        end 
    end 
end 

local DeleteTaskByNameFromLoopGroup = function(handle,duration)
    if not handle then return end 
    local task_index = GetTaskIndexFromLoopGroup(handle,duration)
    if (loops[duration] or e)[task_index] then table.remove(loops[duration],task_index) end 
    if loops[duration] and #loops[duration] == 0 then loops[duration] = nil end 

end 

local deleteTaskAuto = function(handle)
    if not handle then return end 
    local duration = GetDurationByTaskName(handle)
    if duration then DeleteTaskByNameFromLoopGroup(handle,duration) end 
end 

local isTaskAlive = function(handle)
    if not handle then return end 
    return GetDurationByTaskName(handle)
end 

local updateTask = function(handle,newduration,cb)
    if not handle then return end 
    local oldduration = GetDurationByTaskName(handle)
    if oldduration ~= newduration then  
        task_inner_onupdate[handle] = cb 
        return 
    end 
end 


local createLoopGroup;createLoopGroup = function(duration)
    CreateThread(function()
        threads_total = threads_total + 1
        local actionNames = loops[duration]
        repeat 
            Wait(duration)
            local actNames = actionNames or e 
            local hasAction = actNames[1]
            
            if hasAction then 
                
                for i=1,#actNames do 
                    local handle = actNames[i] 
                    if handle then 
                        local action = task[handle]
                        local task_inner_update = task_inner_onupdate[handle]
                        if action then 
                            action((((task_inner_identity or e)[duration] or e)[handle] or e))
                        end 
                        if task_inner_update then 
                            task_inner_update(duration)
                        end 
                    end 
                end 
            end 
        until not hasAction
        threads_total = threads_total - 1
        return 
        
    end) 
end 

local addTask = function(handle,duration,onaction)
    if not handle then return end 
    task[handle] = onaction  
    if loops[duration] == nil then 
        loops[duration] = {}; 
        createLoopGroup(duration)
    end 
    table.insert(loops[duration],handle)
    return handle
end 

RequestLoopThread = function(duration)
    local result = setmetatable({},{
        __tostring = function(t)
            local idx = 0 
            local tasks = {}
            for i,v in pairs(t) do 
                if i ~= "debug" then 
                    idx = idx + 1
                    table.insert(tasks,i)
                end 
            end 
            return "This handle has "..idx .." tasks: "..table.concat(tasks,",")
        end,
        __newindex=function(t,name,fn) 
            if not fn then print('wtf') end 
            local Set 
            local Kill = function()
                DeleteTaskByNameFromLoopGroup(t[name],GetDurationByTaskName(t[name]))
                rawset(t,name,nil)
            end
            
            local newobj = function(default)
                local value = default or 0
                return function(action,v) 
                    if action == 'get' then 
                        if v then 
                            return value 
                        else
                            return value  
                        end 
                    elseif action == 'set' then 
                        if value ~= v then 
                            value = v 
                            Set(v)
                        end 
                    elseif action == 'kill' or action == 'break' then 
                        Kill()
                    end 
                end 
            end
            local obj = newobj(duration)
            Set = function(newduration)
                updateTask(t[name],newduration,function()
                    task_inner_onupdate[t[name]] = nil
                    local oldduration = GetDurationByTaskName(t[name])
                    --Wait(oldduration)
                    if not task_inner_identity[newduration] then task_inner_identity[newduration] = {} end 
                    task_inner_identity[newduration][t[name]] = obj 
                    if loops[newduration] == nil then 
                        loops[newduration] = {}; 
                        createLoopGroup(newduration)
                    end 
                    DeleteTaskByNameFromLoopGroup(t[name],oldduration)
                    table.insert(loops[newduration],t[name])
                    
                end)
            end
            rawset(t,name,obj)
            if fn then 
                if not task_inner_identity[duration] then task_inner_identity[duration] = {} end 
                task_inner_identity[duration][t[name]] = obj
                addTask(t[name],duration,fn) 
            end 
        end}
    )
    return result
end 

```

## Example 
```
local handle = RequestLoopThread(500)
handle['name'] = function(duration)
    duration("set",1000) --new duration 
    print(duration("get")) -- get current duration
    duration("kill") -- kill this task from the loop 
end 


local Loop = RequestLoopThread(1000)
local Loop2 = RequestLoopThread(1000)

Loop["test"] = function(duration)
    print("Loop test "..duration("get"))
    print(GetThreadsTotal())
    print(Loop)
end 

Loop2["test"] = function(duration)
    print("Loop2 test "..duration("get"))
    print(GetThreadsTotal())
    duration("set",500)
    Loop["test"]("set",500)
    
end 

```

