# SoTScript
## _Documentation_

SoTScript is a external overlay for Sea of Thieves that works with Lua 5.3, currently it allow only load one script file.
This tool help your to dump offsets and get actors of current level to do ESP features with some easy functions

## Script file skeleton

```lua
function Register()
    --first step, used to add objects to be fetched in actors list
end

function FetchData()
	--second step, called after all objects is fetched, use this state to add custom data to the object
end

function Render(DeltaTime)
	--third step,called after all scripts fetched data, used to render object on screen, you can get data from object that other script added
end

function Clear()
	--last step, called after all process above ends, it can be used to clear your data stored in this current script
end
```

## Global namespaces
- [Unreal](#unreal)
- [SOT](#sot)
- [Utils](#utils)
- [Draw](#draw)
- [Memory](#memory)

## Namespaces contents

### Unreal
- ObjectCount() : i32 - Get number of elements in GObjects
- GetObject(uint32) : uint64 - Get object by id
- GetObjectPtr(uint32) : uint64 - Get object address by id
- FindObject(string) : uint64 - Get object address by name
- ObjectName(uint64) : string - Get object name by address
- ObjectFullName(uint64) : string - Get object full name by address
- GetOffset(uint64, string) : uint64 : Get offset of field in object by address and field name
- GetClassSize(uint64) : uint64 - Get class/struct size of object by address
- Offsets(uint64) : table<string, uint64> - Get all offsets of object by address
- FName(uint32) : string - Get FName text by id

### Memory
- ReadPtr(uint64) : uint64
- ReadFloat(uint64) : float
- ReadBool(uint64) : boolean
- ReadInt32(uint64) : int32
- ReadUInt32(uint64) : uint32
- ReadInt64(uint64) : int64
- ReadVector3(uint64) : SOT.Vector3
- ReadRotator(uint64) : SOT.Rotator

### Utils
- ScreenSize() : SOT.Vector2 - Returns Width and Height of overlay
- WorldToScreen(SOT.Vector3) : table<boolean, SOT.Vector2> - Return a table, if the object is in screen bounds the boolean will return true, SOT.Vector2 will return position in screen

### Draw
- FrameRate : float - Return FPS of overlay
- Line(SOT.Vector2 start, SOT.Vector2 end, uint32 color, float thickness) : void - Render a line in screen
- Circle(SOT.Vector2 pos, float radius, uint32 color, [opt:int:num_segments], [opt:float:thickness]) : void Render a circle border in screen
- CircleFilled(SOT.Vector2 pos, float radius, uint32 color, int num_segments) : void - Render a circle filled in screen
- Rect(SOT.Vector2 start, SOT.Vector2 end, uint32 color, float rounding, float thickness) : void - Render a square border in screen
- RectFilled(SOT.Vector2 start, SOT.Vector2 end, uint32 color, float rounding, float thickness) : void - Render a square filled in screen
- Text(SOT.Vector2 pos, uint32 color, string text) : void - Render a text on screen
- TextCentered(SOT.Vector2 pos, uint32 color, string text) : void - Render a text centered on position in screen

### SOT

- RegisterObject(uint64) : void - Add object to be fetched in ActorList
- RegisterObject(string) : void - Add object name to be fetched in ActorList
- RegisterPartialName(string) : void - Add part of actor name to be fetched in ActorList
- GetActorList() : array<AActor> - Get all actors fetched
- LocalController : APlayerController - Get local player controller actor
- CameraManager : APlayerCameraManager - Get the CameraManager actor of local player actor


- UObject | class
    - Constructors: void(uint64 address)
    - Address : uint64 - Get address of object
    - GetAddress() : uint64 - Get address of object
    - GetId() : int32 - Get id of object
    - GetName() : string - Get name of object
    - GetFullName() : string - Get full name of object
    - SetData(string key, any value) : void - Store some data in the object (currently not work on components)
    - GetData(string key) : any - Get the saved data stored in the object (currently not work on components)
    - ReadVector2(<u64|string>) : SOT.Vector2 - Read memory using offset or field name inside this object
    - ReadVector3(<u64|string>) : SOT.Vector3 - Same as above
    - ReadVector4(<u64|string>) : SOT.Vector4 - Same as above
    - ReadFloat(<u64|string>) : float - Same as above
    - ReadBool(<u64|string>) : boolean - Same as above
    - ReadPtr(<u64|string>) : uint64 - Same as above
    - ReadRotator(<u64|string>) : SOT.FRotator - Same as above


- USceneComponent | UObject
    - Inherit all contents of UObject, that include the constructor
    - GetPosition() : SOT.Vector3 - Returns position of component in world
    - GetRotation() : SOT.FRotator - Returns rotation of component in world using ComponentToWorld


- AActor | UObject
    - Inherit all contents of UObject, that include the constructor
    - GetRootComponent() : SOT.USceneComponent
    - GetPosition() : SOT.Vector3 - Return actor position in world
    - GetRotation() : SOT.FRotator - Return actor rotation in world usng SceneComponent::ComponentToWorld


- AController | AActor
    - Inherit all contents of AActor, that include the constructor


- APlayerController | AController
    - Inherit all contents of AController, that include the constructor


- APlayerCameraManager | UObject
    - Inherit all contents of UObject, that include the constructor
    - GetPosition() : SOT.Vector3 - Returns player camera position in world
    - GetRotation() : SOT.FRtotator - Returns player camera rotation in world
    - GetFOV() : float - Returns player camera fov value


- Vector2 | structure
    - Constructors: void, void(float), void(float, float)
    - X : float
    - Y: float
    - Length() : float
    - LengthSqr() : float
    - DistTo(SOT.Vector2) : float


- Vector3 | structure
    - Constructors: void(), void(float), void(float, float, float)
    - X : float
    - Y : float
    - Z : float
    - Length() : float
    - Length2d() : float
    - LengthSqr() : float
    - DistTo(SOT.Vector2) : float
    - DistTo2d(SOT.Vector2) : float
    - Zero : static SOT.Vector3
    - One : static SOT.Vector3


- Vector4 | structure
    - Constructors: void(), void(float, float, float, float)
    - X : float
    - Y : float
    - Z : float
    - W : float


- FRotator | structure
    - Constructors: void(), void(float, float, float), void(SOT.Vector4)
    - Pitch : float
    - Yaw : float
    - Roll : float
    - Clamp() : SOT.FRotator
    - Quaternion() : SOT.Vecto4


## Additional information
When registering a object, will automatic set the `_type` to the object data, containing the value used to fetch the actor (`RegisterObject` or `RegisterPartialName`).
If we fetch data using `RegisterObject("Class Engine.Actor")` then `actor:GetData('_type')` will return `Class Engine.Actor` string, if we fetch using `FindObject`, `_type` will return `object address`, same address returned in `FindObject`, same for `PartialName`
It is to help known the type of object fetched if you want to fetch multiples object types

## Additional Lua library
- json
    - encode(any, [opt:boolean:indent]) - Convert data to text
    - decode(string) - Convert text to data

## Examples:

##### How to get offset of class and export it to json file?

```lua
local json = require("json")
local io = require("io")

local objectShipClass = Unreal.FindObject("Class Athena.Ship")

local ofs = Unreal.Offsets(objectShipClass)
local file = io.open("Ship_offset.json", "w")
file:write(json.encode(ofs, true))
file:close()
```


##### How i can render some object in screen?

```lua
--lets render the ship's on screen

--We will start saving objects to be used in this script
local objActor = Unreal.FindObject("Class Engine.Actor")
local objRepMovement = Unreal.FindObject("ScriptStruct Engine.RepMovement")
local objectShipClass = Unreal.FindObject("Class Athena.Ship")

--Lets get the LinearVelocity offset from the RepMovement
local OFFSET_LinearVelocity = Unreal.GetOffset(objRepMovement, "LinearVelocity")

--Now lets get the ReplicatedMovement offset from the Actor, ReplicatedMovement is RepMovement structure
local OFFSET_ActorRepMovement = Unreal.GetOffset(objActor, "ReplicatedMovement")

--Since ReplicatedMovement is a structure and not a pointer, it means that the RepMovement data will be added to the body of this object, so all the RepMovement content will be part of here, so we will sum the offsets to get LinearVelocity
OFFSET_LinearVelocity = OFFSET_ActorRepMovement + OFFSET_LinearVelocity

--Lets register the Ship object to be fetch in ActorList
function Register()
	SOT.RegisterObject(objectShipClass)
end

--Now we will get LinearVelocity to the object and save the data to use in render
function FetchData()
    --Lets loop the fetched actor list
    for key,value in pairs(SOT.GetActorList()) do
        --Checking if the object is a Ship
        if value:GetData("_type") == objectShipClass then
            --Reading a vector3 using offset
			local vec = value:ReadVector3(OFFSET_LinearVelocity)
			--Saving LinearVelocity to the object data
            value:SetData("LinearVelocity", vec)
            --Lets say it is a ship, but in a simpler way than using _type
            value:SetData("IsShip", true)
        end
    end
end

--Lets render the object in screen
function Render(DeltaTime)
    -- I want to see the overlay FPS in screen
	Draw.Text(SOT.Vector2(200, 3), 0xFFFFFFFF, Draw.FrameRate)
	-- We need the current camera position to get object distance
	local myCamPos = SOT.CameraManager:GetPosition()

    --We will go through all saved objects in the list
    for key,value in pairs(SOT.GetActorList()) do
        --We will get object position
		local actorPos = value:GetPosition()
		--We will get distance from object to the camera in 2D space, So the height will not interfere with the distance
		local fDist = myCamPos:DistTo2d(actorPos) * 0.01;

        --Lets get the object position in the screen
        local isInScreen, pos = table.unpack(Utils.WorldToScreen(actorPos))
        --And we will check if object is in screen
        if isInScreen then
            --Lets set the default text to render ([200m] Actor - SomeName)
            local textToDraw = string.format("[%.0fm] Actor - %s", fDist, value:GetName())
            
            --If the object is a ship, we set it in FetchData
            if value:GetData("IsShip") == true then
                --We will get the LinearVelocity, we set it in FetchData
				local shipLinearVel = value:GetData("LinearVelocity")
				--We will update the var to show speed and say it is a ship ([200m] Ship - 12.3m/s)
				textToDraw = string.format("[%.0fm] Ship - %.1fm/s", fDist, shipLinearVel:Length2d())
            end
            --We finally draw the info in screen
            Draw.TextCentered(pos, 0xFFFFFFFF, textToDraw)
        end
    end
end

--We dont will use this function, but we need to add in the script file
function Clear()

end
```
