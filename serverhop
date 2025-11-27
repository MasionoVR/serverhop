local gui = Instance.new("ScreenGui")
gui.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")


local file = "serverhoppos.json"
local HttpService = game:GetService("HttpService")

local saved
pcall(function()
	if isfile(file) then
		saved = HttpService:JSONDecode(readfile(file))
	end
end)


local b = Instance.new("TextButton")
b.Size = UDim2.new(0,120,0,40)
b.BackgroundColor3 = Color3.fromRGB(30,150,255)
b.Text = "ServerHop"

if saved then
	b.Position = UDim2.new(saved.XScale, saved.XOffset, saved.YScale, saved.YOffset)
else
	b.Position = UDim2.new(1,-150,0,20)
end

b.Parent = gui


local ts = game:GetService("TweenService")
local dragging = false
local dragStart, startPos

local function dragMove(inp)
	local d = inp.Position - dragStart
	local pos = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X, startPos.Y.Scale, startPos.Y.Offset + d.Y)
	ts:Create(b, TweenInfo.new(.15, Enum.EasingStyle.Sine), {Position = pos}):Play()
end

b.InputBegan:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = i.Position
		startPos = b.Position
		
		i.Changed:Connect(function()
			if i.UserInputState == Enum.UserInputState.End then
				dragging = false
				
				-- save
				local data = {
					XScale = b.Position.X.Scale,
					XOffset = b.Position.X.Offset,
					YScale = b.Position.Y.Scale,
					YOffset = b.Position.Y.Offset
				}
				writefile(file, HttpService:JSONEncode(data))
			end
		end)
	end
end)

b.InputChanged:Connect(function(i)
	if i.UserInputType == Enum.UserInputType.MouseMovement and dragging then
		dragMove(i)
	end
end)


local TeleportService = game:GetService("TeleportService")
local placeId = game.PlaceId
local Http = game:GetService("HttpService")

local function getServer()
	local list = {}
	local cur = ""

	repeat
		local r = Http:JSONDecode(game:HttpGet("https://games.roblox.com/v1/games/"..placeId.."/servers/Public?limit=100&cursor="..cur))
		for _,v in ipairs(r.data) do
			if v.playing < v.maxPlayers and v.id ~= game.JobId then
				table.insert(list, v)
			end
		end
		cur = r.nextPageCursor or ""
	until cur == "" or #list > 0

	if #list > 0 then
		return list[math.random(1, #list)]
	end
end

b.MouseButton1Click:Connect(function()
	b.Text = "Hopping..."
	b.Active = false

	local s = getServer()
	if s then
		TeleportService:TeleportToPlaceInstance(placeId, s.id)
	else
		b.Text = "No Servers"
		task.wait(2)
		b.Text = "ServerHop"
		b.Active = true
	end
end)
