---
for: client
---
## GetCoordsFromGamePlayCameraPointAtByDistance

```lua
GetCoordsFromGamePlayCameraPointAtByDistance(distance)
```

## Snippet Code
```
function GetCoordsFromGamePlayCameraPointAtByDistance(distance) 
    local distance = distance or 300
    coordsVector =  GetFinalRenderedCamCoord() ;
    rotationVector =  GetFinalRenderedCamRot(2) * math.pi / 180 ;
    directionVector =  vector3(-math.sin(rotationVector.z) * math.cos(rotationVector.x), (math.cos(rotationVector.z) * math.cos(rotationVector.x)), math.sin(rotationVector.x));
    destination =  coordsVector + directionVector * distance ;
    return destination
end 
```

### Source From
```
am_mp_vehicle_weapon.ysc:
v_9 = GetEntityBoneIndexByName(func_30_c(), func_55_c());
if (v_9 ~= -1) then
	v_10 = { GetWorldPositionOfEntityBone(func_30_c(), v_9) + func_54_c(Local_125["pf_31"]) };
	v_11 = { GetFinalRenderedCamCoord() };
	v_12 = { GetFinalRenderedCamRot(2) };
	v_13 = { (-Sin(v_12["pf_2"]) * Cos(v_12["pf_0"])), (Cos(v_12["pf_2"]) * Cos(v_12["pf_0"])), Sin(v_12["pf_0"]) };
	v_14 = { 10, 10, 10 };
	if (not func_50_c(Local_125["pf_56"])) then
		v_14["pf_0"] = GetDistanceBetweenCoords(Local_125["pf_56"], v_11, false);
		v_14["pf_1"] = GetDistanceBetweenCoords(Local_125["pf_56"], v_11, false);
		v_14["pf_2"] = GetDistanceBetweenCoords(Local_125["pf_56"], v_11, false);
	end
	v_15 = { v_11 + v_13 * v_14 };
	ShootSingleBulletBetweenCoordsIgnoreEntityNew(v_10, v_15, v_1, true, func_53_c(1), PlayerPedId(), true, true, -1f, v_2, false, false, false, true, 0, 1, 0);
	PlaySoundFromCoord(-1, "Fire", v_10, "DLC_BTL_Terrobyte_Turret_Sounds", true, 120, true);
	SetBit(&(Local_125["pf_5"]), 1);
	Local_125["pf_33"] = 0;
	func_57_c(&(Local_125["pf_134"]), 0, 0);
	Local_125["pf_31"] = Local_125["pf_31"] + 1;
end
```

