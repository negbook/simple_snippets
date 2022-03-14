---
for: client
---
## GetEntityFromGamePlayCameraPointAt

```lua
GetEntityFromGamePlayCameraPointAt(EntityType,ingoredEntity)
```

## Snippet Code
```
local sin = math.sin
local cos = math.cos
local pi = math.pi
local nowaction = 0
function GetEntityFromGamePlayCameraPointAt(ingoredEntity) 
    local action = 0
    local distance = 300
    local coordsVector =  GetFinalRenderedCamCoord() ;
    local rotationVectorUnrad = GetFinalRenderedCamRot(2);
    rotationVector = rotationVectorUnrad * pi / 180
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
    for i=0,5 do 
        if nowaction == 0 then 
            shapeTestId = StartShapeTestLosProbe(destination_temp, destination, 511, ingoredEntity or playerPed, 7)
            if shapeTestId ~= 0 then 
                nowaction = 1;
            end 
        end 
        if nowaction == 1 then 
            local shapeTestResult , hit , endCoords , surfaceNormal , entityHit = GetShapeTestResult(shapeTestId)
            if (shapeTestResult == 2) then
                if (hit == 0) then
                    endCoords = vector3( 0, 0, 0 );
                else
                    if (DoesEntityExist(entityHit))  then
                        return hit, getentitytype(entityHit), entityHit, #(endCoords - destination_temp), surfaceNormal
                    end
                    shapeTestId = 0;
                    nowaction = 0;
                end
            elseif (shapeTestResult == 0) then
                nowaction = 0;
            end
        end
    end
    return false
end 
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

