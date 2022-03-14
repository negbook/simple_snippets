---
for: client
---
## GetEntityFromGamePlayCameraPointAtSynced

```lua
GetEntityFromGamePlayCameraPointAtSynced(ingoredEntity)
```

## Snippet Code

```
local sin = math.sin
local cos = math.cos
local torad = math.pi / 180
function GetEntityFromGamePlayCameraPointAtSynced(ingoredEntity) 
    local action = 0
    local distance = 300
    local coordsVector =  GetFinalRenderedCamCoord() ;
    local rotationVectorUnrad = GetFinalRenderedCamRot(2);
    rotationVector = rotationVectorUnrad * torad
    local directionVector =  vector3(-sin(rotationVector.z) * cos(rotationVector.x), (cos(rotationVector.z) * cos(rotationVector.x)), sin(rotationVector.x));
    local destination =  coordsVector + directionVector * distance ;
    local playerPed = PlayerPedId()
    local destination_temp = coordsVector + directionVector * 1 ;
    local getentitytype = function(entity)
       local type = GetEntityType(entity)
       local result = "solid"
       if type == 0 then 
          result = "solid"
       elseif type == 1 then 
          result = "ped"
       elseif type == 2 then 
          result = "vehicle"
       elseif type == 3 then 
          result = "object"
       end 
       return result
    end 
    
    local shapeTestId = StartExpensiveSynchronousShapeTestLosProbe(destination_temp, destination, 511, ingoredEntity or playerPed, 4)
    local shapeTestResult , hit , endCoords , surfaceNormal , entityHit = GetShapeTestResult(shapeTestId)
    local ishit, entitytype, entity, distance, surfaceNormal = hit, getentitytype(entityHit), entityHit, #(endCoords - destination_temp), surfaceNormal
    return ishit, entitytype, entity, distance, surfaceNormal
end 
```

### Example:
```
function GetPlayerPedOrVehicle(player)
  local ped = GetPlayerPed(player)
  local veh = GetVehiclePedIsIn(ped,false)
  return veh~=0 and veh or ped
end
CreateThread(function()
    while true do Wait(1000)
        local hit, entitytype, entity, distance, surfaceNormal = GetEntityFromGamePlayCameraPointAtSynced(GetPlayerPedOrVehicle(PlayerId()))
        if entity and entitytype ~= "solid" then 
            DrawLine(GetEntityCoords(PlayerPedId()),GetEntityCoords(entity),255,0,0,255)
            SetEntityCoords(entity,GetOffsetFromEntityInWorldCoords(GetPlayerPedOrVehicle(PlayerId()),0.0,8.0,0.0))
            print(entity,distance,surfaceNormal)
        else print(entity,distance,surfaceNormal)

        end 
    end 
end)
```

### Source From
```
am_mp_vehicle_weapon.ysc:
v_6 = { GetCamCoord(Local_125["pf_61"]) };
v_7 = { GetEntityCoords(Local_125["pf_84"][v_1], true) };
switch (Local_125["pf_10"][v_1])(
	case (0)(function()
		Local_125["pf_98"][v_1] = StartShapeTestLosProbe(v_6, v_7, 511, 0, 7);
		if (Local_125["pf_98"][v_1] ~= 0) then
			Local_125["pf_10"][v_1] = 1;
		end
	end),
	
	case (1)(function()
		v_5 = GetShapeTestResult(Local_125["pf_98"][v_1], &v_0, &v_3, &v_2, &v_4);
		if (v_5 == 2) then
			if (v_0 == 0) then
				Local_125["pf_17"][v_1] = 1;
				v_3 = { 0, 0, 0 };
				if (IsBitSet(Local_125["pf_7"], v_1)) then
					ClearBit(&(Local_125["pf_7"]), v_1);
				end
			end
			else
			{
				Local_125["pf_17"][v_1] = 2;
				if (IsBitSet(Local_125["pf_7"], v_1)) then
					ClearBit(&(Local_125["pf_7"]), v_1);
				end
				if (DoesEntityExist(v_4)) then
					if (Local_125["pf_84"][v_1] == v_4) then
						if (not IsBitSet(Local_125["pf_7"], v_1)) then
							SetBit(&(Local_125["pf_7"]), v_1);
						end
					end
				end
				Local_125["pf_98"][v_1] = 0;
				Local_125["pf_10"][v_1] = 0;
			end
		end
		elseif (v_5 == 0) then
			Local_125["pf_10"][v_1] = 0;
		end
	end),
)
```

