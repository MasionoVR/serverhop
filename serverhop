--// Create ScreenGui
local gui = Instance.new("ScreenGui")
gui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")

-- File name for saving position
local saveFile = "serverhoppos.json"

local savedPos
local HttpService = game:GetService("HttpService")

-- Try to load saved position
pcall(function()
    if isfile(saveFile) then
        savedPos = HttpService:JSONDecode(readfile(saveFile))
    end
end)

--// Create button
local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 120, 0, 40)

-- default or saved location
if savedPos then
    button.Position = UDim2.new(savedPos.XScale, savedPos.XOffset, savedPos.YScale, savedPos.YOffset)
else
    button.Position = UDim2.new(1, -150, 0, 20)
end

button.BackgroundColor3 = Color3.fromRGB(30, 150, 255)
button.Text = "ServerHop"
button.Parent = gui

-------------------------------------------------------------------
-- SMOOTH DRAGGING SYSTEM (Tween-based)
-------------------------------------------------------------------
local TweenService = game:GetService("TweenService")
local dragging = false
local dragStart
local startPos

local function tweenTo(input)
    local delta = input.Position - dragStart

    local newPos = UDim2.new(
        startPos.X.Scale,
        startPos.X.Offset + delta.X,
        startPos.Y.Scale,
        startPos.Y.Offset + delta.Y
    )

    TweenService:Create(button, TweenInfo.new(0.15, Enum.EasingStyle.Sine, Enum.EasingDirection.Out), {
        Position = newPos
    }):Play()
end

button.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 then
        dragging = true
        dragStart = input.Position
        startPos = button.Position

        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false

                -- SAVE NEW POSITION
                local posData = {
                    XScale = button.Position.X.Scale,
                    XOffset = button.Position.X.Offset,
                    YScale = button.Position.Y.Scale,
                    YOffset = button.Position.Y.Offset
                }

                writefile(saveFile, HttpService:JSONEncode(posData))
            end
        end)
    end
end)

button.InputChanged:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseMovement and dragging then
        tweenTo(input)
    end
end)

-------------------------------------------------------------------
-- SERVER HOP SYSTEM
-------------------------------------------------------------------
local TeleportService = game:GetService("TeleportService")
local placeId = game.PlaceId

local function findServer()
    local servers = {}
    local cursor = ""

    repeat
        local url = "https://games.roblox.com/v1/games/"..placeId.."/servers/Public?sortOrder=Asc&limit=100&cursor="..cursor
        local response = HttpService:JSONDecode(game:HttpGet(url))

        for _, server in ipairs(response.data) do
            if server.playing < server.maxPlayers and server.id ~= game.JobId then
                table.insert(servers, server)
            end
        end

        cursor = response.nextPageCursor or ""
    until cursor == "" or #servers > 0

    return servers[math.random(1, #servers)]
end

button.MouseButton1Click:Connect(function()
    button.Text = "Hopping..."
    button.Active = false

    local server = findServer()
    if server then
        TeleportService:TeleportToPlaceInstance(placeId, server.id)
    else
        button.Text = "No Servers"
        task.wait(2)
        button.Text = "ServerHop"
        button.Active = true
    end
end)
