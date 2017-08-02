# Finding Vehicles in Homebrew

This is quite an intensive subject, as finding your own vehicles can be quite an extensive task.

There are a couple of ways of doing it.

Grabbing your _latest spawned vehicle_ from HBU through a helper function.

Manually raycasting, and verifying it's a vehicle object.

Getting every single vehicle in-game, and filtering out to only select your own.

The easiest would be using HBU to return your latest vehicle, this is rather simple

```lua
self.vehicle = HBU.GetMyOldestVehicle()
if(Slua.IsNull(self.vehicle)) then
  Debug.LogError("There was no vehicle of yours spawned!")
  return
end
```

Be sure to check if the returned object is null or not, using ``Slua.IsNull(object)``!

Raycasting is also a bit strange in Lua, since Unity's raycasting system uses some explicit C# language agnostics, more specifically, the ``out`` parameter.

Therefor Slua has a helper object for this.

```lua
local ok, hitinfo = Physics.Raycast(origin, direction, Slua.out)
if(ok) then
  Debug.Log("We've hit " .. hitinfo.transform.name)
end
```

As you can see, it returns 2 values, ok, and hitinfo (using Slua.out), ok is a boolean, to see if you actually hit _anything_, while hitinfo is a [RaycastHit](https://docs.unity3d.com/ScriptReference/RaycastHit.html)

Raycast hit contains the collider it hit, distance, normal, the point of contact, and some other values that might be of interest. Be sure to read up on the Unity3D page linked above about raycastHits!

The last method will be fetching all Vehicles in _'The scene'_, and returning only your own vehicles.

It's best to make a helper function for this

```lua
function GetMyVehicles()
  local vehicles = GameObject.FindObjectsOfType("VehiclePiece")
  local ret = {}
  for t in Slua.iter(vehicles) do
    local nc = t:GetComponent("NetworkBase")
    if not Slua.IsNull(nc) then --make sure networkbase isn't nil
      if nc.Owner then --make sure you're the owner of the vehicle
        table.insert(ret, t) --insert it into your return table
      end
    end
  end
  return ret
end
```
Note that this function returns the vehicle pieces, so if you have a vehicle, with a detacher, you'll return 2 gameObjects!