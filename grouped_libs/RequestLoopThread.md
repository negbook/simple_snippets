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
local e,loops,taskduration,task = {},{},{},{}
setmetatable(e,{__call = function() end })
local task_inner_onupdate = {}
local threads_total = 0
local GetThreadsTotal = function() return ("threads_total"..threads_total) end 

local GetDurationByHandle = function(handle)
    if not handle then return end 
    for duration,handles in pairs(loops) do 
        for i=1,#handles do 
            if handles[i] == handle then 
                return duration 
            end 
        end 
    end 
end 

local DeleteTaskByHandleFromLoopGroup = function(handle,duration)
    if not handle then return end 
    local handle_index
    local handles = loops[duration]
    if handles then 
        for idx=1,#handles do 
            local v = handles[idx]
            if v == handle then 
                handle_index = idx
                break
            end 
        end 
    end 
    
    if (loops[duration] or e)[handle_index] then table.remove(loops[duration],handle_index) end 
    if loops[duration] and #loops[duration] == 0 then loops[duration] = nil end 

end 

local updateTask = function(handle,newduration,cb)
    if not handle then return end 
    local oldduration = GetDurationByHandle(handle)
    if oldduration ~= newduration then  
        task_inner_onupdate[handle] = cb 
        return 
    end 
end 


local createLoopGroup;createLoopGroup = function(duration)
    if loops[duration] == nil then 
        loops[duration] = {}; 
        CreateThread(function()
            threads_total = threads_total + 1
            local handles = loops[duration]
            repeat 
                Wait(duration)
                local handles = handles or e 
                local hasAction = handles[1]
                
                if hasAction then 
                    
                    for i=1,#handles do 
                        local handle = handles[i] 
                        if handle then 
                            local action = task[handle]
                            local task_inner_update = task_inner_onupdate[handle]
                            if action then 
                                action(handle)
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
end 

local takeTaskToNewLoopGroup = function(handle, newduration)
    updateTask(handle,newduration,function()
        task_inner_onupdate[handle] = nil
        local oldduration = GetDurationByHandle(handle)
        createLoopGroup(newduration)
        DeleteTaskByHandleFromLoopGroup(handle,oldduration)
        table.insert(loops[newduration],handle)
        
    end)
end 

local addTask = function(handle,duration,onaction)
    if not handle then return end 
    task[handle] = onaction  
    createLoopGroup(duration)
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
        __mode = "kv",
        __newindex=function(t,name,fn) 
            local Kill = function(newduration)
                DeleteTaskByHandleFromLoopGroup(t[name],GetDurationByHandle(t[name]))
            end 
            
            local Set = function(newduration)
                takeTaskToNewLoopGroup(t[name],newduration)
            end 

            local Check = function()
                return not not  t[name]
            end 
            local newobj = function(default)
                local value = default or 0
                return function(action,v) 
                    if Check() then  
                        if action == 'get' then 
                            if v then 
                                return value or 0
                            else
                                return value or 0
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
            end
            local obj = newobj(duration)
            
            rawset(t,name,obj)
            if fn then 
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

