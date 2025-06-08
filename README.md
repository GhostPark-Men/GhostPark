-- GHOST PARK v2 (Corrigido Fly + Fling com busca parcial e toggle)

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- GUI
local gui = Instance.new("ScreenGui", game.CoreGui)
gui.Name = "GHOSTPARK_Hub"

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 300, 0, 200)
frame.Position = UDim2.new(0, 50, 0, 100)
frame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
frame.BorderSizePixel = 0

-- Título
local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 30)
title.Text = "👻 GHOST PARK"
title.TextColor3 = Color3.fromRGB(180, 0, 255)
title.BackgroundTransparency = 1
title.Font = Enum.Font.GothamBlack
title.TextSize = 20

-- Fly Button
local flyBtn = Instance.new("TextButton", frame)
flyBtn.Size = UDim2.new(0, 120, 0, 40)
flyBtn.Position = UDim2.new(0, 20, 0, 50)
flyBtn.Text = "🕊️ Ativar Fly"
flyBtn.BackgroundColor3 = Color3.fromRGB(60, 0, 100)
flyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
flyBtn.Font = Enum.Font.Gotham
flyBtn.TextSize = 16

-- Nome jogador
local nomeBox = Instance.new("TextBox", frame)
nomeBox.Size = UDim2.new(0, 180, 0, 35)
nomeBox.Position = UDim2.new(0, 20, 0, 110)
nomeBox.PlaceholderText = "Nome do jogador (3+ letras)"
nomeBox.BackgroundColor3 = Color3.fromRGB(40, 0, 60)
nomeBox.TextColor3 = Color3.new(1,1,1)
nomeBox.Font = Enum.Font.Gotham
nomeBox.TextSize = 14

-- Fling Button
local flingBtn = Instance.new("TextButton", frame)
flingBtn.Size = UDim2.new(0, 80, 0, 35)
flingBtn.Position = UDim2.new(0, 210, 0, 110)
flingBtn.Text = "🔄 Fling"
flingBtn.BackgroundColor3 = Color3.fromRGB(120, 0, 200)
flingBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
flingBtn.Font = Enum.Font.GothamBold
flingBtn.TextSize = 14

-- Fly Function
local flying = false
local flyConn

local function toggleFly()
	local char = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
	local hrp = char:WaitForChild("HumanoidRootPart")

	if flying then
		flying = false
		if hrp:FindFirstChild("FlyVelocity") then hrp.FlyVelocity:Destroy() end
		if flyConn then flyConn:Disconnect() end
		flyBtn.Text = "🕊️ Ativar Fly"
	else
		flying = true
		local bv = Instance.new("BodyVelocity", hrp)
		bv.Name = "FlyVelocity"
		bv.MaxForce = Vector3.new(1,1,1) * 1e5
		bv.Velocity = Vector3.zero
		flyBtn.Text = "🛑 Desativar Fly"

		local UIS = game:GetService("UserInputService")
		local cam = workspace.CurrentCamera
		local moveVec = Vector3.zero

		local function updateVelocity()
			if not flying or not bv or not hrp then return end
			bv.Velocity = cam.CFrame:VectorToWorldSpace(moveVec) * 60
		end

		UIS.InputBegan:Connect(function(i)
			if i.UserInputType == Enum.UserInputType.Keyboard then
				if i.KeyCode == Enum.KeyCode.W then moveVec = moveVec + Vector3.new(0,0,-1) end
				if i.KeyCode == Enum.KeyCode.S then moveVec = moveVec + Vector3.new(0,0,1) end
				if i.KeyCode == Enum.KeyCode.A then moveVec = moveVec + Vector3.new(-1,0,0) end
				if i.KeyCode == Enum.KeyCode.D then moveVec = moveVec + Vector3.new(1,0,0) end
				if i.KeyCode == Enum.KeyCode.Space then moveVec = moveVec + Vector3.new(0,1,0) end
				if i.KeyCode == Enum.KeyCode.LeftShift then moveVec = moveVec + Vector3.new(0,-1,0) end
			end
		end)

		UIS.InputEnded:Connect(function(i)
			if i.UserInputType == Enum.UserInputType.Keyboard then
				if i.KeyCode == Enum.KeyCode.W then moveVec = moveVec - Vector3.new(0,0,-1) end
				if i.KeyCode == Enum.KeyCode.S then moveVec = moveVec - Vector3.new(0,0,1) end
				if i.KeyCode == Enum.KeyCode.A then moveVec = moveVec - Vector3.new(-1,0,0) end
				if i.KeyCode == Enum.KeyCode.D then moveVec = moveVec - Vector3.new(1,0,0) end
				if i.KeyCode == Enum.KeyCode.Space then moveVec = moveVec - Vector3.new(0,1,0) end
				if i.KeyCode == Enum.KeyCode.LeftShift then moveVec = moveVec - Vector3.new(0,-1,0) end
			end
		end)

		flyConn = game:GetService("RunService").Heartbeat:Connect(updateVelocity)
	end
end

flyBtn.MouseButton1Click:Connect(toggleFly)

-- Fling Function
local function getPlayerByPartialName(partial)
	partial = partial:lower()
	for _, p in ipairs(Players:GetPlayers()) do
		if p ~= LocalPlayer and p.Name:lower():sub(1, #partial) == partial then
			return p
		end
	end
end

local function flingPlayerByName(partialName)
	local target = getPlayerByPartialName(partialName)
	if not target or not target.Character then
		warn("Jogador não encontrado.")
		return
	end

	local hrp = LocalPlayer.Character:WaitForChild("HumanoidRootPart")
	local targetHRP = target.Character:WaitForChild("HumanoidRootPart")

	local bp = Instance.new("BodyPosition", hrp)
	bp.MaxForce = Vector3.new(1,1,1) * math.huge
	bp.Position = targetHRP.Position

	local bg = Instance.new("BodyGyro", hrp)
	bg.MaxTorque = Vector3.new(1,1,1) * math.huge
	bg.CFrame = hrp.CFrame

	coroutine.wrap(function()
		while targetHRP and targetHRP.Parent and bp.Parent and bg.Parent do
			task.wait()
			bp.Position = targetHRP.Position
			bg.CFrame = bg.CFrame * CFrame.Angles(0, math.rad(1000), 0)
		end
	end)()
end

flingBtn.MouseButton1Click:Connect(function()
	local input = nomeBox.Text
	if input and #input >= 3 then
		flingPlayerByName(input)
	end
end)
